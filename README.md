# Cloud Provider Analytics

## Proyecto integrador de Minería de Datos II (ISTEA, 2C 2026)

## Contexto
Los datos generados por los clientes del proveedor cloud (uso de servicios, facturación, tickets de soporte, entre otros) llegan en formatos que contienen errores, valores nulos e inconsistencias.
El objetivo del proyecto es construir un pipeline de datos que permita limpiar, validar, transformar y disponibilizar esta información de forma confiable para los distintos equipos de negocio.

## Objetivo
Automatizar el procesamiento de datos y la generación de información analítica para reducir tareas manuales, mejorar la calidad de los datos y ofrecer información actualizada tanto para análisis operativos como estratégicos.

Los objetivos específicos son:
- Reducir el tiempo necesario para generar reportes.
- Minimizar errores mediante reglas de calidad de datos.
- Disponibilizar información confiable para FinOps, Soporte y Producto.
- Entregar métricas en tiempo casi real
- - Generar reportes diarios y mensuales para análisis consolidados.

## Arquitectura
La solución implementa una Arquitectura Lambda que combina:
- Procesamiento Batch para información histórica.
- Procesamiento Streaming para eventos en tiempo real.
- Capa de consumo para métricas y visualizaciones.

## Estructura del Repositorio

```
cloud-provider-analytics/
│
├── data/
│   ├── landing/
│   ├── bronze/
│   ├── silver/
│   ├── gold/
│   └── quarantine/
│
├── docs/
│   └── Documentación y diseño de la solución
│
├── notebooks/
│   └── Exploración y análisis de datos
│
├── src/
│   └── Pipelines y transformaciones en PySpark
│
└── evidence/
    └── Evidencias, capturas y registro de decisiones
```

## Data Lake
- **Landing:** recepción inicial de datos.
- **Bronze:** almacenamiento de datos crudos.
- **Silver:** datos limpios y estandarizados.
- **Gold:** métricas y datasets de negocio.
- **Quarantine:** registros invalidos por problemas de calidad.

## Tecnologías
- Python
- PySpark
- Apache Spark
- Arquitectura Lambda
- Data Lake

## Áreas Beneficiadas
- FinOps
- Soporte
- Producto

## Documentación
La documentación detallada de diseño se encuentra en: `docs/`

## Evidencias
Capturas de pantalla, validaciones y decisiones tomadas durante el desarrollo: `evidence/`
