# Terminal API

API для терминалов печати.

—

GET /terminal/jobs

Получить очередь.

—

POST /terminal/jobs/{id}/claim

Начать печать.

—

POST /terminal/jobs/{id}/done

Печать завершена.

—

POST /terminal/jobs/{id}/fail

Ошибка печати.