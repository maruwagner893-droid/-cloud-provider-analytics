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
## 3. Inventario y perfil de fuentes

## 4. Arquitectura de alto nivel

## 5. Patrón arquitectónico

## 6. Matriz requisito-componente

## 7. Diseño del Data Lake

## 8. Flujo batch y streaming

## 9. Flujo MapReduce de referencia

## 10. Supuestos y riesgos

## 11. Estimación preliminar
