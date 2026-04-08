# Dominio 2: Preparar y servir datos (40-45%)

> **Ponderacion del examen:** Este es el **dominio mas grande** del examen DP-600 y cubre casi la mitad de todas las preguntas.  
> Domina cada seccion siguiente para maximizar tu puntuacion.

---

## Tabla de contenido

1. [Ingesta de datos](#1-ingesta-de-datos)
2. [Transformacion de datos](#2-transformacion-de-datos)
3. [Dataflows Gen2](#3-dataflows-gen2)
4. [Data Factory Pipelines](#4-data-factory-pipelines)
5. [Notebooks y Spark Jobs](#5-notebooks-y-spark-jobs)
6. [Arquitectura Lakehouse](#6-arquitectura-lakehouse)
7. [Warehouse](#7-warehouse)
8. [Calidad de datos y administracion de conexiones](#8-calidad-de-datos-y-administracion-de-conexiones)
9. [Medallion Architecture](#9-medallion-architecture)

---

## 1. Ingesta de datos

### Ingesta batch vs. streaming

| Caracteristica | Batch | Streaming |
|---|---|---|
| **Latencia** | Minutos a horas | Segundos a minutos |
| **Herramientas** | Copy Activity, Dataflows Gen2, Notebooks | Eventstreams, KQL Querysets |
| **Caso de uso** | Cargas ETL periodicas, datos historicos | Dashboards en tiempo real, telemetria IoT |
| **Frescura de datos** | Intervalos programados | Casi en tiempo real |
| **Complejidad** | Menor | Mayor (ordenamiento, deduplicacion, ventanas) |

### Conectores

Microsoft Fabric admite **mas de 170 conectores** para ingesta de datos:

- **Nativos de Azure:** Azure SQL, Azure Blob, ADLS Gen2, Cosmos DB, Synapse
- **SaaS:** Salesforce, Dynamics 365, SharePoint, Dataverse
- **Bases de datos:** SQL Server, PostgreSQL, MySQL, Oracle
- **Archivos:** CSV, Parquet, JSON, Excel, XML
- **Plataformas cloud:** AWS S3, Google Cloud Storage, Snowflake

### Copy Activity

Copy Activity mueve datos entre almacenes compatibles. Es la herramienta principal para **ingesta batch** en Data Factory pipelines.

```json
{
  "name": "CopyFromBlobToLakehouse",
  "type": "Copy",
  "inputs": [{ "referenceName": "AzureBlobSource" }],
  "outputs": [{ "referenceName": "LakehouseDestination" }],
  "typeProperties": {
    "source": {
      "type": "DelimitedTextSource",
      "storeSettings": { "type": "AzureBlobStorageReadSettings", "recursive": true }
    },
    "sink": {
      "type": "LakehouseTableSink",
      "tableActionOption": "Append"
    }
  }
}
```

> **Exam Tip:** Conoce la diferencia entre las acciones de tabla **Append**, **Overwrite** y **Merge** para el `sink` de Copy Activity.

### Eventstreams

Eventstreams habilita la **ingesta de datos en tiempo real** en Fabric:

- Origen desde Azure Event Hubs, IoT Hub o apps personalizadas
- Procesamiento con transformaciones no-code (filter, aggregate, group by)
- Enrutamiento a destinos Lakehouse, KQL Database o Reflex
- Compatibilidad con ventanas de tiempo **tumbling**, **hopping** y **sliding**

### Shortcuts (OneLake, ADLS Gen2, S3)

Los Shortcuts proporcionan **acceso virtualizado sin copia** a datos sin moverlos fisicamente.

| Tipo de Shortcut | Origen | Autenticacion |
|---|---|---|
| **OneLake** | Otros Fabric Lakehouses/Warehouses | Identidad de Fabric |
| **ADLS Gen2** | Azure Data Lake Storage Gen2 | Service principal, SAS o clave de cuenta |
| **Amazon S3** | Buckets de AWS S3 | Access key + secret |
| **Google Cloud Storage** | Buckets de GCS | Service account key |
| **Dataverse** | Dynamics 365 / Power Platform | Cuenta organizacional |

> **Exam Tip:** Los Shortcuts **no** copian datos; crean un puntero. Los datos permanecen en su ubicacion original. Esto reduce costos de almacenamiento y evita duplicacion de datos.

**Crear un shortcut mediante PySpark:**

```python
# Los Shortcuts se crean mediante la interfaz de Fabric o la REST API
# Una vez creados, accede a los datos del shortcut en Spark como cualquier otra tabla:
df = spark.read.format("delta").load("Tables/my_shortcut_table")
df.show(5)
```

[Microsoft Docs - Data Ingestion in Fabric](https://learn.microsoft.com/en-us/fabric/data-factory/connector-overview)

---

## 2. Transformacion de datos

### Power Query (lenguaje M)

Power Query es el motor de transformacion detras de **Dataflows Gen2** y el pipeline de datos de Power BI. M es un lenguaje funcional y sensible a mayusculas/minusculas.

**Transformaciones comunes de M:**

```m
let
    // Conectar a SQL Server
    Source = Sql.Database("server.database.windows.net", "SalesDB"),
    
    // Navegar a la tabla
    Sales = Source{[Schema="dbo", Item="FactSales"]}[Data],
    
    // Filtrar filas: conservar solo el anio actual
    FilteredRows = Table.SelectRows(Sales, each Date.Year([OrderDate]) = 2024),
    
    // Agregar columna calculada
    AddMargin = Table.AddColumn(FilteredRows, "ProfitMargin", 
        each [Revenue] - [Cost], type number),
    
    // Agrupar y agregar
    Grouped = Table.Group(AddMargin, {"ProductCategory"}, {
        {"TotalRevenue", each List.Sum([Revenue]), type number},
        {"AvgMargin", each List.Average([ProfitMargin]), type number},
        {"OrderCount", each Table.RowCount(_), Int64.Type}
    }),
    
    // Ordenar de forma descendente por revenue
    Sorted = Table.Sort(Grouped, {{"TotalRevenue", Order.Descending}}),
    
    // Cambiar tipos de columna
    TypedResult = Table.TransformColumnTypes(Sorted, {
        {"TotalRevenue", Currency.Type},
        {"AvgMargin", Currency.Type}
    })
in
    TypedResult
```

**Funciones M clave para el examen:**

| Funcion | Proposito |
|---|---|
| `Table.SelectRows` | Filtrar filas por condicion |
| `Table.AddColumn` | Agregar una columna calculada |
| `Table.Group` | Agrupar y agregar |
| `Table.TransformColumnTypes` | Establecer tipos de datos |
| `Table.ExpandRecordColumn` | Aplanar registros anidados |
| `Table.Pivot` / `Table.Unpivot` | Reestructurar datos |
| `Table.Join` / `Table.NestedJoin` | Combinar tablas |
| `Table.RemoveColumns` | Eliminar columnas innecesarias |
| `Table.ReplaceValue` | Buscar y reemplazar valores |
| `Table.Buffer` | Cargar una tabla en memoria para rendimiento |

### Spark Notebooks (PySpark)

PySpark es el lenguaje de transformacion mas comun en Fabric notebooks.

```python
from pyspark.sql.functions import col, year, sum, avg, when, lit

# Leer desde una tabla Delta de Lakehouse
df = spark.read.format("delta").load("Tables/raw_sales")

# Filtrar, transformar y agregar
result = (
    df
    .filter(year(col("order_date")) == 2024)
    .withColumn("profit_margin", col("revenue") - col("cost"))
    .withColumn("margin_tier", 
        when(col("profit_margin") > 100, "High")
        .when(col("profit_margin") > 50, "Medium")
        .otherwise("Low")
    )
    .groupBy("product_category", "margin_tier")
    .agg(
        sum("revenue").alias("total_revenue"),
        avg("profit_margin").alias("avg_margin"),
        count("order_id").alias("order_count")
    )
    .orderBy(col("total_revenue").desc())
)

# Escribir en Lakehouse como tabla Delta
result.write.format("delta").mode("overwrite").saveAsTable("gold_sales_summary")
```

### Spark SQL

```sql
-- Crear una vista temporal para acceso SQL
CREATE OR REPLACE TEMP VIEW raw_sales AS
SELECT * FROM delta.`Tables/raw_sales`;

-- Transformar y agregar usando Spark SQL
CREATE OR REPLACE TABLE gold_sales_summary AS
SELECT 
    product_category,
    CASE 
        WHEN revenue - cost > 100 THEN 'High'
        WHEN revenue - cost > 50  THEN 'Medium'
        ELSE 'Low'
    END AS margin_tier,
    SUM(revenue) AS total_revenue,
    AVG(revenue - cost) AS avg_margin,
    COUNT(order_id) AS order_count
FROM raw_sales
WHERE YEAR(order_date) = 2024
GROUP BY product_category, margin_tier
ORDER BY total_revenue DESC;
```

### Transformaciones T-SQL (Warehouse)

```sql
-- Crear un stored procedure para transformaciones repetibles
CREATE PROCEDURE dbo.TransformSalesData
AS
BEGIN
    -- Patron merge: upsert desde staging hacia target
    MERGE dbo.DimProduct AS target
    USING dbo.staging_products AS source
    ON target.ProductKey = source.ProductKey
    WHEN MATCHED THEN
        UPDATE SET 
            target.ProductName = source.ProductName,
            target.Category = source.Category,
            target.UpdatedDate = GETDATE()
    WHEN NOT MATCHED THEN
        INSERT (ProductKey, ProductName, Category, UpdatedDate)
        VALUES (source.ProductKey, source.ProductName, source.Category, GETDATE());
END;
```

> **Exam Tip:** Conoce cuando usar cada herramienta de transformacion:
> - **Power Query / M** -> low-code, Dataflows Gen2, citizen developers
> - **PySpark** -> datos a gran escala, logica compleja, data science
> - **Spark SQL** -> analistas familiarizados con SQL que trabajan con Lakehouse
> - **T-SQL** -> transformaciones de Warehouse, stored procedures

[Microsoft Docs - Transform Data in Fabric](https://learn.microsoft.com/en-us/fabric/data-factory/transform-data)

---

## 3. Dataflows Gen2

### Descripcion general

Dataflows Gen2 son **transformaciones de Power Query (M) basadas en cloud** que se ejecutan en el servicio Fabric. Son la evolucion de Power BI Dataflows.

### Crear y configurar Dataflows Gen2

Opciones clave de configuracion:

| Configuracion | Descripcion |
|---|---|
| **Data destination** | Lakehouse, Warehouse, KQL Database o Azure SQL |
| **Update method** | Replace o Append |
| **Column mapping** | Asignar columnas de origen a columnas de destino |
| **Staging** | Habilitar staging Lakehouse para grandes volumenes de datos |

### Incremental Refresh

Configura incremental refresh para cargar solo datos nuevos o modificados:

1. Crea un **parametro de fecha/hora** (por ejemplo, `RangeStart`, `RangeEnd`)
2. Filtra tu consulta usando estos parametros
3. Configura la politica de incremental refresh:
   - **Store rows in the last:** por ejemplo, 3 anios
   - **Refresh rows in the last:** por ejemplo, 10 dias

```m
// Patron de incremental refresh en M
let
    Source = Sql.Database("server.database.windows.net", "SalesDB"),
    Sales = Source{[Schema="dbo", Item="FactSales"]}[Data],
    // Filtrar usando los parametros RangeStart y RangeEnd
    Filtered = Table.SelectRows(Sales, each 
        [ModifiedDate] >= RangeStart and [ModifiedDate] < RangeEnd)
in
    Filtered
```

> **Exam Tip:** Los parametros deben llamarse exactamente `RangeStart` y `RangeEnd` (sensibles a mayusculas/minusculas) para que incremental refresh funcione.

### Manejo de errores

- **Manejo de errores a nivel de fila:** Configura como se tratan los errores por columna (reemplazar valor, quitar fila, mantener error)
- **Query diagnostics:** Supervisa el rendimiento paso a paso
- **Refresh history:** Consulta el estado de exito/error y los mensajes de error en el portal de Fabric

### Data destinations

Dataflows Gen2 puede generar salida hacia varios destinos:

- **Fabric Lakehouse** -> escribe tablas Delta
- **Fabric Warehouse** -> escribe en tablas SQL
- **Azure SQL Database** -> destino SQL externo
- **KQL Database** -> para real-time analytics

### Staging

Staging usa un **Lakehouse como intermediario** para mejorar el rendimiento con datasets grandes:

- Habilita staging en la configuracion del Dataflow
- Primero se escriben los datos en un staging Lakehouse y luego se cargan al destino
- **Recomendado** para datasets mayores que unos pocos cientos de MB

> **Exam Tip:** Staging es obligatorio al usar ciertas transformaciones con datasets grandes y cuando el destino es un Warehouse.

[Microsoft Docs - Dataflows Gen2](https://learn.microsoft.com/en-us/fabric/data-factory/create-first-dataflow-gen2)

---

## 4. Data Factory Pipelines

### Pipeline Activities

| Tipo de Activity | Proposito | Ejemplo |
|---|---|---|
| **Copy** | Mover datos entre almacenes | Blob -> Lakehouse |
| **Dataflow** | Ejecutar un Dataflow Gen2 | Transformar y cargar |
| **Notebook** | Ejecutar un Spark notebook | Logica PySpark compleja |
| **Stored Procedure** | Ejecutar procedimientos T-SQL | Transformaciones de Warehouse |
| **KQL** | Ejecutar consultas KQL | Real-time analytics |
| **Script** | Ejecutar scripts T-SQL | DDL/DML en Warehouse |
| **Lookup** | Recuperar datos para la logica del pipeline | Obtener valores de configuracion |
| **Set Variable** | Asignar un valor a una variable | Almacenar resultados intermedios |
| **Web** | Llamar a una REST API | Disparar servicios externos |
| **Delete** | Eliminar archivos del almacenamiento | Limpiar datos de staging |

### Control Flow Activities

```
ForEach    -> Iterar sobre una coleccion de elementos
If         -> Ramificacion condicional (if/else)
Switch     -> Condicional de multiples ramas (como case/switch)
Until      -> Bucle hasta que se cumpla una condicion
Wait       -> Pausar la ejecucion por una duracion
Fail       -> Forzar el fallo del pipeline con un error personalizado
```

**Ejemplo de ForEach: procesamiento de multiples tablas:**

```json
{
  "name": "ForEachTable",
  "type": "ForEach",
  "typeProperties": {
    "items": {
      "value": "@pipeline().parameters.TableList",
      "type": "Expression"
    },
    "isSequential": false,
    "batchCount": 10,
    "activities": [
      {
        "name": "CopyTable",
        "type": "Copy",
        "typeProperties": {
          "source": { "type": "SqlSource", "sqlReaderQuery": "SELECT * FROM @{item().TableName}" },
          "sink": { "type": "LakehouseTableSink" }
        }
      }
    ]
  }
}
```

### Parameters y Variables

| Concepto | Alcance | Mutabilidad | Caso de uso |
|---|---|---|---|
| **Parameters** | Nivel de pipeline, pasados en el momento del trigger | Solo lectura en runtime | Nombres de tablas, fechas, connection strings |
| **Variables** | Nivel de pipeline, definidas en el pipeline | Lectura/escritura en runtime | Contadores, flags, resultados intermedios |

**Expresiones comunes:**

```
@pipeline().parameters.SourceTable        // Acceder a un parametro
@variables('RecordCount')                  // Acceder a una variable
@utcnow()                                 // Timestamp UTC actual
@formatDateTime(utcnow(), 'yyyy-MM-dd')   // Fecha formateada
@concat('dbo.', pipeline().parameters.TableName) // Concatenacion de strings
@if(equals(activity('Lookup').output.count, 0), 'Empty', 'HasData') // Condicional
```

### Triggers

| Tipo de Trigger | Descripcion |
|---|---|
| **Schedule** | Ejecutar con una programacion cron (por ejemplo, diariamente a las 2 AM) |
| **Tumbling Window** | Intervalos de tiempo de tamanio fijo y no superpuestos |
| **Event-based** | Reaccionar a la llegada de archivos en el almacenamiento |

### Monitoreo y manejo de errores

- **Monitor Hub** -> vista centralizada de todas las ejecuciones de pipelines
- **Reintento a nivel de Activity** -> configurar cantidad e intervalo de reintentos
- **Dependencias entre Activities** -> Succeeded, Failed, Completed, Skipped
- **Rutas On-failure** -> encadenar activities en caso de fallo para alertas o limpieza

```
Activity A (Copy) --[On Success]--> Activity B (Transform)
                  --[On Failure]--> Activity C (Send Alert)
```

> **Exam Tip:** Comprende las condiciones de dependencia entre activities:
> - **Succeeded** -> se ejecuta solo si la activity anterior tuvo exito
> - **Failed** -> se ejecuta solo si la activity anterior fallo
> - **Completed** -> se ejecuta independientemente de exito o fallo
> - **Skipped** -> se ejecuta solo si la activity anterior fue omitida

[Microsoft Docs - Data Factory Pipelines](https://learn.microsoft.com/en-us/fabric/data-factory/activity-overview)

---

## 5. Notebooks y Spark Jobs

### PySpark en Notebooks

```python
from pyspark.sql.functions import *
from pyspark.sql.types import *

# Leer datos desde la seccion Files del Lakehouse (archivos raw)
df_raw = (
    spark.read
    .option("header", "true")
    .option("inferSchema", "true")
    .csv("Files/raw/customers/*.csv")
)

# Aplicar esquema con tipos explicitos
schema = StructType([
    StructField("customer_id", IntegerType(), False),
    StructField("name", StringType(), True),
    StructField("email", StringType(), True),
    StructField("signup_date", DateType(), True),
    StructField("lifetime_value", DoubleType(), True)
])

df_typed = spark.read.schema(schema).csv("Files/raw/customers/*.csv")

# Escribir como tabla Delta
df_typed.write.format("delta").mode("overwrite").saveAsTable("bronze_customers")
```

### Operaciones de lectura/escritura en Delta Lake

```python
# --- Operaciones READ ---
# Leer una tabla Delta por nombre
df = spark.read.table("bronze_customers")

# Leer por ruta
df = spark.read.format("delta").load("Tables/bronze_customers")

# Time travel: leer una version anterior
df_v2 = spark.read.format("delta").option("versionAsOf", 2).load("Tables/bronze_customers")

# Time travel: leer como estaba en un timestamp
df_ts = (spark.read.format("delta")
    .option("timestampAsOf", "2024-01-15T10:00:00Z")
    .load("Tables/bronze_customers"))

# --- Operaciones WRITE ---
# Sobrescribir toda la tabla
df.write.format("delta").mode("overwrite").saveAsTable("silver_customers")

# Anexar nuevas filas
df_new.write.format("delta").mode("append").saveAsTable("silver_customers")

# Merge (upsert) usando la API de Delta Lake
from delta.tables import DeltaTable

target = DeltaTable.forName(spark, "silver_customers")
target.alias("t").merge(
    df_updates.alias("s"),
    "t.customer_id = s.customer_id"
).whenMatchedUpdateAll() \
 .whenNotMatchedInsertAll() \
 .execute()
```

### Spark SQL en Notebooks

```sql
%%sql
-- Crear una tabla Delta administrada
CREATE TABLE IF NOT EXISTS silver_customers (
    customer_id INT,
    name STRING,
    email STRING,
    signup_date DATE,
    lifetime_value DOUBLE,
    _loaded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP()
)
USING DELTA;

-- Insertar con deduplicacion
MERGE INTO silver_customers AS t
USING bronze_customers AS s
ON t.customer_id = s.customer_id
WHEN MATCHED THEN UPDATE SET *
WHEN NOT MATCHED THEN INSERT *;

-- Consultar el historial de la tabla
DESCRIBE HISTORY silver_customers;
```

### Spark Job Definitions

Spark Job Definitions permite ejecutar **scripts Spark de produccion** con una programacion:

- Cargar archivos `.py` o `.jar` como la definicion principal
- Referenciar dependencias de librerias
- Configurar propiedades de Spark (memoria de executor, cores)
- Programar mediante pipelines o ejecucion manual

### Administracion de librerias

```python
# Instalar inline (alcance de sesion, solo notebook)
%pip install great-expectations

# O usar Fabric Environment:
# 1. Crear un item Environment en el workspace
# 2. Agregar librerias publicas (PyPI) o cargar archivos .whl personalizados
# 3. Adjuntar el Environment a tu notebook o Spark Job Definition
```

> **Exam Tip:** Las librerias instaladas con `%pip install` tienen **alcance de sesion** y se pierden cuando termina la sesion. Para librerias persistentes, usa **Fabric Environments**.

[Microsoft Docs - Notebooks in Fabric](https://learn.microsoft.com/en-us/fabric/data-engineering/how-to-use-notebook)

---

## 6. Arquitectura Lakehouse

### OneLake

OneLake es el **data lake unificado** de Fabric: una unica capa de almacenamiento para todo el tenant construida sobre ADLS Gen2.

Conceptos clave:
- **Una copia de los datos** compartida entre todas las cargas de trabajo de Fabric
- **Formato Delta/Parquet automatico** para todas las tablas administradas
- **Namespace jerarquico:** `onelake.dfs.fabric.microsoft.com/{workspace}/{lakehouse}/`
- **Gobernanza integrada** mediante roles de Fabric workspace y permisos de items

### Delta Lake

Delta Lake es el **formato de tabla predeterminado** en Fabric Lakehouse:

| Caracteristica | Beneficio |
|---|---|
| **Transacciones ACID** | Lecturas/escrituras confiables, sin datos corruptos |
| **Schema enforcement** | Evita que se escriban datos invalidos |
| **Schema evolution** | Agregar columnas sin reescribir datos |
| **Time travel** | Consultar versiones anteriores de datos |
| **Audit history** | Log de transacciones completo para cumplimiento |
| **Batch + streaming unificados** | La misma tabla para ambas cargas de trabajo |

### Managed vs. External Tables

| Aspecto | Managed Table | External Table |
|---|---|---|
| **Ubicacion** | Carpeta `Tables/` (auto-administrada) | Carpeta `Files/` o ruta externa |
| **Metadata** | Administrada por Fabric | Administrada por el usuario |
| **Comportamiento de DROP** | Elimina datos + metadata | Elimina solo metadata |
| **Descubrimiento** | Auto-registrada en el SQL endpoint | Debe registrarse explicitamente |
| **Caso de uso** | Tablas analiticas estandar | Archivos raw, datos compartidos |

```python
# Crear una tabla administrada
df.write.format("delta").mode("overwrite").saveAsTable("managed_table")

# Crear una tabla externa
df.write.format("delta").mode("overwrite").save("Files/external/my_table")

# Registrar la tabla externa en el metastore
spark.sql("""
    CREATE TABLE external_table
    USING DELTA
    LOCATION 'Files/external/my_table'
""")
```

### Shortcuts

Shortcuts habilita acceso a datos **cross-Lakehouse, cross-workspace y cross-cloud**:

```
OneLake Shortcut:      Lakehouse A -> Lakehouse B (mismo workspace o distinto)
ADLS Gen2 Shortcut:    Lakehouse -> Azure Data Lake Gen2 container
S3 Shortcut:           Lakehouse -> Amazon S3 bucket
GCS Shortcut:          Lakehouse -> Google Cloud Storage bucket
Dataverse Shortcut:    Lakehouse -> Dataverse tables
```

### Mantenimiento de tablas

El mantenimiento regular asegura un rendimiento optimo de consultas:

```sql
-- OPTIMIZE: Compacta archivos pequenios en archivos mas grandes (objetivo ~1 GB)
OPTIMIZE silver_customers;

-- OPTIMIZE con Z-ORDER: co-ubica datos por columnas filtradas frecuentemente
OPTIMIZE silver_customers ZORDER BY (signup_date, customer_id);

-- VACUUM: Elimina archivos antiguos que ya no son referenciados por el Delta log
-- Retencion predeterminada: 7 dias. Se eliminan archivos anteriores a la retencion.
VACUUM silver_customers RETAIN 168 HOURS;

-- ANALYZE: Recolecta estadisticas para el query optimizer
ANALYZE TABLE silver_customers COMPUTE STATISTICS FOR ALL COLUMNS;
```

**Recomendaciones de mantenimiento:**

| Operacion | Frecuencia | Proposito |
|---|---|---|
| `OPTIMIZE` | Diario o despues de grandes escrituras | Compactar archivos pequenios |
| `VACUUM` | Semanal | Recuperar almacenamiento |
| `Z-ORDER` | Con OPTIMIZE, en columnas de filtro | Acelerar consultas filtradas |
| `ANALYZE` | Despues de cambios significativos de datos | Actualizar estadisticas del query optimizer |

### V-Order

V-Order es una **optimizacion en tiempo de escritura** unica de Fabric que aplica ordenamiento y compresion especiales:

- Habilita **lecturas ultrarrapidas** en todos los motores de Fabric (Spark, SQL, Power BI)
- Se aplica automaticamente a tablas administradas en Lakehouse
- Compatible con el formato abierto Delta/Parquet (sin vendor lock-in)
- Mejora los ratios de compresion entre 10-50%

```python
# V-Order esta habilitado de forma predeterminada en Fabric Lakehouse
# Para habilitar/deshabilitar explicitamente:
spark.conf.set("spark.sql.parquet.vorder.enabled", "true")

# Escribir con V-Order
df.write.format("delta").mode("overwrite").saveAsTable("optimized_table")
```

### SQL Analytics Endpoint

Cada Lakehouse obtiene automaticamente un **SQL analytics endpoint de solo lectura**:

- **Vistas T-SQL autogeneradas** para todas las tablas Delta
- Consulta con cualquier herramienta SQL (SSMS, Azure Data Studio, Power BI)
- Admite **consultas cross-database** hacia otros Lakehouses y Warehouses
- **No** admite DML (INSERT, UPDATE, DELETE): es de solo lectura

```sql
-- Consultar tablas de Lakehouse mediante SQL analytics endpoint
SELECT 
    product_category,
    SUM(revenue) AS total_revenue
FROM dbo.silver_sales
GROUP BY product_category
ORDER BY total_revenue DESC;

-- Consulta cross-database
SELECT l.customer_name, w.total_orders
FROM MyLakehouse.dbo.customers l
JOIN MyWarehouse.dbo.order_summary w
    ON l.customer_id = w.customer_id;
```

> **Exam Tip:** El SQL analytics endpoint es **de solo lectura**. Para escribir datos, usa Spark notebooks o Dataflows Gen2.

[Microsoft Docs - Lakehouse in Fabric](https://learn.microsoft.com/en-us/fabric/data-engineering/lakehouse-overview)

---

## 7. Warehouse

### T-SQL en Fabric Warehouse

Fabric Warehouse admite un **subconjunto de T-SQL** basado en el motor de Synapse Analytics.

**Compatible:**
- SELECT, INSERT, UPDATE, DELETE, MERGE
- CREATE TABLE, ALTER TABLE, DROP TABLE
- Views, stored procedures, functions
- CTEs, window functions, subqueries
- Consultas cross-database

**No compatible:**
- Triggers
- Materialized views (usa views normales o tablas de agregacion)
- Ciertos system stored procedures

### Distribution Strategies

| Estrategia | Descripcion | Ideal para |
|---|---|---|
| **Round-Robin** | Distribuye filas uniformemente entre distribuciones | Staging tables, sin join key |
| **Hash** | Distribuye filas por hash de una columna | Tablas fact grandes, joins frecuentes sobre esa columna |
| **Replicated** | Copia completa en cada nodo de compute | Tablas dimension pequenias (< 2 GB) |

```sql
-- Distribucion Round-Robin (predeterminada)
CREATE TABLE dbo.staging_orders (
    order_id INT,
    customer_id INT,
    order_date DATE,
    amount DECIMAL(18, 2)
);

-- Distribucion Hash sobre una join key
CREATE TABLE dbo.fact_sales (
    sale_id BIGINT,
    customer_id INT,
    product_id INT,
    sale_date DATE,
    quantity INT,
    revenue DECIMAL(18, 2)
)
WITH (DISTRIBUTION = HASH(customer_id));

-- Tabla replicada para dimensiones pequenias
CREATE TABLE dbo.dim_product (
    product_id INT,
    product_name NVARCHAR(200),
    category NVARCHAR(100),
    subcategory NVARCHAR(100)
)
WITH (DISTRIBUTION = REPLICATE);
```

> **Exam Tip:** Elige distribucion **Hash** en la columna usada con mayor frecuencia en clausulas **JOIN** y **GROUP BY**. Elige **Replicated** para tablas dimension menores de 2 GB. Usa **Round-Robin** por defecto para tablas de staging/temp.

### Partitioning

Actualmente Fabric Warehouse no admite particionamiento definido por el usuario de la misma forma que Synapse Dedicated SQL Pool. Sin embargo, el **particionamiento de Delta Lake** puede usarse en escenarios Lakehouse:

```python
# Particionar por columna de fecha al escribir tablas Delta
df.write.format("delta") \
    .partitionBy("sale_year", "sale_month") \
    .mode("overwrite") \
    .saveAsTable("fact_sales_partitioned")
```

### Ajuste de rendimiento

| Tecnica | Descripcion |
|---|---|
| **Statistics** | Se crean automaticamente; asegurate de que esten actualizadas |
| **Result set caching** | Almacena en cache resultados de consultas repetidas |
| **Workload management** | Fabric capacity administra la concurrencia automaticamente |
| **Eleccion de distribucion** | Minimiza el movimiento de datos con la columna hash correcta |
| **Evitar SELECT *** | Consulta solo las columnas necesarias |
| **Usar CTEs y temp tables** | Divide consultas complejas en etapas |

```sql
-- Patron de consulta optimizada para rendimiento
WITH recent_sales AS (
    SELECT 
        customer_id,
        product_id,
        revenue,
        sale_date
    FROM dbo.fact_sales
    WHERE sale_date >= DATEADD(MONTH, -3, GETDATE())
),
customer_totals AS (
    SELECT 
        customer_id,
        SUM(revenue) AS total_revenue,
        COUNT(*) AS order_count,
        AVG(revenue) AS avg_order_value
    FROM recent_sales
    GROUP BY customer_id
)
SELECT 
    c.customer_id,
    d.customer_name,
    c.total_revenue,
    c.order_count,
    c.avg_order_value,
    RANK() OVER (ORDER BY c.total_revenue DESC) AS revenue_rank
FROM customer_totals c
JOIN dbo.dim_customer d ON c.customer_id = d.customer_id
ORDER BY c.total_revenue DESC;
```

### Cross-Database Queries

```sql
-- Consultar entre Lakehouse y Warehouse en el mismo workspace
SELECT 
    w.customer_id,
    w.customer_name,
    l.total_purchases,
    l.last_purchase_date
FROM MyWarehouse.dbo.dim_customer w
JOIN MyLakehouse.dbo.customer_activity l
    ON w.customer_id = l.customer_id
WHERE l.last_purchase_date >= '2024-01-01';
```

### Stored Procedures y Views

```sql
-- Crear una view reutilizable
CREATE VIEW dbo.vw_ActiveCustomers AS
SELECT 
    c.customer_id,
    c.customer_name,
    c.email,
    MAX(s.sale_date) AS last_purchase,
    SUM(s.revenue) AS lifetime_value
FROM dbo.dim_customer c
JOIN dbo.fact_sales s ON c.customer_id = s.customer_id
WHERE s.sale_date >= DATEADD(YEAR, -1, GETDATE())
GROUP BY c.customer_id, c.customer_name, c.email;

-- Crear un stored procedure parametrizado
CREATE PROCEDURE dbo.usp_LoadFactSales
    @StartDate DATE,
    @EndDate DATE
AS
BEGIN
    -- Truncar y recargar para el rango de fechas
    DELETE FROM dbo.fact_sales 
    WHERE sale_date BETWEEN @StartDate AND @EndDate;

    INSERT INTO dbo.fact_sales (sale_id, customer_id, product_id, sale_date, quantity, revenue)
    SELECT 
        sale_id, customer_id, product_id, sale_date, quantity, revenue
    FROM dbo.staging_sales
    WHERE sale_date BETWEEN @StartDate AND @EndDate;
END;
```

[Microsoft Docs - Warehouse in Fabric](https://learn.microsoft.com/en-us/fabric/data-warehouse/data-warehousing)

---

## 8. Calidad de datos y administracion de conexiones

### Monitoreo de calidad de datos

Implementa controles de calidad a lo largo de tu data pipeline:

```python
from pyspark.sql.functions import col, count, when, isnan, isnull

# Perfilar un DataFrame para detectar problemas de calidad
def profile_data_quality(df, table_name):
    """Generate data quality metrics for a DataFrame."""
    total_rows = df.count()
    
    # Conteos de null por columna
    null_counts = df.select([
        count(when(isnull(c) | isnan(c), c)).alias(c) 
        for c in df.columns
    ])
    
    # Verificacion de duplicados
    duplicate_count = total_rows - df.dropDuplicates().count()
    
    print(f"Table: {table_name}")
    print(f"Total rows: {total_rows}")
    print(f"Duplicate rows: {duplicate_count}")
    print("Null counts per column:")
    null_counts.show()
    
    return {
        "table": table_name,
        "total_rows": total_rows,
        "duplicates": duplicate_count
    }

# Ejecutar controles de calidad
quality_report = profile_data_quality(df, "silver_customers")
```

**Validacion de calidad con assertions:**

```python
# Validar la calidad de datos antes de escribir en la capa Gold
assert df.filter(col("customer_id").isNull()).count() == 0, \
    "FAIL: Null customer_id found"

assert df.count() == df.dropDuplicates(["customer_id"]).count(), \
    "FAIL: Duplicate customer_id found"

assert df.filter(col("revenue") < 0).count() == 0, \
    "FAIL: Negative revenue values found"

print("All quality checks passed!")
```

**Controles de calidad T-SQL en Warehouse:**

```sql
-- Procedimiento de validacion de calidad de datos
CREATE PROCEDURE dbo.usp_ValidateFactSales
AS
BEGIN
    DECLARE @NullCount INT, @DuplicateCount INT, @NegativeCount INT;

    -- Verificar claves null
    SELECT @NullCount = COUNT(*) 
    FROM dbo.fact_sales 
    WHERE customer_id IS NULL OR product_id IS NULL;

    -- Verificar duplicados
    SELECT @DuplicateCount = COUNT(*) - COUNT(DISTINCT sale_id) 
    FROM dbo.fact_sales;

    -- Verificar revenue negativo
    SELECT @NegativeCount = COUNT(*) 
    FROM dbo.fact_sales 
    WHERE revenue < 0;

    -- Reportar resultados
    IF @NullCount > 0 OR @DuplicateCount > 0 OR @NegativeCount > 0
    BEGIN
        RAISERROR('Data quality validation failed. Nulls: %d, Duplicates: %d, Negatives: %d', 
            16, 1, @NullCount, @DuplicateCount, @NegativeCount);
    END
    ELSE
        PRINT 'All data quality checks passed.';
END;
```

### Tipos de conexion

| Tipo de conexion | Caso de uso | Autenticacion |
|---|---|---|
| **Cloud connection** | Servicios de Azure, apps SaaS | OAuth, service principal, managed identity |
| **On-premises data gateway** | SQL Server, file shares, Oracle on-prem | Windows auth, basic auth |
| **VNet data gateway** | Recursos de Azure en una VNet | Managed identity |

### Configuracion de Gateway

- **On-premises data gateway:** Instalar en una maquina Windows local para conectar Fabric con data sources on-premises
- **VNet data gateway:** Gateway administrado para conectarse de forma segura a recursos de Azure VNet
- **Gateway cluster:** Multiples gateways para alta disponibilidad y balanceo de carga

> **Exam Tip:** Los on-premises data gateways son necesarios para **scheduled refresh** de datos desde origenes on-premises. DirectQuery hacia origenes on-premises tambien requiere un gateway.

[Microsoft Docs - Data Gateways](https://learn.microsoft.com/en-us/fabric/data-factory/data-factory-overview)

---

## 9. Medallion Architecture

### Descripcion general

La Medallion Architecture organiza los datos en tres capas de calidad:

```
+------------+      +------------+      +-------------+
|  Bronze    | ---> |  Silver    | ---> |    Gold     |
|  (Raw)     |      | (Cleansed) |      | (Aggregated |
|            |      |            |      |  / Curated) |
+------------+      +------------+      +-------------+
```

| Capa | Proposito | Calidad de datos | Consumidores |
|---|---|---|---|
| **Bronze** | Ingesta raw, transformacion minima | Tal como viene del origen | Data engineers |
| **Silver** | Limpia, deduplicada, conformada | Validada, tipada | Data analysts, data scientists |
| **Gold** | Agregaciones de nivel de negocio, KPIs | Confiable, gobernada | Usuarios de negocio, informes Power BI |

### Patrones de implementacion

#### Bronze Layer - Raw Ingestion

```python
# Bronze: Ingestar datos raw con columnas de metadata
from pyspark.sql.functions import current_timestamp, input_file_name, lit

df_raw = (
    spark.read
    .option("header", "true")
    .option("inferSchema", "true")
    .csv("Files/raw/sales/*.csv")
)

df_bronze = (
    df_raw
    .withColumn("_ingestion_timestamp", current_timestamp())
    .withColumn("_source_file", input_file_name())
    .withColumn("_batch_id", lit("batch_2024_01_15"))
)

df_bronze.write.format("delta").mode("append").saveAsTable("bronze_sales")
```

#### Silver Layer - Cleansed and Conformed

```python
# Silver: Limpiar, deduplicar, aplicar esquema y reglas de negocio
from delta.tables import DeltaTable
from pyspark.sql.functions import col, trim, lower, to_date, when

df_bronze = spark.read.table("bronze_sales")

df_silver = (
    df_bronze
    # Limpieza de datos
    .withColumn("customer_name", trim(col("customer_name")))
    .withColumn("email", lower(trim(col("email"))))
    .withColumn("order_date", to_date(col("order_date"), "yyyy-MM-dd"))
    # Quitar nulls en columnas clave
    .filter(col("order_id").isNotNull() & col("customer_id").isNotNull())
    # Aplicacion de tipos de datos
    .withColumn("revenue", col("revenue").cast("decimal(18,2)"))
    .withColumn("quantity", col("quantity").cast("int"))
    # Regla de negocio: marcar pedidos sospechosos
    .withColumn("is_suspicious", 
        when(col("revenue") > 10000, True).otherwise(False))
    # Deduplicar
    .dropDuplicates(["order_id"])
)

# Upsert en tabla Silver
if spark.catalog.tableExists("silver_sales"):
    target = DeltaTable.forName(spark, "silver_sales")
    target.alias("t").merge(
        df_silver.alias("s"), "t.order_id = s.order_id"
    ).whenMatchedUpdateAll() \
     .whenNotMatchedInsertAll() \
     .execute()
else:
    df_silver.write.format("delta").mode("overwrite").saveAsTable("silver_sales")
```

#### Gold Layer - Business Aggregations

```python
# Gold: Agregaciones y KPIs listos para negocio
from pyspark.sql.functions import sum, avg, countDistinct, max, min
from pyspark.sql.window import Window

df_silver = spark.read.table("silver_sales")

# Resumen de clientes
df_gold_customers = (
    df_silver
    .groupBy("customer_id", "customer_name")
    .agg(
        sum("revenue").alias("lifetime_revenue"),
        countDistinct("order_id").alias("total_orders"),
        avg("revenue").alias("avg_order_value"),
        max("order_date").alias("last_order_date"),
        min("order_date").alias("first_order_date")
    )
    .withColumn("customer_segment",
        when(col("lifetime_revenue") > 10000, "Platinum")
        .when(col("lifetime_revenue") > 5000, "Gold")
        .when(col("lifetime_revenue") > 1000, "Silver")
        .otherwise("Bronze")
    )
)

df_gold_customers.write.format("delta").mode("overwrite").saveAsTable("gold_customer_summary")

# KPIs diarios de ventas
df_gold_daily = (
    df_silver
    .groupBy("order_date")
    .agg(
        sum("revenue").alias("daily_revenue"),
        countDistinct("order_id").alias("daily_orders"),
        countDistinct("customer_id").alias("unique_customers")
    )
    .orderBy("order_date")
)

df_gold_daily.write.format("delta").mode("overwrite").saveAsTable("gold_daily_sales_kpi")
```

### Medallion Architecture con T-SQL (Warehouse)

```sql
-- Transformacion Bronze -> Silver en Warehouse
CREATE PROCEDURE dbo.usp_BronzeToSilver_Sales
AS
BEGIN
    MERGE INTO dbo.silver_sales AS t
    USING (
        SELECT DISTINCT
            order_id,
            customer_id,
            TRIM(customer_name) AS customer_name,
            LOWER(TRIM(email)) AS email,
            CAST(order_date AS DATE) AS order_date,
            CAST(revenue AS DECIMAL(18,2)) AS revenue,
            CAST(quantity AS INT) AS quantity,
            GETDATE() AS _processed_at
        FROM dbo.bronze_sales
        WHERE order_id IS NOT NULL 
          AND customer_id IS NOT NULL
    ) AS s
    ON t.order_id = s.order_id
    WHEN MATCHED THEN
        UPDATE SET 
            t.customer_name = s.customer_name,
            t.email = s.email,
            t.revenue = s.revenue,
            t._processed_at = s._processed_at
    WHEN NOT MATCHED THEN
        INSERT (order_id, customer_id, customer_name, email, order_date, revenue, quantity, _processed_at)
        VALUES (s.order_id, s.customer_id, s.customer_name, s.email, s.order_date, s.revenue, s.quantity, s._processed_at);
END;

-- Agregacion Silver -> Gold
CREATE PROCEDURE dbo.usp_SilverToGold_CustomerSummary
AS
BEGIN
    -- Recrear tabla gold
    IF OBJECT_ID('dbo.gold_customer_summary', 'U') IS NOT NULL
        DROP TABLE dbo.gold_customer_summary;

    CREATE TABLE dbo.gold_customer_summary
    WITH (DISTRIBUTION = HASH(customer_id))
    AS
    SELECT 
        customer_id,
        customer_name,
        SUM(revenue) AS lifetime_revenue,
        COUNT(DISTINCT order_id) AS total_orders,
        AVG(revenue) AS avg_order_value,
        MAX(order_date) AS last_order_date,
        MIN(order_date) AS first_order_date,
        CASE 
            WHEN SUM(revenue) > 10000 THEN 'Platinum'
            WHEN SUM(revenue) > 5000 THEN 'Gold'
            WHEN SUM(revenue) > 1000 THEN 'Silver'
            ELSE 'Bronze'
        END AS customer_segment,
        GETDATE() AS _generated_at
    FROM dbo.silver_sales
    GROUP BY customer_id, customer_name;
END;
```

### Best Practices

| Practica | Recomendacion |
|---|---|
| **Bronze** | Append-only, conservar datos raw, agregar columnas de metadata (`_ingestion_timestamp`, `_source_file`) |
| **Silver** | Deduplicar, limpiar, conformar tipos de datos, usar MERGE para upserts |
| **Gold** | Preagregar para reporting, usar diseno de star schema, optimizar para Power BI |
| **Naming** | Prefijar tablas: `bronze_`, `silver_`, `gold_` o usar Lakehouses separados |
| **Separate Lakehouses** | Considerar un Lakehouse por capa para gobernanza y permisos claros |
| **Idempotency** | Disenar pipelines para producir el mismo resultado al reejecutarse |
| **Schema evolution** | Usar la opcion `mergeSchema` de Delta Lake cuando se esperan cambios de esquema |
| **Maintenance** | Programar OPTIMIZE y VACUUM en tablas Silver/Gold |

> **Exam Tip:** El examen evalua frecuentemente la Medallion Architecture. Conoce el proposito de cada capa y se capaz de identificar que transformaciones pertenecen a Bronze, Silver o Gold.

[Microsoft Docs - Medallion Architecture](https://learn.microsoft.com/en-us/fabric/onelake/onelake-medallion-lakehouse-architecture)

---

## Referencia rapida: elegir la herramienta correcta

| Escenario | Herramienta recomendada |
|---|---|
| ETL batch desde origenes cloud | **Copy Activity** en Pipeline |
| Transformacion low-code | **Dataflows Gen2** (Power Query) |
| Procesamiento complejo de datos a escala | **Spark Notebook** (PySpark) |
| Transformaciones de warehouse basadas en SQL | **T-SQL** en Warehouse |
| Ingesta de datos en tiempo real | **Eventstreams** |
| Acceso a datos cross-cloud (sin copia) | **Shortcuts** |
| Orquestacion de multiples activities | **Data Factory Pipeline** |
| Refresh de tabla dimension pequenia | **Dataflows Gen2** |
| Feature engineering de ML | **Spark Notebook** |
| Spark jobs de produccion programados | **Spark Job Definitions** |

---

## Checklist de temas clave del examen

- [ ] Describir patrones de ingesta batch vs. streaming
- [ ] Configurar Copy Activity con diferentes tipos source/sink
- [ ] Crear y optimizar Dataflows Gen2 con incremental refresh
- [ ] Construir pipelines con control flow (ForEach, If, Switch)
- [ ] Usar parameters, variables y expresiones en pipelines
- [ ] Escribir transformaciones PySpark y operaciones Delta Lake
- [ ] Implementar patrones MERGE/upsert (PySpark y T-SQL)
- [ ] Elegir la estrategia de distribucion correcta para tablas Warehouse
- [ ] Configurar y usar shortcuts para acceso virtualizado a datos
- [ ] Realizar mantenimiento de tablas (OPTIMIZE, VACUUM, Z-ORDER)
- [ ] Explicar V-Order y sus beneficios
- [ ] Implementar Medallion Architecture (Bronze -> Silver -> Gold)
- [ ] Configurar gateways para acceso a datos on-premises
- [ ] Monitorear ejecuciones de pipelines y manejar errores
- [ ] Implementar controles de validacion de calidad de datos

---

> **Recursos adicionales:**
> - [DP-600 Exam Study Guide (Microsoft)](https://learn.microsoft.com/en-us/credentials/certifications/fabric-analytics-engineer-associate/)
> - [Microsoft Fabric Documentation](https://learn.microsoft.com/en-us/fabric/)
> - [Delta Lake Documentation](https://docs.delta.io/latest/index.html)
> - [Microsoft Learn - Fabric Learning Paths](https://learn.microsoft.com/en-us/training/paths/get-started-fabric/)
