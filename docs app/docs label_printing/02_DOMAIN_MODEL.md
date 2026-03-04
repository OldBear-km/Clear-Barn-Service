# Label Printing Domain Model

Основные сущности сервиса.

—

# PrintJob

Задание печати.

| поле | описание |
|-—|-—|
id | UUID
organization_id | организация
venue_id | филиал
entity_type | product/goods/ingredient/semi_finished
entity_id | ID сущности каталога
template_id | шаблон
label_format_id | формат этикетки
input_payload | данные пользователя
render_payload_snapshot | snapshot данных
copies | количество
status | queued / claimed / printed / failed
created_by_user_id | кто создал
for_user_id | сотрудник на этикетке
created_at | дата создания

—

# LabelTemplate

Шаблон этикетки.

| поле | описание |
|-—|-—|
id | UUID
organization_id | организация
name | название
entity_type | тип этикетки
html_template | HTML
input_schema | JSON schema
bindings | mapping данных

—

# LabelFormat

Формат этикетки.

| поле | описание |
|-—|-—|
id | UUID
name | название
width_mm | ширина
height_mm | высота
dpi | разрешение

—

# PrinterTerminal

Терминал печати.

| поле | описание |
|-—|-—|
id | UUID
organization_id | организация
venue_id | филиал
name | название
device_token | токен устройства
last_seen | последний онлайн