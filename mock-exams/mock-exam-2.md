# 🧪 Mock Exam 2 — DP-600: Implementing Analytics Solutions Using Microsoft Fabric

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

A company is evaluating Microsoft Fabric for their enterprise analytics platform. The CTO asks what makes Fabric different from running separate Azure Data Factory, Azure Synapse, and Power BI services independently. What is the primary architectural differentiator?

- A) Fabric uses a different programming language than Azure services
- B) Fabric is a unified SaaS platform with shared capacity, OneLake storage, and integrated experiences that eliminates the need to stitch together separate services
- C) Fabric only supports Power BI workloads
- D) Fabric requires separate Azure subscriptions for each workload

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Fabric is a unified SaaS platform with shared capacity, OneLake storage, and integrated experiences that eliminates the need to stitch together separate services**

**Explanation:** Microsoft Fabric's key differentiator is its unified architecture — a single SaaS platform where data engineering, data warehousing, data science, real-time analytics, and business intelligence share the same capacity, storage (OneLake), security, and governance model. This eliminates the integration complexity of managing separate Azure services.

**Why other options are incorrect:**
- A) Fabric supports the same languages (T-SQL, Python, DAX, KQL, etc.) as the individual Azure services.
- C) Fabric includes Data Engineering, Data Warehouse, Data Science, Real-Time Analytics, and Data Factory in addition to Power BI.
- D) Fabric simplifies management — it does not require separate subscriptions per workload.

</details>

---

### Question 2

A Fabric administrator needs to assign workspace permissions so that a business analyst can view reports and dashboards but cannot edit them or create new items. Which workspace role should be assigned?

- A) Admin
- B) Member
- C) Contributor
- D) Viewer

<details>
<summary>Show Answer</summary>

**Correct Answer: D) Viewer**

**Explanation:** The Viewer role in a Fabric workspace allows users to view and interact with content (reports, dashboards) but does not grant the ability to edit, create, or delete workspace items. This is the most restrictive role and is appropriate for business analysts who only consume content.

**Why other options are incorrect:**
- A) Admin has full control including managing workspace settings and membership.
- B) Member can create, edit, and delete content, and manage some workspace settings.
- C) Contributor can create and edit content but cannot manage workspace settings or membership.

</details>

---

### Question 3

An organization has Microsoft 365 E5 licenses and wants to use sensitivity labels from Microsoft Purview to protect Fabric items. The Fabric administrator needs to enable this integration. Where is this setting configured?

- A) Individual workspace settings
- B) Fabric Admin Portal → Tenant settings → Information protection
- C) Power BI Desktop → Options → Privacy
- D) Azure Portal → Microsoft Purview → Data Map

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Fabric Admin Portal → Tenant settings → Information protection**

**Explanation:** The integration of Microsoft Purview sensitivity labels with Fabric is enabled at the tenant level through the Fabric Admin Portal's Information Protection settings. Once enabled, sensitivity labels defined in Microsoft Purview become available for application to Fabric items across all workspaces.

**Why other options are incorrect:**
- A) Individual workspace settings cannot enable tenant-wide sensitivity label integration.
- C) Power BI Desktop privacy options control local query privacy levels, not Purview sensitivity label integration.
- D) The Azure Portal Purview Data Map manages data catalog and scanning, not the Fabric-side integration of sensitivity labels.

</details>

---

### Question 4

A data team discovers that a scheduled Lakehouse notebook refresh failed overnight. The team needs to determine what went wrong. Which Fabric feature provides execution history and error details for scheduled activities?

- A) Data lineage view
- B) Monitoring hub
- C) OneLake file explorer
- D) Workspace usage metrics

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Monitoring hub**

**Explanation:** The Monitoring hub in Microsoft Fabric provides a centralized view of activity execution history, including scheduled refreshes, pipeline runs, notebook executions, and Spark jobs. It shows status (succeeded, failed, in progress), duration, and error details for failed activities.

**Why other options are incorrect:**
- A) Data lineage view shows dependencies between items but does not display execution history or error details.
- C) OneLake file explorer browses data files and folders, not execution logs.
- D) Workspace usage metrics show report and dashboard consumption patterns, not activity execution details.

</details>

---

### Question 5

An enterprise needs to charge back Fabric compute costs to individual business units based on their actual usage. Each business unit operates in its own workspace. What is the recommended approach to enable this chargeback model?

- A) Assign each business unit's workspaces to a dedicated Fabric capacity and track CU usage per capacity
- B) Create separate Fabric tenants for each business unit
- C) Use OneLake storage billing as the sole chargeback metric
- D) Manually estimate usage based on the number of users per business unit

<details>
<summary>Show Answer</summary>

**Correct Answer: A) Assign each business unit's workspaces to a dedicated Fabric capacity and track CU usage per capacity**

**Explanation:** By assigning each business unit's workspaces to a dedicated capacity, compute usage is naturally segmented. The Fabric capacity metrics app tracks CU (Capacity Unit) consumption per capacity, providing the data needed for accurate chargeback to each business unit.

**Why other options are incorrect:**
- B) Separate tenants introduce unnecessary administrative complexity and prevent cross-organization collaboration.
- C) Storage billing alone does not capture compute costs, which are typically the larger cost component.
- D) Manual estimation based on user count is inaccurate — actual compute usage varies significantly by workload complexity.

</details>

---

### Question 6

A Fabric administrator needs to restrict external data sharing so that workspace members cannot share reports with users outside the organization. Where is this control configured?

- A) Microsoft Entra ID → External collaboration settings
- B) Fabric Admin Portal → Tenant settings → Export and sharing settings
- C) Individual report settings → Share dialog
- D) OneLake storage access policies

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Fabric Admin Portal → Tenant settings → Export and sharing settings**

**Explanation:** The Fabric Admin Portal's Tenant settings include Export and Sharing settings that control whether content can be shared externally. Administrators can disable external sharing entirely or restrict it to specific security groups, providing centralized governance over data sharing.

**Why other options are incorrect:**
- A) Entra ID external collaboration settings control guest user invitations at the directory level, not Fabric-specific sharing controls.
- C) Individual report sharing dialogs allow sharing but are governed by the tenant-level policies, not the other way around.
- D) OneLake storage access policies control data file access, not report sharing with external users.

</details>

---

### Question 7

A Fabric capacity is experiencing throttling during peak business hours, causing slow report rendering. The administrator does not want to upgrade to a higher SKU. Which short-term mitigation can help reduce throttling?

- A) Stagger scheduled refresh times to reduce concurrent load during peak hours
- B) Increase the number of workspaces on the capacity
- C) Switch all semantic models to DirectQuery mode
- D) Delete all unused reports from the capacity

<details>
<summary>Show Answer</summary>

**Correct Answer: A) Stagger scheduled refresh times to reduce concurrent load during peak hours**

**Explanation:** Throttling occurs when compute demand exceeds the capacity's CU allocation. By staggering scheduled refreshes (moving some to off-peak hours), concurrent compute demand during peak hours is reduced, alleviating throttling without requiring an SKU upgrade. This is a common and effective short-term mitigation strategy.

**Why other options are incorrect:**
- B) Adding more workspaces increases load on the capacity, worsening throttling.
- C) Switching all models to DirectQuery shifts load to source systems and may degrade query performance; it is not a general solution.
- D) Deleting unused reports frees storage but does not reduce compute throttling caused by active refresh and query operations.

</details>

---

### Question 8

A data engineer needs to ingest data from a SaaS application's REST API on an hourly schedule, apply light transformations (rename columns, filter rows), and write the results to a Lakehouse table. The engineer prefers a low-code approach. Which tool is most appropriate?

- A) Spark notebook with PySpark
- B) Data Pipeline with stored procedure activity
- C) Dataflow Gen2
- D) KQL Database ingestion

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Dataflow Gen2**

**Explanation:** Dataflow Gen2 provides a low-code, Power Query-based interface that can connect to REST APIs, apply transformations (rename, filter, merge columns), and output to a Lakehouse table. Its visual designer makes it ideal for data engineers and analysts who prefer minimal coding.

**Why other options are incorrect:**
- A) Spark notebooks require writing PySpark code, which does not meet the low-code preference.
- B) A stored procedure activity runs SQL procedures and cannot connect to REST APIs for data ingestion.
- D) KQL Database ingestion is for streaming/real-time data into Eventhouse, not for REST API ingestion into Lakehouse.

</details>

---

### Question 9

A data pipeline uses a Copy Activity to move data from Azure SQL Database to a Fabric Lakehouse table. The source table has 200 million rows. The engineer wants to parallelize the copy operation for better throughput. Which setting controls this?

- A) Pipeline concurrency
- B) Copy Activity degree of copy parallelism
- C) Lakehouse table partition count
- D) Gateway connection pool size

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Copy Activity degree of copy parallelism**

**Explanation:** The Copy Activity has a parallelism setting (degree of copy parallelism) that controls how many parallel threads read from the source and write to the destination. Increasing this value allows the Copy Activity to process multiple data partitions concurrently, improving throughput for large datasets.

**Why other options are incorrect:**
- A) Pipeline concurrency controls how many instances of the entire pipeline can run simultaneously, not internal Copy Activity parallelism.
- C) Lakehouse table partition count affects how data is stored but is not a Copy Activity throughput setting.
- D) Gateway connection pool size affects on-premises gateway connections, not cloud-to-cloud Copy Activity parallelism.

</details>

---

### Question 10

An organization uses an on-premises data gateway with a cluster of three gateway machines for high availability. If one machine goes offline, what happens to scheduled data refreshes that use the gateway?

- A) All refreshes fail immediately
- B) Refreshes are automatically routed to the remaining healthy gateway machines in the cluster
- C) Refreshes are paused until the offline machine is restored
- D) A new gateway must be installed before refreshes can continue

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Refreshes are automatically routed to the remaining healthy gateway machines in the cluster**

**Explanation:** An on-premises data gateway cluster provides high availability by distributing requests across multiple gateway machines. If one machine fails, the gateway service automatically routes requests to the remaining healthy machines, ensuring continuity of data refresh operations.

**Why other options are incorrect:**
- A) The cluster provides redundancy — a single machine failure does not cause all refreshes to fail.
- C) Refreshes continue on the healthy machines without pausing.
- D) No new installation is needed — the existing healthy cluster members handle the workload.

</details>

---

### Question 11

A data team has external data in Google Cloud Storage that they want to access from a Fabric Lakehouse. They do not want to copy the data. Which approach works?

- A) Use a Dataflow Gen2 to read from Google Cloud Storage directly into a Lakehouse table
- B) Create a OneLake shortcut pointing to Google Cloud Storage
- C) Mount Google Cloud Storage as a local drive on the gateway
- D) Export the data from Google Cloud Storage to CSV and email it to the team

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Create a OneLake shortcut pointing to Google Cloud Storage**

**Explanation:** OneLake shortcuts support external data sources including Google Cloud Storage (GCS), Amazon S3, and Azure Data Lake Storage. A shortcut creates a virtualized, zero-copy reference that makes the external data accessible as a native location within the Lakehouse.

**Why other options are incorrect:**
- A) Dataflow Gen2 would copy the data, violating the no-copy requirement.
- C) Mounting cloud storage as a local drive is not a supported gateway configuration in Fabric.
- D) Exporting and emailing CSV files is manual, non-scalable, and is not a valid data integration approach.

</details>

---

### Question 12

A data engineer creates a Delta table in a Lakehouse using a Spark notebook. Later, they notice that the table does not appear in the SQL analytics endpoint. What is the most likely cause?

- A) The table was created in the Files section as unmanaged files, not as a managed table
- B) The SQL analytics endpoint is disabled by default
- C) Delta tables are never visible in the SQL analytics endpoint
- D) The Spark notebook needs to be restarted

<details>
<summary>Show Answer</summary>

**Correct Answer: A) The table was created in the Files section as unmanaged files, not as a managed table**

**Explanation:** The SQL analytics endpoint only exposes managed Delta tables registered in the Lakehouse's Tables section. If a Spark notebook writes Delta files to the Files section without registering them as a managed table (e.g., using `spark.sql("CREATE TABLE ... USING DELTA LOCATION ...")` without proper table registration), the data won't appear in the SQL endpoint. Tables must be properly registered as managed tables.

**Why other options are incorrect:**
- B) The SQL analytics endpoint is enabled by default for all Lakehouses.
- C) Managed Delta tables are automatically visible in the SQL analytics endpoint.
- D) Restarting the notebook would not register an unmanaged table in the SQL endpoint.

</details>

---

### Question 13

Which file format does a Fabric Lakehouse use by default for managed tables?

- A) CSV with header
- B) Apache Parquet
- C) Delta Lake (Parquet + _delta_log)
- D) Apache ORC

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Delta Lake (Parquet + _delta_log)**

**Explanation:** All managed tables in a Fabric Lakehouse use Delta Lake format by default. Delta Lake extends Parquet with a JSON-based transaction log (_delta_log) that provides ACID transactions, time travel, schema enforcement, and audit history. This is the foundation of the Lakehouse table storage in Fabric.

**Why other options are incorrect:**
- A) CSV is not a managed table format — it lacks schema enforcement and transactions.
- B) While Delta uses Parquet as the underlying data format, the managed table format includes the Delta transaction log, making "Delta Lake" the correct answer.
- D) Apache ORC is not used as the default table format in Fabric Lakehouses.

</details>

---

### Question 14

In a medallion architecture, a data engineer notices that the Silver layer contains cleansed data with standardized column names and deduplicated records. They now need to create a Gold layer table that pre-computes customer lifetime value. What characterizes the Gold layer?

- A) Raw, unprocessed data in its original format
- B) Cleansed data with standardized schemas
- C) Business-level aggregations and metrics optimized for consumption by reports and analytics
- D) Temporary staging data that is deleted after processing

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Business-level aggregations and metrics optimized for consumption by reports and analytics**

**Explanation:** The Gold layer contains refined, aggregated, business-ready data. This is where metrics like customer lifetime value, daily revenue summaries, and KPI calculations are materialized. Gold layer tables are designed for direct consumption by reports, dashboards, and semantic models.

**Why other options are incorrect:**
- A) Raw, unprocessed data describes the Bronze layer.
- B) Cleansed data with standardized schemas describes the Silver layer.
- D) The Gold layer is a permanent serving layer, not temporary staging.

</details>

---

### Question 15

A data engineer needs to load data into a Fabric Warehouse table from Azure Blob Storage using the most performant method. The data is in Parquet format. What is the recommended approach?

- A) Use a linked server to query the external storage
- B) Use the `COPY INTO` command
- C) Use OPENROWSET inside an INSERT statement
- D) Connect Power BI Desktop to Azure Blob Storage and publish to the warehouse

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use the `COPY INTO` command**

**Explanation:** `COPY INTO` is the highest-throughput data loading method for Fabric Warehouse. It is optimized for bulk loading from external storage (Azure Blob, ADLS Gen2) and supports Parquet, CSV, and other formats. Microsoft recommends it as the primary method for warehouse data loading.

**Why other options are incorrect:**
- A) Linked servers are not supported in Fabric Warehouse.
- C) OPENROWSET can read external files but is not as optimized as COPY INTO for bulk loading.
- D) Power BI Desktop is a reporting tool, not a data loading mechanism for Fabric Warehouse.

</details>

---

### Question 16

A data engineer uses CTAS (`CREATE TABLE AS SELECT`) in a Fabric Warehouse to create a summary table. After running the statement, they discover the new table has no indexes. Is this expected?

- A) No — CTAS always creates a clustered columnstore index automatically
- B) Yes — Fabric Warehouse tables automatically use a columnstore storage format; traditional indexes are not needed
- C) No — the engineer must manually create a primary key after CTAS
- D) Yes — but the table has no storage optimization at all

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Yes — Fabric Warehouse tables automatically use a columnstore storage format; traditional indexes are not needed**

**Explanation:** Fabric Warehouse uses a columnar storage engine that is inherently optimized for analytical queries. Traditional row-based indexes (clustered, non-clustered) are not needed because the columnar format provides efficient compression and query performance by default. CTAS creates a new table in this optimized format automatically.

**Why other options are incorrect:**
- A) Fabric Warehouse does not use traditional clustered columnstore indexes like SQL Server — it uses its own columnar engine.
- C) Primary keys and traditional indexes are not required for Fabric Warehouse tables.
- D) The table does have storage optimization — the columnar format provides compression and query acceleration.

</details>

---

### Question 17

A data analyst needs to combine data from two different Fabric Warehouses in the same workspace using a single T-SQL query. Which syntax should they use?

- A) `SELECT * FROM Warehouse1.dbo.Table1 JOIN Warehouse2.dbo.Table2 ON ...` (three-part naming)
- B) `SELECT * FROM OPENROWSET(...) JOIN OPENROWSET(...)`
- C) `SELECT * FROM LinkedServer1.Table1 JOIN LinkedServer2.Table2`
- D) This is not possible — data must be copied into a single warehouse first

<details>
<summary>Show Answer</summary>

**Correct Answer: A) `SELECT * FROM Warehouse1.dbo.Table1 JOIN Warehouse2.dbo.Table2 ON ...` (three-part naming)**

**Explanation:** Fabric supports cross-database queries using three-part naming (database.schema.table). This allows a single T-SQL query to join tables across different Warehouses and Lakehouse SQL analytics endpoints within the same workspace without copying data.

**Why other options are incorrect:**
- B) OPENROWSET is for reading external files, not for cross-warehouse queries within Fabric.
- C) Linked servers are not supported in Fabric Warehouse.
- D) Cross-database queries are fully supported — data movement is not required.

</details>

---

### Question 18

A data engineer writes a PySpark notebook that reads a CSV file from the Lakehouse Files section and writes it as a managed Delta table. Which PySpark command creates a managed table?

- A) `df.write.format("delta").save("/lakehouse/default/Files/output")`
- B) `df.write.format("delta").saveAsTable("my_table")`
- C) `df.write.format("csv").save("/lakehouse/default/Tables/my_table")`
- D) `df.to_csv("my_table.csv")`

<details>
<summary>Show Answer</summary>

**Correct Answer: B) `df.write.format("delta").saveAsTable("my_table")`**

**Explanation:** `saveAsTable` registers the output as a managed table in the Lakehouse's metastore, making it visible in the Tables section and the SQL analytics endpoint. Using `format("delta")` ensures it is written as a Delta table, which is the required format for managed tables.

**Why other options are incorrect:**
- A) `save()` with a file path writes data to the Files section as unmanaged Delta files — it does not register a managed table.
- C) Writing in CSV format to the Tables path does not create a managed Delta table.
- D) `to_csv()` is a Pandas method, not PySpark, and would not create a managed Lakehouse table.

</details>

---

### Question 19

A data engineer is considering partitioning a large Delta table in a Lakehouse by the `order_date` column. The table has 500 million rows spanning 3 years of daily data. Which partitioning strategy is most appropriate?

- A) Partition by `order_date` at the daily level (1,095 partitions)
- B) Partition by `order_date` at the monthly level (~36 partitions)
- C) Partition by `order_date` at the yearly level (3 partitions)
- D) Do not partition — rely on Delta Lake's built-in optimizations

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Partition by `order_date` at the monthly level (~36 partitions)**

**Explanation:** Monthly partitioning provides a good balance for 500 million rows over 3 years. It creates ~36 manageable partitions that enable effective partition pruning for date-range queries without creating too many small partitions (daily would create 1,095) or too few large partitions (yearly would create only 3). The ideal partition size is typically 1 GB or more.

**Why other options are incorrect:**
- A) Daily partitioning would create 1,095 partitions, potentially resulting in a small files problem with each partition being too small.
- C) Yearly partitioning creates only 3 partitions, each containing ~167M rows, which limits the benefit of partition pruning for most queries.
- D) For a 500M row table with clear date-based query patterns, partitioning provides significant performance benefits over no partitioning.

</details>

---

### Question 20

A data engineer schedules a Spark notebook to run daily using a Data Pipeline. Some days the source data is empty and the notebook fails. The engineer wants the pipeline to succeed even when the notebook encounters an empty dataset. How should this be handled?

- A) Add error handling in the notebook to check for empty data and exit gracefully
- B) Set the pipeline to ignore all errors globally
- C) Remove the notebook from the pipeline
- D) Schedule the pipeline to run only on days with data

<details>
<summary>Show Answer</summary>

**Correct Answer: A) Add error handling in the notebook to check for empty data and exit gracefully**

**Explanation:** The notebook should check for empty data (e.g., `if df.count() == 0: exit with success`) and handle it gracefully rather than throwing an error. This keeps the pipeline's success/failure status meaningful while handling the expected scenario of no data.

**Why other options are incorrect:**
- B) Ignoring all errors globally would mask genuine failures that need attention.
- C) Removing the notebook eliminates the processing logic entirely.
- D) You typically cannot predict which days will have empty data, making conditional scheduling impractical.

</details>

---

### Question 21

A data pipeline needs to process files from multiple folders — one folder per customer. The number of customers (and folders) varies. Which pipeline construct should be used?

- A) Create a separate Copy Activity for each customer folder
- B) Use a ForEach activity that iterates over a list of customer folders
- C) Use a single Copy Activity with a wildcard path
- D) Create separate pipelines for each customer

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use a ForEach activity that iterates over a list of customer folders**

**Explanation:** The ForEach activity in Data Pipelines iterates over a dynamic collection (e.g., a list of folder names from a metadata table or Get Metadata activity). Inside the ForEach loop, a parameterized activity processes each folder. This approach scales dynamically as customers are added or removed.

**Why other options are incorrect:**
- A) Creating separate activities for each customer is static and requires pipeline modifications whenever customers change.
- C) A wildcard path might merge all customer data together rather than processing each customer folder individually.
- D) Separate pipelines per customer is operationally complex and does not scale well.

</details>

---

### Question 22

A data engineer needs to configure incremental data loading in a Data Pipeline. The source table has a `ModifiedDate` column. The pipeline should only process rows where `ModifiedDate` is greater than the last successful run's maximum value. What is this value commonly called?

- A) Checkpoint offset
- B) High-watermark
- C) Partition key
- D) Delta token

<details>
<summary>Show Answer</summary>

**Correct Answer: B) High-watermark**

**Explanation:** A high-watermark is the maximum value of a tracking column (such as a timestamp or auto-incrementing ID) from the last successful load. During the next pipeline run, only records with values exceeding the stored high-watermark are processed, enabling efficient incremental loading.

**Why other options are incorrect:**
- A) Checkpoint offset is associated with stream processing (e.g., Kafka, Event Hubs), not batch pipeline incremental loads.
- C) A partition key divides data for storage or distribution, not for tracking incremental changes.
- D) A delta token is used in some API-based change tracking (e.g., Microsoft Graph) but is not the general term for database incremental loading.

</details>

---

### Question 23

When a Dataflow Gen2 writes data to a Lakehouse table, the output is stored in Delta format by default. If the target table already exists, which write mode replaces all existing data with the new dataflow output?

- A) Append
- B) Replace (overwrite)
- C) Merge
- D) Delete

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Replace (overwrite)**

**Explanation:** The Replace (overwrite) mode in Dataflow Gen2 drops the existing data in the target table and replaces it entirely with the new output. This is useful for reference/dimension tables where a full refresh is acceptable.

**Why other options are incorrect:**
- A) Append adds new rows to the existing data without removing any existing rows.
- C) Merge combines new data with existing data based on key matching (upsert), but does not replace all data.
- D) Delete is not a standard Dataflow Gen2 output write mode.

</details>

---

### Question 24

A data engineer enables schema evolution on a Delta table. Later, a Spark job writes a DataFrame with two new columns that do not exist in the current table schema. What happens?

- A) The write fails with a schema mismatch error
- B) The new columns are automatically added to the table schema
- C) The new columns are silently dropped from the write
- D) The table is recreated from scratch with only the new columns

<details>
<summary>Show Answer</summary>

**Correct Answer: B) The new columns are automatically added to the table schema**

**Explanation:** With schema evolution enabled (e.g., `mergeSchema` option set to true), Delta Lake automatically adds new columns from the incoming data to the table schema. Existing columns remain unchanged, and existing rows will have null values for the new columns. This provides flexibility when source schemas change over time.

**Why other options are incorrect:**
- A) Schema enforcement (the default without schema evolution) would cause an error, but schema evolution explicitly allows new columns.
- C) Columns are not silently dropped — they are added to the schema.
- D) The table is not recreated; only the schema is evolved to include the new columns.

</details>

---

### Question 25

A data engineer runs `VACUUM` on a Delta table with a retention period of 7 days. What does this operation do?

- A) Compacts small files into larger files for better read performance
- B) Removes data files that are older than the retention period and no longer referenced by the transaction log
- C) Deletes all data from the table
- D) Updates table statistics for the query optimizer

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Removes data files that are older than the retention period and no longer referenced by the transaction log**

**Explanation:** `VACUUM` cleans up stale data files that are no longer needed by the current or recent versions of the table. Files older than the specified retention period (7 days in this case) that are not referenced by any transaction log entry are permanently deleted. This reclaims storage but limits time travel to the retention window.

**Why other options are incorrect:**
- A) File compaction is performed by `OPTIMIZE`, not `VACUUM`.
- C) VACUUM does not delete current data — it only removes old, unreferenced files.
- D) Updating statistics is done by `ANALYZE TABLE`, not `VACUUM`.

</details>

---

### Question 26

A data engineer needs to apply Z-ordering to a large Delta table on the `CustomerID` column to optimize point-lookup queries. Which command accomplishes this?

- A) `OPTIMIZE my_table ZORDER BY (CustomerID)`
- B) `ALTER TABLE my_table ADD INDEX ON (CustomerID)`
- C) `CREATE INDEX idx_customer ON my_table (CustomerID)`
- D) `VACUUM my_table ZORDER BY (CustomerID)`

<details>
<summary>Show Answer</summary>

**Correct Answer: A) `OPTIMIZE my_table ZORDER BY (CustomerID)`**

**Explanation:** Z-ordering is applied as part of the `OPTIMIZE` command in Delta Lake. It co-locates related data (rows with similar CustomerID values) within the same set of files, enabling data skipping during queries that filter on CustomerID. This significantly improves query performance for point lookups and range filters on the Z-ordered column.

**Why other options are incorrect:**
- B) `ALTER TABLE ADD INDEX` is not supported in Delta Lake / Fabric Lakehouse.
- C) `CREATE INDEX` is not supported in Delta Lake / Fabric Lakehouse — Z-order is the equivalent optimization.
- D) `VACUUM` removes old files and does not support Z-ordering.

</details>

---

### Question 27

A data engineer configures a Data Pipeline to run a sequence of activities: Copy Activity → Notebook → Stored Procedure. The Notebook activity should only run if the Copy Activity succeeds. Which dependency condition should be set?

- A) On Completion
- B) On Failure
- C) On Success
- D) On Skip

<details>
<summary>Show Answer</summary>

**Correct Answer: C) On Success**

**Explanation:** The "On Success" dependency condition ensures that the downstream activity (Notebook) only executes when the upstream activity (Copy Activity) completes successfully. This is the standard condition for sequential activities where each step depends on the successful completion of the previous step.

**Why other options are incorrect:**
- A) "On Completion" triggers regardless of success or failure, which could run the Notebook even when the Copy Activity fails.
- B) "On Failure" triggers only when the upstream activity fails, which is the opposite of the requirement.
- D) "On Skip" triggers when the upstream activity is skipped, which is not relevant to normal sequential execution.

</details>

---

### Question 28

A data pipeline needs to pass the current date as a parameter to a Notebook activity so that the notebook processes only today's data. How should this be implemented?

- A) Hard-code today's date in the notebook code
- B) Use a pipeline expression like `@utcnow()` to set the notebook's base parameter value
- C) Create a separate pipeline for each date
- D) Use a global variable that auto-updates

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use a pipeline expression like `@utcnow()` to set the notebook's base parameter value**

**Explanation:** Data Pipeline expressions support dynamic content using functions like `@utcnow()`. When configuring the Notebook activity's parameters, the pipeline expression evaluates at runtime and passes the current date to the notebook. This makes the pipeline fully dynamic without code changes.

**Why other options are incorrect:**
- A) Hard-coding the date requires manual updates and is not dynamic.
- C) Creating separate pipelines per date is operationally impractical and does not scale.
- D) Fabric Data Pipelines do not have auto-updating global variables — pipeline expressions serve this purpose.

</details>

---

### Question 29

A data engineer is loading data into a Fabric Warehouse and needs to ensure that duplicate records from the source are handled by updating existing rows and inserting new ones. Which T-SQL statement pattern should be used?

- A) `INSERT INTO ... SELECT` with DISTINCT
- B) `MERGE ... WHEN MATCHED THEN UPDATE WHEN NOT MATCHED THEN INSERT`
- C) `DELETE` then `INSERT`
- D) `UPDATE` then `INSERT` in separate statements

<details>
<summary>Show Answer</summary>

**Correct Answer: B) `MERGE ... WHEN MATCHED THEN UPDATE WHEN NOT MATCHED THEN INSERT`**

**Explanation:** The MERGE statement (also called an upsert) compares source and target data on a specified key. Matching rows are updated, and non-matching rows are inserted. This is the standard, atomic approach for handling duplicate records and ensuring data synchronization in a warehouse.

**Why other options are incorrect:**
- A) `INSERT ... SELECT DISTINCT` only prevents exact duplicates in the inserted set but does not update existing records.
- C) DELETE then INSERT would remove and re-insert all matched rows, which is less efficient and not atomic.
- D) Separate UPDATE and INSERT statements are not atomic and may have race conditions or missed records compared to MERGE.

</details>

---

### Question 30

A data modeler is designing a semantic model for a retail company. The `FactSales` table has millions of rows, and the dimension tables (`DimProduct`, `DimDate`, `DimStore`) have thousands of rows each. All relationships are one-to-many from dimension to fact. This design follows which modeling pattern?

- A) Snowflake schema
- B) Flat denormalized table
- C) Star schema
- D) Third normal form (3NF)

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Star schema**

**Explanation:** A star schema features a central fact table surrounded by denormalized dimension tables with one-to-many relationships from dimensions to facts. This design is the recommended pattern for Power BI / Fabric semantic models because it optimizes the VertiPaq engine's compression and query performance.

**Why other options are incorrect:**
- A) A snowflake schema would have normalized dimension tables (dimensions with sub-dimensions), which is not described here.
- B) A flat denormalized table would combine all data into a single table with no relationships.
- D) Third normal form eliminates all transitive dependencies and would have many more tables with complex relationships, which is not optimal for analytical models.

</details>

---

### Question 31

A DAX measure is defined as:

```dax
Profit Margin = DIVIDE(SUM(Sales[Profit]), SUM(Sales[Revenue]), 0)
```

What is the purpose of the third argument (`0`) in the DIVIDE function?

- A) It sets the minimum value for the result
- B) It specifies the number of decimal places
- C) It is the alternate result returned when the denominator is zero or blank, preventing division-by-zero errors
- D) It multiplies the result by zero

<details>
<summary>Show Answer</summary>

**Correct Answer: C) It is the alternate result returned when the denominator is zero or blank, preventing division-by-zero errors**

**Explanation:** The DIVIDE function in DAX takes three arguments: numerator, denominator, and an optional alternate result. When the denominator evaluates to zero or blank, DIVIDE returns the alternate result (0 in this case) instead of throwing an error. This is a best practice over using the `/` operator for division.

**Why other options are incorrect:**
- A) The third argument is not a minimum value constraint.
- B) The third argument does not control decimal formatting.
- D) The third argument is not a multiplier — it is a safe fallback value.

</details>

---

### Question 32

A report visual uses a measure that calls CALCULATE with multiple filter arguments:

```dax
High Value Sales = CALCULATE(
    SUM(Sales[Amount]),
    Sales[Amount] > 1000,
    Sales[Region] = "North"
)
```

How are the two filter arguments combined?

- A) They are combined with OR logic — either condition must be true
- B) They are combined with AND logic — both conditions must be true
- C) Only the first filter is applied; the second is ignored
- D) The function returns an error because multiple filters are not allowed

<details>
<summary>Show Answer</summary>

**Correct Answer: B) They are combined with AND logic — both conditions must be true**

**Explanation:** When multiple filter arguments are passed to CALCULATE, they are combined with AND logic. The resulting filter context requires all conditions to be true simultaneously. In this case, only sales where Amount > 1000 AND Region = "North" are included in the sum.

**Why other options are incorrect:**
- A) CALCULATE filter arguments use AND logic, not OR. To achieve OR logic, you would need to use a single filter expression with the `||` operator.
- C) All filter arguments are applied, not just the first one.
- D) Multiple filter arguments are fully supported in CALCULATE.

</details>

---

### Question 33

A data modeler wants to use **Direct Lake** mode but discovers that the semantic model occasionally falls back to DirectQuery. What is a common reason for this fallback?

- A) The Lakehouse is in a different workspace
- B) The Delta table has too many small files or the data exceeds the model's memory limits, causing Direct Lake to fail to load column segments
- C) Direct Lake does not support DAX measures
- D) The report has too many visuals

<details>
<summary>Show Answer</summary>

**Correct Answer: B) The Delta table has too many small files or the data exceeds the model's memory limits, causing Direct Lake to fail to load column segments**

**Explanation:** Direct Lake reads Delta/Parquet column segments into memory on demand. If the data files are too fragmented (many small files) or the data volume exceeds available memory, Direct Lake may not be able to load the required segments efficiently and falls back to DirectQuery mode to fulfill the query. Running `OPTIMIZE` on the Delta table and ensuring adequate capacity can mitigate this.

**Why other options are incorrect:**
- A) Direct Lake works across workspaces within the same Fabric tenant.
- C) Direct Lake fully supports DAX measures — measures are not a cause of fallback.
- D) The number of report visuals affects report rendering performance but does not cause Direct Lake to fall back.

</details>

---

### Question 34

A data engineer needs to choose between Import and DirectQuery mode for a semantic model. The source data changes infrequently (once per week), queries must be fast, and the dataset is 5 GB. Which mode is recommended?

- A) DirectQuery
- B) Import
- C) Direct Lake
- D) Live Connection

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Import**

**Explanation:** Import mode loads the entire dataset into the in-memory VertiPaq engine, providing the fastest query performance. Since the data only changes weekly (reducing refresh frequency concerns) and the dataset is only 5 GB (well within Import limits), Import mode is the optimal choice for maximum query speed.

**Why other options are incorrect:**
- A) DirectQuery sends queries to the source at query time, which is slower than in-memory Import — unnecessary when data freshness requirements are low.
- C) Direct Lake is specifically for OneLake Delta tables, and while it would work, Import mode provides the best performance guarantee for a 5 GB dataset with weekly updates.
- D) Live Connection connects to an existing remote model and does not allow local model modifications.

</details>

---

### Question 35

A security team mandates that all sales representatives should only see data for customers assigned to them. The `Sales` table has a `SalesRepEmail` column. Which DAX expression should be used in an RLS role to enforce this?

- A) `[SalesRepEmail] = "specific_email@company.com"`
- B) `[SalesRepEmail] = USERPRINCIPALNAME()`
- C) `[SalesRepEmail] = USERNAME()` only
- D) `FILTER(ALL(Sales), Sales[Amount] > 0)`

<details>
<summary>Show Answer</summary>

**Correct Answer: B) `[SalesRepEmail] = USERPRINCIPALNAME()`**

**Explanation:** `USERPRINCIPALNAME()` returns the email address (UPN) of the currently authenticated user. When used in an RLS role filter expression, it dynamically filters the Sales table so each sales representative sees only rows where SalesRepEmail matches their own identity. This is the standard dynamic RLS pattern.

**Why other options are incorrect:**
- A) Hard-coding a specific email creates a static rule for one user, not a dynamic per-user filter.
- C) `USERNAME()` can return domain\username format which may not match the email column; `USERPRINCIPALNAME()` is preferred for email-based filtering.
- D) This filter expression does not restrict data by user — it filters by amount, which is not a security constraint.

</details>

---

### Question 36

A data modeler configures Object-Level Security (OLS) to hide the `Salary` column in the `Employees` table from the "GeneralUsers" role. What happens when a user in that role tries to build a visual using the `Salary` column?

- A) The visual shows the Salary column with all values masked as "***"
- B) The Salary column is not visible in the field list and cannot be used in visuals or DAX queries
- C) The visual shows an error message for each row
- D) The Salary column shows null values for all rows

<details>
<summary>Show Answer</summary>

**Correct Answer: B) The Salary column is not visible in the field list and cannot be used in visuals or DAX queries**

**Explanation:** When OLS is applied to a column, that column is completely hidden from users in the restricted role. It does not appear in the field list, cannot be dragged onto visuals, and any DAX query referencing it returns an error. The column is effectively invisible and inaccessible to the restricted role.

**Why other options are incorrect:**
- A) OLS hides the column entirely — it does not mask values.
- C) The column is hidden from the field list, so users cannot build a visual using it in the first place.
- D) OLS does not show null values — it completely removes the column from the user's view.

</details>

---

### Question 37

A semantic model has a many-to-many relationship between `Students` and `Courses` through a bridge table `Enrollments`. The relationship `Students → Enrollments` is one-to-many, and `Courses → Enrollments` is one-to-many. To filter Courses based on a Student slicer, what relationship configuration is needed?

- A) Set both relationships to single cross-filter direction — filters will automatically propagate through the bridge table
- B) Set the relationship between Courses and Enrollments to bidirectional cross-filter direction
- C) Delete the bridge table and create a direct many-to-many relationship
- D) No additional configuration — many-to-many relationships always work automatically

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Set the relationship between Courses and Enrollments to bidirectional cross-filter direction**

**Explanation:** To propagate a filter from Students through Enrollments to Courses, the filter must travel from Students (one) → Enrollments (many) naturally, but then from Enrollments (many) → Courses (one) which requires the Courses-Enrollments relationship to have bidirectional cross-filtering enabled. Without this, the filter stops at Enrollments.

**Why other options are incorrect:**
- A) With single direction on both relationships, filters propagate from the "one" side to "many" side only — a filter from Students reaches Enrollments but cannot cross back to Courses.
- C) While Power BI supports direct many-to-many relationships, the scenario already has a proper bridge table design; enabling bidirectional filtering is the correct configuration.
- D) Additional configuration (bidirectional filtering) is required for filters to propagate through the bridge table to the other dimension.

</details>

---

### Question 38

A data modeler adds a DirectQuery table from an Azure SQL Database to an existing Import-mode semantic model. The model now contains both Import and DirectQuery tables. What is this model type called?

- A) Hybrid model
- B) Composite model
- C) Dual-storage model
- D) Mixed-mode model

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Composite model**

**Explanation:** A composite model is a semantic model that contains tables with different storage modes (Import, DirectQuery, or Dual). This allows modelers to keep frequently accessed data in fast in-memory storage (Import) while maintaining real-time connections to frequently changing data sources (DirectQuery).

**Why other options are incorrect:**
- A) "Hybrid model" is not the official Microsoft terminology for this feature.
- C) "Dual-storage model" refers to Dual mode (a specific storage mode), not the overall model type.
- D) "Mixed-mode model" is not the official term used by Microsoft.

</details>

---

### Question 39

A data modeler notices that a DAX measure returns different results when used in a calculated column compared to when used in a card visual. What concept explains this difference?

- A) Data type mismatch
- B) Context transition — the measure evaluates in different filter/row contexts
- C) Caching inconsistency
- D) Refresh timing difference

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Context transition — the measure evaluates in different filter/row contexts**

**Explanation:** A calculated column evaluates a measure in row context, where context transition converts each row's values into filters. A card visual evaluates the measure in the report's filter context (which may include slicer selections and visual-level filters). These different evaluation contexts can produce different results, which is a fundamental DAX concept.

**Why other options are incorrect:**
- A) Data type mismatch would cause an error, not different numeric results.
- C) Caching does not cause measures to return different values between visuals and calculated columns — context does.
- D) Calculated columns are computed at refresh time and card visuals at query time, but the result difference is due to context, not timing.

</details>

---

### Question 40

A semantic model has a large Sales table (200M rows) with incremental refresh configured. The policy defines a 2-year historical window and a 7-day incremental window. During a scheduled refresh, which data is actually reprocessed?

- A) All 200 million rows
- B) Only the rows from the last 7 days
- C) Only the rows from the last 2 years
- D) Only the rows added since the last successful refresh, regardless of the windows

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Only the rows from the last 7 days**

**Explanation:** Incremental refresh divides the table into time-based partitions. During a refresh, only the partitions within the incremental window (last 7 days) are refreshed. Historical partitions (the 2-year window) are retained but not reprocessed. This dramatically reduces refresh time and resource consumption.

**Why other options are incorrect:**
- A) Incremental refresh specifically avoids refreshing all rows — that is the full refresh pattern.
- C) The 2-year window defines the total retention period, not the refresh scope — only the incremental window is refreshed.
- D) The incremental window (7 days) is the unit of refresh, not individual rows since the last run.

</details>

---

### Question 41

A report designer wants to provide users with a way to toggle between "Sales by Region" and "Sales by Product" chart views on the same report page. Which Power BI feature is best suited for this?

- A) Drill-through
- B) Bookmarks with buttons
- C) Drill-down
- D) Cross-filtering

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Bookmarks with buttons**

**Explanation:** Bookmarks capture the state of a report page (visible visuals, filters, selections). By creating two bookmarks — one showing the Region chart and another showing the Product chart — and linking them to action buttons, users can toggle between the two views on the same page. This is the standard approach for creating interactive view-switching experiences.

**Why other options are incorrect:**
- A) Drill-through navigates to a different page filtered by a specific value, not toggling views on the same page.
- C) Drill-down expands within a hierarchy in a single visual, not switching between different chart configurations.
- D) Cross-filtering highlights related data across visuals but does not toggle between different chart layouts.

</details>

---

### Question 42

A data analyst writes the following T-SQL query against a Lakehouse SQL analytics endpoint:

```sql
SELECT ProductCategory, COUNT(*) AS OrderCount, SUM(Amount) AS TotalAmount
FROM Sales
WHERE OrderDate >= '2024-01-01'
GROUP BY ProductCategory
HAVING SUM(Amount) > 50000
ORDER BY TotalAmount DESC
```

What does the `HAVING` clause do?

- A) Filters individual rows before grouping
- B) Filters groups after aggregation, keeping only groups where the total amount exceeds 50,000
- C) Sorts the results by total amount
- D) Limits the number of groups returned

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Filters groups after aggregation, keeping only groups where the total amount exceeds 50,000**

**Explanation:** The `HAVING` clause filters the results after the `GROUP BY` aggregation has been applied. It evaluates the aggregate expression (`SUM(Amount) > 50000`) for each group and excludes groups that do not meet the condition. This is different from `WHERE`, which filters individual rows before grouping.

**Why other options are incorrect:**
- A) Row-level filtering before grouping is done by the `WHERE` clause, not `HAVING`.
- C) Sorting is done by `ORDER BY`, not `HAVING`.
- D) Limiting the number of results is done by `TOP`, not `HAVING`.

</details>

---

### Question 43

A real-time analytics scenario requires ingesting IoT sensor data streaming through Azure Event Hubs into Fabric for near-real-time dashboards. Which Fabric component should be used to ingest this streaming data?

- A) Dataflow Gen2
- B) Data Pipeline Copy Activity
- C) Eventstream
- D) Spark Structured Streaming only

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Eventstream**

**Explanation:** Eventstream is the Fabric component designed for ingesting and routing real-time streaming data from sources like Azure Event Hubs, IoT Hubs, and custom applications. It provides a visual interface for connecting streaming sources to Fabric destinations (Eventhouse, Lakehouse, etc.) with optional real-time transformations.

**Why other options are incorrect:**
- A) Dataflow Gen2 is designed for batch data transformation, not real-time streaming ingestion.
- B) Copy Activity is a batch data movement tool, not a streaming ingestion component.
- D) While Spark Structured Streaming can process streams, Eventstream is the dedicated and preferred Fabric component for streaming ingestion.

</details>

---

### Question 44

A data analyst uses the following DAX query in DAX query view to test a measure:

```dax
EVALUATE
SUMMARIZECOLUMNS(
    DimDate[Year],
    "Total Sales", [Total Sales Measure]
)
```

What does this query return?

- A) An error because EVALUATE is not valid DAX
- B) A table with each year and the corresponding total sales value
- C) A single scalar value
- D) The definition of the Total Sales Measure

<details>
<summary>Show Answer</summary>

**Correct Answer: B) A table with each year and the corresponding total sales value**

**Explanation:** `EVALUATE` is the DAX statement used to execute a table expression and return a result set. `SUMMARIZECOLUMNS` groups by `DimDate[Year]` and evaluates the `[Total Sales Measure]` for each year, producing a table with one row per year showing the corresponding total sales.

**Why other options are incorrect:**
- A) EVALUATE is valid and required DAX syntax for executing table queries.
- C) SUMMARIZECOLUMNS returns a table, not a scalar value.
- D) The query executes the measure, it does not return the measure's definition.

</details>

---

### Question 45

A development team has set up Fabric deployment pipelines with three stages: Development, Test, and Production. A data engineer deploys a semantic model from Development to Test. Which statement is true?

- A) The semantic model is moved from Development to Test — it no longer exists in Development
- B) A copy of the semantic model is deployed to the Test workspace, and the Development version remains unchanged
- C) Only the semantic model's metadata is deployed, not the data
- D) The deployment automatically triggers a data refresh in the Test workspace

<details>
<summary>Show Answer</summary>

**Correct Answer: B) A copy of the semantic model is deployed to the Test workspace, and the Development version remains unchanged**

**Explanation:** Deployment pipelines create a copy of the content in the target stage workspace. The source workspace (Development) retains its version of the semantic model unchanged. This ensures that development work continues uninterrupted while the deployed version is available for testing.

**Why other options are incorrect:**
- A) Content is copied, not moved — the Development version remains intact.
- C) Both the metadata and data configuration are deployed, not just metadata.
- D) Deployment does not automatically trigger a refresh — the admin may need to configure data source connections and trigger a refresh separately in the Test environment.

</details>

---

### Question 46

A development team integrates their Fabric workspace with an Azure DevOps Git repository. After making changes to a report in the Fabric workspace, the developer wants to save the changes to Git. What must the developer do?

- A) Export the report as a .pbix file and manually push it to Git
- B) Use the workspace's Git integration to commit the changes to the connected repository
- C) Create a deployment pipeline to push changes to Git
- D) Email the changes to the DevOps administrator

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use the workspace's Git integration to commit the changes to the connected repository**

**Explanation:** When a workspace is connected to a Git repository through Fabric's Git integration, developers can commit workspace changes directly to the repository from the Fabric interface. The integration serializes workspace items and pushes them to the connected Git branch, enabling version control and collaboration workflows.

**Why other options are incorrect:**
- A) Manual .pbix file management is not required when Git integration is configured — the integration handles serialization.
- C) Deployment pipelines promote content between stages, not to Git repositories.
- D) This is not a valid workflow in any professional development process.

</details>

---

### Question 47

A data governance team applies a "Highly Confidential" sensitivity label to a Fabric Lakehouse. A user with Viewer permissions tries to export data from the Lakehouse to a CSV file. What happens if the sensitivity label policy restricts exports?

- A) The export succeeds without any restrictions
- B) The export is blocked or restricted based on the sensitivity label policy
- C) The export succeeds but the CSV file is automatically encrypted
- D) The sensitivity label is removed from the exported file

<details>
<summary>Show Answer</summary>

**Correct Answer: B) The export is blocked or restricted based on the sensitivity label policy**

**Explanation:** Microsoft Purview sensitivity label policies can enforce protection actions including blocking exports, requiring encryption, or applying visual markings. If the "Highly Confidential" label policy restricts exports, the user's attempt to export data is blocked according to the policy configuration.

**Why other options are incorrect:**
- A) Sensitivity labels exist specifically to restrict actions like unprotected exports.
- C) While encryption is a possible policy action, the question states the policy restricts exports, which means blocking, not auto-encrypting.
- D) Sensitivity labels are designed to persist, not be removed during export.

</details>

---

### Question 48

A report consumer complains that a Power BI report connected to a semantic model is loading very slowly. The data engineer wants to identify which DAX queries are the slowest. Which tool provides query-level performance metrics for the semantic model?

- A) Power BI Performance Analyzer in the report
- B) Fabric capacity metrics app
- C) OneLake storage analytics
- D) Data Pipeline monitoring hub

<details>
<summary>Show Answer</summary>

**Correct Answer: A) Power BI Performance Analyzer in the report**

**Explanation:** Performance Analyzer is a built-in Power BI tool that records and displays the execution time of each visual's DAX query, direct queries, and other rendering operations. It allows the developer to identify which specific DAX queries are contributing most to the report's load time, enabling targeted optimization.

**Why other options are incorrect:**
- B) The capacity metrics app shows overall capacity utilization but does not break down individual DAX query times within a report.
- C) OneLake storage analytics tracks storage metrics, not query performance.
- D) The Data Pipeline monitoring hub tracks pipeline activity runs, not report query performance.

</details>

---

### Question 49

A finance team needs to generate a formatted report that renders one page per customer, with each page containing a detailed invoice table. The report must support export to PDF and printing. Which tool should be used?

- A) Power BI interactive report with a slicer
- B) Power BI paginated report
- C) Power BI dashboard
- D) Excel export from a semantic model

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Power BI paginated report**

**Explanation:** Paginated reports are designed for structured, multi-page documents where each page follows a precise layout. They support per-group page breaks (one page per customer), pixel-perfect formatting, page headers and footers, and clean export to PDF and print. This makes them ideal for invoices, statements, and other per-entity formatted documents.

**Why other options are incorrect:**
- A) Interactive reports are designed for on-screen exploration and do not natively support per-entity page breaks for PDF/print output.
- C) Dashboards are summary views of pinned tiles, not formatted document generators.
- D) Excel export provides raw data, not a formatted report with page-per-customer layout.

</details>

---

### Question 50

A data modeler creates measures in the published semantic model (using the web-based modeling interface) rather than in individual Power BI reports. What is the key advantage of this approach?

- A) Model-level measures execute faster than report-level measures
- B) Model-level measures are available to all reports connected to the semantic model, ensuring consistent calculations across the organization
- C) Model-level measures do not consume memory
- D) Model-level measures are automatically optimized by the Fabric engine

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Model-level measures are available to all reports connected to the semantic model, ensuring consistent calculations across the organization**

**Explanation:** Measures defined in the semantic model are centralized and shared across every report that connects to the model. This ensures that all reports use the same business logic and calculation definitions, promoting consistency and reducing the risk of conflicting metric definitions across different reports.

**Why other options are incorrect:**
- A) Model-level and report-level measures do not have inherent performance differences — they use the same DAX engine.
- C) All measures consume memory during query evaluation, regardless of where they are defined.
- D) The Fabric engine applies the same optimizations to all DAX measures regardless of their definition location.

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
