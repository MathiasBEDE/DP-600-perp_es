# T-SQL Cheat Sheet para Fabric Warehouse y Lakehouse - referencia rapida DP-600

> **Guia de referencia rapida** que cubre la sintaxis T-SQL esencial para Microsoft Fabric
> Warehouses, Lakehouses y el SQL analytics endpoint: conocimiento base para el
> examen Microsoft DP-600.

---

## Tabla de contenido

1. [Tipos de datos en Fabric Warehouses](#1-tipos-de-datos-en-fabric-warehouses)
2. [DDL - Data Definition Language](#2-ddl---data-definition-language)
3. [DML - Data Manipulation Language](#3-dml---data-manipulation-language)
4. [Fundamentos de consulta](#4-fundamentos-de-consulta)
5. [JOINs](#5-joins)
6. [Funciones de agregacion](#6-funciones-de-agregacion)
7. [Window Functions](#7-window-functions)
8. [CTEs y Subqueries](#8-ctes-y-subqueries)
9. [Funciones de string](#9-funciones-de-string)
10. [Funciones de fecha](#10-funciones-de-fecha)
11. [T-SQL especifico de Fabric](#11-t-sql-especifico-de-fabric)

---

## 1. Tipos de datos en Fabric Warehouses

> **Exam tip:** Fabric Warehouse usa un subconjunto de los tipos de datos de SQL Server. Conoce que
> tipos son compatibles y sus implicaciones de almacenamiento para ajuste de rendimiento.

| Categoria | Tipo | Descripcion |
|---|---|---|
| **Integer** | `INT` | Entero de 4 bytes (-2.1 B a 2.1 B) |
| | `BIGINT` | Entero de 8 bytes |
| | `SMALLINT` | Entero de 2 bytes (-32 768 a 32 767) |
| | `TINYINT` | Entero sin signo de 1 byte (0-255) |
| **Decimal** | `DECIMAL(p, s)` | Precision y escala fijas |
| | `NUMERIC(p, s)` | Funcionalmente identico a DECIMAL |
| | `FLOAT` | Numerico aproximado, 8 bytes |
| | `REAL` | Numerico aproximado, 4 bytes |
| **String** | `VARCHAR(n)` | Longitud variable, hasta 8 000 caracteres |
| | `VARCHAR(MAX)` | Longitud variable, hasta 2 GB |
| | `CHAR(n)` | String de longitud fija |
| **Unicode** | `NVARCHAR(n)` | Unicode de longitud variable |
| | `NVARCHAR(MAX)` | Unicode de longitud variable, hasta 2 GB |
| **Date/Time** | `DATE` | Solo fecha (YYYY-MM-DD) |
| | `DATETIME2(n)` | Fecha + hora con segundos fraccionarios |
| | `TIME` | Solo hora |
| | `DATETIMEOFFSET` | Fecha + hora + desplazamiento de zona horaria |
| **Boolean** | `BIT` | 0, 1 o NULL |
| **Binary** | `VARBINARY(n)` | Datos binarios de longitud variable |
| **Unique ID** | `UNIQUEIDENTIFIER` | GUID de 16 bytes |

> **Regla clave:** Fabric Warehouse **no** admite `DATETIME`, `MONEY`,
> `SMALLMONEY`, `TEXT`, `NTEXT`, `IMAGE` ni `SQL_VARIANT`. Usa `DATETIME2`
> en lugar de `DATETIME`.

---

## 2. DDL - Data Definition Language

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

> **Exam tip:** Las tablas de Fabric Warehouse se almacenan como archivos Delta Parquet. Las primary
> keys y foreign keys son **solo informativas**; no se aplican, pero el query optimizer
> puede usarlas.

### ALTER TABLE

```sql
-- Agregar una columna
ALTER TABLE sales.orders ADD region VARCHAR(50);

-- Eliminar una columna
ALTER TABLE sales.orders DROP COLUMN region;

-- Renombrar una columna (sp_rename compatible con Fabric)
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

> **Regla clave:** Las views en el SQL analytics endpoint son **de solo lectura**. En Fabric
> Warehouse, las views admiten DML completo sobre las tablas subyacentes.

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

-- Ejecutar
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

-- Uso
SELECT * FROM sales.fn_orders_after_date('2024-01-01');
```

---

## 3. DML - Data Manipulation Language

### INSERT

```sql
-- Una sola fila
INSERT INTO sales.orders (order_id, customer_id, order_date, total_amount)
VALUES (1, 1001, '2024-06-15', 250.00);

-- Multiples filas
INSERT INTO sales.orders (order_id, customer_id, order_date, total_amount)
VALUES
    (2, 1002, '2024-06-16', 150.00),
    (3, 1003, '2024-06-17', 320.00);

-- Insertar desde una consulta
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

### MERGE (patron Upsert)

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

> **Exam tip:** MERGE es critico para patrones de **slowly changing dimension (SCD)**
> y carga incremental de datos en Fabric pipelines. Conoce las tres clausulas `WHEN`.

### SELECT INTO

```sql
-- Crea una tabla nueva a partir de resultados de consulta
SELECT customer_id,
       COUNT(*)        AS order_count,
       SUM(total_amount) AS lifetime_value
INTO   sales.customer_summary
FROM   sales.orders
GROUP BY customer_id;
```

---

## 4. Fundamentos de consulta

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

### DISTINCT y TOP

```sql
-- Valores unicos
SELECT DISTINCT customer_id FROM sales.orders;

-- Primeras N filas
SELECT TOP 10 * FROM sales.orders ORDER BY total_amount DESC;

-- TOP con empates
SELECT TOP 5 WITH TIES *
FROM   sales.orders
ORDER BY total_amount DESC;
```

### OFFSET-FETCH (paginacion)

```sql
SELECT   order_id, customer_id, total_amount
FROM     sales.orders
ORDER BY order_id
OFFSET   20 ROWS
FETCH NEXT 10 ROWS ONLY;    -- filas 21-30
```

### GROUP BY y HAVING

```sql
SELECT   customer_id,
         COUNT(*)            AS order_count,
         SUM(total_amount)   AS total_spent,
         AVG(total_amount)   AS avg_order
FROM     sales.orders
WHERE    order_date >= '2024-01-01'      -- filtra filas ANTES de agrupar
GROUP BY customer_id
HAVING   COUNT(*) >= 5                   -- filtra grupos DESPUES de agrupar
ORDER BY total_spent DESC;
```

> **Regla clave:** `WHERE` filtra filas individuales **antes** de la agregacion.
> `HAVING` filtra resultados agrupados **despues** de la agregacion.

### Expresion CASE

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

### Manejo de NULL

| Funcion | Descripcion | Ejemplo |
|---|---|---|
| `IS NULL` | Probar si es NULL | `WHERE col IS NULL` |
| `IS NOT NULL` | Probar si no es NULL | `WHERE col IS NOT NULL` |
| `ISNULL(expr, alt)` | Reemplazar NULL con valor | `ISNULL(discount, 0)` |
| `COALESCE(a, b, c)` | Primer valor no NULL | `COALESCE(phone, email, 'N/A')` |
| `NULLIF(a, b)` | NULL si a = b | `NULLIF(quantity, 0)`: evita division por cero |

---

## 5. JOINs

### Tipos de JOIN de un vistazo

| JOIN | Devuelve |
|---|---|
| `INNER JOIN` | Solo filas que coinciden en **ambas** tablas |
| `LEFT JOIN` | Todas las filas de la tabla **izquierda** + coincidencias de la derecha |
| `RIGHT JOIN` | Todas las filas de la tabla **derecha** + coincidencias de la izquierda |
| `FULL OUTER JOIN` | Todas las filas de **ambas** tablas, NULLs donde no hay coincidencia |
| `CROSS JOIN` | Producto cartesiano: cada fila por cada fila |

### INNER JOIN

```sql
SELECT o.order_id, c.customer_name, o.total_amount
FROM   sales.orders    AS o
INNER JOIN sales.customers AS c
    ON o.customer_id = c.customer_id;
```

### LEFT JOIN

```sql
-- Todos los clientes, incluso los que no tienen pedidos
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
-- Generar una fila por cada producto por cada mes
SELECT p.product_name, m.month_date
FROM   dim.products AS p
CROSS JOIN dim.months AS m;
```

### Self Join

```sql
-- Encontrar empleados y sus managers
SELECT e.employee_name, m.employee_name AS manager_name
FROM   hr.employees AS e
LEFT JOIN hr.employees AS m
    ON e.manager_id = m.employee_id;
```

> **Exam tip:** Conoce como identificar que tipo de JOIN produce el resultado correcto
> cuando se da un escenario. LEFT JOIN con una clausula WHERE sobre la tabla derecha
> (por ejemplo, `WHERE b.id IS NULL`) encuentra filas **sin coincidencia**.

---

## 6. Funciones de agregacion

| Funcion | Descripcion | Ejemplo |
|---|---|---|
| `COUNT(*)` | Contar todas las filas (incluye NULLs) | `COUNT(*)` |
| `COUNT(col)` | Contar valores no NULL | `COUNT(email)` |
| `COUNT(DISTINCT col)` | Contar valores unicos no NULL | `COUNT(DISTINCT customer_id)` |
| `SUM(col)` | Total de columna numerica | `SUM(total_amount)` |
| `AVG(col)` | Promedio (ignora NULLs) | `AVG(total_amount)` |
| `MIN(col)` | Valor minimo | `MIN(order_date)` |
| `MAX(col)` | Valor maximo | `MAX(total_amount)` |
| `STRING_AGG(col, sep)` | Concatenar con separador | `STRING_AGG(product_name, ', ')` |

### Ejemplo de STRING_AGG

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

> **Regla clave:** Las window functions calculan un valor para **cada fila** en relacion con un
> conjunto de filas (la "window"), sin colapsar filas como lo hace GROUP BY.

### Patron de sintaxis

```sql
FUNCTION_NAME(args) OVER (
    [PARTITION BY col1, col2]
    [ORDER BY col3 ASC|DESC]
    [ROWS|RANGE BETWEEN ... AND ...]
)
```

### Ranking Functions

| Funcion | Empates | Huecos | Descripcion |
|---|---|---|---|
| `ROW_NUMBER()` | No maneja empates | - | Numero secuencial unico por particion |
| `RANK()` | Mismo rank para empates | Si | Huecos despues de empates (1, 2, 2, 4) |
| `DENSE_RANK()` | Mismo rank para empates | No | Sin huecos (1, 2, 2, 3) |
| `NTILE(n)` | - | - | Divide filas en n grupos aproximadamente iguales |

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

### Patron comun - ultima fila por grupo

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

> **Exam tip:** `ROW_NUMBER()` con `PARTITION BY` es el patron estandar para
> deduplicacion y para obtener las filas "mas recientes" o "top N por grupo".

### Offset Functions - LAG y LEAD

| Funcion | Descripcion |
|---|---|
| `LAG(col, n, default)` | Valor de **n** filas **antes** de la fila actual |
| `LEAD(col, n, default)` | Valor de **n** filas **despues** de la fila actual |
| `FIRST_VALUE(col)` | Primer valor en el window frame |
| `LAST_VALUE(col)` | Ultimo valor en el window frame |

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

> **Regla clave:** `LAST_VALUE` requiere un frame explicito que termine en
> `UNBOUNDED FOLLOWING`; el frame predeterminado termina en `CURRENT ROW`, lo que hace que
> `LAST_VALUE` devuelva el valor de la fila actual.

### Agregados acumulados

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

## 8. CTEs y Subqueries

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

### Multiples CTEs

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
    -- Miembro ancla
    SELECT employee_id, employee_name, manager_id, 0 AS level
    FROM   hr.employees
    WHERE  manager_id IS NULL

    UNION ALL

    -- Miembro recursivo
    SELECT e.employee_id, e.employee_name, e.manager_id, oc.level + 1
    FROM   hr.employees AS e
    JOIN   org_chart    AS oc ON e.manager_id = oc.employee_id
)
SELECT * FROM org_chart ORDER BY level, employee_name;
```

### Correlated Subquery

```sql
-- Clientes cuyo pedido mas reciente supera $500
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
-- Clientes que han realizado al menos un pedido
SELECT c.customer_name
FROM   sales.customers AS c
WHERE  EXISTS (
    SELECT 1 FROM sales.orders AS o
    WHERE  o.customer_id = c.customer_id
);

-- Clientes sin pedidos
SELECT c.customer_name
FROM   sales.customers AS c
WHERE  NOT EXISTS (
    SELECT 1 FROM sales.orders AS o
    WHERE  o.customer_id = c.customer_id
);
```

> **Exam tip:** `EXISTS` generalmente es mas eficiente que `IN` para conjuntos de resultados
> grandes en subqueries porque hace short-circuit en la primera coincidencia.

---

## 9. Funciones de string

| Funcion | Descripcion | Ejemplo | Resultado |
|---|---|---|---|
| `CONCAT(a, b, ...)` | Concatenar strings | `CONCAT('Hello', ' ', 'World')` | `Hello World` |
| `LEFT(str, n)` | Primeros n caracteres | `LEFT('Fabric', 3)` | `Fab` |
| `RIGHT(str, n)` | Ultimos n caracteres | `RIGHT('Fabric', 3)` | `ric` |
| `SUBSTRING(str, start, len)` | Extraer substring | `SUBSTRING('Fabric', 2, 3)` | `abr` |
| `LEN(str)` | Longitud (sin espacios finales) | `LEN('Fabric')` | `6` |
| `CHARINDEX(find, str)` | Posicion de substring | `CHARINDEX('ri', 'Fabric')` | `4` |
| `REPLACE(str, old, new)` | Reemplazar ocurrencias | `REPLACE('2024/06/15','/','-')` | `2024-06-15` |
| `TRIM(str)` | Quitar espacios iniciales/finales | `TRIM('  hi  ')` | `hi` |
| `LTRIM(str)` | Quitar espacios iniciales | `LTRIM('  hi')` | `hi` |
| `RTRIM(str)` | Quitar espacios finales | `RTRIM('hi  ')` | `hi` |
| `UPPER(str)` | Mayusculas | `UPPER('fabric')` | `FABRIC` |
| `LOWER(str)` | Minusculas | `LOWER('FABRIC')` | `fabric` |
| `FORMAT(value, fmt)` | Formatear como string | `FORMAT(1234.5, 'N2')` | `1,234.50` |
| `REVERSE(str)` | Invertir un string | `REVERSE('abc')` | `cba` |
| `STUFF(str, pos, len, new)` | Insertar/reemplazar en pos | `STUFF('abcdef', 3, 2, 'XY')` | `abXYef` |

### Ejemplo practico

```sql
SELECT customer_id,
       UPPER(LEFT(first_name, 1)) + LOWER(SUBSTRING(first_name, 2, LEN(first_name))) AS formatted_name,
       CONCAT(city, ', ', state, ' ', zip_code) AS full_address
FROM   sales.customers;
```

---

## 10. Funciones de fecha

| Funcion | Descripcion | Ejemplo | Resultado |
|---|---|---|---|
| `GETDATE()` | Fecha y hora actuales | `GETDATE()` | `2024-06-15 14:30:00` |
| `GETUTCDATE()` | Fecha y hora UTC actuales | `GETUTCDATE()` | Equivalente UTC |
| `DATEADD(part, n, date)` | Agregar intervalo a fecha | `DATEADD(MONTH, 3, '2024-01-15')` | `2024-04-15` |
| `DATEDIFF(part, start, end)` | Diferencia entre fechas | `DATEDIFF(DAY, '2024-01-01', '2024-03-01')` | `60` |
| `DATEPART(part, date)` | Extraer parte como entero | `DATEPART(QUARTER, '2024-06-15')` | `2` |
| `YEAR(date)` | Extraer anio | `YEAR('2024-06-15')` | `2024` |
| `MONTH(date)` | Extraer mes | `MONTH('2024-06-15')` | `6` |
| `DAY(date)` | Extraer dia | `DAY('2024-06-15')` | `15` |
| `EOMONTH(date)` | Ultimo dia del mes | `EOMONTH('2024-06-15')` | `2024-06-30` |
| `EOMONTH(date, n)` | Ultimo dia del mes +/- n meses | `EOMONTH('2024-06-15', -1)` | `2024-05-31` |
| `FORMAT(date, fmt)` | Formatear fecha como string | `FORMAT(GETDATE(), 'yyyy-MM-dd')` | `2024-06-15` |
| `CAST(expr AS type)` | Convertir tipos | `CAST('2024-06-15' AS DATE)` | Valor de fecha |

### Abreviaturas de date part

| Parte | Abreviatura |
|---|---|
| Year | `YEAR`, `yy`, `yyyy` |
| Quarter | `QUARTER`, `qq`, `q` |
| Month | `MONTH`, `mm`, `m` |
| Day | `DAY`, `dd`, `d` |
| Week | `WEEK`, `wk`, `ww` |
| Hour | `HOUR`, `hh` |
| Minute | `MINUTE`, `mi`, `n` |
| Second | `SECOND`, `ss`, `s` |

### Ejemplo practico

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

## 11. T-SQL especifico de Fabric

### COPY INTO (cargar datos desde almacenamiento externo)

> **Regla clave:** `COPY INTO` es el **comando principal de ingesta de alto rendimiento**
> para cargar datos en tablas de Fabric Warehouse desde Azure Data Lake Storage (ADLS).

```sql
-- Cargar CSV desde ADLS Gen2
COPY INTO sales.orders
FROM 'https://storageaccount.dfs.core.windows.net/container/orders/*.csv'
WITH (
    FILE_TYPE    = 'CSV',
    FIRSTROW     = 2,                  -- omitir fila de encabezado
    FIELDTERMINATOR = ',',
    ROWTERMINATOR   = '\n',
    CREDENTIAL = (IDENTITY = 'Shared Access Signature',
                  SECRET   = '<sas-token>')
);
```

```sql
-- Cargar archivos Parquet
COPY INTO sales.orders
FROM 'https://storageaccount.dfs.core.windows.net/container/orders/*.parquet'
WITH (
    FILE_TYPE = 'PARQUET'
);
```

| Opcion de COPY INTO | Descripcion |
|---|---|
| `FILE_TYPE` | `CSV`, `PARQUET` |
| `FIRSTROW` | Numero de fila desde donde empezar a leer (omitir headers) |
| `FIELDTERMINATOR` | Delimitador de columnas para CSV |
| `ROWTERMINATOR` | Delimitador de filas para CSV |
| `FIELDQUOTE` | Caracter de comillas para campos CSV |
| `ENCODING` | Codificacion de archivo (por ejemplo, `UTF8`) |
| `CREDENTIAL` | Metodo de autenticacion para almacenamiento externo |

> **Exam tip:** `COPY INTO` admite comodines (`*`) para cargar varios archivos
> a la vez. Esta optimizado para cargas masivas y es mas rapido que INSERT fila por fila.

### CREATE TABLE AS SELECT (CTAS)

```sql
-- Crear una tabla nueva desde el resultado de una consulta
CREATE TABLE sales.orders_2024
AS
SELECT *
FROM   sales.orders
WHERE  order_date >= '2024-01-01';
```

> **Regla clave:** CTAS es un patron de alto rendimiento en Fabric Warehouse para
> materializar resultados de consulta. Crea la tabla y carga datos en una sola
> operacion optimizada, mucho mas rapido que `SELECT INTO` para datasets grandes.

### Cross-Database Queries

```sql
-- Consultar entre bases de datos dentro del mismo Fabric workspace
SELECT w.order_id,
       w.total_amount,
       l.product_category
FROM   my_warehouse.sales.orders        AS w
JOIN   my_lakehouse.dbo.product_catalog  AS l
    ON w.product_id = l.product_id;
```

> **Exam tip:** Las cross-database queries funcionan dentro del mismo Fabric workspace. Referencias
> otras bases de datos usando **nombres de tres partes**: `database.schema.table`.
> Esta es una caracteristica clave para unir datos de Warehouse y Lakehouse.

### SQL Analytics Endpoint

El **SQL analytics endpoint** proporciona una interfaz T-SQL de solo lectura sobre tablas Delta
de Lakehouse. Caracteristicas clave:

| Caracteristica | Warehouse | SQL Analytics Endpoint |
|---|---|---|
| **DML** (INSERT, UPDATE, DELETE) | Si | No (solo lectura) |
| **DDL** (CREATE TABLE) | Si | No |
| **CREATE VIEW** | Si | Si |
| **CREATE FUNCTION** | Si | Si |
| **Cross-database queries** | Si | Si |
| **T-SQL SELECT** | Si | Si |
| **Security (column/row-level)** | Si | Si |
| **Formato de almacenamiento** | Delta Parquet | Delta Parquet |
| **Modificacion de datos** | T-SQL, pipelines | Spark, pipelines |

> **Exam tip:** El SQL analytics endpoint refleja automaticamente las tablas Delta
> creadas por Spark en el Lakehouse. **No** necesitas definir tablas manualmente:
> aparecen automaticamente. Puedes agregar views, functions y seguridad encima.

### Dynamic Data Masking

```sql
-- Enmascarar columnas sensibles
ALTER TABLE sales.customers
ALTER COLUMN email ADD MASKED WITH (FUNCTION = 'email()');

ALTER TABLE sales.customers
ALTER COLUMN phone ADD MASKED WITH (FUNCTION = 'partial(0,"XXX-XXX-",4)');
```

### Row-Level Security (RLS)

```sql
-- Crear una funcion de predicado de seguridad
CREATE FUNCTION security.fn_region_filter (@region VARCHAR(50))
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN
    SELECT 1 AS access
    WHERE  @region = USER_NAME()
       OR  USER_NAME() = 'admin@contoso.com';

-- Aplicar la security policy
CREATE SECURITY POLICY security.region_policy
ADD FILTER PREDICATE security.fn_region_filter(region)
ON sales.orders
WITH (STATE = ON);
```

> **Regla clave:** Row-level security y dynamic data masking son compatibles en
> **Fabric Warehouse** y en el SQL analytics endpoint. Son importantes
> para implementar data governance en el contexto del examen DP-600.

---

## Referencia rapida - orden logico de procesamiento de consultas

Comprender el orden en que SQL Server procesa las clausulas de una consulta ayuda a depurar
resultados inesperados:

| Paso | Clausula | Proposito |
|---|---|---|
| 1 | `FROM` / `JOIN` | Identificar tablas de origen y condiciones de join |
| 2 | `WHERE` | Filtrar filas individuales |
| 3 | `GROUP BY` | Agrupar filas para agregacion |
| 4 | `HAVING` | Filtrar grupos |
| 5 | `SELECT` | Evaluar expresiones y aliases |
| 6 | `DISTINCT` | Quitar duplicados |
| 7 | `ORDER BY` | Ordenar el result set |
| 8 | `OFFSET-FETCH` / `TOP` | Limitar filas devueltas |

> **Exam tip:** Los aliases de columna definidos en `SELECT` **no estan disponibles** en
> `WHERE` ni `HAVING`; usa una CTE o subquery para referenciarlos.

---

> **Punto clave:** Para el examen DP-600, concentrate en CTAS, COPY INTO, cross-database
> queries, window functions (especialmente `ROW_NUMBER` para deduplicacion), MERGE para
> cargas incrementales y las diferencias entre Fabric Warehouse y el SQL
> analytics endpoint. Estos son los temas de T-SQL evaluados con mayor frecuencia en el
> contexto de Fabric.
