# 🔷 T-SQL Cheat Sheet for Fabric Warehouse & Lakehouse — DP-600 Exam Quick Reference

> **Quick reference guide** covering T-SQL syntax essential for Microsoft Fabric
> Warehouses, Lakehouses, and the SQL analytics endpoint — core knowledge for the
> Microsoft DP-600 exam.

---

## Table of Contents

1. [Data Types in Fabric Warehouses](#1-data-types-in-fabric-warehouses)
2. [DDL — Data Definition Language](#2-ddl--data-definition-language)
3. [DML — Data Manipulation Language](#3-dml--data-manipulation-language)
4. [Query Fundamentals](#4-query-fundamentals)
5. [JOINs](#5-joins)
6. [Aggregate Functions](#6-aggregate-functions)
7. [Window Functions](#7-window-functions)
8. [CTEs and Subqueries](#8-ctes-and-subqueries)
9. [String Functions](#9-string-functions)
10. [Date Functions](#10-date-functions)
11. [Fabric-Specific T-SQL](#11-fabric-specific-t-sql)

---

## 1. Data Types in Fabric Warehouses

> **Exam tip:** Fabric Warehouse uses a subset of SQL Server data types. Know which
> types are supported and their storage implications for performance tuning.

| Category | Type | Description |
|---|---|---|
| **Integer** | `INT` | 4-byte integer (–2.1 B to 2.1 B) |
| | `BIGINT` | 8-byte integer |
| | `SMALLINT` | 2-byte integer (–32 768 to 32 767) |
| | `TINYINT` | 1-byte unsigned (0–255) |
| **Decimal** | `DECIMAL(p, s)` | Fixed precision and scale |
| | `NUMERIC(p, s)` | Functionally identical to DECIMAL |
| | `FLOAT` | Approximate numeric, 8 bytes |
| | `REAL` | Approximate numeric, 4 bytes |
| **String** | `VARCHAR(n)` | Variable-length, up to 8 000 chars |
| | `VARCHAR(MAX)` | Variable-length, up to 2 GB |
| | `CHAR(n)` | Fixed-length string |
| **Unicode** | `NVARCHAR(n)` | Variable-length Unicode |
| | `NVARCHAR(MAX)` | Variable-length Unicode, up to 2 GB |
| **Date/Time** | `DATE` | Date only (YYYY-MM-DD) |
| | `DATETIME2(n)` | Date + time with fractional seconds |
| | `TIME` | Time only |
| | `DATETIMEOFFSET` | Date + time + time-zone offset |
| **Boolean** | `BIT` | 0, 1, or NULL |
| **Binary** | `VARBINARY(n)` | Variable-length binary data |
| **Unique ID** | `UNIQUEIDENTIFIER` | 16-byte GUID |

> **Key rule:** Fabric Warehouse does **not** support `DATETIME`, `MONEY`,
> `SMALLMONEY`, `TEXT`, `NTEXT`, `IMAGE`, or `SQL_VARIANT`. Use `DATETIME2`
> instead of `DATETIME`.

---

## 2. DDL — Data Definition Language

### CREATE SCHEMA

```sql
CREATE SCHEMA sales;
```

### CREATE TABLE

```sql
CREATE TABLE sales.orders (
    order_id        INT             NOT NULL,
    customer_id     INT             NOT NULL,
    order_date      DATE            NOT NULL,
    total_amount    DECIMAL(18, 2)  NOT NULL,
    status          VARCHAR(20)     DEFAULT 'Pending',
    created_at      DATETIME2       DEFAULT GETDATE()
);
```

> **Exam tip:** Fabric Warehouse tables are stored as Delta Parquet files. Primary
> keys and foreign keys are **informational only** — they are not enforced but can
> be used by the query optimizer.

### ALTER TABLE

```sql
-- Add a column
ALTER TABLE sales.orders ADD region VARCHAR(50);

-- Drop a column
ALTER TABLE sales.orders DROP COLUMN region;

-- Rename a column (Fabric-supported sp_rename)
EXEC sp_rename 'sales.orders.status', 'order_status', 'COLUMN';
```

### DROP TABLE

```sql
DROP TABLE IF EXISTS sales.orders;
```

### CREATE VIEW

```sql
CREATE VIEW sales.v_active_orders
AS
SELECT order_id, customer_id, order_date, total_amount
FROM   sales.orders
WHERE  order_status = 'Active';
```

> **Key rule:** Views in the SQL analytics endpoint are **read-only**. In Fabric
> Warehouse, views support full DML on the underlying tables.

### CREATE PROCEDURE

```sql
CREATE PROCEDURE sales.usp_get_orders_by_customer
    @customer_id INT
AS
BEGIN
    SELECT order_id, order_date, total_amount
    FROM   sales.orders
    WHERE  customer_id = @customer_id
    ORDER BY order_date DESC;
END;

-- Execute
EXEC sales.usp_get_orders_by_customer @customer_id = 1001;
```

### CREATE FUNCTION (Inline Table-Valued)

```sql
CREATE FUNCTION sales.fn_orders_after_date (
    @min_date DATE
)
RETURNS TABLE
AS
RETURN (
    SELECT order_id, customer_id, order_date, total_amount
    FROM   sales.orders
    WHERE  order_date >= @min_date
);

-- Usage
SELECT * FROM sales.fn_orders_after_date('2024-01-01');
```

---

## 3. DML — Data Manipulation Language

### INSERT

```sql
-- Single row
INSERT INTO sales.orders (order_id, customer_id, order_date, total_amount)
VALUES (1, 1001, '2024-06-15', 250.00);

-- Multiple rows
INSERT INTO sales.orders (order_id, customer_id, order_date, total_amount)
VALUES
    (2, 1002, '2024-06-16', 150.00),
    (3, 1003, '2024-06-17', 320.00);

-- Insert from query
INSERT INTO sales.orders (order_id, customer_id, order_date, total_amount)
SELECT order_id, customer_id, order_date, total_amount
FROM   staging.new_orders;
```

### UPDATE

```sql
UPDATE sales.orders
SET    order_status = 'Shipped',
       total_amount = total_amount * 1.1
WHERE  order_date < '2024-01-01'
  AND  order_status = 'Pending';
```

### DELETE

```sql
DELETE FROM sales.orders
WHERE  order_status = 'Cancelled'
  AND  order_date < '2023-01-01';
```

### MERGE (Upsert Pattern)

```sql
MERGE INTO sales.orders AS target
USING staging.new_orders  AS source
   ON target.order_id = source.order_id

WHEN MATCHED THEN
    UPDATE SET
        target.total_amount = source.total_amount,
        target.order_status = source.order_status

WHEN NOT MATCHED BY TARGET THEN
    INSERT (order_id, customer_id, order_date, total_amount, order_status)
    VALUES (source.order_id, source.customer_id, source.order_date,
            source.total_amount, source.order_status)

WHEN NOT MATCHED BY SOURCE THEN
    DELETE;
```

> **Exam tip:** MERGE is critical for **slowly changing dimension (SCD)** patterns
> and incremental data loading in Fabric pipelines. Know all three `WHEN` clauses.

### SELECT INTO

```sql
-- Creates a new table from query results
SELECT customer_id,
       COUNT(*)        AS order_count,
       SUM(total_amount) AS lifetime_value
INTO   sales.customer_summary
FROM   sales.orders
GROUP BY customer_id;
```

---

## 4. Query Fundamentals

### SELECT, WHERE, ORDER BY

```sql
SELECT   order_id,
         customer_id,
         order_date,
         total_amount
FROM     sales.orders
WHERE    total_amount > 100
  AND    order_date >= '2024-01-01'
ORDER BY order_date DESC, total_amount ASC;
```

### DISTINCT and TOP

```sql
-- Unique values
SELECT DISTINCT customer_id FROM sales.orders;

-- Top N rows
SELECT TOP 10 * FROM sales.orders ORDER BY total_amount DESC;

-- Top with ties
SELECT TOP 5 WITH TIES *
FROM   sales.orders
ORDER BY total_amount DESC;
```

### OFFSET-FETCH (Pagination)

```sql
SELECT   order_id, customer_id, total_amount
FROM     sales.orders
ORDER BY order_id
OFFSET   20 ROWS
FETCH NEXT 10 ROWS ONLY;    -- rows 21–30
```

### GROUP BY and HAVING

```sql
SELECT   customer_id,
         COUNT(*)            AS order_count,
         SUM(total_amount)   AS total_spent,
         AVG(total_amount)   AS avg_order
FROM     sales.orders
WHERE    order_date >= '2024-01-01'      -- filters rows BEFORE grouping
GROUP BY customer_id
HAVING   COUNT(*) >= 5                   -- filters groups AFTER grouping
ORDER BY total_spent DESC;
```

> **Key rule:** `WHERE` filters individual rows **before** aggregation.
> `HAVING` filters grouped results **after** aggregation.

### CASE Expression

```sql
SELECT order_id,
       total_amount,
       CASE
           WHEN total_amount >= 1000 THEN 'High'
           WHEN total_amount >= 500  THEN 'Medium'
           WHEN total_amount >= 100  THEN 'Low'
           ELSE 'Micro'
       END AS order_tier
FROM   sales.orders;
```

### NULL Handling

| Function | Description | Example |
|---|---|---|
| `IS NULL` | Test for NULL | `WHERE col IS NULL` |
| `IS NOT NULL` | Test for non-NULL | `WHERE col IS NOT NULL` |
| `ISNULL(expr, alt)` | Replace NULL with value | `ISNULL(discount, 0)` |
| `COALESCE(a, b, c)` | First non-NULL value | `COALESCE(phone, email, 'N/A')` |
| `NULLIF(a, b)` | NULL if a = b | `NULLIF(quantity, 0)` — avoids divide-by-zero |

---

## 5. JOINs

### JOIN Types at a Glance

| JOIN | Returns |
|---|---|
| `INNER JOIN` | Only rows that match in **both** tables |
| `LEFT JOIN` | All rows from the **left** table + matches from right |
| `RIGHT JOIN` | All rows from the **right** table + matches from left |
| `FULL OUTER JOIN` | All rows from **both** tables, NULLs where no match |
| `CROSS JOIN` | Cartesian product — every row × every row |

### INNER JOIN

```sql
SELECT o.order_id, c.customer_name, o.total_amount
FROM   sales.orders    AS o
INNER JOIN sales.customers AS c
    ON o.customer_id = c.customer_id;
```

### LEFT JOIN

```sql
-- All customers, even those with no orders
SELECT c.customer_name,
       COALESCE(SUM(o.total_amount), 0) AS total_spent
FROM   sales.customers AS c
LEFT JOIN sales.orders AS o
    ON c.customer_id = o.customer_id
GROUP BY c.customer_name;
```

### FULL OUTER JOIN

```sql
SELECT COALESCE(a.product_id, b.product_id) AS product_id,
       a.warehouse_qty,
       b.store_qty
FROM   inventory.warehouse AS a
FULL OUTER JOIN inventory.store AS b
    ON a.product_id = b.product_id;
```

### CROSS JOIN

```sql
-- Generate a row for every product × every month
SELECT p.product_name, m.month_date
FROM   dim.products AS p
CROSS JOIN dim.months AS m;
```

### Self Join

```sql
-- Find employees and their managers
SELECT e.employee_name, m.employee_name AS manager_name
FROM   hr.employees AS e
LEFT JOIN hr.employees AS m
    ON e.manager_id = m.employee_id;
```

> **Exam tip:** Know how to identify which JOIN type produces the correct result
> set when given a scenario. LEFT JOIN with a WHERE clause on the right table
> column (e.g., `WHERE b.id IS NULL`) finds **unmatched** rows.

---

## 6. Aggregate Functions

| Function | Description | Example |
|---|---|---|
| `COUNT(*)` | Count all rows (incl. NULLs) | `COUNT(*)` |
| `COUNT(col)` | Count non-NULL values | `COUNT(email)` |
| `COUNT(DISTINCT col)` | Count unique non-NULL values | `COUNT(DISTINCT customer_id)` |
| `SUM(col)` | Total of numeric column | `SUM(total_amount)` |
| `AVG(col)` | Average (ignores NULLs) | `AVG(total_amount)` |
| `MIN(col)` | Minimum value | `MIN(order_date)` |
| `MAX(col)` | Maximum value | `MAX(total_amount)` |
| `STRING_AGG(col, sep)` | Concatenate with separator | `STRING_AGG(product_name, ', ')` |

### STRING_AGG Example

```sql
SELECT customer_id,
       STRING_AGG(product_name, ', ')
           WITHIN GROUP (ORDER BY product_name) AS products_ordered
FROM   sales.order_items AS oi
JOIN   sales.products    AS p ON oi.product_id = p.product_id
GROUP BY customer_id;
```

---

## 7. Window Functions

> **Key rule:** Window functions compute a value for **each row** relative to a
> set of rows (the "window"), without collapsing rows like GROUP BY does.

### Syntax Pattern

```sql
FUNCTION_NAME(args) OVER (
    [PARTITION BY col1, col2]
    [ORDER BY col3 ASC|DESC]
    [ROWS|RANGE BETWEEN ... AND ...]
)
```

### Ranking Functions

| Function | Ties | Gaps | Description |
|---|---|---|---|
| `ROW_NUMBER()` | No tie handling | — | Unique sequential number per partition |
| `RANK()` | Same rank for ties | Yes | Gaps after ties (1, 2, 2, 4) |
| `DENSE_RANK()` | Same rank for ties | No | No gaps (1, 2, 2, 3) |
| `NTILE(n)` | — | — | Divides rows into n roughly equal buckets |

```sql
SELECT customer_id,
       order_date,
       total_amount,
       ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date DESC) AS rn,
       RANK()       OVER (PARTITION BY customer_id ORDER BY total_amount DESC) AS rnk,
       DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY total_amount DESC) AS drnk,
       NTILE(4)     OVER (ORDER BY total_amount DESC)                         AS quartile
FROM   sales.orders;
```

### Common Pattern — Latest Row per Group

```sql
WITH ranked AS (
    SELECT *,
           ROW_NUMBER() OVER (
               PARTITION BY customer_id
               ORDER BY order_date DESC
           ) AS rn
    FROM sales.orders
)
SELECT * FROM ranked WHERE rn = 1;
```

> **Exam tip:** `ROW_NUMBER()` with `PARTITION BY` is the standard pattern for
> deduplication and getting the "most recent" or "top N per group" rows.

### Offset Functions — LAG and LEAD

| Function | Description |
|---|---|
| `LAG(col, n, default)` | Value from **n** rows **before** current row |
| `LEAD(col, n, default)` | Value from **n** rows **after** current row |
| `FIRST_VALUE(col)` | First value in the window frame |
| `LAST_VALUE(col)` | Last value in the window frame |

```sql
SELECT order_date,
       total_amount,
       LAG(total_amount, 1, 0)  OVER (ORDER BY order_date) AS prev_amount,
       LEAD(total_amount, 1, 0) OVER (ORDER BY order_date) AS next_amount,
       total_amount - LAG(total_amount, 1, 0)
           OVER (ORDER BY order_date) AS change_from_prev
FROM   sales.orders;
```

### FIRST_VALUE / LAST_VALUE

```sql
SELECT customer_id,
       order_date,
       total_amount,
       FIRST_VALUE(total_amount) OVER (
           PARTITION BY customer_id ORDER BY order_date
           ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
       ) AS first_order_amount,
       LAST_VALUE(total_amount)  OVER (
           PARTITION BY customer_id ORDER BY order_date
           ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING
       ) AS last_order_amount
FROM   sales.orders;
```

> **Key rule:** `LAST_VALUE` requires an explicit frame ending at
> `UNBOUNDED FOLLOWING`; the default frame ends at `CURRENT ROW`, which makes
> `LAST_VALUE` return the current row's value.

### Running Aggregates

```sql
SELECT order_date,
       total_amount,
       SUM(total_amount)   OVER (ORDER BY order_date) AS running_total,
       AVG(total_amount)   OVER (ORDER BY order_date) AS running_avg,
       COUNT(*)            OVER (ORDER BY order_date) AS running_count,
       SUM(total_amount)   OVER (
           ORDER BY order_date
           ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
       ) AS rolling_7day_sum
FROM   sales.orders;
```

---

## 8. CTEs and Subqueries

### Common Table Expression (CTE)

```sql
WITH monthly_sales AS (
    SELECT YEAR(order_date)  AS yr,
           MONTH(order_date) AS mo,
           SUM(total_amount) AS monthly_total
    FROM   sales.orders
    GROUP BY YEAR(order_date), MONTH(order_date)
)
SELECT yr, mo, monthly_total,
       AVG(monthly_total) OVER (ORDER BY yr, mo
           ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS moving_avg_3mo
FROM   monthly_sales
ORDER BY yr, mo;
```

### Multiple CTEs

```sql
WITH customers_cte AS (
    SELECT customer_id, customer_name
    FROM   sales.customers
    WHERE  region = 'West'
),
orders_cte AS (
    SELECT customer_id,
           SUM(total_amount) AS total_spent
    FROM   sales.orders
    GROUP BY customer_id
)
SELECT c.customer_name, o.total_spent
FROM   customers_cte AS c
JOIN   orders_cte    AS o ON c.customer_id = o.customer_id
ORDER BY o.total_spent DESC;
```

### Recursive CTE

```sql
WITH RECURSIVE org_chart AS (
    -- Anchor member
    SELECT employee_id, employee_name, manager_id, 0 AS level
    FROM   hr.employees
    WHERE  manager_id IS NULL

    UNION ALL

    -- Recursive member
    SELECT e.employee_id, e.employee_name, e.manager_id, oc.level + 1
    FROM   hr.employees AS e
    JOIN   org_chart    AS oc ON e.manager_id = oc.employee_id
)
SELECT * FROM org_chart ORDER BY level, employee_name;
```

### Correlated Subquery

```sql
-- Customers whose latest order exceeds $500
SELECT c.customer_name
FROM   sales.customers AS c
WHERE  (
    SELECT MAX(o.total_amount)
    FROM   sales.orders AS o
    WHERE  o.customer_id = c.customer_id
) > 500;
```

### EXISTS / NOT EXISTS

```sql
-- Customers who have placed at least one order
SELECT c.customer_name
FROM   sales.customers AS c
WHERE  EXISTS (
    SELECT 1 FROM sales.orders AS o
    WHERE  o.customer_id = c.customer_id
);

-- Customers with no orders
SELECT c.customer_name
FROM   sales.customers AS c
WHERE  NOT EXISTS (
    SELECT 1 FROM sales.orders AS o
    WHERE  o.customer_id = c.customer_id
);
```

> **Exam tip:** `EXISTS` is generally more efficient than `IN` for large
> subquery result sets because it short-circuits on the first match.

---

## 9. String Functions

| Function | Description | Example | Result |
|---|---|---|---|
| `CONCAT(a, b, ...)` | Concatenate strings | `CONCAT('Hello', ' ', 'World')` | `Hello World` |
| `LEFT(str, n)` | First n characters | `LEFT('Fabric', 3)` | `Fab` |
| `RIGHT(str, n)` | Last n characters | `RIGHT('Fabric', 3)` | `ric` |
| `SUBSTRING(str, start, len)` | Extract substring | `SUBSTRING('Fabric', 2, 3)` | `abr` |
| `LEN(str)` | Length (no trailing spaces) | `LEN('Fabric')` | `6` |
| `CHARINDEX(find, str)` | Position of substring | `CHARINDEX('ri', 'Fabric')` | `4` |
| `REPLACE(str, old, new)` | Replace occurrences | `REPLACE('2024/06/15','/','-')` | `2024-06-15` |
| `TRIM(str)` | Remove leading/trailing spaces | `TRIM('  hi  ')` | `hi` |
| `LTRIM(str)` | Remove leading spaces | `LTRIM('  hi')` | `hi` |
| `RTRIM(str)` | Remove trailing spaces | `RTRIM('hi  ')` | `hi` |
| `UPPER(str)` | Uppercase | `UPPER('fabric')` | `FABRIC` |
| `LOWER(str)` | Lowercase | `LOWER('FABRIC')` | `fabric` |
| `FORMAT(value, fmt)` | Format as string | `FORMAT(1234.5, 'N2')` | `1,234.50` |
| `REVERSE(str)` | Reverse a string | `REVERSE('abc')` | `cba` |
| `STUFF(str, pos, len, new)` | Insert/replace at pos | `STUFF('abcdef', 3, 2, 'XY')` | `abXYef` |

### Practical Example

```sql
SELECT customer_id,
       UPPER(LEFT(first_name, 1)) + LOWER(SUBSTRING(first_name, 2, LEN(first_name))) AS formatted_name,
       CONCAT(city, ', ', state, ' ', zip_code) AS full_address
FROM   sales.customers;
```

---

## 10. Date Functions

| Function | Description | Example | Result |
|---|---|---|---|
| `GETDATE()` | Current date & time | `GETDATE()` | `2024-06-15 14:30:00` |
| `GETUTCDATE()` | Current UTC date & time | `GETUTCDATE()` | UTC equivalent |
| `DATEADD(part, n, date)` | Add interval to date | `DATEADD(MONTH, 3, '2024-01-15')` | `2024-04-15` |
| `DATEDIFF(part, start, end)` | Difference between dates | `DATEDIFF(DAY, '2024-01-01', '2024-03-01')` | `60` |
| `DATEPART(part, date)` | Extract part as integer | `DATEPART(QUARTER, '2024-06-15')` | `2` |
| `YEAR(date)` | Extract year | `YEAR('2024-06-15')` | `2024` |
| `MONTH(date)` | Extract month | `MONTH('2024-06-15')` | `6` |
| `DAY(date)` | Extract day | `DAY('2024-06-15')` | `15` |
| `EOMONTH(date)` | Last day of month | `EOMONTH('2024-06-15')` | `2024-06-30` |
| `EOMONTH(date, n)` | Last day of month ± n months | `EOMONTH('2024-06-15', -1)` | `2024-05-31` |
| `FORMAT(date, fmt)` | Format date as string | `FORMAT(GETDATE(), 'yyyy-MM-dd')` | `2024-06-15` |
| `CAST(expr AS type)` | Convert types | `CAST('2024-06-15' AS DATE)` | Date value |

### Date Part Abbreviations

| Part | Abbreviation |
|---|---|
| Year | `YEAR`, `yy`, `yyyy` |
| Quarter | `QUARTER`, `qq`, `q` |
| Month | `MONTH`, `mm`, `m` |
| Day | `DAY`, `dd`, `d` |
| Week | `WEEK`, `wk`, `ww` |
| Hour | `HOUR`, `hh` |
| Minute | `MINUTE`, `mi`, `n` |
| Second | `SECOND`, `ss`, `s` |

### Practical Example

```sql
SELECT order_id,
       order_date,
       YEAR(order_date)                              AS order_year,
       DATEPART(QUARTER, order_date)                  AS order_quarter,
       DATEDIFF(DAY, order_date, GETDATE())           AS days_since_order,
       DATEADD(DAY, 30, order_date)                   AS due_date,
       EOMONTH(order_date)                            AS month_end,
       FORMAT(order_date, 'MMMM dd, yyyy')            AS formatted_date
FROM   sales.orders;
```

---

## 11. Fabric-Specific T-SQL

### COPY INTO (Loading Data from External Storage)

> **Key rule:** `COPY INTO` is the **primary high-throughput ingestion command**
> for loading data into Fabric Warehouse tables from Azure Data Lake Storage (ADLS).

```sql
-- Load CSV from ADLS Gen2
COPY INTO sales.orders
FROM 'https://storageaccount.dfs.core.windows.net/container/orders/*.csv'
WITH (
    FILE_TYPE    = 'CSV',
    FIRSTROW     = 2,                  -- skip header row
    FIELDTERMINATOR = ',',
    ROWTERMINATOR   = '\n',
    CREDENTIAL = (IDENTITY = 'Shared Access Signature',
                  SECRET   = '<sas-token>')
);
```

```sql
-- Load Parquet files
COPY INTO sales.orders
FROM 'https://storageaccount.dfs.core.windows.net/container/orders/*.parquet'
WITH (
    FILE_TYPE = 'PARQUET'
);
```

| COPY INTO Option | Description |
|---|---|
| `FILE_TYPE` | `CSV`, `PARQUET` |
| `FIRSTROW` | Row number to start reading (skip headers) |
| `FIELDTERMINATOR` | Column delimiter for CSV |
| `ROWTERMINATOR` | Row delimiter for CSV |
| `FIELDQUOTE` | Quote character for CSV fields |
| `ENCODING` | File encoding (e.g., `UTF8`) |
| `CREDENTIAL` | Authentication method for external storage |

> **Exam tip:** `COPY INTO` supports wildcards (`*`) for loading multiple files
> at once. It is optimized for bulk loading and is faster than row-by-row INSERT.

### CREATE TABLE AS SELECT (CTAS)

```sql
-- Create a new table from a query result
CREATE TABLE sales.orders_2024
AS
SELECT *
FROM   sales.orders
WHERE  order_date >= '2024-01-01';
```

> **Key rule:** CTAS is a high-performance pattern in Fabric Warehouse for
> materializing query results. It creates the table and loads data in a single
> optimized operation — much faster than `SELECT INTO` for large datasets.

### Cross-Database Queries

```sql
-- Query across databases within the same Fabric workspace
SELECT w.order_id,
       w.total_amount,
       l.product_category
FROM   my_warehouse.sales.orders        AS w
JOIN   my_lakehouse.dbo.product_catalog  AS l
    ON w.product_id = l.product_id;
```

> **Exam tip:** Cross-database queries work within the same Fabric workspace. You
> reference other databases using **three-part naming**: `database.schema.table`.
> This is a key feature for joining Warehouse and Lakehouse data.

### SQL Analytics Endpoint

The **SQL analytics endpoint** provides a read-only T-SQL interface over Lakehouse
Delta tables. Key characteristics:

| Feature | Warehouse | SQL Analytics Endpoint |
|---|---|---|
| **DML** (INSERT, UPDATE, DELETE) | ✅ Yes | ❌ No (read-only) |
| **DDL** (CREATE TABLE) | ✅ Yes | ❌ No |
| **CREATE VIEW** | ✅ Yes | ✅ Yes |
| **CREATE FUNCTION** | ✅ Yes | ✅ Yes |
| **Cross-database queries** | ✅ Yes | ✅ Yes |
| **T-SQL SELECT** | ✅ Yes | ✅ Yes |
| **Security (column/row-level)** | ✅ Yes | ✅ Yes |
| **Storage format** | Delta Parquet | Delta Parquet |
| **Data modification** | T-SQL, pipelines | Spark, pipelines |

> **Exam tip:** The SQL analytics endpoint automatically reflects Delta tables
> created by Spark in the Lakehouse. You do **not** need to manually define tables
> — they appear automatically. You can add views, functions, and security on top.

### Dynamic Data Masking

```sql
-- Mask sensitive columns
ALTER TABLE sales.customers
ALTER COLUMN email ADD MASKED WITH (FUNCTION = 'email()');

ALTER TABLE sales.customers
ALTER COLUMN phone ADD MASKED WITH (FUNCTION = 'partial(0,"XXX-XXX-",4)');
```

### Row-Level Security (RLS)

```sql
-- Create a security predicate function
CREATE FUNCTION security.fn_region_filter (@region VARCHAR(50))
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN
    SELECT 1 AS access
    WHERE  @region = USER_NAME()
       OR  USER_NAME() = 'admin@contoso.com';

-- Apply the security policy
CREATE SECURITY POLICY security.region_policy
ADD FILTER PREDICATE security.fn_region_filter(region)
ON sales.orders
WITH (STATE = ON);
```

> **Key rule:** Row-level security and dynamic data masking are supported in
> **both** Fabric Warehouse and the SQL analytics endpoint. These are important
> for implementing data governance in the DP-600 exam context.

---

## Quick Reference — Logical Query Processing Order

Understanding the order in which SQL Server processes query clauses helps debug
unexpected results:

| Step | Clause | Purpose |
|---|---|---|
| 1 | `FROM` / `JOIN` | Identify source tables and join conditions |
| 2 | `WHERE` | Filter individual rows |
| 3 | `GROUP BY` | Group rows for aggregation |
| 4 | `HAVING` | Filter groups |
| 5 | `SELECT` | Evaluate expressions and aliases |
| 6 | `DISTINCT` | Remove duplicates |
| 7 | `ORDER BY` | Sort result set |
| 8 | `OFFSET-FETCH` / `TOP` | Limit rows returned |

> **Exam tip:** Column aliases defined in `SELECT` are **not available** in
> `WHERE` or `HAVING` — use a CTE or subquery to reference them.

---

> **Key Takeaway:** For the DP-600 exam, focus on CTAS, COPY INTO, cross-database
> queries, window functions (especially `ROW_NUMBER` for dedup), MERGE for
> incremental loads, and the differences between Fabric Warehouse and the SQL
> analytics endpoint. These are the most frequently tested T-SQL topics in the
> Fabric context.
