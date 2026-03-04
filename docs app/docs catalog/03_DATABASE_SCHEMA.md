# Catalog Database Schema

Catalog хранит данные на уровне **организации**.

Все таблицы содержат:

- id (UUID)
- organization_id (UUID)

UUID используется как основной тип идентификатора.

—

# Products

Готовая продукция производства.

Пример:
- круассан
- десерт
- сэндвич

## Таблица

products

| поле | тип |
|——|——|
id | UUID
organization_id | UUID
name | text
composition | text
allergens | text[]
calories_100g | numeric
protein_100g | numeric
fat_100g | numeric
carbs_100g | numeric
storage_conditions | text
shelf_life_value | int
shelf_life_unit | text
default_weight | numeric
weight_unit | text
sku | text
barcode | text

—

# Goods

Товары для продажи.

Особенность:
срок годности может быть указан на упаковке.

## Таблица

goods

| поле | тип |
|——|——|
id | UUID
organization_id | UUID
name | text
price | numeric
composition | text
manufacturer_id | UUID
shelf_life_value | int
shelf_life_unit | text
sku | text
barcode | text

—

# Ingredients

Сырье кухни.

Срок годности считается **после вскрытия**.

## Таблица

ingredients

| поле | тип |
|——|——|
id | UUID
organization_id | UUID
name | text
shelf_life_after_open_value | int
shelf_life_after_open_unit | text

—

# SemiFinished

Полуфабрикаты кухни.

Срок считается **от даты производства**.

## Таблица

semi_finished

| поле | тип |
|——|——|
id | UUID
organization_id | UUID
name | text
shelf_life_value | int
shelf_life_unit | text

—

# Manufacturers

Производители товаров.

## Таблица

manufacturers

| поле | тип |
|——|——|
id | UUID
organization_id | UUID
name | text
address | text
country | text