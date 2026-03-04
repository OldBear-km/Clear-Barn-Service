# Label Printing Database

—

# print_jobs

| поле | тип |
|——|——|
id | UUID
organization_id | UUID
venue_id | UUID
entity_type | text
entity_id | UUID
template_id | UUID
label_format_id | UUID
input_payload | jsonb
render_payload_snapshot | jsonb
copies | int
status | text
created_by_user_id | UUID
for_user_id | UUID
created_at | timestamp

—

# label_templates

| поле | тип |
|——|——|
id | UUID
organization_id | UUID
name | text
entity_type | text
html_template | text
input_schema | jsonb
bindings | jsonb

—

# label_formats

| поле | тип |
|——|——|
id | UUID
name | text
width_mm | int
height_mm | int
dpi | int

—

# printer_terminals

| поле | тип |
|——|——|
id | UUID
organization_id | UUID
venue_id | UUID
name | text
device_token | text
last_seen | timestamp