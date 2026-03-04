# Implementation Guide

Документ описывает практическую реализацию модулей:

- `catalog` — справочник продукции/товаров/ингредиентов/полуфабрикатов (уровень организации)
- `label_printing` — сервис очереди печати, шаблонов, форматов и терминалов (bitmap печать)

Стек:
- FastAPI
- SQLAlchemy 2.x
- Alembic
- PostgreSQL (UUID type)
- Android Terminal (native) для рендера HTML → bitmap → Bluetooth печать

—

## 1. Структура проекта

Рекомендуемая структура для modular monolith:
app/

  main.py

  core/
    __init__.py
    config.py
    db.py
    security.py
    errors.py

  modules/

    catalog/

      api/
        router.py

      application/
        services/
          product_service.py
          goods_service.py
          ingredient_service.py
          semi_finished_service.py

      domain/
        entities/
          product.py
          goods.py
          ingredient.py
          semi_finished.py
          manufacturer.py

      infrastructure/
        models/
          product_model.py
          goods_model.py
          ingredient_model.py
          semi_finished_model.py
          manufacturer_model.py

        repositories/
          product_repository.py
          goods_repository.py
          ingredient_repository.py
          semi_finished_repository.py
          manufacturer_repository.py


    label_printing/

      api/
        router.py
        terminal_router.py

      application/
        services/
          print_job_service.py

—

## 2. Модуль Catalog

### 2.1 Domain

`app/modules/catalog/domain/entities/`

- `product.py`
- `goods.py`
- `ingredient.py`
- `semi_finished.py`
- `manufacturer.py`

Domain слой не должен зависеть от FastAPI или SQLAlchemy.

—

### 2.2 Infrastructure (SQLAlchemy models + repositories)

`app/modules/catalog/infrastructure/models/`

- `product_model.py`
- `goods_model.py`
- `ingredient_model.py`
- `semi_finished_model.py`
- `manufacturer_model.py`

Рекомендации:
- UUID как PK/FK
- `organization_id` обязателен
- soft delete можно добавить позже

`app/modules/catalog/infrastructure/repositories/`

- `product_repo.py`
- `goods_repo.py`
- `ingredient_repo.py`
- `semi_finished_repo.py`
- `manufacturer_repo.py`

—

### 2.3 Application services

`app/modules/catalog/application/services/`

- `product_service.py`
- `goods_service.py`
- `ingredient_service.py`
- `semi_finished_service.py`
- `manufacturer_service.py`

Сервисы отвечают за:
- валидацию
- use-case операции
- вызов репозиториев

—

### 2.4 API

`app/modules/catalog/api/router.py`

Роутеры:
- `/catalog/products`
- `/catalog/goods`
- `/catalog/ingredients`
- `/catalog/semi_finished`
- `/catalog/manufacturers`

—

## 3. Модуль Label Printing

### 3.1 Domain

`app/modules/label_printing/domain/entities/`

- `print_job.py`
- `label_template.py`
- `label_format.py`
- `printer_terminal.py`
- `print_batch.py` (MVP можно позже, но лучше заложить)

`app/modules/label_printing/domain/enums/`
- `print_job_status.py`
- `entity_type.py` (`product|goods|ingredient|semi_finished`)

`app/modules/label_printing/domain/exceptions/`
- `job_not_found.py`
- `invalid_status_transition.py`
- `shelf_life_not_defined.py`
- `terminal_forbidden.py`

—

### 3.2 Infrastructure

#### SQLAlchemy models

`app/modules/label_printing/infrastructure/models/`

- `print_job_model.py`
- `label_template_model.py`
- `label_format_model.py`
- `printer_terminal_model.py`
- `print_batch_model.py` (опционально)

#### Repositories

`app/modules/label_printing/infrastructure/repositories/`

- `print_job_repo.py`
- `label_template_repo.py`
- `label_format_repo.py`
- `terminal_repo.py`
- `batch_repo.py` (опционально)

—

## 4. Claim flow и snapshot (самое важное)

### 4.1 Почему snapshot при claim

Snapshot сохраняет “что реально печатали”:

- данные из каталога
- ручные данные (made_at/opened_at/weight/expires_text)
- вычисленные поля (expires_at)

Записывается в:

- `print_jobs.render_payload_snapshot`

—

### 4.2 Render Context Builder

`app/modules/label_printing/application/services/render_context_builder.py`

Отвечает за:
- загрузку сущности по `entity_type/entity_id` из `catalog`
- валидации (например shelf_life обязателен)
- вычисления `expires_at`
- форматирование `shelf_life` в строку (“12 часов”, “3 дня”)
- подготовку `employee` (из `for_user_id`)
- добавление producer_name/producer_address (для продуктов)

—

## 5. Resolvers (интеграция с Catalog)

`app/modules/label_printing/infrastructure/catalog_resolvers/`

- `product_resolver.py`
- `goods_resolver.py`
- `ingredient_resolver.py`
- `semi_finished_resolver.py`

Каждый resolver возвращает словарь данных для render context.

Пример интерфейса:

- `resolve(entity_id, organization_id) -> dict`

—

## 6. Use Cases

`app/modules/label_printing/application/use_cases/`

- `create_print_job.py`
- `cancel_job.py`
- `claim_job.py`
- `start_job.py`
- `finish_job.py`
- `fail_job.py`
- `retry_and_claim.py`
- `reprint_and_claim.py`

### 6.1 Create PrintJob

Проверки:
- существует template_id и format_id
- entity существует в каталоге
- shelf_life задан (кроме goods, где “годен до” может быть “СМ. УП.”, но shelf_life всё равно нужен для печати “Срок хранения”)

Заполняем:
- `created_by_user_id` из токена
- `for_user_id` = created_by_user_id, если не передано
- status = queued

—

### 6.2 Claim PrintJob (терминал)

Проверки:
- терминал авторизован и привязан к `venue_id`
- job принадлежит этому venue
- job status = queued

Действия:
- статус → claimed (атомарно)
- собрать render_context
- сохранить snapshot
- вернуть терминалу:

  - html_template
  - snapshot
  - label_format
  - copies

—

## 7. Terminal API (native Android)

### 7.1 Auth терминала

`POST /terminal/auth`

Терминал получает token, который включает:
- organization_id
- venue_id
- terminal_id

—

### 7.2 Inbox

`GET /terminal/print-jobs?status=queued`

Сервер фильтрует по venue_id терминала.
Группировку по сотруднику можно делать сервером или клиентом.

—

### 7.3 Claim / start / finish / fail

- `POST /terminal/print-jobs/{id}/claim`
- `POST /terminal/print-jobs/{id}/start`
- `POST /terminal/print-jobs/{id}/finish`
- `POST /terminal/print-jobs/{id}/fail`

—

### 7.4 Retry & Reprint

- `POST /terminal/print-jobs/{id}/retry-and-claim`
- `POST /terminal/print-jobs/{id}/reprint-and-claim`

Возвращают payload для мгновенной печати.

—

## 8. Bitmap rendering (делает терминал)

Backend:
- не генерирует bitmap
- отдаёт HTML + snapshot

Terminal:
- рендерит HTML (WebView)
- снимает bitmap
- применяет threshold/dither
- печатает через Bluetooth SDK/driver

—

## 9. Валидации (ключевые правила)

### 9.1 Shelf life обязателен

Если shelf_life отсутствует у:
- product
- semi_finished
- ingredient
- goods

то печать запрещается.

—

### 9.2 Даты и формат печати

На этикетке всегда:
- `DD.MM.YYYY HH:MM`

`made_at/opened_at` по умолчанию = now, но редактируются при создании job.

—

### 9.3 Goods “годен до”

Вводится вручную:

- либо datetime `expires_at`
- либо текст `expires_at_text = «СМ. УП.»`

—

## 10. Alembic migrations

### 10.1 UUID

Включить расширение:

- `CREATE EXTENSION IF NOT EXISTS pgcrypto;`

И использовать:

- `gen_random_uuid()`

—

## 11. Минимальный набор тестов

- Create PrintJob → queued
- Terminal claim job:
  - atomic claim
  - snapshot saved
- Cancel job:
  - только queued
- Retry-and-claim:
  - создает новый job
  - сразу claimed
- Reprint-and-claim:
  - создает новый job
  - сразу claimed
- Goods expires_at_text корректно печатается

—

## 12. Рекомендация по дальнейшему развитию

Следующие улучшения после MVP:

- PrintBatch entity + batch execution
- WebSocket обновления очереди
- Printer drivers для TSPL/ZPL (будущие принтеры)
- QR/Barcode rendering
- Bitmap cache (опционально)