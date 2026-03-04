# Bitmap Generation

Принтеры этикеток используют bitmap.

Поэтому HTML преобразуется в изображение.

Pipeline:

HTML
↓
Headless Browser
↓
PNG
↓
Monochrome Bitmap
↓
Printer Commands

—

# Step 1 — HTML render

HTML шаблон генерируется через Jinja.

—

# Step 2 — Render HTML to PNG

Используется headless chromium.

Playwright
или
Puppeteer

Размер страницы соответствует LabelFormat.

Например:

58mm × 40mm

—

# Step 3 — Convert to monochrome

Принтеры используют:

1-bit bitmap

Изображение конвертируется:

grayscale
↓
threshold
↓
1bit

—

# Step 4 — Bitmap packing

Bitmap разбивается на байты.

Каждый байт = 8 пикселей.

—

# Step 5 — Send to printer

Для Netum P2 используется:

ESC/POS compatible commands.

Пример:

GS v 0

—

# Printer Abstraction

Будущий интерфейс:

PrinterDriver

drivers/
netum_p2_driver.py
zebra_driver.py
tsc_driver.py

Это позволит поддерживать разные принтеры.

—

# Bitmap Cache

Опционально bitmap может кэшироваться.

print_jobs.bitmap_cache

Это ускоряет повторную печать.