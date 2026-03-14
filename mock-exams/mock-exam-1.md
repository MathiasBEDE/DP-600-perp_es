# 🧪 Mock Exam 1 — DP-600: Implementing Analytics Solutions Using Microsoft Fabric

> **50 Questions | Recommended Time: 100 minutes**
> Covers all four exam domains with realistic question distribution.
> Scroll down for answers with detailed explanations.

---

## Instructions

1. Set a timer for **100 minutes** to simulate exam conditions.
2. Answer all 50 questions before reviewing any answers.
3. Use the expandable answer sections only after completing the full exam.
4. Record your score at the end: \_\_\_ / 50 (\_\_\_%)

---

## Questions

### Question 1

Your company is rolling out Microsoft Fabric across several business units. The analytics team wants every Fabric experience — Data Engineering, Data Warehousing, Data Science, and Power BI — to read and write to a single shared storage layer without duplicating data. Which Fabric component fulfills this requirement?

- A) Azure Synapse Analytics workspace
- B) OneLake
- C) Power BI Premium per-user license
- D) Azure Data Lake Storage Gen2 (standalone)

<details>
<summary>Show Answer</summary>

**Correct Answer: B) OneLake**

**Explanation:** OneLake is the unified, built-in data lake for Microsoft Fabric. It provides a single copy of data that every Fabric workload can access natively, eliminating the need to copy data between services. OneLake is built on ADLS Gen2 technology but is fully managed within Fabric.

**Why other options are incorrect:**
- A) Azure Synapse Analytics is a separate service that predates Fabric's unified approach and is not the built-in storage layer.
- C) Power BI Premium per-user is a licensing SKU, not a storage component.
- D) Standalone ADLS Gen2 is an Azure storage service but is not the integrated single-storage layer within Fabric.

</details>

---

### Question 2

A Fabric tenant administrator needs to prevent users outside the IT department from creating new workspaces. Where should this restriction be configured?

- A) Microsoft Entra ID Conditional Access policies
- B) Fabric Admin Portal → Tenant settings
- C) Individual workspace settings → Access tab
- D) Microsoft Purview compliance portal

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Fabric Admin Portal → Tenant settings**

**Explanation:** The Fabric Admin Portal contains tenant-level settings that control who can create workspaces. Administrators can restrict workspace creation to specific security groups, effectively limiting it to IT staff. This is a centralized governance control applied across the entire tenant.

**Why other options are incorrect:**
- A) Conditional Access policies control sign-in conditions (location, device compliance) but do not manage workspace creation permissions.
- C) Individual workspace settings manage access to an existing workspace but cannot control who creates new workspaces.
- D) Microsoft Purview manages data governance and compliance, not Fabric workspace creation policies.

</details>

---

### Question 3

An organization needs to choose between Fabric F64 and F128 SKUs. The primary deciding factors are the number of concurrent users and the volume of data processing. Which Fabric concept determines the compute power available to workspaces?

- A) Workspace role assignments
- B) Capacity
- C) OneLake storage quota
- D) Gateway cluster size

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Capacity**

**Explanation:** In Microsoft Fabric, capacity is the compute resource pool assigned to workspaces. The SKU (F64, F128, etc.) determines the amount of compute power available. All workloads running in workspaces assigned to a capacity share those compute resources, so the SKU choice directly affects concurrency and processing throughput.

**Why other options are incorrect:**
- A) Workspace roles control user permissions (Admin, Member, Contributor, Viewer), not compute power.
- C) OneLake storage is billed separately and is not the factor that determines processing throughput.
- D) Gateway cluster size affects on-premises data connectivity, not the overall Fabric compute capacity.

</details>

---

### Question 4

A data governance lead wants to automatically classify semantic models and reports that contain customer PII. The classification must persist when items are exported or shared. Which feature should be implemented?

- A) Microsoft Purview sensitivity labels
- B) Row-level security (RLS)
- C) Workspace-level access controls
- D) Azure Policy assignments

<details>
<summary>Show Answer</summary>

**Correct Answer: A) Microsoft Purview sensitivity labels**

**Explanation:** Microsoft Purview sensitivity labels classify and protect content based on its sensitivity level. When applied to Fabric items, these labels travel with the content — persisting through exports, shares, and downloads — ensuring that PII-containing items remain classified regardless of where they are consumed.

**Why other options are incorrect:**
- B) RLS restricts which data rows a user can see, but it does not classify or label content for governance purposes.
- C) Workspace access controls who can view or edit items but do not apply persistent classification labels.
- D) Azure Policy enforces resource configuration compliance at the Azure subscription level, not content-level classification inside Fabric.

</details>

---

### Question 5

After deploying several Fabric solutions, the analytics team discovers that a change to a Lakehouse table could break multiple downstream reports. Which Fabric feature helps identify these dependencies before making changes?

- A) Dataflow Gen2 refresh history
- B) Data lineage and impact analysis
- C) Monitoring hub CPU metrics
- D) Power BI usage metrics report

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Data lineage and impact analysis**

**Explanation:** Fabric's lineage view shows the end-to-end flow of data from source to destination, and impact analysis reveals which downstream items (datasets, reports, dashboards) depend on a given artifact. This allows teams to assess the blast radius of changes before implementing them.

**Why other options are incorrect:**
- A) Refresh history shows past success/failure of dataflow runs but does not map cross-item dependencies.
- C) CPU metrics show compute utilization, not data dependencies between items.
- D) Usage metrics show report consumption patterns (views, users) but not the structural dependency chain.

</details>

---

### Question 6

Your organization wants to audit who accessed specific reports and datasets over the past 30 days for compliance purposes. Which tool provides this information?

- A) Power BI activity log via Microsoft 365 audit logs
- B) OneLake file access logs
- C) Fabric capacity metrics app — compute only
- D) Microsoft Entra ID sign-in logs

<details>
<summary>Show Answer</summary>

**Correct Answer: A) Power BI activity log via Microsoft 365 audit logs**

**Explanation:** The Power BI / Fabric activity log, accessible through the Microsoft 365 compliance center (unified audit log), records user actions including report views, dataset refreshes, and export operations. This is the primary tool for auditing user access to specific Fabric items over a defined period.

**Why other options are incorrect:**
- B) OneLake does not expose granular per-report or per-dataset user access logs directly.
- C) The capacity metrics app tracks compute utilization (CU seconds, throttling) but not individual user actions on specific items.
- D) Entra ID sign-in logs show authentication events (who signed in, from where) but not what specific reports or datasets were accessed.

</details>

---

### Question 7

A Fabric administrator notices that a workspace is consuming excessive compute resources during overnight refresh jobs. Which action can the administrator take to limit the impact on other workspaces sharing the same capacity?

- A) Move the workspace to a dedicated capacity
- B) Delete the workspace
- C) Remove all viewer-role users from the workspace
- D) Change the workspace storage format to CSV

<details>
<summary>Show Answer</summary>

**Correct Answer: A) Move the workspace to a dedicated capacity**

**Explanation:** Moving a resource-intensive workspace to its own dedicated capacity isolates its compute consumption, preventing it from affecting other workspaces. This is the standard approach for managing noisy-neighbor situations in Fabric capacity management.

**Why other options are incorrect:**
- B) Deleting the workspace removes all content and is not a resource management strategy.
- C) Removing viewers does not reduce compute consumption — viewers do not consume capacity during refresh operations.
- D) Changing storage format to CSV is not a valid Fabric operation and would not address compute resource consumption.

</details>

---

### Question 8

A data engineer needs to extract data from a REST API, apply column-level transformations using a visual interface, and load the results into a Lakehouse table. Which Fabric tool is best suited for this scenario?

- A) Data Pipeline with Copy Activity only
- B) Dataflow Gen2
- C) Spark notebook
- D) KQL Queryset

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Dataflow Gen2**

**Explanation:** Dataflow Gen2 provides a Power Query-based visual interface that supports connecting to REST APIs, applying column-level transformations (rename, filter, merge, data type changes), and writing the output directly to a Lakehouse table. It is purpose-built for visual, low-code data transformation scenarios.

**Why other options are incorrect:**
- A) Copy Activity moves data from source to destination but has limited transformation capabilities — it cannot perform complex column-level transformations visually.
- C) Spark notebooks can achieve this but require coding (PySpark/Scala), which does not meet the "visual interface" requirement.
- D) KQL Querysets are designed for querying real-time analytics data, not for ETL from REST APIs into Lakehouse tables.

</details>

---

### Question 9

A data pipeline needs to copy 50 GB of Parquet files from an external Azure Blob Storage account into a Fabric Lakehouse's Files section. The data should land as-is with no transformations. Which activity should be used?

- A) Dataflow Gen2 activity
- B) Copy Activity
- C) Spark notebook activity
- D) Stored Procedure activity

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Copy Activity**

**Explanation:** Copy Activity in Data Factory is optimized for high-throughput, no-transformation data movement between supported sources and destinations. For copying 50 GB of Parquet files as-is into a Lakehouse Files section, Copy Activity provides the most efficient and straightforward approach.

**Why other options are incorrect:**
- A) Dataflow Gen2 adds transformation overhead that is unnecessary when no transformations are required.
- C) A Spark notebook can copy files but introduces development complexity and compute overhead beyond what is needed for a simple copy.
- D) Stored Procedure activity runs SQL stored procedures and cannot copy files from Blob Storage.

</details>

---

### Question 10

A data engineer must connect a Fabric Data Pipeline to an on-premises Oracle database. The corporate firewall blocks all inbound connections. What must be installed in the on-premises environment?

- A) A Fabric capacity node
- B) An on-premises data gateway
- C) The Azure Data Factory self-hosted integration runtime (standalone)
- D) A VPN client on each Oracle server

<details>
<summary>Show Answer</summary>

**Correct Answer: B) An on-premises data gateway**

**Explanation:** The on-premises data gateway establishes a secure outbound connection from the corporate network to Microsoft Fabric, enabling data transfer without requiring inbound firewall rules. It acts as a bridge between on-premises data sources and cloud services.

**Why other options are incorrect:**
- A) Fabric capacity runs in the cloud and cannot be deployed on-premises to access local databases.
- C) While ADF uses a self-hosted integration runtime, Fabric pipelines use the on-premises data gateway for this purpose.
- D) A VPN client on each server is not required — the gateway handles connectivity centrally.

</details>

---

### Question 11

A team wants to make data from an external Amazon S3 bucket appear as a native folder in their Fabric Lakehouse without physically copying the data. Which feature should they use?

- A) Copy Activity with a scheduled trigger
- B) Dataflow Gen2 with incremental refresh
- C) OneLake shortcut
- D) Mirrored database

<details>
<summary>Show Answer</summary>

**Correct Answer: C) OneLake shortcut**

**Explanation:** OneLake shortcuts create a virtualized, zero-copy reference to external data sources including Amazon S3, ADLS Gen2, and other OneLake locations. The data is not moved; instead, a shortcut makes the external data appear as a native folder in the Lakehouse, enabling Fabric workloads to access it directly.

**Why other options are incorrect:**
- A) Copy Activity physically moves data, which contradicts the zero-copy requirement.
- B) Dataflow Gen2 would also physically copy and transform data rather than virtualizing access.
- D) Mirrored databases replicate relational database data into OneLake as Delta tables — they are not designed for S3 bucket virtualization.

</details>

---

### Question 12

A data engineer uploads CSV files to the Lakehouse **Files** section and needs them queryable via the SQL analytics endpoint. What must happen first?

- A) The files are automatically queryable once uploaded
- B) The CSV files must be loaded into a Lakehouse **table** (Delta format)
- C) A KQL database must be created to query the files
- D) The files must be converted to JSON format

<details>
<summary>Show Answer</summary>

**Correct Answer: B) The CSV files must be loaded into a Lakehouse table (Delta format)**

**Explanation:** The SQL analytics endpoint of a Lakehouse only exposes managed Delta tables for T-SQL querying. Files in the Files section are not automatically registered as tables. The data engineer must load the CSV data into a Lakehouse table (which stores it in Delta format) before it becomes queryable through the SQL analytics endpoint.

**Why other options are incorrect:**
- A) Files in the Files section are not automatically queryable via the SQL analytics endpoint — only managed tables are.
- C) KQL databases are for real-time analytics (Eventhouse/KQL Queryset), not for querying Lakehouse files.
- D) Converting to JSON does not make files queryable via the SQL analytics endpoint — Delta format is required.

</details>

---

### Question 13

Which statement about Delta Lake tables in a Fabric Lakehouse is correct?

- A) Delta tables store data in Avro format with a separate schema registry
- B) Delta tables store data as Parquet files with a JSON-based transaction log (_delta_log)
- C) Delta tables can only be read by Spark — the SQL analytics endpoint cannot access them
- D) Delta tables do not support ACID transactions

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Delta tables store data as Parquet files with a JSON-based transaction log (_delta_log)**

**Explanation:** Delta Lake is an open-source storage layer that stores data in Parquet format and maintains a JSON-based transaction log in the _delta_log directory. This transaction log enables ACID transactions, schema enforcement, time travel, and audit history — all core features of Delta tables in Fabric.

**Why other options are incorrect:**
- A) Delta tables use Parquet, not Avro, and the transaction log is stored in the _delta_log folder, not a separate schema registry.
- C) Delta tables in Fabric are accessible through both Spark and the SQL analytics endpoint (T-SQL).
- D) ACID transactions are a fundamental feature of Delta Lake — it is one of the key reasons Delta is used.

</details>

---

### Question 14

A data engineer is implementing a medallion architecture. Raw data arrives from multiple sources in different formats (CSV, JSON, Parquet). In which layer should this raw data be stored first, preserving the original format?

- A) Silver layer
- B) Gold layer
- C) Bronze layer
- D) Presentation layer

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Bronze layer**

**Explanation:** The Bronze layer (also called the raw or landing layer) is where raw data from source systems is ingested and stored with minimal or no transformation. The goal is to preserve the original data exactly as it arrived, serving as the system of record. Subsequent layers (Silver, Gold) then progressively cleanse and refine the data.

**Why other options are incorrect:**
- A) The Silver layer stores cleansed, validated, and deduplicated data — not raw source data.
- B) The Gold layer contains aggregated, business-ready data optimized for reporting and analytics.
- D) The Presentation layer is another name for the Gold/serving layer and is the final consumption point, not the raw landing zone.

</details>

---

### Question 15

In a medallion architecture, data moves from Silver to Gold. A data engineer needs to create an aggregated table that summarizes daily sales by region from a cleansed Silver table. Which approach is most appropriate in a Fabric Lakehouse?

- A) Create a view in the SQL analytics endpoint that aggregates on-the-fly
- B) Use a Spark notebook to read the Silver table, aggregate, and write a new Gold Delta table
- C) Export the Silver data to Excel, pivot manually, and re-upload
- D) Use a Dataflow Gen2 to send the aggregation to an external Azure SQL Database

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use a Spark notebook to read the Silver table, aggregate, and write a new Gold Delta table**

**Explanation:** In a medallion architecture, the Gold layer contains physically materialized, aggregated tables. Using a Spark notebook to read from Silver, perform aggregations (GROUP BY region, date), and write a new Gold Delta table is the standard and scalable approach in a Fabric Lakehouse.

**Why other options are incorrect:**
- A) A view provides on-the-fly computation but does not materialize the aggregation into a Gold table, which is the pattern for medallion architecture.
- C) Exporting to Excel is a manual, non-scalable process and is not appropriate for production data pipelines.
- D) Writing to an external Azure SQL Database moves data outside the Lakehouse, which defeats the purpose of maintaining Gold data within the medallion architecture.

</details>

---

### Question 16

A team needs to run complex T-SQL queries with full DML support (INSERT, UPDATE, DELETE) against structured data. They also need multi-table transactions. Which Fabric item should they use?

- A) Lakehouse SQL analytics endpoint
- B) Fabric Warehouse
- C) KQL Database
- D) Dataflow Gen2

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Fabric Warehouse**

**Explanation:** The Fabric Warehouse provides a full T-SQL experience with complete DML support (INSERT, UPDATE, DELETE, MERGE) and multi-statement transaction capabilities. It is the correct choice when teams need traditional data warehouse functionality including transactional data manipulation.

**Why other options are incorrect:**
- A) The Lakehouse SQL analytics endpoint provides read-only T-SQL access to Delta tables — it does not support INSERT, UPDATE, or DELETE operations.
- C) KQL Database uses Kusto Query Language for real-time analytics, not T-SQL with DML support.
- D) Dataflow Gen2 is a data integration tool for ETL, not a queryable data store supporting T-SQL DML.

</details>

---

### Question 17

A data engineer needs to bulk-load a large Parquet file from an Azure storage account into a Fabric Warehouse table. Which T-SQL statement is specifically designed for this purpose?

- A) `INSERT INTO ... SELECT * FROM OPENROWSET()`
- B) `COPY INTO`
- C) `BULK INSERT`
- D) `bcp` utility

<details>
<summary>Show Answer</summary>

**Correct Answer: B) `COPY INTO`**

**Explanation:** The `COPY INTO` statement is the recommended and most efficient method for bulk-loading data from external storage into a Fabric Warehouse table. It supports Parquet, CSV, and other formats, and is optimized for high-throughput data ingestion in the Fabric Warehouse environment.

**Why other options are incorrect:**
- A) OPENROWSET can read external files but is not the optimized bulk-loading statement for Fabric Warehouse.
- C) BULK INSERT is a SQL Server feature that is not supported in Fabric Warehouse.
- D) bcp is a command-line utility for SQL Server and is not applicable to Fabric Warehouse.

</details>

---

### Question 18

A data engineer wants to create a new Warehouse table from the result of a complex SELECT query that joins and aggregates several existing tables. Which T-SQL approach should be used?

- A) `CREATE TABLE ... AS SELECT (CTAS)`
- B) `SELECT INTO` with a temporary table
- C) `INSERT INTO` an existing empty table
- D) `CREATE VIEW` with a materialized hint

<details>
<summary>Show Answer</summary>

**Correct Answer: A) `CREATE TABLE ... AS SELECT (CTAS)`**

**Explanation:** CTAS (CREATE TABLE AS SELECT) is the recommended approach in Fabric Warehouse for creating a new table from a query result. It creates the table and populates it in a single, optimized operation that takes advantage of distributed processing.

**Why other options are incorrect:**
- B) `SELECT INTO` is not supported in Fabric Warehouse.
- C) `INSERT INTO` requires the table to already exist and involves two separate operations (CREATE TABLE then INSERT), which is less efficient than CTAS.
- D) `CREATE VIEW` with a materialized hint does not create a physical table — Fabric Warehouse does not support materialized views in this manner.

</details>

---

### Question 19

A data analyst needs to query a Lakehouse Delta table and a Warehouse table together in a single T-SQL query within Microsoft Fabric. Which feature enables this?

- A) Linked server connections
- B) Cross-database queries
- C) External table federation
- D) OneLake data replication

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Cross-database queries**

**Explanation:** Fabric supports cross-database queries that allow T-SQL queries to reference tables across different Fabric Warehouses and Lakehouse SQL analytics endpoints within the same workspace using three-part naming (database.schema.table). This enables joining data across these items without physically moving data.

**Why other options are incorrect:**
- A) Linked servers are a SQL Server feature not available in Fabric.
- C) External table federation is not the Fabric term for this capability — cross-database querying is the native feature.
- D) Data replication would physically copy data, which is unnecessary when cross-database queries can access it directly.

</details>

---

### Question 20

A data engineer schedules a Spark notebook to run every morning at 6 AM to process new data from the Bronze layer to the Silver layer. Which Fabric component should be used to schedule and orchestrate this notebook?

- A) Power Automate flow
- B) Data Pipeline with a Notebook activity
- C) Windows Task Scheduler
- D) Azure Logic Apps

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Data Pipeline with a Notebook activity**

**Explanation:** Fabric Data Pipelines support Notebook activities that can execute Spark notebooks on a schedule or as part of an orchestration workflow. This is the native and recommended approach for scheduling and orchestrating notebook execution within Fabric.

**Why other options are incorrect:**
- A) Power Automate is designed for business process automation, not for orchestrating Fabric Spark notebooks directly.
- C) Windows Task Scheduler is an on-premises tool and cannot natively trigger Fabric notebook runs.
- D) Azure Logic Apps could theoretically trigger Fabric APIs, but Data Pipelines provide the native, integrated orchestration mechanism within Fabric.

</details>

---

### Question 21

In a PySpark notebook running in a Fabric Lakehouse, a data engineer needs to remove duplicate rows from a DataFrame based on a composite key of `customer_id` and `order_date`, keeping only the latest record by `updated_at`. Which PySpark approach is correct?

- A) `df.distinct()`
- B) `df.dropDuplicates(["customer_id", "order_date"])` after sorting by `updated_at` descending
- C) Use a window function with `ROW_NUMBER()` partitioned by the composite key, ordered by `updated_at` DESC, and filter for row number = 1
- D) `df.groupBy("customer_id", "order_date").count()`

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Use a window function with `ROW_NUMBER()` partitioned by the composite key, ordered by `updated_at` DESC, and filter for row number = 1**

**Explanation:** A window function with ROW_NUMBER() partitioned by the composite key and ordered by `updated_at` DESC deterministically identifies the latest record for each key combination. Filtering for row_number = 1 reliably returns the most recent record. This is the most precise and reliable method.

**Why other options are incorrect:**
- A) `distinct()` removes exact duplicate rows but does not handle choosing the latest record among rows that differ in the `updated_at` column.
- B) `dropDuplicates` does not guarantee which duplicate is kept — PySpark does not honor a preceding sort for deduplication purposes.
- D) `groupBy().count()` counts rows per group but does not return the actual data or the latest record.

</details>

---

### Question 22

A data engineer is optimizing a large Delta table in a Fabric Lakehouse for read performance. Fabric applies a special columnar sorting optimization by default when writing data. What is this optimization called?

- A) Z-order
- B) V-Order
- C) Bloom filter
- D) Bucketing

<details>
<summary>Show Answer</summary>

**Correct Answer: B) V-Order**

**Explanation:** V-Order is a write-time optimization in Microsoft Fabric that applies special sorting and encoding to Parquet files, achieving high compression rates and significantly faster read performance. It is applied by default when writing data to Lakehouse Delta tables and is designed to accelerate queries across all Fabric compute engines.

**Why other options are incorrect:**
- A) Z-order is a multi-dimensional clustering optimization that must be explicitly applied — it is not the default optimization in Fabric.
- C) Bloom filters are probabilistic data structures for optimizing point lookups, not a default columnar sorting optimization.
- D) Bucketing is a partitioning technique that distributes data into fixed buckets, not the default Fabric write optimization.

</details>

---

### Question 23

A data engineer has a Data Pipeline that processes data from three independent sources in parallel before loading results into a final table. Which pipeline design pattern achieves this?

- A) Sequential activities connected end-to-end
- B) Three parallel activity branches that converge using dependency conditions before the final load activity
- C) A ForEach loop iterating over the three sources sequentially
- D) A single Copy Activity with three data sources in one mapping

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Three parallel activity branches that converge using dependency conditions before the final load activity**

**Explanation:** Data Pipelines support parallel execution by creating multiple activity branches. Activities without dependencies between them run concurrently. You can configure the final load activity to depend on the completion of all three parallel branches, ensuring data is loaded only after all sources are processed.

**Why other options are incorrect:**
- A) Sequential execution processes one source at a time, which is slower than parallel processing.
- C) ForEach loops iterate sequentially by default (or in batches), but the question calls for three independent parallel branches — distinct parallel paths are cleaner for a fixed set of sources.
- D) A single Copy Activity handles one source-to-destination mapping, not three independent sources.

</details>

---

### Question 24

A data pipeline must refresh data nightly but should only process records modified since the last successful run. Which data loading pattern does this describe?

- A) Full refresh
- B) Incremental load
- C) Real-time streaming
- D) Snapshot load

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Incremental load**

**Explanation:** An incremental load pattern processes only new or modified records since the last successful pipeline run. This is typically implemented by tracking a high-watermark value (such as a last-modified timestamp) and filtering source data to include only changes beyond that watermark. This reduces processing time and compute costs compared to full refreshes.

**Why other options are incorrect:**
- A) A full refresh reloads all data regardless of whether it has changed, which is not what is described.
- C) Real-time streaming continuously ingests data as it arrives, rather than running on a nightly schedule.
- D) A snapshot load captures the entire state of the source at a point in time, not just the changes since the last run.

</details>

---

### Question 25

When configuring a Dataflow Gen2 in Microsoft Fabric, the engineer notices the option to set a **staging Lakehouse**. What is the purpose of the staging Lakehouse?

- A) It permanently stores the final output of the dataflow
- B) It serves as an intermediate staging area to improve performance when loading data into the destination
- C) It creates a backup copy of the source data
- D) It replaces the need for a destination altogether

<details>
<summary>Show Answer</summary>

**Correct Answer: B) It serves as an intermediate staging area to improve performance when loading data into the destination**

**Explanation:** The staging Lakehouse in Dataflow Gen2 acts as a temporary intermediate storage location where data is staged before being loaded into the final destination. This staging step leverages the Lakehouse's optimized storage to improve the performance and reliability of the data loading process, especially for large datasets.

**Why other options are incorrect:**
- A) The staging Lakehouse holds temporary/intermediate data, not the final output — the final output goes to the configured destination.
- C) The staging area is not a backup mechanism; it is a performance optimization for the loading process.
- D) A destination is still required; the staging Lakehouse supplements but does not replace it.

</details>

---

### Question 26

A data engineer creates a Delta table with a defined schema and sets the table property to reject writes that add new columns not in the schema. Which Delta Lake feature is being used?

- A) Schema evolution
- B) Schema enforcement (schema validation)
- C) Time travel
- D) Partition pruning

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Schema enforcement (schema validation)**

**Explanation:** Schema enforcement (also called schema validation) is a Delta Lake feature that ensures all data written to a table conforms to the table's predefined schema. If a write operation attempts to add columns that do not exist in the schema, the write is rejected with an error. This protects the table from unintended schema changes.

**Why other options are incorrect:**
- A) Schema evolution is the opposite — it allows the schema to change automatically when new columns appear in the data.
- C) Time travel allows querying previous versions of a table, not schema validation.
- D) Partition pruning is a query optimization technique that skips irrelevant partitions, unrelated to schema validation.

</details>

---

### Question 27

A data analyst queries a Lakehouse table through the SQL analytics endpoint and attempts to run an `UPDATE` statement. What happens?

- A) The update executes successfully
- B) The update is queued until the next Spark session
- C) The operation fails because the SQL analytics endpoint is read-only
- D) The update succeeds but only for the current session

<details>
<summary>Show Answer</summary>

**Correct Answer: C) The operation fails because the SQL analytics endpoint is read-only**

**Explanation:** The SQL analytics endpoint of a Fabric Lakehouse provides read-only T-SQL access to the Delta tables. It supports SELECT queries but does not support DML operations like INSERT, UPDATE, or DELETE. To modify data, users must use Spark notebooks or the full Fabric Warehouse experience.

**Why other options are incorrect:**
- A) The SQL analytics endpoint does not allow UPDATE operations.
- B) Updates are not queued — they are rejected outright.
- D) There is no session-scoped write capability in the SQL analytics endpoint.

</details>

---

### Question 28

A Lakehouse Delta table has accumulated many small files over time due to frequent appends, causing query performance to degrade. Which maintenance operation should the data engineer run?

- A) `VACUUM` to delete old files
- B) `OPTIMIZE` to compact small files into larger ones
- C) `DROP TABLE` and recreate it
- D) `ANALYZE TABLE` to update column statistics only

<details>
<summary>Show Answer</summary>

**Correct Answer: B) `OPTIMIZE` to compact small files into larger ones**

**Explanation:** The `OPTIMIZE` command compacts many small files into fewer, larger files. This process, called bin-packing, improves read performance by reducing the number of files that must be scanned during queries. It is the standard maintenance operation for addressing the "small files problem" in Delta tables.

**Why other options are incorrect:**
- A) `VACUUM` removes old, unreferenced files that are no longer needed for time travel, but it does not compact current small files.
- C) Dropping and recreating the table is destructive and unnecessary when `OPTIMIZE` addresses the issue directly.
- D) `ANALYZE TABLE` updates statistics for the query optimizer but does not address file fragmentation.

</details>

---

### Question 29

A Data Pipeline activity fails during execution. The engineer wants the pipeline to retry the failed activity up to 3 times with a 5-minute delay between attempts before marking the pipeline as failed. Where is this configured?

- A) In the pipeline-level settings under "Concurrency"
- B) In the activity's retry policy settings (retry count and retry interval)
- C) In the Fabric Admin Portal under "Pipeline defaults"
- D) In the Lakehouse table's Delta log

<details>
<summary>Show Answer</summary>

**Correct Answer: B) In the activity's retry policy settings (retry count and retry interval)**

**Explanation:** Each activity in a Fabric Data Pipeline has configurable retry settings. The retry count specifies how many times to retry on failure, and the retry interval specifies the delay between attempts. Setting retry count to 3 and interval to 5 minutes achieves the desired behavior.

**Why other options are incorrect:**
- A) Pipeline-level concurrency controls how many pipeline instances can run simultaneously, not activity retry behavior.
- C) The Fabric Admin Portal does not have pipeline-level default retry settings — retries are configured per activity.
- D) The Delta log records table transactions, not pipeline execution retry policies.

</details>

---

### Question 30

When designing a semantic model for a sales analytics solution, a data modeler chooses a star schema with a central `FactSales` table surrounded by `DimProduct`, `DimCustomer`, `DimDate`, and `DimStore` tables. What is the primary benefit of using a star schema?

- A) It minimizes storage by normalizing all tables to third normal form
- B) It optimizes query performance by reducing the number of joins and enabling efficient filtering through dimension tables
- C) It eliminates the need for relationships between tables
- D) It allows unlimited columns in the fact table without performance impact

<details>
<summary>Show Answer</summary>

**Correct Answer: B) It optimizes query performance by reducing the number of joins and enabling efficient filtering through dimension tables**

**Explanation:** A star schema denormalizes dimension tables so that each dimension is a single, flat table surrounding the fact table. This design minimizes the number of joins needed for queries and allows the VertiPaq engine (used by Power BI / Fabric semantic models) to efficiently filter and aggregate data through dimension relationships.

**Why other options are incorrect:**
- A) Star schemas intentionally denormalize dimensions, which uses more storage than normalized forms — but gains query performance.
- C) Star schemas rely on relationships (one-to-many from dimensions to facts) — they do not eliminate relationships.
- D) Adding unlimited columns to a fact table would negatively impact performance — star schemas keep fact tables narrow with keys and measures.

</details>

---

### Question 31

A DAX measure is defined as:

```dax
Total Revenue = CALCULATE(SUM(Sales[Revenue]), REMOVEFILTERS(Sales[Region]))
```

A report visual shows Total Revenue sliced by Product Category. What does this measure return?

- A) Revenue filtered by both Product Category and Region
- B) Revenue for all regions, but still filtered by the Product Category selected in the visual
- C) Revenue with no filters applied at all
- D) An error because REMOVEFILTERS is not a valid DAX function

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Revenue for all regions, but still filtered by the Product Category selected in the visual**

**Explanation:** `REMOVEFILTERS(Sales[Region])` removes any filter on the Region column while preserving all other filters in the context. Since the visual slices by Product Category, that filter remains active. The measure therefore returns total revenue across all regions for the selected Product Category.

**Why other options are incorrect:**
- A) The Region filter is explicitly removed by REMOVEFILTERS, so revenue is not filtered by Region.
- C) Only the Region filter is removed — all other context filters (like Product Category) remain in effect.
- D) REMOVEFILTERS is a valid DAX function that serves the same purpose as ALL when used as a CALCULATE filter modifier.

</details>

---

### Question 32

A data modeler notices that a calculated column in a DAX expression references a measure. The calculated column evaluates the measure in a **row context**. What must occur for the measure to evaluate correctly?

- A) Nothing special — measures always evaluate in row context
- B) Context transition — CALCULATE must be used (implicitly or explicitly) to convert the row context into a filter context
- C) The measure must be converted into a calculated column
- D) Row context is automatically converted to filter context without any function

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Context transition — CALCULATE must be used (implicitly or explicitly) to convert the row context into a filter context**

**Explanation:** Measures in DAX evaluate in filter context. When a measure is called from within a row context (such as a calculated column or an iterator), context transition occurs — each column value in the current row becomes a filter. This transition is triggered by CALCULATE (which is implicitly present when a measure is invoked). Understanding context transition is critical for writing correct DAX.

**Why other options are incorrect:**
- A) Measures evaluate in filter context, not row context — context transition is required to bridge the gap.
- C) Converting the measure to a calculated column would change its semantics and is not the correct approach.
- D) Row context does not automatically become filter context — CALCULATE (implicit or explicit) is required for the transition.

</details>

---

### Question 33

A data engineer creates a semantic model pointing to a Lakehouse Delta table and selects **Direct Lake** storage mode. Which statement about Direct Lake is true?

- A) It copies all data into the model's in-memory cache during refresh
- B) It reads Delta/Parquet files directly from OneLake without importing data or translating queries to SQL
- C) It requires an on-premises data gateway to connect to OneLake
- D) It does not support DAX queries

<details>
<summary>Show Answer</summary>

**Correct Answer: B) It reads Delta/Parquet files directly from OneLake without importing data or translating queries to SQL**

**Explanation:** Direct Lake mode reads columnar data directly from Delta/Parquet files in OneLake. It avoids the Import mode's full data copy and the DirectQuery mode's SQL translation overhead, achieving near-Import query performance while keeping data in the lake. It is a unique Fabric capability.

**Why other options are incorrect:**
- A) Direct Lake does not copy all data during refresh — it reads data on-demand from OneLake (it may load column segments into memory as needed, but this is not a full import).
- C) OneLake is a cloud-native Fabric component — no on-premises data gateway is needed.
- D) Direct Lake fully supports DAX queries — that is its primary purpose as a semantic model storage mode.

</details>

---

### Question 34

A data analyst needs a semantic model that provides real-time data from a live SQL Server database for a dashboard that must always show the latest data. However, query performance is a secondary concern. Which storage mode is most appropriate?

- A) Import
- B) DirectQuery
- C) Dual
- D) Direct Lake

<details>
<summary>Show Answer</summary>

**Correct Answer: B) DirectQuery**

**Explanation:** DirectQuery sends queries directly to the source database at query time, ensuring that the dashboard always displays the most current data without needing a refresh schedule. The trade-off is potentially slower query performance compared to Import mode, but the requirement prioritizes data freshness over speed.

**Why other options are incorrect:**
- A) Import mode loads a snapshot of data into memory during refresh — it does not provide real-time data between refreshes.
- C) Dual mode allows a table to be queried in either Import or DirectQuery mode depending on the context, but it does not guarantee real-time data for all queries.
- D) Direct Lake reads from OneLake Delta tables, not from a live SQL Server database.

</details>

---

### Question 35

A security administrator needs to ensure that regional sales managers can only see sales data for their own region when viewing a Power BI report. Which feature should be configured in the semantic model?

- A) Object-level security (OLS)
- B) Row-level security (RLS)
- C) Column-level security
- D) Workspace viewer role

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Row-level security (RLS)**

**Explanation:** Row-level security (RLS) filters data at the row level based on the user's identity. By creating RLS roles with DAX filter expressions (e.g., `[Region] = USERPRINCIPALNAME()` mapped to a region lookup), each sales manager sees only their region's data in reports. RLS is enforced at the semantic model level and applies to all reports connected to it.

**Why other options are incorrect:**
- A) Object-level security (OLS) hides entire tables or columns, not specific rows of data.
- C) Column-level security restricts access to specific columns, not rows — the requirement is to filter by region (rows).
- D) The Workspace viewer role controls whether a user can access the workspace at all but does not filter data within reports.

</details>

---

### Question 36

A data modeler wants to hide a sensitive column (`Employee_SSN`) from report consumers while still allowing it to be used in DAX calculations within the model. Which feature achieves this?

- A) Row-level security (RLS)
- B) Object-level security (OLS)
- C) Deleting the column from the model
- D) Setting the column as a "key" column

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Object-level security (OLS)**

**Explanation:** Object-level security (OLS) restricts access to specific tables or columns within a semantic model. When OLS is applied to the `Employee_SSN` column, report consumers cannot see or query that column, but it remains available for DAX calculations within the model. OLS is configured through roles in the semantic model.

**Why other options are incorrect:**
- A) RLS filters rows, not columns — it cannot hide a specific column from users.
- C) Deleting the column would make it unavailable for DAX calculations, which contradicts the requirement.
- D) Setting a column as a "key" affects relationships but does not hide the column from report consumers.

</details>

---

### Question 37

A semantic model has a `Sales` table related to a `Products` table in a one-to-many relationship (Products → Sales). The cross-filter direction is set to **Single**. A report visual is filtered by a `Sales` column. Can this filter propagate to the `Products` table?

- A) Yes, single direction always filters both ways
- B) No, with single cross-filter direction the filter propagates only from the "one" side (Products) to the "many" side (Sales)
- C) Yes, but only if the relationship is inactive
- D) No, cross-filter direction does not affect filter propagation

<details>
<summary>Show Answer</summary>

**Correct Answer: B) No, with single cross-filter direction the filter propagates only from the "one" side (Products) to the "many" side (Sales)**

**Explanation:** In a single cross-filter direction relationship, filters propagate only from the "one" side to the "many" side. In this case, filtering Products will filter Sales, but filtering Sales will NOT propagate back to Products. To enable bidirectional filtering, the cross-filter direction must be changed to "Both."

**Why other options are incorrect:**
- A) Single direction explicitly means one-way propagation, not both ways.
- C) Inactive relationships do not participate in filter propagation at all unless activated via USERELATIONSHIP in DAX.
- D) Cross-filter direction is the primary setting that controls filter propagation behavior between related tables.

</details>

---

### Question 38

A data modeler builds a semantic model that combines Import mode tables from a Lakehouse with a DirectQuery connection to a live Azure SQL Database. What type of model is this?

- A) Pure Import model
- B) Pure DirectQuery model
- C) Composite model
- D) Live Connection model

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Composite model**

**Explanation:** A composite model combines tables with different storage modes (Import, DirectQuery, or Dual) within a single semantic model. This allows modelers to optimize for both performance (Import for frequently accessed data) and freshness (DirectQuery for real-time data) in the same model.

**Why other options are incorrect:**
- A) A pure Import model contains only Import mode tables — this model also has DirectQuery tables.
- B) A pure DirectQuery model contains only DirectQuery tables — this model also has Import tables.
- D) Live Connection connects to a remote model without modification — it does not allow combining storage modes.

</details>

---

### Question 39

A data modeler creates a user-defined aggregation table called `SalesAgg` with pre-computed summaries at the monthly grain, while the detail table `SalesDetail` contains daily records. Both tables are in the same semantic model. When a report visual requests monthly data, which table does the engine query?

- A) Always `SalesDetail`
- B) Always `SalesAgg`
- C) The engine automatically routes the query to `SalesAgg` if the aggregation is configured and the query matches the aggregation grain
- D) The user must manually select which table to query in the report

<details>
<summary>Show Answer</summary>

**Correct Answer: C) The engine automatically routes the query to `SalesAgg` if the aggregation is configured and the query matches the aggregation grain**

**Explanation:** When aggregation tables are configured in a semantic model, the VertiPaq engine automatically routes queries to the aggregation table when the query's grain matches the aggregation level. This happens transparently — report users see the same visuals, but queries are served faster from the pre-aggregated table. Queries that require finer grain automatically fall back to the detail table.

**Why other options are incorrect:**
- A) The engine prefers the aggregation table when the query matches its grain, not the detail table.
- B) The engine uses the aggregation table only when the query grain matches — finer-grain queries use the detail table.
- D) Aggregation routing is automatic and transparent to report users — no manual selection is needed.

</details>

---

### Question 40

A semantic model refreshes a large table with 100 million rows. Each refresh currently reloads all data, taking 2 hours. The data source adds approximately 500K new rows daily. Which feature should the modeler configure to reduce refresh time?

- A) Full refresh with a parallel processing hint
- B) Incremental refresh
- C) DirectQuery mode for the table
- D) Remove the table and create a view

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Incremental refresh**

**Explanation:** Incremental refresh partitions a table into time-based ranges and refreshes only the partitions containing new or changed data. For a table with 100M rows where only 500K change daily, incremental refresh drastically reduces refresh time by reprocessing only the recent partition rather than the entire table.

**Why other options are incorrect:**
- A) Parallel processing may speed up a full refresh slightly but still reloads all 100M rows each time.
- C) Switching to DirectQuery eliminates refresh but degrades query performance and changes the model's behavior significantly.
- D) Removing the table and creating a view does not address the refresh time problem — it changes the data access pattern entirely.

</details>

---

### Question 41

A report designer wants users to right-click on a product in a summary table and navigate to a detailed page showing all orders for that product. The detail page should be automatically filtered to the selected product. Which Power BI feature should be used?

- A) Bookmarks
- B) Drill-through
- C) Drill-down
- D) Page tooltips

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Drill-through**

**Explanation:** Drill-through allows report consumers to right-click a data point and navigate to a target page that is automatically filtered to the selected value. This is the standard mechanism for navigating from a summary to a detail page with context-aware filtering in Power BI.

**Why other options are incorrect:**
- A) Bookmarks capture a report state but do not dynamically filter a target page based on a selected data point.
- C) Drill-down expands within a hierarchy on the same visual (e.g., Year → Quarter → Month), not to a separate page.
- D) Page tooltips show a custom tooltip popup when hovering over a data point but do not navigate to a new page.

</details>

---

### Question 42

A data analyst writes the following T-SQL query against a Fabric Warehouse:

```sql
SELECT TOP 10 c.CustomerName, SUM(s.Amount) AS TotalSpent
FROM Sales s
JOIN Customers c ON s.CustomerID = c.CustomerID
WHERE s.OrderDate >= '2024-01-01'
GROUP BY c.CustomerName
ORDER BY TotalSpent DESC
```

What does this query return?

- A) All customers with their total spending across all time
- B) The 10 customers who spent the most since January 1, 2024
- C) The first 10 rows of the Sales table
- D) An error because TOP is not supported in Fabric Warehouse

<details>
<summary>Show Answer</summary>

**Correct Answer: B) The 10 customers who spent the most since January 1, 2024**

**Explanation:** The query filters orders from 2024 onward (WHERE), groups by customer name (GROUP BY), computes total spending (SUM), sorts by highest spending (ORDER BY DESC), and returns only the top 10 results (TOP 10). This correctly identifies the highest-spending customers in the specified period.

**Why other options are incorrect:**
- A) The WHERE clause limits results to 2024 and later, not all time.
- C) The query joins, groups, and aggregates — it does not simply return 10 rows from Sales.
- D) TOP is fully supported T-SQL syntax in Fabric Warehouse.

</details>

---

### Question 43

An operations team needs to query real-time telemetry data streaming into a Fabric Eventhouse. Which query language is natively used for this purpose?

- A) T-SQL
- B) DAX
- C) KQL (Kusto Query Language)
- D) PySpark

<details>
<summary>Show Answer</summary>

**Correct Answer: C) KQL (Kusto Query Language)**

**Explanation:** KQL (Kusto Query Language) is the native query language for querying data in Fabric Eventhouse (and KQL Databases). It is optimized for exploring, filtering, and analyzing large volumes of time-series and telemetry data in near real-time. KQL uses a pipe-based syntax that is highly readable for log and telemetry analysis.

**Why other options are incorrect:**
- A) T-SQL is the language for Fabric Warehouses and Lakehouse SQL analytics endpoints, not Eventhouse.
- B) DAX is used for querying semantic models, not real-time telemetry data in Eventhouse.
- D) PySpark is used in Spark notebooks for batch processing, not for native Eventhouse queries.

</details>

---

### Question 44

A Power BI developer needs to test a complex DAX query against a published semantic model without creating a report visual. Which tool should be used?

- A) Power BI report editor
- B) DAX Studio or the EVALUATE statement in a DAX query view
- C) T-SQL query editor in the Warehouse
- D) PySpark notebook

<details>
<summary>Show Answer</summary>

**Correct Answer: B) DAX Studio or the EVALUATE statement in a DAX query view**

**Explanation:** DAX Studio is a dedicated tool for writing, testing, and optimizing DAX queries against a semantic model. Additionally, Power BI's built-in DAX query view allows running EVALUATE queries directly. Both approaches let developers test DAX logic without creating visual elements.

**Why other options are incorrect:**
- A) The report editor creates visuals, which is not required for testing a standalone DAX query.
- C) The T-SQL query editor is for SQL queries against Warehouses, not DAX queries against semantic models.
- D) PySpark notebooks are for Spark processing, not for querying semantic models with DAX.

</details>

---

### Question 45

A development team manages a Fabric analytics solution with separate Dev, Test, and Prod workspaces. They want to promote content from Dev to Test to Prod with controlled approvals at each stage. Which Fabric feature enables this?

- A) Workspace roles
- B) Deployment pipelines
- C) Dataflow Gen2 staging
- D) Git branches

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Deployment pipelines**

**Explanation:** Fabric deployment pipelines provide a structured mechanism for promoting content (reports, datasets, dataflows, etc.) through stages — typically Dev → Test → Prod. Each stage maps to a Fabric workspace, and administrators can configure deployment rules and approval gates to control promotions between stages.

**Why other options are incorrect:**
- A) Workspace roles manage user permissions within a single workspace, not cross-workspace content promotion.
- C) Dataflow Gen2 staging is about temporary data storage during ETL, not content deployment across environments.
- D) Git branches support source control for development but do not provide the stage-based deployment and promotion workflow that deployment pipelines offer.

</details>

---

### Question 46

A team wants to store their Fabric workspace items (reports, semantic model definitions, notebooks) in a Git repository for version control and collaboration. Which Fabric feature integrates workspaces with Git?

- A) Deployment pipelines
- B) Fabric Git integration
- C) OneLake file versioning
- D) Power BI template files (.pbit)

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Fabric Git integration**

**Explanation:** Fabric Git integration connects a workspace to a Git repository (Azure DevOps or GitHub). Workspace items are serialized to the repository, enabling version control, branching, pull requests, and collaborative development workflows. Changes can be synced bidirectionally between the workspace and the repository.

**Why other options are incorrect:**
- A) Deployment pipelines manage stage-based promotion (Dev → Test → Prod) but do not provide Git-based version control.
- C) OneLake file versioning is based on Delta Lake time travel for data tables, not version control for workspace items like reports.
- D) Power BI template files (.pbit) export a single report's definition but do not provide ongoing version control or collaboration features.

</details>

---

### Question 47

A compliance team requires that all Power BI reports containing financial data be labeled as "Confidential" and that the label persists when the report is exported to PDF. Which feature ensures this?

- A) Row-level security
- B) Microsoft Purview sensitivity labels
- C) Watermarking in Power BI Desktop
- D) Export restrictions in workspace settings

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Microsoft Purview sensitivity labels**

**Explanation:** Microsoft Purview sensitivity labels classify content and persist through exports. When a report labeled "Confidential" is exported to PDF, the sensitivity label metadata and protection policies travel with the exported file. This ensures compliance requirements are maintained even outside the Fabric environment.

**Why other options are incorrect:**
- A) RLS filters data rows but does not apply classification labels that persist in exports.
- C) Watermarking is a visual overlay, not a metadata-based classification and protection mechanism.
- D) Export restrictions can block exports entirely but do not apply persistent classification labels to exported content.

</details>

---

### Question 48

A data engineer notices that a report backed by an Import-mode semantic model is responding slowly. They want to identify which DAX queries are consuming the most resources. Which monitoring approach is most appropriate?

- A) Check the Fabric capacity metrics app for overall CU usage
- B) Query DMVs (Dynamic Management Views) in the semantic model to analyze query performance
- C) Review the Data Pipeline activity run history
- D) Check OneLake storage metrics

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Query DMVs (Dynamic Management Views) in the semantic model to analyze query performance**

**Explanation:** DMVs in semantic models (accessible via DAX Studio or XMLA endpoints) provide detailed information about query execution times, memory usage, and storage engine operations. This allows data engineers to identify specific DAX queries that are consuming the most resources and optimize them.

**Why other options are incorrect:**
- A) The capacity metrics app shows overall capacity utilization but does not pinpoint individual DAX queries.
- C) Pipeline activity history tracks data pipeline runs, not DAX query performance within semantic models.
- D) OneLake storage metrics show storage consumption, not query execution performance.

</details>

---

### Question 49

A finance team needs a formatted, multi-page report that can be exported as a pixel-perfect PDF for board presentations. The report must include page headers, footers, and precise layout control. Which reporting tool in Fabric should be used?

- A) Power BI interactive report
- B) Paginated report (Power BI Report Builder)
- C) Power BI dashboard
- D) KQL Queryset visualization

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Paginated report (Power BI Report Builder)**

**Explanation:** Paginated reports are designed for pixel-perfect, print-ready output with precise layout control, page headers/footers, and multi-page formatting. They are ideal for formal documents like board reports, invoices, and regulatory filings that must export cleanly to PDF.

**Why other options are incorrect:**
- A) Interactive reports are optimized for on-screen exploration, not for precise print-layout control with page headers and footers.
- C) Dashboards display pinned tiles from multiple reports but do not support pixel-perfect PDF formatting.
- D) KQL Queryset visualizations are for real-time data exploration, not formatted document output.

</details>

---

### Question 50

A report developer creates a DAX measure directly in a Power BI report (a "report-level measure") rather than in the underlying semantic model. What is a key limitation of this approach?

- A) Report-level measures cannot use CALCULATE
- B) Report-level measures are only available in the report where they are created, not shared across other reports connected to the same model
- C) Report-level measures execute faster than model measures
- D) Report-level measures automatically override model measures with the same name

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Report-level measures are only available in the report where they are created, not shared across other reports connected to the same model**

**Explanation:** Report-level measures are scoped to the specific report file. Other reports connected to the same underlying semantic model cannot access them. If the measure needs to be shared across multiple reports, it should be created in the semantic model itself so that all connected reports can use it.

**Why other options are incorrect:**
- A) Report-level measures support the full DAX language, including CALCULATE.
- C) Report-level measures do not inherently execute faster than model measures — the performance characteristics are similar.
- D) Report-level measures do not automatically override model measures; they exist as separate measures with their own names.

</details>

---

## 📊 Score Tracker

| Metric | Value |
|---|---|
| **Total Questions** | 50 |
| **Correct Answers** | ___ / 50 |
| **Percentage** | ___% |
| **Passing Threshold (estimated)** | 70% (35/50) |

### Domain Breakdown

| Domain | Questions | Correct | Score |
|---|---|---|---|
| Domain 1: Plan, Implement, and Manage | 1–7 | ___ / 7 | ___% |
| Domain 2: Prepare and Serve Data | 8–29 | ___ / 22 | ___% |
| Domain 3: Semantic Models | 30–40 | ___ / 11 | ___% |
| Domain 4: Explore and Analyze | 41–50 | ___ / 10 | ___% |
