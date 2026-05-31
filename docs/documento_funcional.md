Diplomatura de Data Engineer
18/05/2026
Por: Desiree Alvarado

Pipeline de Ingeniería de Datos para Análisis de Producción Petrolera utilizando Azure Data Factory y Databricks
1. Introducción
El presente trabajo tiene como objetivo desarrollar un pipeline de ingeniería de datos orientado al análisis de producción petrolera mundial utilizando servicios cloud de Microsoft Azure. 

La solución implementa una arquitectura tipo Medallion (Bronze, Silver y Gold) para almacenar, transformar y analizar datos provenientes de distintas fuentes, incluyendo archivos CSV históricos y una API REST de precios internacionales del petróleo.

Para ello se utilizaron herramientas como Azure Data Lake Storage Gen2, Azure Data Factory y Azure Databricks con procesamiento mediante PySpark.

2. Caso de Estudio
El caso de estudio consiste en analizar datos históricos de producción de petróleo por país y enriquecerlos con información de precios internacionales del crudo obtenidos desde una API REST.

El objetivo es construir un pipeline automatizado capaz de:
- Ingerir datos desde múltiples fuentes,
- Almacenar información en distintas capas,
- Validar y transformar datos,
- Generar métricas analíticas, y
- Producir información lista para visualización.
3. Arquitectura
A continuación, se presenta el diagrama general de la arquitectura implementada:
 
La arquitectura implementada sigue el modelo Medallion, compuesto por tres capas principales:

- Bronze: almacenamiento de datos crudos provenientes del archivo CSV.
- Silver: datos limpios y transformados en formato Parquet.
- Gold: datos analíticos enriquecidos con información proveniente de la API REST.

![Storage](pipeline-medallion-azure/Storage.png)

![Bronze](pipeline-medallion-azure/bronce.png)

![Silver](pipeline-medallion-azure/silver.png)

![gold](pipeline-medallion-azure/gold.png)
 
Azure Data Factory se utilizó para la ingesta y automatización, mientras que Azure Databricks permitió realizar las transformaciones y validaciones mediante PySpark.

4. Fuentes y Destinos de Datos
El pipeline utiliza dos orígenes de datos principales:

1. Un archivo CSV con datos históricos de producción petrolera por país. Descargado desde https://www.energyinst.org/statistical-review/
2. Una API REST pública utilizada para obtener precios actualizados del petróleo. Fuente: https://www.eia.gov/opendata

Los datos fueron almacenados progresivamente en Azure Data Lake Storage Gen2 utilizando distintas capas de procesamiento.

Tipo	Descripción
CSV	Producción histórica de petróleo
API REST	Precio internacional del petróleo
Bronze	Datos raw CSV
Silver	Datos límpios en Parquet
Gold	Datos analíticos en Delta

5. Implementación en Azure Data Factory
Azure Data Factory fue utilizado para automatizar la ingesta de datos desde la capa Bronze hacia Silver mediante actividades Copy Data.
Linked Service
 
![Linked Service](pipeline-medallion-azure/linked_service.png)

Trigger Programado
 
![Trigger](pipeline-medallion-azure/trigger_programado.png)

6. Implementación en Databricks
Cluster Databricks

 ![cluster Databricks](pipeline-medallion-azure/databricks.png)

Lectura parquet
df = spark.read.parquet(
    "abfss://silver@stenergydatos.dfs.core.windows.net/produccion/")

Conversión de tipos
df = df.withColumn(
    "anio",
    col("anio").cast("int")
)

![conversion](pipeline-medallion-azure/conversion.png)
 
Azure Databricks permitió realizar tareas ETL utilizando PySpark, 

7. Controles de calidad implementados
Se implementaron distintos controles de calidad para asegurar la consistencia de los datos procesados.
Validación	Descripción
País no nulo	Eliminación de registros sin país
Producción positiva	Filtrado de valores negativos
Año válido	Validación de años mayores o iguales a 1900
Validaciones
- Validación de campos obligatorios, 
df = df.filter(col("pais").isNotNull())

- Control de valores negativos, y
df = df.filter(col("produccion_petroleo") >= 0)

- Validación de rangos temporales.
df = df.filter(col("anio") >= 1900)
8. Resultados obtenidos
Como resultado del procesamiento se generaron datasets analíticos enriquecidos con información de precios del petróleo.

Se obtuvieron métricas como:
- Producción total por país,

![Producción](pipeline-medallion-azure/produccion.png)

 
.- Evolución temporal de producción,
 
![Evolución](pipeline-medallion-azure/evolucion.png)

- Valor estimado de producción petrolera.

  ![Valor Estimado](pipeline-medallion-azure/valor_estimado.png)
 
9. Automatización

La automatización fue implementada mediante triggers programados en Azure Data Factory.
El pipeline fue configurado para ejecutarse diariamente de forma automática.
 

10. Conclusiones
La solución desarrollada permitió implementar un pipeline completo de ingeniería de datos utilizando servicios cloud de Microsoft Azure y arquitectura Medallion.
Entre los principales desafíos encontrados se destacan:
- la integración entre distintos servicios,
- la configuración de permisos y acceso,
- y la transformación de datos provenientes de múltiples fuentes.

Como mejoras futuras podrían incorporarse:
- dashboards interactivos,
- procesamiento en tiempo real,
- integración con Power BI,
- y escalabilidad mediante clusters distribuidos.

11. Tecnologías Utilizadas
Tecnología	Uso
Azure Data Lake Gen2	Almacenamiento
Azure Data Factory	Orquestación
Azure Databricks	ETL
PySpark	Procesamiento
Delta Lake	Almacenamiento analítico

