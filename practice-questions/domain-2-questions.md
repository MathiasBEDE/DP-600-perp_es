# Domain 2: Practice Questions — Prepare and Serve Data

> **50 Multiple-Choice Questions** covering Domain 2 of the DP-600 exam (40–45% weight).
> This is the largest domain — master these topics to maximize your score.
> Each question includes a detailed answer explanation.

---

### Question 1

A data engineer needs to ingest CSV files that arrive daily in an Azure Data Lake Storage Gen2 container into a Fabric Lakehouse. The files are up to 2 GB each and must land in the Bronze layer without transformation. Which approach is MOST efficient?

- A) Use a Dataflow Gen2 to read and write the CSV files
- B) Use a Copy Activity in a Data Factory pipeline
- C) Write a PySpark notebook to read and write the files
- D) Create a shortcut to the ADLS Gen2 container

<details>
<summary>Show Answer</summary>

**Correct Answer: D) Create a shortcut to the ADLS Gen2 container**

**Explanation:** Shortcuts in Microsoft Fabric provide a zero-copy, virtualized reference to external data sources. Since the requirement is to land data in the Bronze layer without transformation, a shortcut eliminates the need for data movement entirely, making it the most efficient option. The data remains in place while appearing as a native folder in the Lakehouse.

**Why other options are incorrect:**
- A) Dataflow Gen2 is designed for data transformation scenarios and adds unnecessary overhead for a simple no-transform ingestion.
- B) Copy Activity would physically move data, consuming compute and storage when no movement is actually needed.
- C) A PySpark notebook adds development and compute overhead that is unnecessary for a no-transform landing.

</details>

---

### Question 2

You are building a medallion architecture in Microsoft Fabric. Data flows from Bronze (raw) → Silver (cleansed) → Gold (aggregated). Which file format is used by default when writing data to Lakehouse tables?

- A) Parquet
- B) CSV
- C) Delta Lake (Parquet + transaction log)
- D) Avro

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Delta Lake (Parquet + transaction log)**

**Explanation:** Microsoft Fabric Lakehouses use Delta Lake as the default table format. Delta Lake stores data as Parquet files with an additional transaction log (_delta_log) that provides ACID transactions, schema enforcement, and time travel capabilities. All managed tables in a Fabric Lakehouse are Delta tables by default.

**Why other options are incorrect:**
- A) While the underlying data files are Parquet, the table format includes the Delta transaction log—plain Parquet alone does not capture the full picture.
- B) CSV is not a managed table format in the Lakehouse and lacks schema enforcement and transactional guarantees.
- D) Avro is not the default format for Lakehouse tables in Fabric.

</details>

---

### Question 3

A data engineer is configuring a Data Factory pipeline to copy data from an on-premises SQL Server to a Fabric Lakehouse. The on-premises network does not allow inbound connections. What must be installed to enable connectivity?

- A) A Fabric capacity in the same region as the SQL Server
- B) An on-premises data gateway
- C) A VPN connection to OneLake
- D) Azure ExpressRoute with private endpoints

<details>
<summary>Show Answer</summary>

**Correct Answer: B) An on-premises data gateway**

**Explanation:** An on-premises data gateway creates a secure outbound connection from the on-premises network to Microsoft Fabric, enabling data transfer without requiring inbound firewall rules. The gateway acts as a bridge, relaying data from the local SQL Server to the cloud through encrypted outbound connections.

**Why other options are incorrect:**
- A) Fabric capacity region placement does not resolve on-premises network connectivity requirements.
- C) OneLake does not support VPN connections as a native connectivity method for on-premises data sources.
- D) While ExpressRoute is a valid Azure connectivity option, it is not required and is far more complex and costly than an on-premises data gateway for this scenario.

</details>

---

### Question 4

You need to load 500 million rows from a staging area into a Fabric Warehouse table as quickly as possible. Which T-SQL statement provides the BEST bulk-load performance?

- A) INSERT INTO … SELECT
- B) COPY INTO
- C) BULK INSERT
- D) MERGE

<details>
<summary>Show Answer</summary>

**Correct Answer: B) COPY INTO**

**Explanation:** The COPY INTO statement is optimized for high-throughput bulk loading into Fabric Warehouse tables. It supports parallel reads from source files, efficient data distribution, and is the recommended method for loading large volumes of data. It outperforms row-by-row or set-based INSERT operations for bulk scenarios.

**Why other options are incorrect:**
- A) INSERT INTO … SELECT works but is not optimized for bulk loading at the scale of 500 million rows.
- C) BULK INSERT is a SQL Server feature that is not available in Fabric Warehouse.
- D) MERGE is designed for upsert operations (insert/update/delete) and is not optimal for pure bulk loading.

</details>

---

### Question 5

A retail company streams point-of-sale transactions into Microsoft Fabric in real time. They need to capture these events for downstream analytics. Which Fabric component should they use for real-time event ingestion?

- A) Dataflow Gen2
- B) Data Factory pipeline with a scheduled trigger
- C) Eventstream
- D) Power BI streaming dataset

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Eventstream**

**Explanation:** Eventstreams in Microsoft Fabric are purpose-built for capturing, transforming, and routing real-time event data. They integrate with sources like Azure Event Hubs, IoT Hub, and custom applications, making them the correct choice for streaming point-of-sale transactions into Fabric for downstream analytics.

**Why other options are incorrect:**
- A) Dataflow Gen2 is a batch-oriented ETL tool and does not support real-time streaming ingestion.
- B) A scheduled pipeline trigger runs at fixed intervals (e.g., every 15 minutes) and is not true real-time streaming.
- D) Power BI streaming datasets are limited to visualization scenarios and do not serve as a general-purpose event ingestion layer for analytics.

</details>

---

### Question 6

In a Fabric Lakehouse, you write data to the `Tables` section using a Spark notebook. Later, you notice the data also appears in the SQL analytics endpoint. Why?

- A) The SQL analytics endpoint caches all Spark outputs automatically
- B) Managed Delta tables in the Tables section are automatically registered in the SQL analytics endpoint
- C) You must manually register each table in the SQL analytics endpoint
- D) The data is duplicated by a background Copy Activity

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Managed Delta tables in the Tables section are automatically registered in the SQL analytics endpoint**

**Explanation:** When you write data as managed Delta tables in the Lakehouse Tables section, they are automatically discovered and exposed through the SQL analytics endpoint. This provides a read-only T-SQL query experience without requiring manual registration or data duplication. The SQL analytics endpoint simply references the same underlying Delta files.

**Why other options are incorrect:**
- A) The SQL analytics endpoint does not cache data—it reads directly from the Delta files stored in OneLake.
- C) Manual registration is not required for managed tables in the Tables section.
- D) No data duplication occurs; the SQL analytics endpoint reads from the same Delta Lake files.

</details>

---

### Question 7

A data engineer is designing a Dataflow Gen2 to cleanse and transform customer data before loading it into a Lakehouse table. Which query language does Dataflow Gen2 use for defining transformations?

- A) T-SQL
- B) Python
- C) Power Query M
- D) Spark SQL

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Power Query M**

**Explanation:** Dataflow Gen2 uses Power Query M as its transformation language, providing a low-code/no-code experience through the Power Query editor. This is the same engine and language used in Power BI dataflows and Power Query in Excel, making it familiar to a broad range of users. Transformations are defined visually or in M code.

**Why other options are incorrect:**
- A) T-SQL is used in Fabric Warehouse and the SQL analytics endpoint, not in Dataflow Gen2.
- B) Python is used in Spark notebooks, not in Dataflow Gen2 transformations.
- D) Spark SQL is used within Spark notebooks and Lakehouse queries, not in Dataflow Gen2.

</details>

---

### Question 8

You are loading data into a Fabric Warehouse using COPY INTO from Parquet files stored in ADLS Gen2. The Parquet files contain a column named `event_timestamp` that does not exist in the destination table. What happens when you run the COPY INTO statement?

- A) The statement succeeds and ignores the extra column
- B) The statement fails with a schema mismatch error
- C) The extra column is automatically added to the destination table
- D) The statement succeeds but loads NULL for all columns

<details>
<summary>Show Answer</summary>

**Correct Answer: A) The statement succeeds and ignores the extra column**

**Explanation:** COPY INTO in Fabric Warehouse maps source columns to destination columns by ordinal position (by default) or by name. Extra columns in the source that do not map to the destination table are ignored without raising an error. This behavior allows flexible loading from files that may contain additional fields.

**Why other options are incorrect:**
- B) COPY INTO does not enforce strict schema matching—extra source columns are simply skipped.
- C) COPY INTO does not alter the destination table schema; it only loads data into existing columns.
- D) The mapped columns are loaded correctly; only unmapped extra columns are ignored.

</details>

---

### Question 9

A data engineer needs to create a dimension table in a Fabric Warehouse that will be frequently joined with a large fact table. The dimension has approximately 50,000 rows. Which table distribution strategy should they choose?

- A) Hash distribution on the join key
- B) Round-robin distribution
- C) Replicated distribution
- D) Range partitioning on the join key

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Replicated distribution**

**Explanation:** For small dimension tables (generally under a few million rows), replicated distribution copies the entire table to every compute node. This eliminates data movement during joins with large fact tables, significantly improving query performance. With only 50,000 rows, the storage overhead of replication is negligible.

**Why other options are incorrect:**
- A) Hash distribution is better suited for large fact tables where co-locating rows on the join key reduces shuffle operations.
- B) Round-robin spreads rows evenly but requires data movement during joins since matching keys are not co-located.
- D) Range partitioning is a data organization strategy within a table, not a distribution strategy for cross-node placement.

</details>

---

### Question 10

You have a Delta table in your Lakehouse with daily partitions. Over time, many small files have accumulated in each partition due to frequent appends. Queries are becoming slow. What should you do?

- A) Drop and recreate the table
- B) Run the OPTIMIZE command on the Delta table
- C) Convert the table to Parquet format
- D) Increase the Spark cluster size

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Run the OPTIMIZE command on the Delta table**

**Explanation:** The OPTIMIZE command compacts small files into larger files, reducing the number of files that need to be read during queries. This is the standard solution for the "small file problem" in Delta Lake. It improves read performance without requiring a table rebuild and preserves the table's history and metadata.

**Why other options are incorrect:**
- A) Dropping and recreating the table is destructive and unnecessary when OPTIMIZE addresses the problem directly.
- C) Converting to Parquet would lose Delta Lake features like ACID transactions, time travel, and schema enforcement.
- D) Increasing cluster size adds cost without addressing the root cause of small file overhead.

</details>

---

### Question 11

In the medallion architecture, what is the PRIMARY purpose of the Silver layer?

- A) Store raw data exactly as ingested from source systems
- B) Store cleansed, validated, and deduplicated data conforming to an enterprise data model
- C) Store pre-aggregated data optimized for business reporting
- D) Archive historical data for compliance purposes

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Store cleansed, validated, and deduplicated data conforming to an enterprise data model**

**Explanation:** The Silver layer in the medallion architecture serves as the cleansed and conformed data layer. Data from the Bronze layer is validated, deduplicated, and transformed into a consistent enterprise data model. This layer provides a trusted, reliable source for further downstream processing into the Gold layer.

**Why other options are incorrect:**
- A) Storing raw data exactly as ingested describes the Bronze layer, not the Silver layer.
- C) Pre-aggregated data optimized for reporting describes the Gold layer.
- D) Archival for compliance is not the primary purpose of any specific medallion layer; retention policies span across layers.

</details>

---

### Question 12

A data engineer writes a PySpark notebook to ingest JSON files into a Lakehouse. The JSON files occasionally contain new fields that were not present in earlier files. The engineer wants the table to automatically accept these new fields. Which Spark option should they configure?

- A) `mergeSchema = true`
- B) `overwriteSchema = true`
- C) `enforceSchema = false`
- D) `autoSchema = true`

<details>
<summary>Show Answer</summary>

**Correct Answer: A) `mergeSchema = true`**

**Explanation:** Setting `mergeSchema` to `true` (or using `.option("mergeSchema", "true")`) tells Delta Lake to automatically merge the schema of new data with the existing table schema. New columns from incoming data are added to the table without overwriting the existing schema. This is the correct approach for evolving schemas.

**Why other options are incorrect:**
- B) `overwriteSchema = true` replaces the entire table schema with the new data's schema, which could remove existing columns.
- C) `enforceSchema = false` is not a valid Delta Lake option.
- D) `autoSchema = true` is not a recognized Delta Lake or Spark configuration option.

</details>

---

### Question 13

You need to build a pipeline in Data Factory that runs a series of activities: first copy raw data, then run a notebook to transform it, and finally refresh a semantic model. If the notebook fails, the pipeline should send an email notification. Which pipeline feature handles this conditional logic?

- A) Parameters
- B) Activity dependencies with failure conditions
- C) Variables
- D) ForEach loops

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Activity dependencies with failure conditions**

**Explanation:** Data Factory pipelines support activity dependencies where you configure the condition under which a downstream activity executes: Success, Failure, Completion, or Skipped. By setting the email notification activity to depend on the notebook activity with a "Failure" condition, the email is sent only when the notebook fails.

**Why other options are incorrect:**
- A) Parameters pass values into the pipeline but do not control conditional execution flow between activities.
- C) Variables store intermediate values during pipeline execution but do not define activity dependency conditions.
- D) ForEach loops iterate over collections of items and do not handle failure-based branching logic.

</details>

---

### Question 14

A data engineer creates a shortcut in a Fabric Lakehouse pointing to an Amazon S3 bucket. What happens to the data in S3 when the shortcut is created?

- A) The data is copied into OneLake for local access
- B) The data remains in S3; the shortcut provides a virtualized reference
- C) The data is moved from S3 to OneLake permanently
- D) Only metadata is copied; the data is cached in OneLake on first access

<details>
<summary>Show Answer</summary>

**Correct Answer: B) The data remains in S3; the shortcut provides a virtualized reference**

**Explanation:** Shortcuts in Fabric are pointers that create a virtualized reference to external storage locations such as Amazon S3, ADLS Gen2, or Google Cloud Storage. No data is copied or moved when a shortcut is created. Queries against the shortcut read data directly from the source location, making it a zero-copy integration.

**Why other options are incorrect:**
- A) Shortcuts do not copy data—they access it in place at the source location.
- C) Shortcuts never move data; the source data remains untouched.
- D) Shortcuts do not cache data on first access; each query reads from the source. OneLake caching may apply at the compute layer but is not a shortcut behavior.

</details>

---

### Question 15

You are running a COPY INTO command to load data into a Fabric Warehouse table. Which file formats are supported as source files? (Choose the BEST answer.)

- A) Parquet and CSV only
- B) Parquet, CSV, and JSON
- C) Parquet, CSV, JSON, and Avro
- D) Any format supported by Azure Data Factory

<details>
<summary>Show Answer</summary>

**Correct Answer: A) Parquet and CSV only**

**Explanation:** The COPY INTO statement in Fabric Warehouse supports Parquet and CSV (delimited text) as source file formats. These are the two formats that can be directly loaded using COPY INTO. For other formats, data must first be converted to a supported format using a notebook or Dataflow Gen2 before loading.

**Why other options are incorrect:**
- B) JSON is not a directly supported source format for COPY INTO in Fabric Warehouse.
- C) Avro is also not supported as a source format for COPY INTO.
- D) COPY INTO is a T-SQL statement with specific format support, not equivalent to Data Factory's broader format capabilities.

</details>

---

### Question 16

A Spark notebook reads a CSV file without headers and writes it to a Delta table. The engineer notices all columns are named `_c0`, `_c1`, `_c2`, etc. What should the engineer do to assign meaningful column names?

- A) Set `.option("header", "true")` when reading the CSV
- B) Use the `.toDF("col1", "col2", "col3")` method or define a schema
- C) Rename the columns in the SQL analytics endpoint after loading
- D) Edit the CSV file to add headers before ingesting

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use the `.toDF("col1", "col2", "col3")` method or define a schema**

**Explanation:** When a CSV file has no headers, Spark assigns default column names (_c0, _c1, etc.). The engineer can rename columns using `.toDF()` with the desired names or provide a predefined schema using `StructType` when reading the file. Both approaches assign meaningful column names during the read/transform step.

**Why other options are incorrect:**
- A) Setting `header=true` on a headerless CSV would incorrectly interpret the first data row as column names, losing data.
- C) The SQL analytics endpoint provides read-only access to Lakehouse tables and does not support renaming columns.
- D) Modifying source files is impractical and does not address the root problem programmatically.

</details>

---

### Question 17

Your organization requires that all data in the Gold layer of the Lakehouse be optimized for fast Power BI report performance. Which Fabric-specific optimization technique should you apply to Gold layer Delta tables?

- A) Z-ordering on all columns
- B) V-Order optimization
- C) Bloom filter indexing
- D) Columnar compression with gzip

<details>
<summary>Show Answer</summary>

**Correct Answer: B) V-Order optimization**

**Explanation:** V-Order is a write-time optimization specific to Microsoft Fabric that sorts and organizes data within Parquet files to maximize read performance for Power BI and other analytic engines. V-Order is enabled by default in Fabric and is specifically designed to accelerate query performance for BI workloads reading from OneLake.

**Why other options are incorrect:**
- A) Z-ordering on all columns would be counterproductive; Z-ordering is effective on a small number of frequently filtered columns, not all columns.
- C) Bloom filter indexing helps with point lookups but is not the primary Fabric-specific optimization for BI performance.
- D) Gzip is a general compression codec and not a Fabric-specific optimization technique for Power BI performance.

</details>

---

### Question 18

A pipeline needs to copy data from 20 different source tables in a SQL database to 20 corresponding Lakehouse tables. What is the MOST efficient pipeline design?

- A) Create 20 separate Copy Activities, one for each table
- B) Use a ForEach activity with a Copy Activity inside, iterating over a list of table names
- C) Create 20 separate pipelines and use a master pipeline to invoke them
- D) Use a single Copy Activity with a wildcard table name

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use a ForEach activity with a Copy Activity inside, iterating over a list of table names**

**Explanation:** The ForEach activity allows you to iterate over a collection (e.g., a list of table names) and execute a Copy Activity for each item. This pattern is concise, maintainable, and supports parallel execution within the loop. Adding a new table requires only updating the list, not modifying the pipeline structure.

**Why other options are incorrect:**
- A) Creating 20 individual Copy Activities results in a cluttered, hard-to-maintain pipeline design.
- C) Creating 20 separate pipelines adds unnecessary complexity and management overhead.
- D) Copy Activity does not support wildcard table names for copying multiple tables in a single activity.

</details>

---

### Question 19

You create a Delta table partitioned by `region` in a Fabric Lakehouse. A query filters on `order_date` but not on `region`. How does the partitioning affect this query?

- A) The query benefits from partition pruning on `order_date`
- B) The query scans all partitions, gaining no benefit from the partitioning scheme
- C) The query fails because it does not filter on the partition column
- D) Delta Lake automatically re-partitions the table based on the query filter

<details>
<summary>Show Answer</summary>

**Correct Answer: B) The query scans all partitions, gaining no benefit from the partitioning scheme**

**Explanation:** Partition pruning only occurs when the query filters on the partition column. Since the table is partitioned by `region` but the query filters on `order_date`, Spark cannot eliminate any partitions. All partition directories must be scanned, meaning the partitioning provides no performance benefit for this particular query pattern.

**Why other options are incorrect:**
- A) Partition pruning requires a filter on the partition column (`region`), not on `order_date`.
- C) Queries do not fail when they omit the partition column—they simply read all partitions.
- D) Delta Lake does not dynamically re-partition tables based on query patterns.

</details>

---

### Question 20

A data engineer needs to implement a slowly changing dimension (SCD) Type 2 in a Fabric Lakehouse using a notebook. Which Delta Lake operation is BEST suited for this?

- A) INSERT INTO
- B) UPDATE
- C) MERGE INTO
- D) DELETE followed by INSERT

<details>
<summary>Show Answer</summary>

**Correct Answer: C) MERGE INTO**

**Explanation:** MERGE INTO (also known as upsert) is the ideal operation for SCD Type 2 implementation. It allows you to match incoming records against existing records and, in a single atomic operation, insert new records, update existing records (e.g., set end dates), and insert new versions of changed records. This matches the SCD Type 2 pattern perfectly.

**Why other options are incorrect:**
- A) INSERT INTO only adds new rows and cannot update existing records to close out old dimension versions.
- B) UPDATE alone cannot insert new versioned rows for changed dimension records.
- D) DELETE followed by INSERT is not atomic, risks data loss during failure, and does not preserve historical versions required by SCD Type 2.

</details>

---

### Question 21

You are designing a streaming analytics solution. IoT sensors send telemetry to Azure Event Hubs. You need to process this data in near real-time and land it in a Lakehouse Delta table. Which Fabric feature should you use?

- A) A scheduled Data Factory pipeline running every minute
- B) An Eventstream connected to the Event Hub with a Lakehouse destination
- C) A Dataflow Gen2 with a refresh schedule of one minute
- D) A Power BI real-time dashboard connected to Event Hubs

<details>
<summary>Show Answer</summary>

**Correct Answer: B) An Eventstream connected to the Event Hub with a Lakehouse destination**

**Explanation:** Eventstreams natively connect to Azure Event Hubs and can route streaming data directly to a Lakehouse Delta table. This provides true near real-time processing with low latency, continuous ingestion, and optional in-stream transformations. It is the Fabric-native solution for streaming-to-Lakehouse scenarios.

**Why other options are incorrect:**
- A) A scheduled pipeline running every minute is micro-batch, not true streaming, and introduces unnecessary complexity and latency.
- C) Dataflow Gen2 does not support sub-minute refresh schedules and is not designed for streaming workloads.
- D) A Power BI real-time dashboard is a visualization tool, not a data ingestion mechanism for landing data in a Lakehouse.

</details>

---

### Question 22

A data engineer writes a Spark notebook that reads data from a Bronze table, applies transformations, and writes to a Silver table. The notebook should run every day at 6 AM. How should they schedule this?

- A) Use the Spark job scheduler built into the notebook
- B) Embed the notebook in a Data Factory pipeline and add a schedule trigger
- C) Use a Windows Task Scheduler on a VM
- D) Set up an Azure Logic App to call the notebook API

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Embed the notebook in a Data Factory pipeline and add a schedule trigger**

**Explanation:** The recommended approach in Fabric is to orchestrate notebook execution through Data Factory pipelines. By adding the notebook as a pipeline activity and configuring a schedule trigger for 6 AM daily, you get reliable scheduling, monitoring, retry logic, and integration with other pipeline activities—all within the Fabric ecosystem.

**Why other options are incorrect:**
- A) Fabric notebooks do not have a built-in Spark job scheduler for recurring execution.
- C) Using a Windows Task Scheduler on a VM introduces external infrastructure and management overhead outside of Fabric.
- D) Azure Logic Apps add unnecessary complexity when Fabric Data Factory provides native scheduling capabilities.

</details>

---

### Question 23

You have a Fabric Warehouse with a fact table containing 10 billion rows. The table is hash-distributed on `customer_id`. Analysts frequently run queries that filter by `order_date`. What should you add to improve query performance for date-filtered queries?

- A) Change the distribution to hash on `order_date`
- B) Add a clustered columnstore index on `order_date`
- C) Create a table partition on `order_date` (e.g., by month or year)
- D) Create a materialized view grouped by `order_date`

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Create a table partition on `order_date` (e.g., by month or year)**

**Explanation:** Partitioning the fact table by `order_date` enables partition elimination when queries filter on date ranges. This dramatically reduces the amount of data scanned for date-filtered queries. The hash distribution on `customer_id` is maintained for join performance, while partitioning optimizes for the common date filter pattern.

**Why other options are incorrect:**
- A) Changing distribution to `order_date` would hurt join performance on `customer_id` and does not provide the same pruning benefit as partitioning.
- B) Fabric Warehouse automatically uses columnstore storage; you cannot manually create clustered columnstore indexes.
- D) A materialized view could help specific aggregation queries but does not generally optimize arbitrary date-filtered queries as effectively as partitioning.

</details>

---

### Question 24

A Dataflow Gen2 is configured to load data into a Lakehouse table. During execution, it fails with an error indicating that the destination table's schema does not match the source data. What is the MOST likely cause?

- A) The Lakehouse is in read-only mode
- B) The Dataflow Gen2 output mapping has column name or data type mismatches with the existing table
- C) Dataflow Gen2 does not support writing to Lakehouse tables
- D) The Fabric capacity is paused

<details>
<summary>Show Answer</summary>

**Correct Answer: B) The Dataflow Gen2 output mapping has column name or data type mismatches with the existing table**

**Explanation:** When Dataflow Gen2 writes to an existing Lakehouse table, the output column names and data types must be compatible with the destination table schema. If column names differ or data types are incompatible (e.g., writing a string to an integer column), the operation fails with a schema mismatch error. The mapping in the Dataflow output step must be verified.

**Why other options are incorrect:**
- A) Lakehouse tables do not have a read-only mode that prevents Dataflow Gen2 writes.
- C) Dataflow Gen2 fully supports writing to Lakehouse tables as a destination.
- D) A paused capacity would prevent execution entirely, not produce a schema mismatch error.

</details>

---

### Question 25

In a Fabric Lakehouse, what is the difference between the `Tables` section and the `Files` section?

- A) Tables stores relational data; Files stores only images and documents
- B) Tables contains managed Delta tables queryable via SQL; Files contains unmanaged files in any format
- C) Tables and Files are aliases for the same storage location
- D) Tables is for structured data only; Files is for semi-structured data only

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Tables contains managed Delta tables queryable via SQL; Files contains unmanaged files in any format**

**Explanation:** The Tables section of a Lakehouse holds managed Delta tables that are automatically registered in the SQL analytics endpoint and can be queried using T-SQL. The Files section is a general-purpose storage area where you can store files in any format (CSV, JSON, Parquet, images, etc.) without them being treated as managed tables.

**Why other options are incorrect:**
- A) Files can contain any file type, not just images and documents; it includes CSV, JSON, Parquet, and more.
- C) Tables and Files are distinct storage areas within the Lakehouse with different behaviors and metadata management.
- D) Both sections can contain structured data; the distinction is about managed table registration, not data structure.

</details>

---

### Question 26

You need to enforce data quality rules on incoming data before it reaches the Silver layer. Which approach provides schema enforcement at the storage layer in a Fabric Lakehouse?

- A) Create validation rules in Power BI
- B) Rely on Delta Lake schema enforcement when writing to Delta tables
- C) Add CHECK constraints in the SQL analytics endpoint
- D) Use Azure Purview data quality rules

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Rely on Delta Lake schema enforcement when writing to Delta tables**

**Explanation:** Delta Lake enforces schema on write by default. When new data is written to a Delta table, it must match the table's schema in terms of column names, data types, and nullability. Records that violate the schema are rejected, preventing bad data from entering the table. This is the primary storage-layer schema enforcement mechanism in a Lakehouse.

**Why other options are incorrect:**
- A) Power BI validation rules operate at the reporting layer, not at the data storage layer.
- C) The SQL analytics endpoint is read-only for Lakehouse tables and does not support adding constraints.
- D) Azure Purview provides data governance and cataloging but does not enforce schema at the write level in a Lakehouse.

</details>

---

### Question 27

A data engineer uses a Spark notebook to write data to a Delta table with the `overwrite` mode. After the write completes, they realize the schema of the new data was different from the original table. What happened to the table?

- A) The write failed because the schema did not match
- B) The table was overwritten with the new data, but the original schema was preserved
- C) The table was overwritten with the new data and the original schema, unless `overwriteSchema` was set to true
- D) The table was dropped and recreated automatically

<details>
<summary>Show Answer</summary>

**Correct Answer: C) The table was overwritten with the new data and the original schema, unless `overwriteSchema` was set to true**

**Explanation:** In Delta Lake, when using `overwrite` mode, the new data must conform to the existing table schema by default. If the new data has a different schema, the write fails unless `.option("overwriteSchema", "true")` is specified. With `overwriteSchema` enabled, the table's schema is replaced by the new data's schema.

**Why other options are incorrect:**
- A) The write fails only if the schema differs AND `overwriteSchema` is not set—but the question states the write completed, implying the schema either matched or `overwriteSchema` was used.
- B) The schema is not automatically preserved if `overwriteSchema` is enabled; the new schema replaces the old one.
- D) Delta tables are not dropped and recreated; the overwrite mode replaces data within the existing table structure.

</details>

---

### Question 28

You have a Data Factory pipeline that copies data from a REST API. The API returns paginated results with a `nextLink` field in each response. How should you configure the Copy Activity to handle pagination?

- A) Use a ForEach loop to manually iterate through pages
- B) Configure the pagination rules in the Copy Activity's source settings
- C) Create multiple Copy Activities, one per page
- D) Write a custom notebook to handle pagination

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Configure the pagination rules in the Copy Activity's source settings**

**Explanation:** The Copy Activity in Data Factory supports built-in pagination rules for REST API sources. You can configure it to follow the `nextLink` (or similar pagination token) automatically, reading all pages until no more data is available. This eliminates the need for manual loop logic and keeps the pipeline design simple.

**Why other options are incorrect:**
- A) A ForEach loop adds complexity that is unnecessary when the Copy Activity has built-in pagination support.
- C) Creating a Copy Activity per page is impractical when the total number of pages is unknown or varies.
- D) A custom notebook is overkill when the Copy Activity natively supports REST API pagination.

</details>

---

### Question 29

A data engineer needs to query data across both a Fabric Lakehouse and a Fabric Warehouse within the same workspace. Which approach allows cross-database queries?

- A) Create shortcuts between the Lakehouse and Warehouse
- B) Use cross-database queries in the SQL analytics endpoint with three-part naming
- C) Export data from both to a shared CSV file
- D) This is not possible in Microsoft Fabric

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use cross-database queries in the SQL analytics endpoint with three-part naming**

**Explanation:** Microsoft Fabric supports cross-database queries using three-part naming (database.schema.table) in the SQL analytics endpoint. This allows you to join data from a Lakehouse's SQL analytics endpoint and a Warehouse in a single T-SQL query without moving or duplicating data.

**Why other options are incorrect:**
- A) Shortcuts reference external storage locations, not other Fabric items like Warehouses within the same workspace.
- C) Exporting to CSV is inefficient and loses the benefits of the analytical engines.
- D) Cross-database queries are fully supported in Fabric using three-part naming conventions.

</details>

---

### Question 30

When using the COPY INTO statement in Fabric Warehouse, which authentication method is used to access files in OneLake?

- A) Shared Access Signature (SAS) tokens
- B) Service principal with client secret
- C) Organizational identity (pass-through authentication)
- D) Storage account access keys

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Organizational identity (pass-through authentication)**

**Explanation:** When COPY INTO accesses files stored in OneLake, it uses the organizational identity (Microsoft Entra ID) of the user or service executing the command. Since OneLake is the native storage for Fabric, authentication is handled through Fabric's built-in identity management, not through external credentials like SAS tokens or access keys.

**Why other options are incorrect:**
- A) SAS tokens are an Azure Storage mechanism not used for OneLake-internal access in Fabric.
- B) Service principals with client secrets are not the default authentication method for COPY INTO accessing OneLake.
- D) Storage account access keys do not apply to OneLake, which uses Microsoft Entra ID-based authentication.

</details>

---

### Question 31

A data engineering team is building a Bronze layer in their Lakehouse. They receive data from 15 different source systems in various formats (CSV, JSON, Parquet, XML). What is the recommended approach for the Bronze layer?

- A) Transform all data into a single standardized format before storing in Bronze
- B) Store data in its original format with minimal or no transformation
- C) Only ingest data that passes data quality checks
- D) Store all data as CSV for universal compatibility

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Store data in its original format with minimal or no transformation**

**Explanation:** The Bronze layer in the medallion architecture is designed to capture raw data from source systems with minimal or no transformation. This preserves the original data fidelity, enables re-processing if transformation logic changes, and provides a complete audit trail. Data cleansing and standardization occur in the Silver layer.

**Why other options are incorrect:**
- A) Transforming data before storing in Bronze defeats the purpose of having a raw data layer for auditability and reprocessing.
- C) Filtering data by quality checks at the Bronze level risks losing source data that may be needed later for investigation or reprocessing.
- D) Converting all data to CSV would lose schema information from structured formats like Parquet and add unnecessary processing.

</details>

---

### Question 32

You have a Delta table that was accidentally updated with incorrect data 3 hours ago. You need to restore the table to its state before the bad update. Which Delta Lake feature enables this?

- A) Soft delete recovery
- B) Time travel using `VERSION AS OF` or `TIMESTAMP AS OF`
- C) OneLake recycle bin
- D) Backup and restore from Azure Backup

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Time travel using `VERSION AS OF` or `TIMESTAMP AS OF`**

**Explanation:** Delta Lake maintains a transaction log that records every change to the table. Time travel allows you to query or restore a table to any previous version using `RESTORE TABLE table_name TO VERSION AS OF <version>` or `RESTORE TABLE table_name TO TIMESTAMP AS OF <timestamp>`. This is the built-in mechanism for recovering from accidental data changes.

**Why other options are incorrect:**
- A) Delta Lake does not have a "soft delete recovery" feature; recovery is handled through time travel and the RESTORE command.
- C) OneLake does not provide a recycle bin feature for table-level recovery from bad updates.
- D) Azure Backup is not integrated with Delta Lake tables in Fabric for point-in-time recovery.

</details>

---

### Question 33

A data engineer writes a Spark notebook to process a 500 GB dataset. The notebook runs out of memory and fails. Which approach is MOST likely to resolve the issue without increasing cluster resources?

- A) Cache the entire dataset in memory using `.cache()`
- B) Repartition the data into more partitions and process incrementally
- C) Convert the data to CSV format for smaller file sizes
- D) Use `.collect()` to bring data into the driver and process it locally

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Repartition the data into more partitions and process incrementally**

**Explanation:** Repartitioning distributes the data across more partitions, reducing the memory required per partition during processing. Processing incrementally (e.g., by date range or partition) further reduces memory pressure. This approach works within existing cluster resources by optimizing how Spark distributes work across executors.

**Why other options are incorrect:**
- A) Caching the entire 500 GB dataset in memory would worsen the memory problem, not solve it.
- C) CSV files are typically larger than Parquet/Delta files due to lack of compression and columnar storage, making memory issues worse.
- D) Using `.collect()` brings the entire dataset to the driver node, which would immediately cause an out-of-memory error on the single driver.

</details>

---

### Question 34

You are creating a Data Factory pipeline that must execute different activities based on the value of a pipeline parameter. For example, if `source_type` is "SQL," run Copy Activity A; if "API," run Copy Activity B. Which pipeline construct should you use?

- A) ForEach activity
- B) If Condition activity (Switch or If)
- C) Lookup activity
- D) Set Variable activity

<details>
<summary>Show Answer</summary>

**Correct Answer: B) If Condition activity (Switch or If)**

**Explanation:** The If Condition activity evaluates a boolean expression and executes different activity branches based on the result (True/False). For multiple discrete values, the Switch activity can evaluate a parameter against multiple cases. Both constructs enable conditional execution paths based on pipeline parameter values.

**Why other options are incorrect:**
- A) ForEach iterates over collections and does not provide conditional branching based on parameter values.
- C) Lookup retrieves data from a source but does not implement conditional execution logic.
- D) Set Variable assigns values but does not control which activities execute based on conditions.

</details>

---

### Question 35

A data engineer needs to ensure that duplicate records are not inserted when a Spark notebook re-runs due to a pipeline retry. The source data contains a unique `transaction_id`. Which strategy prevents duplicates?

- A) Use `append` mode and rely on Delta Lake to detect duplicates automatically
- B) Use `MERGE INTO` with a match condition on `transaction_id`
- C) Use `overwrite` mode to replace all data on each run
- D) Add a unique constraint on `transaction_id` in the Delta table

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use `MERGE INTO` with a match condition on `transaction_id`**

**Explanation:** MERGE INTO performs an upsert operation: it inserts records when no match is found on `transaction_id` and updates (or skips) when a match exists. This makes the operation idempotent—re-running the notebook produces the same result without creating duplicate records. This is the standard pattern for handling retries in data pipelines.

**Why other options are incorrect:**
- A) Delta Lake does not automatically detect or prevent duplicate rows in `append` mode; it simply adds all incoming rows.
- C) `overwrite` mode replaces all existing data, which could cause data loss if the notebook processes only a subset of the total data.
- D) Delta Lake does not support unique constraints; uniqueness must be enforced through application logic like MERGE.

</details>

---

### Question 36

You need to create a view in a Fabric Warehouse that combines data from three tables and applies business logic. Analysts should be able to query this view for reporting. Which type of view should you create?

- A) A standard SQL view
- B) A materialized view
- C) An indexed view
- D) A temporary view

<details>
<summary>Show Answer</summary>

**Correct Answer: A) A standard SQL view**

**Explanation:** A standard SQL view in Fabric Warehouse encapsulates the join and business logic into a reusable, queryable object. Analysts can query the view as if it were a table. Standard views are the most straightforward way to abstract complex queries and present a simplified interface for reporting purposes.

**Why other options are incorrect:**
- B) While materialized views exist in Fabric Warehouse, the question asks for a general-purpose view for reporting; a standard view is sufficient and does not require additional storage or refresh management.
- C) Indexed views are a SQL Server concept not available in Fabric Warehouse.
- D) Temporary views are session-scoped and would not be available to other analysts.

</details>

---

### Question 37

A Delta table in your Lakehouse has accumulated old transaction log entries and data files from previous versions. The table has grown to consume excessive storage. What should you run to reclaim space?

- A) TRUNCATE TABLE
- B) VACUUM
- C) DROP TABLE and recreate
- D) DBCC SHRINKDATABASE

<details>
<summary>Show Answer</summary>

**Correct Answer: B) VACUUM**

**Explanation:** The VACUUM command removes old data files that are no longer referenced by the current version of the Delta table (beyond the configured retention period). This reclaims storage consumed by files from previous versions, deleted records, and compacted files that are no longer needed by the transaction log.

**Why other options are incorrect:**
- A) TRUNCATE TABLE removes all current data from the table but does not clean up historical file versions.
- C) Dropping and recreating the table is destructive, loses table history, and is unnecessary when VACUUM handles the cleanup.
- D) DBCC SHRINKDATABASE is a SQL Server command not available in Fabric Lakehouse or Spark.

</details>

---

### Question 38

An Eventstream in Fabric is receiving JSON events from Azure Event Hubs. You need to filter the stream to keep only events where `event_type = 'purchase'` before sending data to the Lakehouse. Where should you apply this filter?

- A) In the Event Hub itself using consumer groups
- B) In the Eventstream using an in-stream transformation
- C) In the Lakehouse after the data has landed
- D) In the source application before sending events

<details>
<summary>Show Answer</summary>

**Correct Answer: B) In the Eventstream using an in-stream transformation**

**Explanation:** Eventstreams support in-stream transformations that can filter, project, and aggregate data before it reaches the destination. Applying a filter for `event_type = 'purchase'` within the Eventstream reduces the volume of data written to the Lakehouse and avoids storing unwanted events, making the pipeline more efficient.

**Why other options are incorrect:**
- A) Event Hub consumer groups control which consumers read from the hub but do not support content-based filtering.
- C) Filtering after landing in the Lakehouse wastes storage and compute by first ingesting unwanted data.
- D) Modifying the source application may not be feasible and pushes data engineering logic outside the analytics platform.

</details>

---

### Question 39

You have a Spark notebook that reads from a Delta table and writes results to another Delta table. You want to ensure that both the read and write use a consistent snapshot of data. Which feature of Delta Lake provides this?

- A) Row-level locking
- B) ACID transactions with snapshot isolation
- C) Optimistic locking with pessimistic reads
- D) Read-write locks at the file level

<details>
<summary>Show Answer</summary>

**Correct Answer: B) ACID transactions with snapshot isolation**

**Explanation:** Delta Lake provides ACID transactions with snapshot isolation, meaning each read operation sees a consistent snapshot of the table as of the start of the transaction. Concurrent writers do not affect the reader's view. This ensures that the notebook reads a consistent state and writes results atomically, without interference from concurrent operations.

**Why other options are incorrect:**
- A) Delta Lake does not use row-level locking; it uses optimistic concurrency with file-level atomicity.
- C) Delta Lake uses optimistic concurrency control, not pessimistic reads.
- D) Delta Lake does not implement file-level read-write locks; consistency is ensured through the transaction log and snapshot isolation.

</details>

---

### Question 40

A data engineer needs to create a Gold layer table that pre-aggregates daily sales by product and store. This table will be refreshed nightly. Where should this table reside?

- A) In the Files section of the Lakehouse as a Parquet file
- B) In the Tables section of the Lakehouse as a managed Delta table
- C) In a separate Power BI dataset
- D) In an Azure SQL Database outside of Fabric

<details>
<summary>Show Answer</summary>

**Correct Answer: B) In the Tables section of the Lakehouse as a managed Delta table**

**Explanation:** Gold layer aggregated tables should be stored as managed Delta tables in the Lakehouse Tables section. This provides ACID transactions, schema enforcement, automatic registration in the SQL analytics endpoint, and seamless integration with Power BI through DirectLake mode. Managed Delta tables are the standard choice for Gold layer data in Fabric.

**Why other options are incorrect:**
- A) Parquet files in the Files section are not managed tables and lack SQL queryability, transaction support, and automatic endpoint registration.
- C) A Power BI dataset is a semantic model for reporting, not a storage layer for aggregated data.
- D) An external Azure SQL Database introduces unnecessary complexity and moves data outside the Fabric ecosystem.

</details>

---

### Question 41

You are designing a Fabric Warehouse schema for a star schema model. Which statement about Fabric Warehouse is TRUE?

- A) You must define primary and foreign key constraints for query optimization
- B) Primary and foreign key constraints are informational only and are not enforced
- C) Fabric Warehouse does not support any form of constraints
- D) Foreign key constraints are enforced but primary key constraints are not

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Primary and foreign key constraints are informational only and are not enforced**

**Explanation:** In Fabric Warehouse, primary key and foreign key constraints can be defined but they are informational (not enforced). The query optimizer may use these constraints to generate better execution plans, but the database engine does not prevent violations. Data integrity must be ensured through ETL logic rather than relying on constraint enforcement.

**Why other options are incorrect:**
- A) Constraints do not need to be defined for optimization, though they may help; they are not enforced regardless.
- C) Fabric Warehouse does support defining constraints, but they are informational only.
- D) Neither primary key nor foreign key constraints are enforced in Fabric Warehouse.

</details>

---

### Question 42

A notebook uses `spark.read.format("delta").load("/lakehouse/default/Tables/sales")` and the read takes over 10 minutes for a 50 GB table. The engineer suspects poor file organization. Which TWO actions would MOST improve read performance? (Choose the BEST single answer.)

- A) Run OPTIMIZE with ZORDER on the most commonly filtered columns
- B) Convert the table from Delta to Parquet
- C) Increase the number of Spark executors to 100
- D) Move the table to the Files section

<details>
<summary>Show Answer</summary>

**Correct Answer: A) Run OPTIMIZE with ZORDER on the most commonly filtered columns**

**Explanation:** OPTIMIZE compacts small files into larger ones, reducing file-level overhead. Adding ZORDER on frequently filtered columns co-locates related data within files, enabling data skipping during reads. Together, these improvements reduce the number of files and the amount of data scanned, dramatically improving read performance for filtered queries.

**Why other options are incorrect:**
- B) Converting to Parquet removes Delta Lake benefits (ACID transactions, time travel) and does not address file organization.
- C) Adding more executors may help with parallelism but does not fix the underlying small file or poor data layout problem.
- D) Moving to the Files section removes managed table features and does not improve file organization.

</details>

---

### Question 43

A data engineer creates a pipeline with the following activities in sequence: Lookup → ForEach → Copy Activity (inside ForEach) → Notebook. The Lookup returns 100 items. How many times does the Notebook activity execute?

- A) 1 time
- B) 100 times
- C) It depends on whether the Notebook is inside or outside the ForEach
- D) 0 times because Notebooks cannot follow ForEach activities

<details>
<summary>Show Answer</summary>

**Correct Answer: C) It depends on whether the Notebook is inside or outside the ForEach**

**Explanation:** If the Notebook activity is inside the ForEach loop, it executes once per iteration (100 times). If the Notebook is outside the ForEach loop (sequenced after it), it executes exactly once after all ForEach iterations complete. The question states "in sequence" but the key detail is the Notebook's placement relative to the ForEach scope.

**Why other options are incorrect:**
- A) The Notebook runs once only if it is positioned after (outside) the ForEach loop.
- B) The Notebook runs 100 times only if it is positioned inside the ForEach loop.
- D) Notebooks can absolutely follow ForEach activities in a pipeline; there is no such restriction.

</details>

---

### Question 44

You want to create a Lakehouse shortcut that points to data in another Lakehouse within the same Fabric workspace. What type of shortcut should you create?

- A) ADLS Gen2 shortcut
- B) Amazon S3 shortcut
- C) OneLake shortcut
- D) Dataverse shortcut

<details>
<summary>Show Answer</summary>

**Correct Answer: C) OneLake shortcut**

**Explanation:** OneLake shortcuts allow you to create references to data in other Fabric items (Lakehouses, Warehouses) within the same or different workspaces. Since the target is another Lakehouse in the same Fabric workspace, a OneLake shortcut is the appropriate type. It provides zero-copy access to the data without duplication.

**Why other options are incorrect:**
- A) ADLS Gen2 shortcuts are for external Azure Data Lake Storage Gen2 accounts, not for internal Fabric items.
- B) Amazon S3 shortcuts connect to AWS S3 buckets, not internal Fabric Lakehouses.
- D) Dataverse shortcuts connect to Dataverse tables, not Fabric Lakehouses.

</details>

---

### Question 45

A data quality check in the Silver layer reveals that 5% of incoming records have NULL values in a required `customer_id` column. The team wants to quarantine these bad records rather than discard them. What is the BEST approach?

- A) Use a Spark notebook to filter bad records into a separate quarantine Delta table and write good records to the Silver table
- B) Allow all records into the Silver table and flag bad ones with a column
- C) Reject the entire batch if any record has a NULL `customer_id`
- D) Replace NULL values with a default value like "UNKNOWN"

<details>
<summary>Show Answer</summary>

**Correct Answer: A) Use a Spark notebook to filter bad records into a separate quarantine Delta table and write good records to the Silver table**

**Explanation:** Separating bad records into a quarantine table preserves data quality in the Silver layer while retaining the problematic records for investigation and remediation. This pattern ensures the Silver layer contains only valid data while maintaining a full audit trail of rejected records. The quarantine table can be reviewed and records can be corrected and reprocessed.

**Why other options are incorrect:**
- B) Allowing bad records into the Silver table undermines the purpose of having a cleansed, trusted data layer.
- C) Rejecting the entire batch due to 5% bad records would discard 95% of valid data, which is unnecessarily aggressive.
- D) Replacing NULLs with default values masks data quality issues and may produce incorrect downstream analytics.

</details>

---

### Question 46

You need to write data from a Spark notebook to a Fabric Warehouse table. Which method is recommended?

- A) Use `spark.write.format("delta").save()` pointing to the Warehouse path
- B) Use the JDBC connector to write directly to the Warehouse
- C) Write to a staging Lakehouse table and use a COPY INTO or INSERT in the Warehouse
- D) Use `spark.write.format("sqldw")` with a Warehouse connection string

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Write to a staging Lakehouse table and use a COPY INTO or INSERT in the Warehouse**

**Explanation:** Spark notebooks in Fabric cannot write directly to Warehouse tables. The recommended pattern is to write the data to a staging Lakehouse Delta table first, then use T-SQL (COPY INTO or cross-database INSERT INTO … SELECT) to load the data into the Warehouse. This leverages each engine's strengths: Spark for transformation and T-SQL for Warehouse loading.

**Why other options are incorrect:**
- A) The `delta` format writer targets Lakehouse storage, not Warehouse tables, which have a different storage engine.
- B) JDBC connectivity to Fabric Warehouse from Spark notebooks is not the recommended pattern in Fabric.
- D) The `sqldw` format connector is designed for Azure Synapse dedicated SQL pools, not Fabric Warehouse.

</details>

---

### Question 47

A data engineer runs `DESCRIBE HISTORY` on a Delta table and sees 500 versions. Each version represents an hourly micro-batch write. They want to retain only the last 7 days of history (168 versions). How should they configure this?

- A) Set the Delta table property `delta.logRetentionDuration` to `7 days`
- B) Manually delete old entries from the `_delta_log` folder
- C) Run VACUUM with a retention of 168 hours
- D) Recreate the table and reload only the last 7 days of data

<details>
<summary>Show Answer</summary>

**Correct Answer: A) Set the Delta table property `delta.logRetentionDuration` to `7 days`**

**Explanation:** The `delta.logRetentionDuration` table property controls how long Delta Lake retains transaction log entries. Setting it to `7 days` means log entries older than 7 days will be cleaned up during checkpoint operations. Combined with VACUUM (which removes old data files), this controls both the log history and storage footprint.

**Why other options are incorrect:**
- B) Manually deleting from `_delta_log` can corrupt the table's transaction log and break consistency guarantees.
- C) VACUUM removes old data files but does not control transaction log retention; `logRetentionDuration` is needed for log cleanup.
- D) Recreating the table is destructive and unnecessary when Delta Lake provides built-in retention configuration.

</details>

---

### Question 48

You have a large fact table in a Fabric Lakehouse partitioned by `year` and `month`. A data engineer writes new data for January 2024. Which partitions are affected by this write?

- A) All partitions are rewritten
- B) Only the `year=2024/month=1` partition is written
- C) The entire table is locked during the write
- D) All partitions for `year=2024` are rewritten

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Only the `year=2024/month=1` partition is written**

**Explanation:** Delta Lake uses partition-level granularity for writes. When writing data for January 2024, only the partition directory `year=2024/month=1` is affected. Other partitions remain untouched. This is one of the key benefits of partitioning—it limits the scope of writes and enables concurrent reads on unaffected partitions.

**Why other options are incorrect:**
- A) Delta Lake does not rewrite all partitions; it only writes to the partitions that contain new or modified data.
- C) Delta Lake uses optimistic concurrency, not full table locks; other partitions can be read and written concurrently.
- D) Only the specific month partition is affected, not all partitions for the year.

</details>

---

### Question 49

A data engineer needs to schedule a Dataflow Gen2 to run every 4 hours. They want to ensure the Dataflow outputs are available in the Lakehouse for downstream pipelines. What is the BEST way to schedule and orchestrate this?

- A) Use the built-in Dataflow Gen2 refresh schedule set to every 4 hours
- B) Embed the Dataflow Gen2 in a Data Factory pipeline with a tumbling window trigger
- C) Use Power Automate to trigger the Dataflow Gen2 every 4 hours
- D) Manually refresh the Dataflow Gen2 from the Fabric portal

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Embed the Dataflow Gen2 in a Data Factory pipeline with a tumbling window trigger**

**Explanation:** Embedding the Dataflow Gen2 in a Data Factory pipeline provides orchestration capabilities including scheduling, dependency management, error handling, and integration with downstream activities. A tumbling window trigger runs the pipeline at fixed 4-hour intervals and ensures each window is processed exactly once, making it ideal for regular interval scheduling.

**Why other options are incorrect:**
- A) While Dataflow Gen2 supports built-in refresh schedules, embedding it in a pipeline provides better orchestration with downstream activities and error handling.
- C) Power Automate adds external dependency and does not integrate as tightly with Fabric's orchestration and monitoring capabilities.
- D) Manual refresh is not automated and does not meet the requirement for scheduled execution.

</details>

---

### Question 50

Your team is debating whether to implement a data transformation as a Dataflow Gen2 or a Spark notebook. The transformation involves joining two tables, applying 15 business rules, handling slowly changing dimensions, and writing to multiple output tables. Which option is BETTER and why?

- A) Dataflow Gen2, because it provides a visual interface that business users can maintain
- B) Spark notebook, because it provides full programmatic control needed for complex multi-output transformations
- C) Either option works equally well for this scenario
- D) Neither option works; this requires a stored procedure in the Warehouse

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Spark notebook, because it provides full programmatic control needed for complex multi-output transformations**

**Explanation:** The transformation requirements—complex joins, 15 business rules, SCD handling, and multiple output tables—demand the flexibility of a Spark notebook. Notebooks provide full programmatic control with PySpark or Scala, support complex logic with conditional branching and custom functions, and can write to multiple Delta tables within a single execution. Dataflow Gen2 is better suited for simpler, single-destination transformations.

**Why other options are incorrect:**
- A) While Dataflow Gen2 has a visual interface, it becomes unwieldy and hard to maintain with 15 business rules, SCD logic, and multiple outputs.
- C) The scenario heavily favors notebooks due to the complexity and multi-output requirements; the two options are not equal here.
- D) Stored procedures in the Warehouse can handle T-SQL transformations but are not the best tool for SCD handling and multi-table Lakehouse writes.

</details>

---
