# Catalog API

Catalog предоставляет API для других модулей системы.

Основные потребители:

- label_printing
- inventory
- recipes
- menu

—

# Products

GET /catalog/products

Список продуктов организации.

—

GET /catalog/products/{id}

Получить продукт.

—

POST /catalog/products

Создать продукт.

—

PATCH /catalog/products/{id}

Изменить продукт.

—

DELETE /catalog/products/{id}

Удалить продукт.

—

# Goods

GET /catalog/goods

—

GET /catalog/goods/{id}

—

POST /catalog/goods

—

PATCH /catalog/goods/{id}

—

DELETE /catalog/goods/{id}

—

# Ingredients

GET /catalog/ingredients

GET /catalog/ingredients/{id}

POST /catalog/ingredients

PATCH /catalog/ingredients/{id}

DELETE /catalog/ingredients/{id}

—

# SemiFinished

GET /catalog/semi_finished

GET /catalog/semi_finished/{id}

POST /catalog/semi_finished

PATCH /catalog/semi_finished/{id}

DELETE /catalog/semi_finished/{id}

—

# Manufacturers

GET /catalog/manufacturers

POST /catalog/manufacturers