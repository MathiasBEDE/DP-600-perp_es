# Microsoft Fabric Components Cheat Sheet

> **Quick-reference guide for the DP-600 exam** — covers every Fabric experience, OneLake, storage, ingestion, orchestration, semantic models, deployment, and licensing.

---

## Table of Contents

- [1. Fabric Experiences Overview](#1-fabric-experiences-overview)
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
- [12. Capacity and Licensing](#12-capacity-and-licensing)
- [13. Comparison Tables](#13-comparison-tables)

---

## 1. Fabric Experiences Overview

Microsoft Fabric is a unified SaaS analytics platform. Each **experience** is a persona-oriented view of the same underlying platform and OneLake storage.

| Experience | Primary Persona | Key Capabilities |
|---|---|---|
| **Data Factory** | Data Engineer | Dataflows Gen2, Data Pipelines, connectors, orchestration |
| **Synapse Data Engineering** | Data Engineer | Lakehouses, Notebooks (Spark), Spark jobs |
| **Synapse Data Warehouse** | Data Analyst / Engineer | T-SQL warehouse, cross-database queries, stored procedures |
| **Synapse Data Science** | Data Scientist | ML models, experiments, MLflow tracking, predict functions |
| **Synapse Real-Time Analytics** | Data Engineer / Analyst | KQL databases, Eventstreams, real-time dashboards |
| **Power BI** | Business Analyst | Reports, dashboards, semantic models, paginated reports |

### Architecture Diagram

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
│  │                    (Delta Parquet format)                      │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                                                                     │
│  ┌───────────────────┐           ┌───────────────────────────────┐  │
│  │ Synapse Real-Time │           │          Power BI             │  │
│  │ Analytics         │           │  (Reports, Semantic Models)   │  │
│  └───────────────────┘           └───────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
```

> **Exam Tip:** All Fabric experiences share a **single capacity** and **OneLake storage**. You do not provision separate compute clusters — Fabric manages this for you.

---

## 2. OneLake

OneLake is the **single, unified data lake** for the entire Fabric tenant — think of it as the "OneDrive for data."

### Hierarchy

```
Tenant (one per organization)
 └── Workspace (security / collaboration boundary)
      ├── Lakehouse Item
      │    ├── Tables/   (managed Delta tables)
      │    └── Files/    (unmanaged files — CSV, Parquet, JSON, images …)
      ├── Warehouse Item
      ├── KQL Database Item
      ├── Semantic Model
      └── …other items
```

### Key Properties

| Property | Detail |
|---|---|
| **Format** | Delta Parquet (open-source, versioned, ACID) |
| **Protocol** | ABFS — `abfss://<workspace>@onelake.dfs.fabric.microsoft.com/<item>/…` |
| **Access** | OneLake File Explorer (Windows), REST APIs, Azure Storage SDKs |
| **Governance** | Microsoft Purview integration, data lineage, sensitivity labels |
| **One copy** | Data is stored once; every engine reads the same copy (no duplication) |

### Shortcuts

Shortcuts let you reference data **without copying it** into OneLake.

| Shortcut Type | Target | Notes |
|---|---|---|
| **Internal (OneLake)** | Another Lakehouse/Warehouse in the same or different workspace | Same tenant only |
| **ADLS Gen2** | Azure Data Lake Storage Gen2 | Requires service principal or account key |
| **Amazon S3** | AWS S3 bucket | Requires access key / secret |
| **Google Cloud Storage** | GCS bucket | Requires HMAC key |
| **Dataverse** | Dataverse tables (Dynamics 365 / Power Platform) | Read-only virtual tables |

> **Exam Tip:** Shortcuts appear as folders in the Lakehouse but data remains at the source. Write operations to external shortcuts are **not** supported — they are **read-only**.

---

## 3. Lakehouse

The Lakehouse combines a **data lake** (Files) with a **data warehouse** (Tables) in a single item.

### Two Sections

```
Lakehouse
 ├── Tables/          ← Managed Delta tables (appear in SQL analytics endpoint)
 │    ├── sales
 │    ├── customers
 │    └── products
 └── Files/           ← Unmanaged area (raw files, staging, images, etc.)
      ├── raw/
      ├── staging/
      └── landing/
```

### Automatic Endpoints

| Endpoint | What It Does |
|---|---|
| **SQL analytics endpoint** | Auto-generated read-only T-SQL endpoint over Delta tables. Supports views, functions, cross-Lakehouse queries. No DML (INSERT/UPDATE/DELETE). |
| **Default semantic model** | Auto-generated Power BI dataset reflecting the tables. Enables quick report building. |

### Loading Data into Lakehouse Tables

| Method | When to Use |
|---|---|
| **Notebooks (Spark)** | Complex transformations, PySpark/Spark SQL, large-scale ETL |
| **Dataflows Gen2** | Low-code / Power Query transformations |
| **Data Pipelines (Copy activity)** | Simple data movement from 100+ connectors |
| **Shortcut** | Virtual access — no copy needed |
| **File upload** | Manual one-time loads, small files |
| **LOAD DATA** | Via the SQL analytics endpoint (limited) |

> **Exam Tip:** The SQL analytics endpoint is **read-only**. To write data to Lakehouse tables, use Spark (Notebooks), Dataflows Gen2, or Pipelines — not T-SQL.

---

## 4. Warehouse

The Synapse Data Warehouse provides a **full T-SQL experience** with DML support.

### Key Capabilities

| Feature | Detail |
|---|---|
| **DDL** | CREATE TABLE, ALTER, DROP, schemas, views, stored procedures, functions |
| **DML** | INSERT, UPDATE, DELETE, MERGE |
| **COPY INTO** | High-performance bulk ingestion from ADLS Gen2 / OneLake (Parquet, CSV) |
| **Cross-database queries** | Query across Warehouses and Lakehouse SQL endpoints in the same workspace |
| **Security** | Column-level, row-level, object-level, dynamic data masking |
| **Transactions** | Full ACID compliance |
| **Storage format** | Delta Parquet (same as Lakehouse — stored in OneLake) |

### COPY INTO Syntax

```sql
COPY INTO dbo.sales
FROM 'https://onelake.dfs.fabric.microsoft.com/<workspace>/<item>/Files/raw/*.parquet'
WITH (
    FILE_TYPE = 'PARQUET'
);
```

### Cross-Database Query Example

```sql
-- Query a Lakehouse SQL analytics endpoint from a Warehouse
SELECT w.OrderID, l.CustomerName
FROM dbo.Orders AS w
JOIN MyLakehouse.dbo.Customers AS l
    ON w.CustomerID = l.CustomerID;
```

> **Exam Tip:** The Warehouse supports **read AND write** T-SQL. The Lakehouse SQL analytics endpoint is **read-only**. If the question requires INSERT/UPDATE/DELETE via T-SQL, the answer is Warehouse.

---

## 5. Dataflows Gen2

Dataflows Gen2 are **Power Query Online** transformations that run in the cloud and land data into Fabric destinations.

### Key Features

| Feature | Detail |
|---|---|
| **Engine** | Power Query Online (M language) |
| **Code style** | Low-code / no-code with formula bar |
| **300+ connectors** | Databases, files, SaaS apps, web APIs |
| **Staging** | Uses OneLake staging (auto-enabled) |
| **Destinations** | Lakehouse table, Warehouse table, KQL Database, Azure SQL |
| **Refresh** | On-demand or scheduled |
| **Data gateway** | On-premises data gateway for on-prem sources |

### When to Use Dataflows Gen2

- Non-technical users who prefer a **visual / low-code** experience
- **Light transformations**: filter, merge, pivot, type changes
- Landing data from **SaaS connectors** or on-prem sources
- You need a **reusable** query that other items can reference

> **Exam Tip:** Dataflows Gen2 replace Power BI dataflows (Gen1) and add **output destinations** (you can write to Lakehouse/Warehouse tables directly). Gen1 dataflows only output to the Power BI storage.

---

## 6. Data Pipelines

Data Pipelines are the **orchestration** layer in Fabric — similar to Azure Data Factory pipelines.

### Core Activities

| Activity | Purpose |
|---|---|
| **Copy Data** | Move data between 100+ sources and sinks |
| **Dataflow** | Run a Dataflows Gen2 transformation |
| **Notebook** | Execute a Spark notebook |
| **Stored Procedure** | Run a Warehouse stored procedure |
| **ForEach** | Loop over a collection of items |
| **If Condition** | Branch logic based on an expression |
| **Until** | Repeat activities until a condition is true |
| **Wait** | Pause execution for a defined duration |
| **Set Variable** | Assign a value to a pipeline variable |
| **Lookup** | Return a small result set from a source |
| **Web** | Call an HTTP endpoint |
| **Script** | Run a SQL script against a Warehouse |

### Pipeline Concepts

| Concept | Detail |
|---|---|
| **Parameters** | Inputs that can be set at runtime |
| **Variables** | Internal state within a pipeline run |
| **Triggers** | Schedule-based or event-based execution |
| **Scheduling** | Cron-like recurring schedules via the Schedule tab |
| **Monitoring** | Pipeline run history in the Monitoring Hub |
| **Dependencies** | On Success, On Failure, On Completion, On Skipped |

### Orchestration Pattern

```
Pipeline: Daily Ingestion
 │
 ├─► Copy Activity (land raw files)
 │        │ On Success
 │        ▼
 ├─► Notebook Activity (transform Bronze → Silver)
 │        │ On Success
 │        ▼
 ├─► Dataflow Activity (Silver → Gold dimensions)
 │        │ On Success
 │        ▼
 └─► Stored Procedure Activity (refresh aggregations)
```

> **Exam Tip:** Pipelines **orchestrate** work but do not transform data themselves. Use Copy for movement, Notebooks/Dataflows/Stored Procedures for transformation.

---

## 7. Notebooks

Notebooks are the primary tool for **Spark-based** data engineering and data science in Fabric.

### Supported Languages

| Language | Magic Command | Use Case |
|---|---|---|
| **PySpark** | `%%pyspark` (default) | DataFrames, ETL, ML |
| **Spark SQL** | `%%sql` | SQL queries on Spark tables |
| **Scala** | `%%spark` | JVM-based Spark operations |
| **R** | `%%sparkr` | Statistical analysis |

### Key Features

| Feature | Detail |
|---|---|
| **Lakehouse integration** | Attach a default Lakehouse; tables appear in `spark.sql("SELECT * FROM …")` |
| **Inline installation** | `%pip install <library>` or `%conda install <library>` for session-scoped packages |
| **Workspace libraries** | Upload `.whl`, `.jar`, or `.tar.gz` for shared use |
| **Environment items** | Reusable environment definitions (libraries, Spark config) attached to notebooks |
| **V-Order optimization** | Fabric writes V-Order optimized Parquet for faster reads |
| **High concurrency mode** | Share a single Spark session across multiple notebooks |
| **Parameterization** | Toggle a cell as a "parameter cell" to inject values from Pipelines |

### Common PySpark Patterns

```python
# Read from Lakehouse Files
df = spark.read.format("csv") \
    .option("header", "true") \
    .load("Files/raw/sales.csv")

# Write to Lakehouse Tables (managed Delta)
df.write.format("delta") \
    .mode("overwrite") \
    .saveAsTable("sales_clean")

# Incremental merge using Delta
from delta.tables import DeltaTable
target = DeltaTable.forName(spark, "customers")
target.alias("t").merge(
    source_df.alias("s"),
    "t.CustomerID = s.CustomerID"
).whenMatchedUpdateAll() \
 .whenNotMatchedInsertAll() \
 .execute()
```

> **Exam Tip:** Notebooks use **Apache Spark** under the hood. Fabric auto-provisions Spark pools — you do not manually create or configure cluster sizes.

---

## 8. Semantic Models

Semantic models (formerly "datasets") define the business logic layer consumed by Power BI reports.

### Storage Modes

| Mode | Data Location | Refresh Needed? | Query Speed | Best For |
|---|---|---|---|---|
| **Import** | In-memory (VertiPaq) | Yes — scheduled or on-demand | ⚡ Fastest | Small-to-medium datasets, complex DAX |
| **DirectQuery** | Source system (live) | No | 🐢 Depends on source | Real-time data, very large datasets |
| **Direct Lake** | OneLake (Delta Parquet) | No (auto-sync) | ⚡ Near-import speed | **Fabric-native** — best of both worlds |
| **Dual** | Both Import + DirectQuery | Partial | Mixed | Dimension tables in composite models |

### Direct Lake Deep Dive

```
Power BI Report
      │
      ▼
Semantic Model (Direct Lake mode)
      │  reads Delta Parquet directly
      ▼
OneLake
 └── Lakehouse / Warehouse Delta tables
```

- **No data copy** — reads directly from Delta Parquet files in OneLake
- **No scheduled refresh** — automatically detects new Delta log versions
- **Fallback** — if the data exceeds memory or unsupported DAX is used, it may fall back to DirectQuery mode
- **Framing** — a snapshot of the Delta log is "framed" to ensure consistent reads

| Direct Lake Guardrail | F2 | F64 | F128+ |
|---|---|---|---|
| Max tables per model | 500 | 500 | 500 |
| Max rows per table | 300 M | 1.5 B | 3 B |
| Max model size on disk | 10 GB | 40 GB | 80 GB+ |

> **Exam Tip:** Direct Lake is the **recommended** storage mode for Fabric-native solutions. It avoids import refresh overhead AND avoids DirectQuery source latency. Know the fallback behavior — if Direct Lake cannot serve a query, it **falls back to DirectQuery** against the SQL endpoint.

---

## 9. Eventstreams

Eventstreams provide **real-time data ingestion** in Fabric.

### Sources (Inputs)

| Source | Description |
|---|---|
| **Azure Event Hubs** | Managed event streaming service |
| **Azure IoT Hub** | IoT device telemetry |
| **Custom App** | SDK-based custom producers |
| **Sample data** | Built-in sample streams for testing |
| **Azure Blob Storage** | Event-based (new blob triggers) |

### Destinations (Outputs)

| Destination | Description |
|---|---|
| **Lakehouse** | Land events as Delta tables |
| **KQL Database** | Real-time analytics with KQL |
| **Custom App** | SDK-based consumers |
| **Reflex** | Trigger alerts and actions on patterns |
| **Derived stream** | Intermediate stream for fan-out scenarios |

### Processing

| Feature | Detail |
|---|---|
| **No-code editor** | Visual drag-and-drop event processing |
| **Transformations** | Filter, Manage Fields, Group By (windowed aggregations) |
| **Windowing** | Tumbling, Hopping, Session, Snapshot windows |
| **Serialization** | JSON, Avro, CSV |

> **Exam Tip:** Eventstreams + KQL Database is the go-to pattern for **real-time analytics** in Fabric. If a question involves streaming or near-real-time data, think Eventstreams.

---

## 10. Deployment Pipelines

Deployment pipelines manage the **ALM (Application Lifecycle Management)** of Fabric items across environments.

### Pipeline Stages

```
┌────────────┐       ┌────────────┐       ┌────────────┐
│    Dev      │──────▶│    Test     │──────▶│    Prod     │
│ (workspace) │       │ (workspace) │       │ (workspace) │
└────────────┘       └────────────┘       └────────────┘
     Author            Validate             Serve users
     & build           & QA                 & monitor
```

### Key Concepts

| Concept | Detail |
|---|---|
| **Stages** | Up to 10 custom stages (commonly Dev → Test → Prod) |
| **Deployment rules** | Override connection strings, parameters, or data sources per stage |
| **Selective deploy** | Deploy individual items or all items at once |
| **Supported items** | Semantic models, reports, dataflows, Lakehouses, Warehouses, notebooks, and more |
| **Permissions** | Workspace member or admin role required |
| **Comparison** | Side-by-side diff of item changes between stages |

### Deployment Rules

| Rule Type | Purpose | Example |
|---|---|---|
| **Data source rules** | Change connection strings between stages | Dev DB → Prod DB |
| **Parameter rules** | Override semantic model parameters | `Environment = "Production"` |

> **Exam Tip:** Deployment pipelines operate at the **workspace level** — each stage maps to a separate Fabric workspace. Items are copied (not linked) between stages.

---

## 11. Git Integration

Fabric supports **source control** integration for version-controlled development.

### Supported Providers

| Provider | Status |
|---|---|
| **Azure DevOps (Azure Repos)** | Fully supported |
| **GitHub** | Supported |

### How It Works

```
Fabric Workspace  ◄───sync───►  Git Repository (branch)
      │                                │
      │  commit / update               │  pull requests
      ▼                                ▼
 Live items                      Code review & merge
```

### Key Concepts

| Concept | Detail |
|---|---|
| **Connection** | One workspace connects to one branch of one repo |
| **Sync direction** | Bi-directional — commit workspace → repo, update repo → workspace |
| **Item representation** | Each Fabric item is serialized as JSON / TMDL files in the repo |
| **Conflict resolution** | Manual — workspace vs. repo changes must be resolved |
| **Supported items** | Semantic models, reports, notebooks, Spark job definitions, and more |
| **Branching strategy** | Typically feature branches → main → deployment pipeline |

### Recommended Branching Strategy

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

> **Exam Tip:** Git integration and deployment pipelines are **complementary**. Git manages **source control and collaboration**. Deployment pipelines manage **promotion across environments**.

---

## 12. Capacity and Licensing

Fabric runs on **capacity-based licensing**. All workloads share a pool of **Capacity Units (CUs)**.

### SKUs and CU Allocation

| SKU | Capacity Units | Equivalent | Max Memory per Query | Power BI Equivalent |
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

### Key Licensing Concepts

| Concept | Detail |
|---|---|
| **Capacity Units (CUs)** | Shared compute pool; all Fabric workloads (Spark, SQL, Power BI, Dataflows) draw from the same CUs |
| **Bursting** | Fabric allows temporary bursting above the CU limit — excess usage is smoothed over time |
| **Throttling** | If sustained usage exceeds capacity, background jobs are throttled, then interactive jobs |
| **Pause / Resume** | F SKUs can be paused to save cost (no compute charges while paused; storage charges continue) |
| **Autoscale** | Not the same as Power BI autoscale — Fabric uses a smoothing/throttling model |
| **Trial capacity** | 60-day Fabric trial with F64-equivalent capacity for evaluation |
| **Per-user licensing** | Free, Pro, or PPU — needed for Power BI content sharing; Fabric items require capacity |

### Capacity Management

```
Capacity (e.g., F64 = 64 CU)
 ├── Workspace A  ──►  Lakehouse ETL jobs (Spark CU usage)
 ├── Workspace B  ──►  Warehouse queries (SQL CU usage)
 └── Workspace C  ──►  Power BI reports (VertiPaq CU usage)
         │
         └── All share the same 64 CU pool
```

> **Exam Tip:** Know that **F64** is the minimum SKU equivalent to Power BI Premium P1. For the exam, understand when to scale up vs. optimize workloads to reduce CU consumption.

---

## 13. Comparison Tables

### Lakehouse vs. Warehouse

| Feature | Lakehouse | Warehouse |
|---|---|---|
| **Primary interface** | Spark (notebooks) + SQL analytics endpoint | T-SQL |
| **Write via T-SQL** | ❌ No (SQL endpoint is read-only) | ✅ Yes (INSERT, UPDATE, DELETE, MERGE) |
| **Write via Spark** | ✅ Yes | ❌ No |
| **Storage format** | Delta Parquet (OneLake) | Delta Parquet (OneLake) |
| **Schema enforcement** | Schema-on-read (flexible) | Schema-on-write (strict) |
| **Unstructured data** | ✅ Files/ section supports any format | ❌ Structured data only |
| **Cross-database queries** | ✅ Via SQL analytics endpoint | ✅ Native T-SQL |
| **Stored procedures** | ❌ Not in SQL endpoint | ✅ Supported |
| **Row/column-level security** | ❌ Limited | ✅ Full support |
| **Best for** | Data engineers, data scientists, flexible ELT | SQL analysts, BI developers, strict governance |

### Dataflow Gen2 vs. Data Pipeline

| Feature | Dataflow Gen2 | Data Pipeline |
|---|---|---|
| **Primary purpose** | Data transformation | Orchestration and data movement |
| **Interface** | Power Query Online (visual / M code) | Canvas designer (activities) |
| **Data transformation** | ✅ Rich — merge, pivot, unpivot, types | ❌ Minimal (use activities for transforms) |
| **Orchestration** | ❌ Single-step execution | ✅ Multi-step with dependencies, loops, conditions |
| **Connectors** | 300+ Power Query connectors | 100+ Copy activity connectors |
| **Scheduling** | ✅ Standalone schedule | ✅ Standalone or triggered by events |
| **Can call the other** | ❌ No | ✅ Yes — Dataflow activity inside a Pipeline |
| **Best for** | Self-service data prep, light ETL | Complex workflows, orchestration, multi-activity jobs |

### Import vs. DirectQuery vs. Direct Lake

| Feature | Import | DirectQuery | Direct Lake |
|---|---|---|---|
| **Data stored in** | VertiPaq (in-memory) | Source system | OneLake (Delta Parquet) |
| **Refresh required** | ✅ Yes (scheduled) | ❌ No (live query) | ❌ No (auto-sync on Delta log change) |
| **Query performance** | ⚡ Fastest | 🐢 Source-dependent | ⚡ Near-import speed |
| **Data freshness** | Stale between refreshes | Always current | Near-real-time (on Delta commit) |
| **Data size limit** | Model memory limit | Source capacity | OneLake guardrails (rows/table, model size) |
| **Works outside Fabric** | ✅ Any source | ✅ Any supported source | ❌ Fabric OneLake only |
| **Fallback behavior** | N/A | N/A | Falls back to DirectQuery via SQL endpoint |
| **Best for** | Complex DAX, small-medium data | Real-time needs, huge data | Fabric-native analytics |

### Quick Decision Flowchart

```
Is data in OneLake (Lakehouse / Warehouse)?
 ├── YES → Use Direct Lake mode
 │          └── Does it exceed guardrails or need unsupported DAX?
 │               ├── YES → Import mode (or optimize data)
 │               └── NO  → ✅ Direct Lake
 └── NO  → Is real-time freshness critical?
             ├── YES → DirectQuery
             └── NO  → Import mode
```

---

## Exam Tips Summary

| # | Tip |
|---|---|
| 1 | **OneLake = one lake per tenant.** All data in Delta Parquet. Shortcuts avoid copying. |
| 2 | **Lakehouse SQL endpoint is read-only.** Use Spark, Dataflows, or Pipelines to write. |
| 3 | **Warehouse supports full T-SQL DML.** Choose it when INSERT/UPDATE/DELETE via SQL is required. |
| 4 | **Direct Lake is the preferred storage mode** for Fabric-native semantic models. |
| 5 | **Direct Lake falls back to DirectQuery** if it cannot serve the query (memory limits, unsupported DAX). |
| 6 | **Pipelines orchestrate; Dataflows transform.** Pipelines can call Dataflows, not vice versa. |
| 7 | **Notebooks run on Spark.** Fabric auto-provisions pools — no manual cluster management. |
| 8 | **Deployment pipelines promote items** across workspaces (Dev → Test → Prod). |
| 9 | **Git integration and deployment pipelines are complementary**, not alternatives. |
| 10 | **F64 ≈ P1.** All workloads share CUs. Pause capacity to save cost (storage still charged). |
| 11 | **Eventstreams + KQL Database** is the real-time analytics pattern in Fabric. |
| 12 | **Shortcuts are read-only** for external sources (ADLS Gen2, S3, GCS). |
| 13 | **COPY INTO** is the fastest way to bulk-load data into a Warehouse from files. |

---

> **Last updated for DP-600 exam prep.** Always verify against the latest [Microsoft Learn documentation](https://learn.microsoft.com/en-us/fabric/).
