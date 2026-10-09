
# Decisiones de diseño

## Por qué Lambda y no Kappa

Kappa solo utiliza procesamiento en tiempo real (streaming), con un único camino para todos los datos. En este proyecto la mayoría de las fuentes son archivos CSV que llegan periódicamente (billing, tickets, usuarios, etc.), no en tiempo real. Usar Kappa obligaría a procesar esos datos como si fueran un stream cuando no lo son. Lambda permite tener un camino batch para los CSV y un camino streaming solo para usage_events_stream, que es la única fuente que llega en tiempo real.

## Por qué los costos negativos de billing_monthly no van a Quarantine

En la exploración encontramos un subtotal de -1671.83 en billing_monthly. En un primer momento parece un error, pero al analizar el contexto de negocio se trata de un crédito real del proveedor de nube, como una devolución o un ajuste por compromiso de uso. Si lo mandáramos a Quarantine, el costo neto mensual quedaría inflado y FinOps estaría tomando decisiones sobre números incorrectos. Por eso se mantiene en el pipeline y se lo marca como crédito.

## Por qué los costos negativos de usage_events_stream sí van a Quarantine

En usage_events_stream encontramos cost_usd_increment con valores hasta -154.46. A diferencia de billing, acá no tiene sentido de negocio: un recurso de nube no puede generar un consumo físico negativo. Por eso se tratan como anomalías y se envían a Quarantine.

## Por qué se particiona por year/month/org_id

Las tres áreas  siempre consultan filtrando por organización y por período de tiempo. Con esa estructura de carpetas, PySpark va directo a la carpeta que corresponde sin necesidad de leer el dataset completo. En un escenario de producción con miles de organizaciones, esto reduce significativamente el tiempo de procesamiento.

## Por qué los nulos de carbon_kg no van a Quarantine

En la exploración se verificó que los 10.800 registros con carbon_kg nulo coinciden exactamente con los registros de schema_version = 1. Eso indica que es una evolución del esquema y no un error aleatorio: la versión 1 simplemente no incluía ese campo. Mandarlos a Quarantine eliminaría la mitad del historial de eventos sin razón válida.

## Por qué se usa Parquet y no CSV en Bronze, Silver y Gold

Parquet guarda los datos por columnas y comprimidos. Si PySpark necesita solo algunos campos, lee solo esas columnas sin abrir el archivo completo. Con 43.200 eventos y múltiples tablas, esto hace el procesamiento más rápido y ocupa menos espacio que CSV.
