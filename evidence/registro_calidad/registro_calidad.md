# Registro de calidad de datos

## customers_orgs
Los nulos en nps_score (13.75%) se mantienen como nulos en Silver para no distorsionar el promedio. El valor 101, que está fuera del rango válido de NPS (-100 a 100), se envía a Quarantine. La columna signup_date se convierte a datetime en Bronze.

## users
Los nulos en last_login (17.38%) se mantienen como nulos en Silver. Las columnas created_at y last_login se castean a datetime en Bronze.

## resources
Los nulos en tags_json (20.75%) se mantienen como nulos en Silver. La columna created_at se castea a datetime en Bronze.

## support_tickets
Los nulos en resolved_at (24%) se mantienen como nulos porque indican tickets que todavía están abiertos. Los nulos en csat (25.4%) se mantienen como nulos en Silver. Las columnas created_at y resolved_at se convierte a datetime en Bronze.

## marketing_touches
No tiene problemas de nulos. La columna timestamp se castea a datetime en Bronze.

## nps_surveys
Los nulos en nps_score (20.65%) y comment (10.87%) se mantienen como nulos en Silver. La columna survey_date se convierte a datetime en Bronze.

## billing_monthly
Los nulos en credits (57.08%) se tratan como 0 en Silver, previa confirmación con FinOps. El subtotal negativo de -1671.83 se mantiene y se marca como crédito del proveedor, no como error. Las facturas que vienen en ARS y EUR se normalizan a USD aplicando exchange_rate_to_usd en Bronze. La columna month se convierte a datetime en Bronze.

## usage_events_stream
Los nulos en carbon_kg (25%) y genai_tokens (92.75%) se mantienen porque corresponden exactamente a los registros de schema_version = 1 y no son errores de carga. Los valores negativos en cost_usd_increment se envían a Quarantine como anomalías. La columna value se transforma a tipo numérico en Bronze. La columna timestamp se convierte a datetime en Bronze. Los nulos en value (2.03%) y unit (4.80%) se mantienen como nulos en Silver.
