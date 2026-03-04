# Label Template Examples

Этот документ содержит примеры HTML шаблонов для всех типов этикеток.

Все шаблоны используют:

- Jinja2
- CSS для фиксированной ширины
- адаптацию под bitmap печать

—

# Общие правила

Все шаблоны должны:

- иметь фиксированную ширину
- использовать системные шрифты
- избегать сложной верстки
- поддерживать перенос строк

—

# Базовый стиль

<style>

body {
  width: 58mm;
  font-family: sans-serif;
  font-size: 12px;
}

.title {
  font-weight: bold;
  font-size: 16px;
}

.section {
  margin-top: 4px;
}

.price {
  font-size: 22px;
  font-weight: bold;
  text-align: center;
}

.small {
  font-size: 10px;
}

</style>

—

# Product Label

Используется для:

- готовой продукции
- десертов
- выпечки
- кухни

—

<h1 class=«title»>
{{ name }}
</h1>

<div class=«section»>
Состав: {{ composition }}
</div>

<div class=«section»>
КБЖУ (на 100 г):
К: {{ calories_100g }}
Б: {{ protein_100g }}
Ж: {{ fat_100g }}
У: {{ carbs_100g }}
</div>

{% if allergens %}

<div class=«section»>
АЛЛЕРГЕНЫ:
{{ allergens | join(«, «) | upper }}
</div>

{% endif %}

<div class=«section»>
Дата производства: {{ made_at }}
</div>

<div class=«section»>
Срок хранения: {{ shelf_life }}
</div>

<div class=«section»>
Годен до: {{ expires_at }}
</div>

{% if weight %}

<div class=«section»>
Вес: {{ weight }}
</div>

{% endif %}

<div class=«section»>
Хранение: {{ storage_conditions }}
</div>

<div class=«section small»>
Производитель: {{ producer_name }}
</div>

<div class=«section small»>
Адрес: {{ producer_address }}
</div>

—

# Goods Label

Используется для:

- товаров
- шоколада
- напитков
- упаковки

—

<h1 class=«title»>
{{ name }}
</h1>

<div class=«price»>
{{ price }}
</div>

<div class=«section»>
Состав: {{ composition }}
</div>

<div class=«section»>
Производитель: {{ manufacturer_name }}
</div>

<div class=«section»>
Адрес: {{ manufacturer_address }}
</div>

<div class=«section»>
Срок хранения: {{ shelf_life }}
</div>

<div class=«section»>
Годен до: {{ expires_at }}
</div>

<div class=«section small»>
Подробная информация на упаковке
</div>

—

# Ingredient Label

Используется для:

- молока
- сливок
- ингредиентов

—

<h1 class=«title»>
{{ name }}
</h1>

<div class=«section»>
Дата вскрытия: {{ opened_at }}
</div>

<div class=«section»>
Срок хранения: {{ shelf_life }}
</div>

<div class=«section»>
Годен до: {{ expires_at }}
</div>

<div class=«section»>
Сотрудник: {{ employee }}
</div>

—

# Semi Finished Label

Используется для:

- сиропов
- кремов
- теста
- заготовок

—

<h1 class=«title»>
{{ name }}
</h1>

<div class=«section»>
Дата производства: {{ made_at }}
</div>

<div class=«section»>
Срок хранения: {{ shelf_life }}
</div>

<div class=«section»>
Годен до: {{ expires_at }}
</div>

<div class=«section»>
Сотрудник: {{ employee }}
</div>

—

# Template Variables

## Product

name  
composition  
allergens  
calories_100g  
protein_100g  
fat_100g  
carbs_100g  
storage_conditions  
shelf_life  
made_at  
expires_at  
weight  
producer_name  
producer_address  

—

## Goods

name  
price  
composition  
manufacturer_name  
manufacturer_address  
shelf_life  
expires_at  

—

## Ingredient

name  
opened_at  
shelf_life  
expires_at  
employee  

—

## Semi Finished

name  
made_at  
shelf_life  
expires_at  
employee  

—

# Template Testing

Для тестирования шаблонов используется endpoint:

POST /internal/render-test

Payload:

{
  «template_id»: «...»,
  «data»: {...}
}

Ответ:

PNG preview