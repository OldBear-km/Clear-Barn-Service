# Label Printing Service

Label Printing — сервис печати этикеток.

Сервис отвечает за:

- формирование заданий печати
- очередь печати
- шаблоны этикеток
- работу терминалов печати

—

# Основные функции

- создание заданий печати
- хранение очереди
- генерация bitmap этикеток
- печать через терминалы
- хранение истории печати

—

# Поддерживаемые типы этикеток

1. Product label
2. Goods label
3. Ingredient label
4. Semi-finished label

—

# Архитектура

Catalog → Label Printing → Terminal → Printer