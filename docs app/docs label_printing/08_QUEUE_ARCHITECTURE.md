# Print Queue Architecture

Очередь печати хранится в таблице:

print_jobs

Каждый job проходит через жизненный цикл.

—

# Job Status

queued
claimed
printed
failed
cancelled

—

# Lifecycle

1. Job создается

status = queued

—

2. Терминал получает очередь

GET /terminal/jobs

—

3. Пользователь нажимает Print

POST /terminal/jobs/{id}/claim

status = claimed

—

4. Терминал печатает

—

5. Успешно

POST /terminal/jobs/{id}/done

status = printed

—

6. Ошибка

POST /terminal/jobs/{id}/fail

status = failed

—

# Concurrency Protection

Чтобы два терминала не печатали один job.

claim выполняется атомарно:

UPDATE print_jobs
SET status = ‘claimed’
WHERE id = :id
AND status = ‘queued’

—

# Queue UI

Терминал показывает:

Queued jobs
Grouped by for_user_id

Также доступны:

- Print
- Cancel
- Print All

—

# Print All

Терминал последовательно claim-ит jobs:

queued → claimed → printed