# Database Architecture — Архитектура базы данных

## 1. Общие принципы

База данных: PostgreSQL  
ORM: SQLAlchemy 2.0 (async)  
Миграции: Alembic  

Основные правила:

- Все бизнес-таблицы содержат organization_id
- Используется Soft Delete (deleted_at)
- Все внешние ключи индексируются
- Нет raw SQL без обоснования
- Нет синхронных операций
- Tenant isolation обязателен

—

## 2. Основные сущности

В системе разделяются:

1. User — физический человек
2. Organization — филиал
3. UserOrganization — роль пользователя в филиале
4. Employment — работа пользователя в филиале
5. Shift — смена
6. PayrollRecord — расчет зарплаты
7. BonusPenalty — бонусы и штрафы
8. AuditLog — аудит действий
9. NotificationLog — лог уведомлений
10. Configuration — параметры филиала

—

## 3. Таблицы

### 3.1 organizations
- id (PK)
- name
- created_at
- updated_at
- deleted_at

### 3.2 users
- id (PK)
- email (nullable для Telegram-only)
- password_hash (nullable)
- telegram_id (unique, nullable)
- global_role (superadmin | owner | none)
- is_active
- created_at
- updated_at
- deleted_at

### 3.3 user_organizations
- id (PK)
- user_id (FK → users.id, indexed)
- organization_id (FK → organizations.id, indexed)
- role (owner | manager | employee)
- created_at
- deleted_at

Один пользователь может иметь несколько записей.

### 3.4 employments
- id (PK)
- user_id (FK → users.id, indexed)
- organization_id (FK → organizations.id, indexed)
- position
- hourly_rate
- percent_rate
- is_active
- created_at
- updated_at
- deleted_at

Это конкретная работа пользователя в филиале.

### 3.5 shifts
- id (PK)
- employment_id (FK → employments.id, indexed)
- organization_id (indexed)
- start_time
- end_time
- closed_by (user_id)
- edited_by (user_id, nullable)
- is_auto_closed (boolean)
- created_at
- updated_at
- deleted_at

Ограничение:
- один employment не может иметь более одной открытой смены.

### 3.6 payroll_records
- id (PK)
- employment_id (FK → employments.id, indexed)
- organization_id (indexed)
- period_start
- period_end
- hours_worked
- base_amount
- percent_amount
- total_amount
- created_at
- updated_at
- deleted_at

Зарплата рассчитывается отдельно по каждому employment.

### 3.7 bonuses_penalties
- id (PK)
- employment_id (FK → employments.id, indexed)
- organization_id (indexed)
- type (bonus | penalty)
- amount
- comment
- created_by (user_id)
- created_at
- updated_at
- deleted_at

Не влияет на основной расчет, только суммируется.

### 3.8 audit_logs
- id (PK)
- organization_id (indexed)
- user_id (кто совершил действие)
- entity_type
- entity_id
- action
- old_value (JSONB)
- new_value (JSONB)
- created_at

Soft delete не применяется.

### 3.9 notification_logs
- id (PK)
- organization_id (indexed)
- user_id
- type
- status
- message
- created_at

### 3.10 configuration
- id (PK)
- organization_id (indexed)
- key
- value (JSONB)
- updated_at

Используется для:
- времени автозакрытия смен
- порога процентов
- других динамических параметров

—

## 4. Индексы (обязательные)

- Все FK поля
- (organization_id, deleted_at)
- (employment_id, deleted_at)
- telegram_id (unique)
- email (unique, если используется)
- shifts (employment_id, end_time)

—

## 5. Soft Delete

Используется поле deleted_at (nullable).

Удаление = установка deleted_at.

Глобальные SELECT обязаны фильтровать:
deleted_at IS NULL

—

## 6. Ограничения целостности

- Нельзя создать employment без user_organization
- Нельзя создать shift без employment
- Нельзя рассчитать payroll для удалённого employment
- Нельзя добавить бонус для неактивного employment

—

## 7. Глобальный Payroll

Для расчета общей зарплаты пользователя по сети:

SUM(payroll_records.total_amount
    WHERE user_id = X
    AND deleted_at IS NULL)

—

## 8. Масштабируемость

Архитектура позволяет:

- выделить payroll в отдельный сервис
- выделить shifts в отдельный сервис
- разнести организации по разным БД при росте сети
- подключить read replicas

—

## 9. Инварианты базы

- нет сущностей без organization_id
- нет бизнес-логики в БД (триггеры минимальны)
- все проверки выполняются в сервисном слое
- tenant isolation обеспечивается на уровне запросов