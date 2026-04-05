# Cheat Sheet de componentes de Microsoft Fabric

> **Guía de referencia rápida para el examen DP-600** — cubre cada experience de Fabric, OneLake, almacenamiento, ingesta, orquestación, semantic models, despliegue y licenciamiento.

---

## Tabla de contenido

- [1. Resumen de experiences de Fabric](#1-resumen-de-experiences-de-fabric)
- [2. OneLake](#2-onelake)
- [3. Lakehouse](#3-lakehouse)
- [4. Warehouse](#4-warehouse)
- [5. Dataflows Gen2](#5-dataflows-gen2)
- [6. Data Pipelines](#6-data-pipelines)
- [7. Notebooks](#7-notebooks)
- [8. Semantic Models](#8-semantic-models)
- [9. Eventstreams](#9-eventstreams)
- [10. Deployment Pipelines](#10-deployment-pipelines)
- [11. Git Integration](#11-git-integration)
- [12. Capacity y licenciamiento](#12-capacity-y-licenciamiento)
- [13. Tablas comparativas](#13-tablas-comparativas)

---

## 1. Resumen de experiences de Fabric

Microsoft Fabric es una plataforma unificada de analítica SaaS. Cada **experience** es una vista orientada a una persona específica sobre la misma plataforma subyacente y el mismo almacenamiento en OneLake.

| Experience | Persona principal | Capacidades clave |
|---|---|---|
| **Data Factory** | Data Engineer | Dataflows Gen2, Data Pipelines, conectores, orquestación |
| **Synapse Data Engineering** | Data Engineer | Lakehouses, Notebooks (Spark), trabajos de Spark |
| **Synapse Data Warehouse** | Data Analyst / Engineer | Warehouse con T-SQL, consultas cross-database, procedimientos almacenados |
| **Synapse Data Science** | Data Scientist | Modelos de ML, experimentos, seguimiento con MLflow, funciones `predict` |
| **Synapse Real-Time Analytics** | Data Engineer / Analyst | Bases de datos KQL, Eventstreams, dashboards en tiempo real |
| **Power BI** | Business Analyst | Reports, dashboards, semantic models, paginated reports |

### Diagrama de arquitectura

```
┌─────────────────────────────────────────────────────────────────────┐
│                      Microsoft Fabric (SaaS)                        │
│                                                                     │
│  ┌──────────┐ ┌──────────────┐ ┌──────────────┐ ┌───────────────┐  │
│  │  Data     │ │ Synapse Data │ │ Synapse Data │ │ Synapse Data  │  │
│  │  Factory  │ │ Engineering  │ │ Warehouse    │ │ Science       │  │
│  └────┬─────┘ └──────┬───────┘ └──────┬───────┘ └──────┬────────┘  │
│       │               │                │                │           │
│  ┌────┴───────────────┴────────────────┴────────────────┴────────┐  │
│  │                         OneLake                               │  │
│  │                    (formato Delta Parquet)                     │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                                                                     │
│  ┌───────────────────┐           ┌───────────────────────────────┐  │
│  │ Synapse Real-Time │           │          Power BI             │  │
│  │ Analytics         │           │  (Reports, Semantic Models)   │  │
│  └───────────────────┘           └───────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
```

> **Consejo de examen:** Todas las experiences de Fabric comparten una **single capacity** y el almacenamiento de **OneLake**. No aprovisionas clusters de `compute` separados; Fabric lo administra por ti.

---

## 2. OneLake

OneLake es el **single, unified data lake** para todo el tenant de Fabric. Piensa en él como el `"OneDrive para datos"`.

### Jerarquía

```
Tenant (uno por organización)
 └── Workspace (límite de seguridad / colaboración)
      ├── Lakehouse Item
      │    ├── Tables/   (tablas Delta administradas)
      │    └── Files/    (archivos no administrados — CSV, Parquet, JSON, imágenes …)
      ├── Warehouse Item
      ├── KQL Database Item
      ├── Semantic Model
      └── …otros items
```

### Propiedades clave

| Propiedad | Detalle |
|---|---|
| **Format** | Delta Parquet (open-source, versionado, ACID) |
| **Protocol** | ABFS — `abfss://<workspace>@onelake.dfs.fabric.microsoft.com/<item>/…` |
| **Access** | OneLake File Explorer (Windows), REST APIs, Azure Storage SDKs |
| **Governance** | Integración con Microsoft Purview, data lineage, sensitivity labels |
| **One copy** | Los datos se almacenan una sola vez; cada motor lee la misma copia (sin duplicación) |

### Shortcuts

Los shortcuts te permiten referenciar datos **sin copiarlos** dentro de OneLake.

| Tipo de shortcut | Destino | Notas |
|---|---|---|
| **Internal (OneLake)** | Otro Lakehouse/Warehouse en el mismo o en distinto workspace | Solo mismo tenant |
| **ADLS Gen2** | Azure Data Lake Storage Gen2 | Requiere `service principal` o `account key` |
| **Amazon S3** | Bucket de AWS S3 | Requiere `access key` / `secret` |
| **Google Cloud Storage** | Bucket de GCS | Requiere clave HMAC |
| **Dataverse** | Tablas de Dataverse (Dynamics 365 / Power Platform) | Tablas virtuales de solo lectura |

> **Consejo de examen:** Los shortcuts aparecen como carpetas en el Lakehouse, pero los datos permanecen en el origen. Las operaciones de escritura sobre shortcuts externos **no** están soportadas; son de **solo lectura**.

---

## 3. Lakehouse

El Lakehouse combina un **data lake** (`Files`) con un **data warehouse** (`Tables`) en un solo item.

### Dos secciones

```
Lakehouse
 ├── Tables/          ← Tablas Delta administradas (aparecen en SQL analytics endpoint)
 │    ├── sales
 │    ├── customers
 │    └── products
 └── Files/           ← Área no administrada (archivos raw, staging, imágenes, etc.)
      ├── raw/
      ├── staging/
      └── landing/
```

### Endpoints automáticos

| Endpoint | Qué hace |
|---|---|
| **SQL analytics endpoint** | Endpoint T-SQL de solo lectura generado automáticamente sobre tablas Delta. Admite `views`, funciones, consultas cross-Lakehouse. Sin DML (`INSERT`/`UPDATE`/`DELETE`). |
| **Default semantic model** | `Dataset` de Power BI generado automáticamente que refleja las tablas. Permite crear reports rápidamente. |

### Cargar datos en tablas de Lakehouse

| Método | Cuándo usarlo |
|---|---|
| **Notebooks (Spark)** | Transformaciones complejas, PySpark/Spark SQL, ETL a gran escala |
| **Dataflows Gen2** | Transformaciones low-code / Power Query |
| **Data Pipelines (Copy activity)** | Movimiento simple de datos desde más de 100 conectores |
| **Shortcut** | Acceso virtual, sin necesidad de copia |
| **File upload** | Cargas manuales únicas, archivos pequeños |
| **LOAD DATA** | Mediante el SQL analytics endpoint (limitado) |

> **Consejo de examen:** El SQL analytics endpoint es **de solo lectura**. Para escribir datos en tablas de Lakehouse, usa Spark (`Notebooks`), `Dataflows Gen2` o `Pipelines`, no T-SQL.

---

## 4. Warehouse

Synapse Data Warehouse proporciona una **experiencia completa de T-SQL** con soporte de DML.

### Capacidades clave

| Funcionalidad | Detalle |
|---|---|
| **DDL** | `CREATE TABLE`, `ALTER`, `DROP`, `schemas`, `views`, procedimientos almacenados, funciones |
| **DML** | `INSERT`, `UPDATE`, `DELETE`, `MERGE` |
| **COPY INTO** | Ingesta masiva de alto rendimiento desde ADLS Gen2 / OneLake (Parquet, CSV) |
| **Cross-database queries** | Consultas entre Warehouses y Lakehouse SQL endpoints dentro del mismo workspace |
| **Security** | Nivel de columna, fila, objeto, y `dynamic data masking` |
| **Transactions** | Cumplimiento completo de ACID |
| **Storage format** | Delta Parquet (igual que Lakehouse; almacenado en OneLake) |

### Sintaxis de COPY INTO

```sql
COPY INTO dbo.sales
FROM 'https://onelake.dfs.fabric.microsoft.com/<workspace>/<item>/Files/raw/*.parquet'
WITH (
    FILE_TYPE = 'PARQUET'
);
```

### Ejemplo de consulta cross-database

```sql
-- Consultar un Lakehouse SQL analytics endpoint desde un Warehouse
SELECT w.OrderID, l.CustomerName
FROM dbo.Orders AS w
JOIN MyLakehouse.dbo.Customers AS l
    ON w.CustomerID = l.CustomerID;
```

> **Consejo de examen:** El Warehouse soporta T-SQL de **lectura Y escritura**. El Lakehouse SQL analytics endpoint es **de solo lectura**. Si la pregunta requiere `INSERT`/`UPDATE`/`DELETE` mediante T-SQL, la respuesta es Warehouse.

---

## 5. Dataflows Gen2

Los Dataflows Gen2 son transformaciones de **Power Query Online** que se ejecutan en la nube y cargan datos en destinos de Fabric.

### Características clave

| Funcionalidad | Detalle |
|---|---|
| **Engine** | Power Query Online (lenguaje M) |
| **Code style** | Low-code / no-code con barra de fórmulas |
| **300+ connectors** | Bases de datos, archivos, aplicaciones SaaS, web APIs |
| **Staging** | Usa OneLake staging (habilitado automáticamente) |
| **Destinations** | Tabla de Lakehouse, tabla de Warehouse, KQL Database, Azure SQL |
| **Refresh** | Bajo demanda o programado |
| **Data gateway** | On-premises data gateway para orígenes on-prem |

### Cuándo usar Dataflows Gen2

- Usuarios no técnicos que prefieren una experiencia **visual / low-code**
- **Transformaciones ligeras**: filtrar, combinar, pivotar, cambios de tipo
- Carga de datos desde **SaaS connectors** u orígenes on-prem
- Necesitas una consulta **reusable** que otros items puedan referenciar

> **Consejo de examen:** Dataflows Gen2 reemplaza a Power BI dataflows (Gen1) y agrega **output destinations**. Puedes escribir directamente en tablas de Lakehouse o Warehouse. Los dataflows Gen1 solo escriben en el almacenamiento de Power BI.

---

## 6. Data Pipelines

Los Data Pipelines son la capa de **orchestration** en Fabric, similar a los pipelines de Azure Data Factory.

### Actividades principales

| Actividad | Propósito |
|---|---|
| **Copy Data** | Mover datos entre más de 100 orígenes y destinos |
| **Dataflow** | Ejecutar una transformación de Dataflows Gen2 |
| **Notebook** | Ejecutar un notebook de Spark |
| **Stored Procedure** | Ejecutar un procedimiento almacenado de Warehouse |
| **ForEach** | Iterar sobre una colección de items |
| **If Condition** | Ramificar lógica basada en una expresión |
| **Until** | Repetir actividades hasta que una condición sea verdadera |
| **Wait** | Pausar la ejecución por una duración definida |
| **Set Variable** | Asignar un valor a una variable del pipeline |
| **Lookup** | Devolver un conjunto pequeño de resultados desde una fuente |
| **Web** | Llamar a un endpoint HTTP |
| **Script** | Ejecutar un script SQL contra un Warehouse |

### Conceptos de Pipeline

| Concepto | Detalle |
|---|---|
| **Parameters** | Entradas que pueden establecerse en tiempo de ejecución |
| **Variables** | Estado interno dentro de una ejecución del pipeline |
| **Triggers** | Ejecución basada en horario o en eventos |
| **Scheduling** | Programaciones recurrentes tipo cron desde la pestaña Schedule |
| **Monitoring** | Historial de ejecución del pipeline en Monitoring Hub |
| **Dependencies** | `On Success`, `On Failure`, `On Completion`, `On Skipped` |

### Patrón de orchestration

```
Pipeline: Daily Ingestion
 │
 ├─► Copy Activity (cargar archivos raw)
 │        │ On Success
 │        ▼
 ├─► Notebook Activity (transformar Bronze → Silver)
 │        │ On Success
 │        ▼
 ├─► Dataflow Activity (Silver → Gold dimensions)
 │        │ On Success
 │        ▼
 └─► Stored Procedure Activity (actualizar agregaciones)
```

> **Consejo de examen:** Los Pipelines **orquestan** trabajo, pero no transforman datos por sí mismos. Usa Copy para movimiento y Notebooks, Dataflows o Stored Procedures para transformación.

---

## 7. Notebooks

Los Notebooks son la herramienta principal para data engineering y data science **basados en Spark** dentro de Fabric.

### Lenguajes compatibles

| Lenguaje | Magic Command | Caso de uso |
|---|---|---|
| **PySpark** | `%%pyspark` (predeterminado) | DataFrames, ETL, ML |
| **Spark SQL** | `%%sql` | Consultas SQL sobre tablas Spark |
| **Scala** | `%%spark` | Operaciones de Spark basadas en JVM |
| **R** | `%%sparkr` | Análisis estadístico |

### Características clave

| Funcionalidad | Detalle |
|---|---|
| **Lakehouse integration** | Adjunta un Lakehouse predeterminado; las tablas aparecen en `spark.sql("SELECT * FROM …")` |
| **Inline installation** | `%pip install <library>` o `%conda install <library>` para paquetes con alcance de sesión |
| **Workspace libraries** | Carga `.whl`, `.jar` o `.tar.gz` para uso compartido |
| **Environment items** | Definiciones reutilizables de environment, bibliotecas y configuración de Spark, adjuntas a notebooks |
| **V-Order optimization** | Fabric escribe Parquet optimizado con V-Order para lecturas más rápidas |
| **High concurrency mode** | Comparte una sola sesión de Spark entre varios notebooks |
| **Parameterization** | Marca una celda como `"parameter cell"` para inyectar valores desde Pipelines |

### Patrones comunes de PySpark

```python
# Leer desde archivos de Lakehouse
df = spark.read.format("csv") \
    .option("header", "true") \
    .load("Files/raw/sales.csv")

# Escribir en tablas de Lakehouse (Delta administrado)
df.write.format("delta") \
    .mode("overwrite") \
    .saveAsTable("sales_clean")

# Merge incremental usando Delta
from delta.tables import DeltaTable
target = DeltaTable.forName(spark, "customers")
target.alias("t").merge(
    source_df.alias("s"),
    "t.CustomerID = s.CustomerID"
).whenMatchedUpdateAll() \
 .whenNotMatchedInsertAll() \
 .execute()
```

> **Consejo de examen:** Los Notebooks usan **Apache Spark** internamente. Fabric aprovisiona pools de Spark automáticamente; no creas ni configuras manualmente tamaños de cluster.

---

## 8. Semantic Models

Los Semantic Models, antes llamados `datasets`, definen la capa de lógica de negocio que consumen los reports de Power BI.

### Modos de almacenamiento

| Modo | Ubicación de los datos | ¿Requiere refresh? | Velocidad de consulta | Ideal para |
|---|---|---|---|---|
| **Import** | En memoria (`VertiPaq`) | Sí, programado o bajo demanda | ⚡ La más rápida | `datasets` pequeños o medianos, DAX complejo |
| **DirectQuery** | Sistema origen (`live`) | No | 🐢 Depende del origen | Datos en tiempo real, `datasets` muy grandes |
| **Direct Lake** | OneLake (Delta Parquet) | No (`auto-sync`) | ⚡ Velocidad cercana a Import | **Nativo de Fabric**, lo mejor de ambos mundos |
| **Dual** | Import + DirectQuery | Parcial | Mixto | Tablas de dimensión en modelos compuestos |

### Direct Lake en detalle

```
Power BI Report
      │
      ▼
Semantic Model (modo Direct Lake)
      │  lee Delta Parquet directamente
      ▼
OneLake
 └── Tablas Delta de Lakehouse / Warehouse
```

- **Sin copia de datos**: lee directamente desde archivos Delta Parquet en OneLake
- **Sin refresh programado**: detecta automáticamente nuevas versiones del Delta log
- **Fallback**: si los datos exceden memoria o se usa DAX no soportado, puede volver a modo DirectQuery
- **Framing**: se toma una instantánea del Delta log para asegurar lecturas consistentes

| Guardrail de Direct Lake | F2 | F64 | F128+ |
|---|---|---|---|
| Máx. tablas por modelo | 500 | 500 | 500 |
| Máx. filas por tabla | 300 M | 1.5 B | 3 B |
| Tamaño máximo del modelo en disco | 10 GB | 40 GB | 80 GB+ |

> **Consejo de examen:** Direct Lake es el modo de almacenamiento **recomendado** para soluciones nativas de Fabric. Evita la sobrecarga de refresh de Import y también la latencia del origen de DirectQuery. Debes conocer el comportamiento de fallback: si Direct Lake no puede atender una consulta, **cae a DirectQuery** contra el SQL endpoint.

---

## 9. Eventstreams

Los Eventstreams proporcionan **ingesta de datos en tiempo real** en Fabric.

### Sources (Inputs)

| Source | Descripción |
|---|---|
| **Azure Event Hubs** | Servicio administrado de streaming de eventos |
| **Azure IoT Hub** | Telemetría de dispositivos IoT |
| **Custom App** | Productores personalizados basados en SDK |
| **Sample data** | Flujos de ejemplo integrados para pruebas |
| **Azure Blob Storage** | Basado en eventos, disparado por nuevos blobs |

### Destinations (Outputs)

| Destination | Descripción |
|---|---|
| **Lakehouse** | Aterrizar eventos como tablas Delta |
| **KQL Database** | Analítica en tiempo real con KQL |
| **Custom App** | Consumidores basados en SDK |
| **Reflex** | Disparar alertas y acciones según patrones |
| **Derived stream** | Flujo intermedio para escenarios de `fan-out` |

### Processing

| Funcionalidad | Detalle |
|---|---|
| **No-code editor** | Procesamiento visual de eventos con `drag-and-drop` |
| **Transformations** | `Filter`, `Manage Fields`, `Group By` con agregaciones por ventana |
| **Windowing** | Ventanas `Tumbling`, `Hopping`, `Session`, `Snapshot` |
| **Serialization** | JSON, Avro, CSV |

> **Consejo de examen:** Eventstreams + KQL Database es el patrón típico para **real-time analytics** en Fabric. Si una pregunta involucra streaming o datos casi en tiempo real, piensa en Eventstreams.

---

## 10. Deployment Pipelines

Los Deployment Pipelines administran el **ALM (Application Lifecycle Management)** de los items de Fabric entre entornos.

### Etapas del pipeline

```
┌────────────┐       ┌────────────┐       ┌────────────┐
│    Dev      │──────▶│    Test     │──────▶│    Prod     │
│ (workspace) │       │ (workspace) │       │ (workspace) │
└────────────┘       └────────────┘       └────────────┘
     Autor             Validar             Atender usuarios
     y construir       y QA                y monitorear
```

### Conceptos clave

| Concepto | Detalle |
|---|---|
| **Stages** | Hasta 10 etapas personalizadas, comúnmente `Dev → Test → Prod` |
| **Deployment rules** | Sobrescriben cadenas de conexión, parámetros o fuentes de datos por etapa |
| **Selective deploy** | Despliega items individuales o todos los items a la vez |
| **Supported items** | Semantic models, reports, dataflows, Lakehouses, Warehouses, notebooks y más |
| **Permissions** | Se requiere rol Member o Admin del workspace |
| **Comparison** | Diff lado a lado de cambios entre etapas |

### Deployment Rules

| Tipo de regla | Propósito | Ejemplo |
|---|---|---|
| **Data source rules** | Cambiar cadenas de conexión entre etapas | `Dev DB → Prod DB` |
| **Parameter rules** | Sobrescribir parámetros de semantic models | `Environment = "Production"` |

> **Consejo de examen:** Los deployment pipelines operan a nivel de **workspace**. Cada etapa se asigna a un workspace de Fabric distinto. Los items se copian, no se enlazan, entre etapas.

---

## 11. Git Integration

Fabric admite integración con **source control** para desarrollo versionado.

### Proveedores compatibles

| Proveedor | Estado |
|---|---|
| **Azure DevOps (Azure Repos)** | Totalmente soportado |
| **GitHub** | Soportado |

### Cómo funciona

```
Fabric Workspace  ◄───sync───►  Git Repository (branch)
      │                                │
      │  commit / update               │  pull requests
      ▼                                ▼
 Live items                      Code review & merge
```

### Conceptos clave

| Concepto | Detalle |
|---|---|
| **Connection** | Un workspace se conecta a una branch de un repo |
| **Sync direction** | Bidireccional: `commit` workspace → repo, `update` repo → workspace |
| **Item representation** | Cada item de Fabric se serializa como archivos JSON / TMDL en el repo |
| **Conflict resolution** | Manual: los cambios entre workspace y repo deben resolverse |
| **Supported items** | Semantic models, reports, notebooks, Spark job definitions y más |
| **Branching strategy** | Típicamente `feature branches → main → deployment pipeline` |

### Estrategia de branching recomendada

```
main (protected)
 ├── feature/add-sales-report
 ├── feature/update-etl-pipeline
 └── bugfix/fix-date-dimension
         │
         └── Pull Request → Code Review → Merge to main
                                              │
                                              ▼
                                     Dev workspace (synced to main)
                                              │
                                     Deployment Pipeline → Test → Prod
```

> **Consejo de examen:** Git integration y deployment pipelines son **complementarios**. Git administra **source control y colaboración**. Los deployment pipelines administran la **promoción entre entornos**.

---

## 12. Capacity y licenciamiento

Fabric funciona con un modelo de licenciamiento **basado en capacity**. Todas las cargas de trabajo comparten un pool de **Capacity Units (CUs)**.

### SKUs y asignación de CU

| SKU | Capacity Units | Equivalente | Memoria máxima por consulta | Equivalente en Power BI |
|---|---|---|---|---|
| **F2** | 2 CU | — | 3 GB | — |
| **F4** | 4 CU | — | 3 GB | — |
| **F8** | 8 CU | — | 3 GB | EM1 / A1 |
| **F16** | 16 CU | — | 6 GB | EM2 / A2 |
| **F32** | 32 CU | — | 12 GB | EM3 / A3 |
| **F64** | 64 CU | P1 | 24 GB | P1 / A4 |
| **F128** | 128 CU | P2 | 48 GB | P2 / A5 |
| **F256** | 256 CU | P3 | 96 GB | P3 / A6 |
| **F512** | 512 CU | P4 | 192 GB | P4 / A7 |
| **F1024** | 1024 CU | P5 | 384 GB | P5 / A8 |
| **F2048** | 2048 CU | — | 768 GB | — |

### Conceptos clave de licenciamiento

| Concepto | Detalle |
|---|---|
| **Capacity Units (CUs)** | Pool de `compute` compartido; todas las cargas de trabajo de Fabric, Spark, SQL, Power BI y Dataflows, consumen de las mismas CUs |
| **Bursting** | Fabric permite picos temporales por encima del límite de CU; el exceso de uso se suaviza en el tiempo |
| **Throttling** | Si el uso sostenido excede la capacity, primero se limitan los trabajos en background y después los interactivos |
| **Pause / Resume** | Los SKUs F pueden pausarse para ahorrar costo, sin cargos de `compute` mientras están en pausa; el almacenamiento sigue cobrándose |
| **Autoscale** | No es igual al autoscale de Power BI; Fabric usa un modelo de smoothing y throttling |
| **Trial capacity** | Trial de Fabric por 60 días con capacity equivalente a F64 para evaluación |
| **Per-user licensing** | Free, Pro o PPU; necesario para compartir contenido de Power BI; los items de Fabric requieren capacity |

### Administración de Capacity

```
Capacity (p. ej., F64 = 64 CU)
 ├── Workspace A  ──►  Trabajos ETL de Lakehouse (uso de Spark CU)
 ├── Workspace B  ──►  Consultas de Warehouse (uso de SQL CU)
 └── Workspace C  ──►  Reports de Power BI (uso de VertiPaq CU)
         │
         └── Todos comparten el mismo pool de 64 CU
```

> **Consejo de examen:** Debes saber que **F64** es el SKU mínimo equivalente a Power BI Premium P1. Para el examen, entiende cuándo escalar verticalmente y cuándo optimizar cargas de trabajo para reducir consumo de CU.

---

## 13. Tablas comparativas

### Lakehouse vs. Warehouse

| Funcionalidad | Lakehouse | Warehouse |
|---|---|---|
| **Primary interface** | Spark (`notebooks`) + SQL analytics endpoint | T-SQL |
| **Write via T-SQL** | ❌ No, el SQL endpoint es de solo lectura | ✅ Sí, `INSERT`, `UPDATE`, `DELETE`, `MERGE` |
| **Write via Spark** | ✅ Sí | ❌ No |
| **Storage format** | Delta Parquet (OneLake) | Delta Parquet (OneLake) |
| **Schema enforcement** | `Schema-on-read` (flexible) | `Schema-on-write` (estricto) |
| **Unstructured data** | ✅ La sección `Files/` soporta cualquier formato | ❌ Solo datos estructurados |
| **Cross-database queries** | ✅ Mediante SQL analytics endpoint | ✅ T-SQL nativo |
| **Stored procedures** | ❌ No en el SQL endpoint | ✅ Soportado |
| **Row/column-level security** | ❌ Limitado | ✅ Soporte completo |
| **Best for** | Data engineers, data scientists, ELT flexible | SQL analysts, BI developers, gobernanza estricta |

### Dataflow Gen2 vs. Data Pipeline

| Funcionalidad | Dataflow Gen2 | Data Pipeline |
|---|---|---|
| **Primary purpose** | Transformación de datos | Orchestration y movimiento de datos |
| **Interface** | Power Query Online (visual / código M) | Diseñador tipo canvas con activities |
| **Data transformation** | ✅ Rica: `merge`, `pivot`, `unpivot`, tipos | ❌ Mínima, usar activities para transformar |
| **Orchestration** | ❌ Ejecución de un solo paso | ✅ Varios pasos con dependencias, bucles y condiciones |
| **Connectors** | 300+ Power Query connectors | 100+ conectores de Copy activity |
| **Scheduling** | ✅ Schedule independiente | ✅ Schedule independiente o disparado por eventos |
| **Can call the other** | ❌ No | ✅ Sí, Dataflow activity dentro de un Pipeline |
| **Best for** | Preparación self-service de datos, ETL ligero | Flujos complejos, orchestration, trabajos con varias actividades |

### Import vs. DirectQuery vs. Direct Lake

| Funcionalidad | Import | DirectQuery | Direct Lake |
|---|---|---|---|
| **Data stored in** | VertiPaq, en memoria | Sistema origen | OneLake, Delta Parquet |
| **Refresh required** | ✅ Sí, programado | ❌ No, consulta en vivo | ❌ No, auto-sync ante cambios del Delta log |
| **Query performance** | ⚡ La más rápida | 🐢 Depende del origen | ⚡ Velocidad cercana a Import |
| **Data freshness** | Se vuelve obsoleto entre refreshes | Siempre actual | Casi en tiempo real, al hacer commit en Delta |
| **Data size limit** | Límite de memoria del modelo | Capacity del origen | Guardrails de OneLake |
| **Works outside Fabric** | ✅ Cualquier origen | ✅ Cualquier origen soportado | ❌ Solo OneLake de Fabric |
| **Fallback behavior** | N/A | N/A | Pasa a DirectQuery vía SQL endpoint |
| **Best for** | DAX complejo, datos pequeños o medianos | Necesidad de tiempo real, datos enormes | Analítica nativa de Fabric |

### Flujo rápido de decisión

```
¿Los datos están en OneLake (Lakehouse / Warehouse)?
 ├── SÍ → Usa modo Direct Lake
 │          └── ¿Excede guardrails o requiere DAX no soportado?
 │               ├── SÍ → Import mode (u optimizar datos)
 │               └── NO  → ✅ Direct Lake
 └── NO  → ¿Es crítica la frescura en tiempo real?
             ├── SÍ → DirectQuery
             └── NO  → Import mode
```

---

## Resumen de consejos de examen

| # | Consejo |
|---|---|
| 1 | **OneLake = un lago por tenant.** Todos los datos en Delta Parquet. Los shortcuts evitan copias. |
| 2 | **El Lakehouse SQL endpoint es de solo lectura.** Usa Spark, Dataflows o Pipelines para escribir. |
| 3 | **Warehouse soporta T-SQL DML completo.** Elígelo cuando se requiera `INSERT`/`UPDATE`/`DELETE` vía SQL. |
| 4 | **Direct Lake es el modo de almacenamiento preferido** para semantic models nativos de Fabric. |
| 5 | **Direct Lake cae a DirectQuery** si no puede atender la consulta, por límites de memoria o DAX no soportado. |
| 6 | **Los Pipelines orquestan; los Dataflows transforman.** Los Pipelines pueden llamar Dataflows, no al revés. |
| 7 | **Los Notebooks se ejecutan sobre Spark.** Fabric aprovisiona pools automáticamente; no hay gestión manual de clusters. |
| 8 | **Los deployment pipelines promueven items** entre workspaces (`Dev → Test → Prod`). |
| 9 | **Git integration y deployment pipelines son complementarios**, no alternativas. |
| 10 | **F64 ≈ P1.** Todas las cargas comparten CUs. Pausar la capacity ahorra costo, pero el almacenamiento sigue cobrándose. |
| 11 | **Eventstreams + KQL Database** es el patrón de analítica en tiempo real en Fabric. |
| 12 | **Los shortcuts son de solo lectura** para orígenes externos como ADLS Gen2, S3 y GCS. |
| 13 | **COPY INTO** es la forma más rápida de cargar datos masivamente en un Warehouse desde archivos. |

---

> **Última actualización para preparación del examen DP-600.** Verifica siempre contra la documentación más reciente de [Microsoft Learn](https://learn.microsoft.com/en-us/fabric/).
