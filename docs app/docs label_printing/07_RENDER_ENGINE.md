# Render Engine

Render Engine отвечает за преобразование данных PrintJob в готовую этикетку.

Pipeline:

PrintJob
↓
Catalog Resolver
↓
Render Context
↓
HTML Template
↓
HTML Render
↓
Bitmap
↓
Printer

—

# Render Context

Render Context — объединенные данные из:

- Catalog
- PrintJob.input_payload
- Organization
- Venue

Пример:

{
  «name»: «Круассан миндальный»,
  «composition»: «мука, масло, сахар, миндаль»,
  «allergens»: [«глютен»,»орехи»],
  «calories_100g»: 420,
  «protein_100g»: 12,
  «fat_100g»: 21,
  «carbs_100g»: 46,
  «storage_conditions»: «+2…+6 °C»,
  «made_at»: «2026-03-05T11:42:00»,
  «expires_at»: «2026-03-05T23:42:00»,
  «shelf_life»: «12 часов»,
  «weight»: «120 г»,
  «producer_name»: «Brū»,
  «producer_address»: «Екатеринбург, ул. Малышева 36»
}

—

# Resolver System

Каждый тип сущности имеет свой resolver.

product_resolver.py
goods_resolver.py
ingredient_resolver.py
semi_finished_resolver.py

Resolver отвечает за:

- получение данных из Catalog
- расчет сроков
- объединение данных

—

# Snapshot

Перед печатью создается snapshot.

snapshot = render_context

Он сохраняется в:

print_jobs.render_payload_snapshot

Это гарантирует:

- повторную печать
- аудит
- неизменность данных

—

# HTML Rendering

HTML рендерится через Jinja2.

template.render(render_context)

Результат:

HTML label