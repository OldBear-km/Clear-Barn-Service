# Domain Model

Catalog содержит следующие сущности:

## Product

- id (UUID)
- organization_id (UUID)
- name
- composition
- allergens
- calories_100g
- protein_100g
- fat_100g
- carbs_100g
- storage_conditions
- shelf_life_value
- shelf_life_unit
- default_weight
- weight_unit
- sku
- barcode

—

## Goods

- id
- organization_id
- name
- price
- composition
- manufacturer_id
- shelf_life_value
- shelf_life_unit
- sku
- barcode

—

## Ingredient

- id
- organization_id
- name
- shelf_life_after_open_value
- shelf_life_after_open_unit

—

## SemiFinished

- id
- organization_id
- name
- shelf_life_value
- shelf_life_unit

—

## Manufacturer

- id
- organization_id
- name
- address
- country