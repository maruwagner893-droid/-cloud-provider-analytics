# Documento de diseño — Cloud Provider Analytics

## 1. Encuadre del problema
Soy del equipo de ciencia de datos de un proveedor de nube. Los datos de nuestros clientes (uso de servicios, facturación, tickets de soporte, etc.) llegan crudos, con errores, nulos y formatos inconsistentes. Mi trabajo es limpiarlos, ordenarlos y transformarlos para que las tres áreas de la empresa —FinOps, Soporte y Producto— puedan usarlos sin complicaciones la  información  de forma clara, confiable y que sea fácil de consultar.

Hoy, mucha de esta información se arma de forma manual, lo que lleva tiempo y es propenso a errores. Con este proyecto busco que se pueda  automatizar ese proceso, combinando dos formas de entregar la información:

Un dashboard online, actualizado casi en tiempo real, para que el usuario pueda ver el día a día.
Informes diarios y mensuales, para análisis más consolidados.
Como objetivos concretos para saber si esto funcionó:
Reducir el tiempo que hoy toma armar estos informes a mano, comparado con el tiempo que tarda una vez automatizado.
Reducir los errores que hoy aparecen en los informes, gracias a las reglas de calidad del pipeline.
Tener la información disponible rápido y de forma confiable, tanto en el dashboard como en los reportes periódicos.
## 2. Justificación de la necesidad de Big Data mediante volumen, velocidad, variedad, veracidad y valor
Volumen:
"por la cantidad de datos que se manejan"
"Aunque solo se tiene 80 organizaciones, cada una genera eventos de uso constantemente  y en 60 días ya se acumulo  43.200 registros solo de esa tabla. Sumado a los tickets (1000), usuarios (800) y recursos (400), cruzar toda esta información a mano o en Excel se vuelve inmanejable, más aún pensando que en producción real serían miles de organizaciones, no 80."

Velocidad:
"por que la tabla mas pequena es una tabla de refencia y la otra es de un historial o logeo" 
"customers_orgs es una tabla de referencia  donde cada organización se registra una vez y cambia muy poco con el tiempo (80 filas fijas). En cambio, usage_events_stream es un historial/log de eventos que se genera constantemente, cada vez que un cliente usa un servicio y por eso en solo 60 días ya  se acumula 43.200 registros. Esta diferencia de frecuencia es justamente lo que justifica usar Structured Streaming para los eventos.

Variedad:
"Tengo dos tipos de archivo: CSV y JSONL. Dentro de los archivos JSONL, según la versión  (schema_version), los eventos con versión 2 incluyen los campos carbon_kg y genai_tokens, mientras que los de versión 1 no los tienen (aparecen como nulos)."

Veracidad:
"Se encontro fechas guardadas con formata objet donde panda lo toma  como string (texto), varias tablas con nulos significativos como por ejemplo la columna carbon_kg tiene el 25% de valores nulos de un totla de 43.200 eventos valores fuera de rango cuando realice estudios preliminares en el estudio de calidad y tambien se encotraron costos negativos"

Valor:
"En este proyecto busco convertir datos crudos y con errores en información clara, confiable y fácil de consultar. Por ejemplo, FinOps hoy tiene datos con nulos y valores negativos raros mezclados con los reales; una vez que el pipeline los limpia y los separa, FinOps puede confiar en los números que ve y detectar rápidamente una anomalía real de costo, en vez de perder tiempo revisando si el dato está mal cargado o si es un problema genuino y para que el usuario tenga la informacion feaciente mas visible mas organizada."
## 3. Inventario y perfil inicial de las fuentes: grano, frecuencia, tipos, calidad, trazabilidad y riesgos.
| Relación | Cardinalidad | Grano | Descripción |
|---|---|---|---|
| customers_orgs - users | 1:N | un usuario | Una organización puede tener muchos usuarios; un usuario pertenece a una sola organización. |
| customers_orgs - resources | 1:N | un recurso cloud | Una organización puede tener muchos recursos; un recurso pertenece a una sola organización. |
| customers_orgs - support_tickets | 1:N | un ticket | Una organización puede tener muchos tickets; un ticket pertenece a una sola organización. |
| customers_orgs - marketing_touches | 1:N | una interacción de marketing | Una organización puede tener muchas interacciones de marketing; una interacción pertenece a una sola organización. |
| customers_orgs - nps_surveys | 1:N | una respuesta de encuesta | Una organización puede tener muchas respuestas de encuesta; una respuesta pertenece a una sola organización. |
| customers_orgs - billing_monthly | 1:N | una factura | Una organización puede tener muchas facturas; una factura pertenece a una sola organización. |
| customers_orgs - usage_events_stream | 1:N | un evento de uso | Una organización puede tener muchos eventos de uso; un evento pertenece a una sola organización. |
| resources - usage_events_stream | 1:N | un evento de uso | Un recurso puede tener muchos eventos de uso; un evento pertenece a un solo recurso. |

Frecuencias: 

customers_orgs y users se crean  una sola vez, porque una organización se registra una sola vez . Lo mismo con un usuario: se da de alta una vez. 

Resources, support_tickets, marketing_touches, nps_surveys para mi se crean de vez en cuando porque  considero que estos eventos pasan cuando algo puntual ocurre, sin un patrón fijo de tiempo, en el caso de un recurso se crea cuando el cliente decide desplegar algo nuevo , lo mismo con un ticket se abre cuando hay un problema y un touch de marketing pasa cuando el equipo decide hacer una campaña  y las  encuesta NPS  considero que se manda esporádicamente, no en fechas fijas.

Billing_monthly  es de manera fija pero mensual porque acá hay un patrón regular y predecible: se genera exactamente una vez por mes. Lo podemos ver  con el datos reales: 240 filas ÷ 80 organizaciones = 3 (una por cada uno de los 3 meses del dataset: junio, julio, agosto).

usage_events_stream  es de forma "continua" porque a diferencia de todas las demás, esta no tiene pausas  y se genera todo el tiempo, las 24 horas, cada vez que alguien usa un servicio cloud. Por eso en solo 60 días ya acumuló 43.200 eventos.

Las tablas tienen los siguientes problemas de calidad:

Customers_orgs: nulos en nps_score (13.75%), y un valor fuera de rango (101).

Users: nulos en last_login (17.38%).

Resources: nulos en tags_json (20.75%).

Support_tickets: nulos en resolved_at (24%) y csat (25.4%).

Marketing_touches: sin problemas de nulos.

Nps_surveys: nulos en nps_score (20.65%) y comment (10.87%).

Billing_monthly: nulos en credits (57.08%), y un subtotal negativo (-1671.83).

Usage_events_stream: nulos en value, unit, carbon_kg y genai_tokens; además costos negativos.

Trazabilidad:

customers_orgs es un archivo formato	CSV y su clave unica es org_id donde es la Tabla central por ende todas las demas tablas se conectan a ellas por ese ID.

users es un archivo formato CSV y su clave única es user_id y se conecta con customers_orgs a través de org_id.

Resources es un archivo formato CSV y su clave única es resource_id y se conecta con customers_orgs a través de org_id.

Support_tickets es un archivo formato CSV y su clave única es ticket_id y se conecta con customers_orgs a través de org_id.

Marketing_touches es un archivo formato CSV y su clave única es touch_id y se conecta con customers_orgs a través de org_id.

Nps_surveys es un archivo formato CSV y no tiene una clave única propia, por lo que se identifica cada encuesta con la combinación de org_id + survey_date (clave compuesta), y se conecta con customers_orgs a través de org_id.

Billing_monthly es un archivo formato CSV y su clave única es invoice_id y se conecta con customers_orgs a través de org_id.

Usage_events_stream es un archivo formato JSONL y su clave única es event_id y se conecta con customers_orgs a través de org_id y con resources a través de resource_id.
## 4. Arquitectura de alto nivel

## 5. Patrón arquitectónico

## 6. Matriz requisito-componente

## 7. Diseño del Data Lake

## 8. Flujo batch y streaming

## 9. Flujo MapReduce de referencia

## 10. Supuestos y riesgos

## 11. Estimación preliminar
