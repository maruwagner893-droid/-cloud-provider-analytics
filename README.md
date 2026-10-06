# Cloud Provider Analytics

## Proyecto integrador de Minería de Datos II (ISTEA, 2C 2026).

Pipeline con PySpark (ETL batch + streaming) y serving en Cassandra/AstraDB para FinOps, Soporte y Producto.

Este proyecto implementa una arquitectura Lambda (Batch + Streaming) para procesar datos de un proveedor cloud y generar métricas de FinOps, Soporte y Producto.

### Objetivos
Procesar datos históricos y en tiempo real.
Construir un Data Lake con múltiples capas de calidad.
Generar métricas para la toma de decisiones.
Facilitar el análisis de costos, incidencias y uso de productos.
### Arquitectura
EL proyecto sigue una arquitectura Lambda que se divide en:
Batch Layer
Speed Layer
Serving Layer
### Estructura del Repositorio
data/ — Zonas del Data Lake (landing, bronze, silver, gold, quarantine)
docs/ — Documento de diseño del proyecto
notebooks/ — Scripts de exploración de datos
src/ — Código fuente de los pipelines PySpark
evidence/ — Capturas y registro de decisiones

### Capas del Data Lake

El Data Lake se organiza en las siguientes capas:
Landing: recepción inicial de datos.
Bronze: datos ingeridos sin transformaciones significativas.
Silver: datos limpios, validados y enriquecidos.
Gold: métricas y datasets listos para consumo.
Quarantine: registros con errores o problemas de calidad.

### Tecnologías utilizadas
Python
PySpark
Apache Spark
Arquitectura Lambda
Data Lake

