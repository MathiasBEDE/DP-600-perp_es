# Domain 3: Implement and Manage Semantic Models (20–25%)

> **Exam Weight:** This domain represents 20–25% of the DP-600 exam.
> Mastering semantic models, DAX, storage modes, security, and performance optimization is critical for passing.

---

## Table of Contents

1. [Build and Optimize Semantic Models](#1-build-and-optimize-semantic-models)
2. [DAX (Data Analysis Expressions)](#2-dax-data-analysis-expressions)
3. [Storage Modes](#3-storage-modes)
4. [Row-Level Security (RLS)](#4-row-level-security-rls)
5. [Object-Level Security (OLS)](#5-object-level-security-ols)
6. [Aggregations Management](#6-aggregations-management)
7. [Model Refresh Strategies](#7-model-refresh-strategies)
8. [Performance Optimization](#8-performance-optimization)

---

## 1. Build and Optimize Semantic Models

### What Are Semantic Models?

A **semantic model** (formerly called a "dataset" in Power BI) is a structured representation of data that sits between raw data sources and the reports/visuals end users interact with. It defines:

- **Tables and columns** that hold or reference data
- **Relationships** between tables
- **Measures** (DAX calculations) for business logic
- **Hierarchies** for drill-down navigation
- **Metadata** such as display folders, formatting, and descriptions

In Microsoft Fabric, semantic models are first-class items in a workspace. They can be built in Power BI Desktop, the Fabric portal (web modeling), or through XMLA endpoints using third-party tools like Tabular Editor.

> **📝 Exam Tip:** The term "dataset" has been rebranded to "semantic model" across Microsoft documentation. The exam uses the newer terminology.

### Creating Models from Different Sources

| Source Type | Description | Typical Use Case |
|---|---|---|
| **Lakehouse** | Delta tables in Fabric OneLake | Primary Fabric-native source for Direct Lake models |
| **Warehouse** | Fabric Synapse Data Warehouse | Enterprise-grade SQL analytics |
| **SQL Server / Azure SQL** | Relational databases | Traditional enterprise data |
| **Dataflows Gen2** | Power Query Online outputs | Reusable data preparation layers |
| **Excel / CSV** | File-based sources | Ad-hoc analysis, small datasets |
| **Analysis Services** | Existing SSAS/AAS models | Migration or live connection |
| **Dataverse** | Dynamics 365 / Power Platform | Business application data |

When creating a semantic model in Fabric, the most common pattern is:

1. Ingest data into a **Lakehouse** (Bronze/Silver/Gold layers).
2. Build a semantic model on top of the Gold layer using **Direct Lake** mode.
3. Add DAX measures, relationships, and hierarchies.

### Table Relationships

Relationships connect tables and allow filters to propagate from one table to another. Understanding relationship types is essential for both model design and the exam.

#### Cardinality Types

| Cardinality | Description | Example |
|---|---|---|
| **One-to-Many (1:M)** | Most common. One row in the lookup table relates to many rows in the fact table. | `DimDate[DateKey]` → `FactSales[OrderDateKey]` |
| **One-to-One (1:1)** | Each row in one table matches exactly one row in the other. | `Employee[EmployeeID]` → `EmployeeDetails[EmployeeID]` |
| **Many-to-Many (M:M)** | Multiple rows on both sides. Requires careful handling. | `Students` ↔ `Courses` (via a bridge table or direct M:M) |

> **⚠️ Warning:** Many-to-many relationships can produce unexpected results if not combined with proper DAX or bidirectional filtering. Avoid them when a bridge table can resolve the relationship to two 1:M relationships.

#### Relationship Cross-Filter Direction

| Direction | Behavior | When to Use |
|---|---|---|
| **Single** (default for 1:M) | Filter flows from the "one" side to the "many" side only. | Standard star schema design |
| **Both** (bidirectional) | Filters flow in both directions. | M:M relationships, or when the fact table must filter a dimension |

**Best practice:** Keep cross-filter direction as **Single** unless you have a specific requirement. Bidirectional filtering can cause ambiguity, performance issues, and unexpected results.

### Star Schema vs. Snowflake Schema

#### Star Schema (Recommended)

```
          ┌──────────┐
          │ DimDate  │
          └────┬─────┘
               │ 1:M
┌──────────┐   │   ┌──────────────┐
│DimProduct├───┤───┤  FactSales   │
└──────────┘ 1:M   └───┬──────────┘
               │        │ 1:M
          ┌────┴─────┐  │
          │DimStore  │  │
          └──────────┘  │
                   ┌────┴─────┐
                   │DimCustomer│
                   └──────────┘
```

- **Dimension tables** surround a central **fact table**.
- Dimensions are **denormalized** (flattened) — no chains of dimension tables.
- Optimal for VertiPaq compression and query performance.

#### Snowflake Schema

- Dimension tables are **normalized** into sub-dimensions (e.g., `DimProduct` → `DimSubcategory` → `DimCategory`).
- Increases the number of relationships and can degrade query performance.
- Generally **not recommended** for Power BI / Fabric semantic models.

> **📝 Exam Tip:** The exam strongly favors star schema design. Know why it outperforms snowflake in the VertiPaq engine (fewer relationships, better compression, simpler DAX).

### Calculated Columns vs. Measures

| Feature | Calculated Column | Measure |
|---|---|---|
| **Evaluation time** | Row-by-row at refresh time | At query time in response to filter context |
| **Storage** | Stored in the model (consumes memory) | Not stored; computed on the fly |
| **Use in slicers/filters** | Yes | No (unless wrapped in a visual) |
| **Use in relationships** | Yes | No |
| **Typical use** | Categorization, concatenation, static flags | Aggregations, KPIs, dynamic calculations |
| **DAX context** | Row context | Filter context |

**Example — Calculated Column:**

```dax
Profit Margin Category =
IF(
    Sales[ProfitMargin] > 0.3, "High",
    IF(Sales[ProfitMargin] > 0.1, "Medium", "Low")
)
```

**Example — Measure:**

```dax
Total Revenue = SUM(Sales[Revenue])
```

> **💡 Best Practice:** Prefer **measures** over calculated columns whenever possible. Measures are more flexible, do not increase model size, and respond dynamically to filter context.

### Best Practices for Model Design

1. **Use a star schema** — flatten dimensions, avoid snowflake chains.
2. **Hide foreign key columns** — end users should filter on dimension attributes, not keys.
3. **Use integer surrogate keys** for relationships — smaller, faster than text keys.
4. **Avoid bidirectional relationships** unless absolutely necessary.
5. **Organize measures in display folders** — improves discoverability.
6. **Add descriptions to tables, columns, and measures** — critical for self-service.
7. **Remove unused columns** — reduces model size and eliminates confusion.
8. **Use date tables** — mark a dedicated date table for time intelligence.
9. **Avoid calculated columns for aggregations** — use measures instead.
10. **Minimize auto date/time** — disable it and use a proper date dimension.

---

## 2. DAX (Data Analysis Expressions)

### DAX Fundamentals and Syntax

DAX is the formula language for defining calculations in semantic models. It is used for:

- **Measures** — dynamic aggregation formulas
- **Calculated columns** — row-level computed values
- **Calculated tables** — entire tables generated by DAX
- **Row-Level Security (RLS)** — filter expressions to restrict data access

DAX syntax resembles Excel formulas but operates on tables and columns rather than cells.

```dax
Measure Name = FUNCTION(Table[Column], ...)
```

Key syntax rules:
- Column references use `Table[Column]` notation.
- Measure references use `[Measure Name]` (no table prefix required, but recommended for clarity).
- String literals use double quotes: `"text"`.
- DAX is **not** case-sensitive, but consistent casing improves readability.

### Filter Context and Row Context

Understanding **evaluation context** is the single most important concept in DAX.

#### Filter Context

- Defined by **slicers, filters, rows/columns in a visual, and the CALCULATE function**.
- Determines which subset of data a measure sees.
- Example: If a slicer is set to "2024", then `SUM(Sales[Revenue])` only sums revenue for 2024.

#### Row Context

- Exists when DAX iterates over a table **row by row**.
- Created by **calculated columns**, **iterator functions** (SUMX, AVERAGEX, etc.), and **table expressions**.
- Inside a row context, you can reference individual column values directly.

```dax
-- Calculated column (row context exists automatically)
Line Total = Sales[Quantity] * Sales[UnitPrice]
```

### Context Transition

**Context transition** occurs when a **row context is converted to a filter context** — specifically when a measure is called inside an iterator or calculated column.

```dax
-- Context transition example
Revenue Rank =
RANKX(
    ALL(Product[ProductName]),
    [Total Revenue]   -- measure reference triggers context transition
)
```

When `[Total Revenue]` is evaluated inside RANKX, the current row context (one product) is converted into a filter context that filters the Sales table to that product. This is an implicit `CALCULATE` call.

> **⚠️ Warning:** Context transition can be expensive. Be cautious when using measure references inside large iterators.

### CALCULATE and Filter Modification

`CALCULATE` is the most important DAX function. It:

1. Evaluates an expression in a **modified filter context**.
2. Accepts filter arguments that **override** or **add to** the current filter context.

```dax
CALCULATE(<expression>, <filter1>, <filter2>, ...)
```

**Example — Override filter context:**

```dax
Revenue All Products =
CALCULATE(
    SUM(Sales[Revenue]),
    ALL(Product)            -- removes any filter on the Product table
)
```

**Example — Add filter:**

```dax
Revenue Electronics =
CALCULATE(
    SUM(Sales[Revenue]),
    Product[Category] = "Electronics"
)
```

**Filter modification behaviors:**

| Modifier | Behavior |
|---|---|
| Table/column filter argument | Overrides existing filters on those columns |
| `ALL(Table)` | Removes all filters from the table |
| `KEEPFILTERS(filter)` | Adds filter as intersection (does not override) |
| `REMOVEFILTERS(Table)` | Same as `ALL(Table)` — removes filters |
| `USERELATIONSHIP(col1, col2)` | Activates an inactive relationship |
| `CROSSFILTER(col1, col2, direction)` | Changes cross-filter direction at query time |

### FILTER, ALL, ALLEXCEPT, ALLSELECTED

#### ALL

Returns all rows of a table (or all distinct values of a column), ignoring any filters.

```dax
-- Percentage of total
% of Total Revenue =
DIVIDE(
    SUM(Sales[Revenue]),
    CALCULATE(SUM(Sales[Revenue]), ALL(Sales))
)
```

#### ALLEXCEPT

Removes all filters from a table **except** the specified columns.

```dax
-- Revenue share within category
Category Share =
DIVIDE(
    SUM(Sales[Revenue]),
    CALCULATE(SUM(Sales[Revenue]), ALLEXCEPT(Sales, Product[Category]))
)
```

#### ALLSELECTED

Returns all rows of a table considering only the filters applied **outside** the current visual (i.e., respects slicer context but ignores row/column grouping within the visual).

```dax
-- Percentage of visible total
% of Visible Total =
DIVIDE(
    SUM(Sales[Revenue]),
    CALCULATE(SUM(Sales[Revenue]), ALLSELECTED(Sales))
)
```

#### FILTER

Returns a table that is a subset of another table based on a condition. Used as a filter argument in `CALCULATE`.

```dax
-- Revenue from high-value orders only
High Value Revenue =
CALCULATE(
    SUM(Sales[Revenue]),
    FILTER(Sales, Sales[Revenue] > 1000)
)
```

> **💡 Tip:** Prefer simple column filter arguments (`Product[Category] = "Electronics"`) over `FILTER` when possible, as the engine can optimize them more efficiently.

### Time Intelligence Functions

Time intelligence functions require a **contiguous date table** marked as a date table (or auto-detected).

| Function | Description | Example |
|---|---|---|
| `TOTALYTD` | Year-to-date total | `TOTALYTD(SUM(Sales[Revenue]), Dates[Date])` |
| `TOTALQTD` | Quarter-to-date total | `TOTALQTD(SUM(Sales[Revenue]), Dates[Date])` |
| `TOTALMTD` | Month-to-date total | `TOTALMTD(SUM(Sales[Revenue]), Dates[Date])` |
| `SAMEPERIODLASTYEAR` | Same period in previous year | `CALCULATE(SUM(Sales[Revenue]), SAMEPERIODLASTYEAR(Dates[Date]))` |
| `DATEADD` | Shift dates by interval | `CALCULATE(SUM(Sales[Revenue]), DATEADD(Dates[Date], -1, YEAR))` |
| `DATESYTD` | Returns dates from start of year to current date | `CALCULATE(SUM(Sales[Revenue]), DATESYTD(Dates[Date]))` |
| `PARALLELPERIOD` | Returns a parallel period | `CALCULATE(SUM(Sales[Revenue]), PARALLELPERIOD(Dates[Date], -1, QUARTER))` |
| `PREVIOUSMONTH` | Previous month's dates | `CALCULATE(SUM(Sales[Revenue]), PREVIOUSMONTH(Dates[Date]))` |
| `PREVIOUSYEAR` | Previous year's dates | `CALCULATE(SUM(Sales[Revenue]), PREVIOUSYEAR(Dates[Date]))` |

**Year-over-Year Growth Pattern:**

```dax
YoY Growth % =
VAR CurrentRevenue = SUM(Sales[Revenue])
VAR PriorYearRevenue =
    CALCULATE(
        SUM(Sales[Revenue]),
        SAMEPERIODLASTYEAR(Dates[Date])
    )
RETURN
    DIVIDE(CurrentRevenue - PriorYearRevenue, PriorYearRevenue)
```

> **📝 Exam Tip:** Know the difference between `SAMEPERIODLASTYEAR` (same date range, prior year) and `DATEADD` (flexible shift by any interval). `TOTALYTD` is shorthand for `CALCULATE(..., DATESYTD(...))`.

### Iterator Functions

Iterator functions scan a table row by row and aggregate the results. They create a **row context** during iteration.

| Function | Description |
|---|---|
| `SUMX(table, expression)` | Sum of expression evaluated row by row |
| `AVERAGEX(table, expression)` | Average of expression evaluated row by row |
| `MAXX(table, expression)` | Maximum value of expression |
| `MINX(table, expression)` | Minimum value of expression |
| `COUNTX(table, expression)` | Count of non-blank expression results |
| `RANKX(table, expression)` | Rank of current context within the table |

**Example — Weighted average:**

```dax
Weighted Avg Price =
SUMX(
    Sales,
    Sales[Quantity] * Sales[UnitPrice]
) / SUM(Sales[Quantity])
```

**Example — RANKX:**

```dax
Product Revenue Rank =
RANKX(
    ALL(Product[ProductName]),
    [Total Revenue],
    ,       -- optional value (defaults to current context)
    DESC,   -- order
    DENSE   -- tie-breaking (DENSE, SKIP)
)
```

### Variables (VAR / RETURN)

Variables improve readability, avoid repeated calculations, and can aid debugging.

```dax
Profit Margin % =
VAR TotalRevenue = SUM(Sales[Revenue])
VAR TotalCost = SUM(Sales[Cost])
VAR Profit = TotalRevenue - TotalCost
RETURN
    DIVIDE(Profit, TotalRevenue)
```

Key points:
- Variables are evaluated **once** (lazy evaluation) and the result is stored.
- Variables capture the **filter context at the point of definition**.
- Use `RETURN` to specify the final expression.
- Variables cannot be modified after assignment (they are constants).

### Table Functions

| Function | Description |
|---|---|
| `SUMMARIZE(table, groupByCol1, ...)` | Groups a table by specified columns |
| `SUMMARIZECOLUMNS(groupByCol1, ..., "Name", expr)` | Creates a summary table with calculated columns (preferred over SUMMARIZE for adding columns) |
| `ADDCOLUMNS(table, "Name", expression)` | Adds calculated columns to a table |
| `SELECTCOLUMNS(table, "Name", expression)` | Creates a new table with selected/renamed columns |
| `UNION(table1, table2)` | Combines two tables vertically (must have same schema) |
| `CROSSJOIN(table1, table2)` | Cartesian product of two tables |
| `DISTINCT(column)` | Returns distinct values of a column |
| `VALUES(column)` | Returns distinct values including the blank row |
| `TOPN(n, table, expression, order)` | Returns top N rows |

**Example — ADDCOLUMNS with SUMMARIZE:**

```dax
Category Summary =
ADDCOLUMNS(
    SUMMARIZE(Sales, Product[Category]),
    "Total Revenue", CALCULATE(SUM(Sales[Revenue])),
    "Total Quantity", CALCULATE(SUM(Sales[Quantity]))
)
```

**Example — SELECTCOLUMNS:**

```dax
Product List =
SELECTCOLUMNS(
    Product,
    "Product", Product[ProductName],
    "Category", Product[Category],
    "List Price", Product[ListPrice]
)
```

### USERELATIONSHIP, TREATAS, CROSSFILTER

#### USERELATIONSHIP

Activates an **inactive relationship** for the duration of a CALCULATE evaluation. Useful when a fact table has multiple date columns (e.g., OrderDate, ShipDate).

```dax
Revenue by Ship Date =
CALCULATE(
    SUM(Sales[Revenue]),
    USERELATIONSHIP(Sales[ShipDateKey], DimDate[DateKey])
)
```

#### TREATAS

Applies the data lineage of one column to another, enabling **virtual relationships** without physical model relationships.

```dax
Budget by Product =
CALCULATE(
    SUM(Budget[Amount]),
    TREATAS(VALUES(Product[ProductKey]), Budget[ProductKey])
)
```

#### CROSSFILTER

Changes the cross-filter direction of a relationship at query time.

```dax
Customers With Sales =
CALCULATE(
    COUNTROWS(Customer),
    CROSSFILTER(Sales[CustomerKey], Customer[CustomerKey], BOTH)
)
```

### Common DAX Patterns

#### Year-over-Year (YoY) Growth

```dax
YoY Revenue Growth =
VAR CurrentYear = SUM(Sales[Revenue])
VAR PriorYear =
    CALCULATE(SUM(Sales[Revenue]), SAMEPERIODLASTYEAR(Dates[Date]))
RETURN
    DIVIDE(CurrentYear - PriorYear, PriorYear)
```

#### Running Total

```dax
Running Total Revenue =
CALCULATE(
    SUM(Sales[Revenue]),
    FILTER(
        ALL(Dates[Date]),
        Dates[Date] <= MAX(Dates[Date])
    )
)
```

#### Market Share (Percentage of Parent)

```dax
Market Share =
DIVIDE(
    SUM(Sales[Revenue]),
    CALCULATE(SUM(Sales[Revenue]), ALL(Product[Brand]))
)
```

#### ABC Analysis (Pareto)

```dax
ABC Class =
VAR CurrentProductRevenue = [Total Revenue]
VAR AllProductRevenue =
    ADDCOLUMNS(
        ALL(Product[ProductName]),
        "Rev", [Total Revenue]
    )
VAR CumulativePct =
    DIVIDE(
        SUMX(
            FILTER(AllProductRevenue, [Rev] >= CurrentProductRevenue),
            [Rev]
        ),
        SUMX(AllProductRevenue, [Rev])
    )
RETURN
    SWITCH(
        TRUE(),
        CumulativePct <= 0.7, "A",
        CumulativePct <= 0.9, "B",
        "C"
    )
```

#### Moving Average

```dax
3-Month Moving Average =
AVERAGEX(
    DATESINPERIOD(Dates[Date], MAX(Dates[Date]), -3, MONTH),
    CALCULATE(SUM(Sales[Revenue]))
)
```

---

## 3. Storage Modes

### Import Mode (VertiPaq)

**Import mode** loads data into the in-memory VertiPaq columnar storage engine.

| Aspect | Detail |
|---|---|
| **Data location** | Compressed in-memory (RAM) on the capacity |
| **Query speed** | Fastest — all queries served from memory |
| **Data freshness** | Snapshot at last refresh; requires scheduled/manual refresh |
| **Model size limit** | Depends on capacity SKU (e.g., up to 400 GB on P3/F64) |
| **Best for** | Small-to-medium datasets, complex DAX, maximum performance |

**How VertiPaq works:**
1. Data is loaded column by column.
2. Each column is **dictionary encoded** — unique values are stored in a dictionary, and rows store integer references.
3. Further compression is applied using **run-length encoding (RLE)** and **value encoding**.
4. The result is a highly compressed, query-optimized in-memory store.

> **💡 Tip:** Sort tables by low-cardinality columns before import to improve RLE compression.

### DirectQuery Mode

**DirectQuery** does not import data. Every visual interaction generates a live SQL query against the source.

| Aspect | Detail |
|---|---|
| **Data location** | Remains in the source system |
| **Query speed** | Depends on source performance and query complexity |
| **Data freshness** | Real-time (always current) |
| **Model size limit** | No size limit in the model itself |
| **Best for** | Large datasets, real-time requirements, regulated data that cannot be copied |

**Limitations:**
- DAX calculations are translated to source SQL; not all DAX patterns translate efficiently.
- No VertiPaq compression benefits — source must be optimized.
- Performance depends heavily on the source system and network latency.
- Some DAX functions are not supported in DirectQuery mode.

### Composite Models (Dual Storage)

Composite models combine **Import** and **DirectQuery** tables in the same model.

- **Dual mode** tables are stored in VertiPaq AND can also be queried via DirectQuery.
- Allows dimension tables to be imported (fast) while large fact tables use DirectQuery.
- Relationships between Import and DirectQuery tables require careful planning.

```
┌─────────────────┐     ┌─────────────────┐
│  DimDate        │     │  FactSales      │
│  (Import/Dual)  │────▶│  (DirectQuery)  │
└─────────────────┘     └─────────────────┘
```

**Key consideration:** When a relationship crosses storage mode boundaries (Import ↔ DirectQuery), the engine must send the imported dimension values to the source, which can impact performance. Setting dimension tables to **Dual** mode helps mitigate this.

### Direct Lake Mode (Unique to Fabric)

**Direct Lake** is a storage mode exclusive to Microsoft Fabric. It reads data directly from **Delta Parquet files** in OneLake without importing into VertiPaq or sending queries to a SQL endpoint.

| Aspect | Detail |
|---|---|
| **Data location** | Delta tables in OneLake (Parquet files) |
| **Query speed** | Near-Import performance; Parquet files are memory-mapped |
| **Data freshness** | Near-real-time — reads latest Delta log version |
| **Model size limit** | Governed by guardrails per SKU |
| **Best for** | Fabric Lakehouse/Warehouse scenarios, large datasets needing fast queries |

**How it works:**
1. The semantic model references Delta tables in a Lakehouse or Warehouse.
2. At query time, column data is loaded directly from Parquet files into memory on demand.
3. No scheduled refresh needed for data — the model automatically detects new Delta versions.
4. Schema changes (new columns, table structure) do require a model refresh.

**Fallback behavior:**
If a DAX query cannot be served by Direct Lake (e.g., exceeds row limits, unsupported column type), the engine **falls back to DirectQuery** mode, sending a SQL query to the SQL analytics endpoint.

| Guardrail | F2 | F64 | F128 |
|---|---|---|---|
| Max rows per table | 300M | 1.5B | 3B |
| Max model size on disk | 10 GB | 40 GB | 80 GB |
| Max columns per table | 1,000 | 1,000 | 1,000 |

> **📝 Exam Tip:** Direct Lake is a key differentiator for Fabric. Know the fallback behavior, guardrails, and that no scheduled refresh is needed for data changes (only schema changes).

### When to Use Each Mode

| Scenario | Recommended Mode |
|---|---|
| Small dataset, complex DAX, maximum speed | **Import** |
| Real-time data requirements | **DirectQuery** |
| Large dataset with dimension tables needing speed | **Composite** |
| Fabric Lakehouse with Delta tables | **Direct Lake** |
| Data that cannot leave the source for compliance | **DirectQuery** |
| Need near-import speed without scheduled refresh | **Direct Lake** |

---

## 4. Row-Level Security (RLS)

Row-Level Security restricts which **rows** of data a user can see. It is enforced at the semantic model level and applies to all reports connected to the model.

### Static RLS

Static RLS uses fixed values in the DAX filter expression.

**Example — Restrict to a specific region:**

```dax
-- Role: "West Region"
[Region] = "West"
```

Anyone assigned to the "West Region" role only sees rows where `Region = "West"`.

**Pros:** Simple to implement.
**Cons:** Requires creating a separate role for each region/group, which does not scale well.

### Dynamic RLS

Dynamic RLS uses the identity of the signed-in user to filter data dynamically.

**Step 1:** Create a mapping table that links users to their permissions.

| UserEmail | Region |
|---|---|
| alice@contoso.com | West |
| bob@contoso.com | East |
| carol@contoso.com | West |

**Step 2:** Define a role with a DAX filter on the mapping table:

```dax
-- Role: "Dynamic Region Security"
-- Applied to the UserRegionMapping table:
[UserEmail] = USERPRINCIPALNAME()
```

**Step 3:** Ensure a relationship exists from the mapping table to the fact/dimension tables so the filter propagates.

#### Key Functions

| Function | Returns |
|---|---|
| `USERPRINCIPALNAME()` | The UPN (email) of the signed-in user, e.g., `alice@contoso.com` |
| `USERNAME()` | The user identity; in cloud environments this is typically the same as UPN |

> **💡 Tip:** Prefer `USERPRINCIPALNAME()` in Fabric/Power BI Service as it reliably returns the email address.

### Testing RLS

**In Power BI Desktop:**
1. Go to **Modeling** → **View as Roles**.
2. Select a role and optionally specify "Other user" for dynamic RLS.
3. The report renders as if the user is a member of that role.

**In the Power BI Service:**
1. Navigate to the semantic model settings.
2. Use **Security** → **Test as role**.
3. Enter a UPN for dynamic RLS testing.

### RLS with DirectQuery and Direct Lake

| Mode | RLS Support | Notes |
|---|---|---|
| **Import** | Full support | RLS filters are applied to the in-memory model |
| **DirectQuery** | Full support | RLS filters are translated to SQL and pushed to the source |
| **Direct Lake** | Full support | RLS filters are applied when reading from Parquet files |
| **Live Connection** | Model-level only | RLS must be defined in the source model (e.g., AAS or remote semantic model) |

> **📝 Exam Tip:** With DirectQuery, RLS predicates are pushed to the source. Ensure the source can handle the additional filter complexity efficiently.

### RLS Best Practices

1. **Use dynamic RLS** over static RLS for scalability.
2. **Maintain a security mapping table** in the model — makes administration easier.
3. **Test thoroughly** — use "View as Role" and test with multiple user identities.
4. **Keep filter expressions simple** — complex DAX in RLS can degrade performance.
5. **Ensure proper relationship propagation** — the filter on the security table must flow to all relevant fact tables.
6. **Document roles** — list what each role filters and who should be assigned.
7. **Assign roles in the Power BI Service** — role membership is managed on the semantic model, not in Desktop.

---

## 5. Object-Level Security (OLS)

### Overview

Object-Level Security restricts access to entire **tables or columns** within a semantic model. Unlike RLS (which filters rows), OLS completely **hides** objects from unauthorized users.

| Security Type | Scope | Effect |
|---|---|---|
| **RLS** | Rows | User sees the table/column but only filtered rows |
| **OLS** | Tables/Columns | User cannot see the protected table or column at all |

### OLS Configuration Using Tabular Editor

OLS cannot be configured in Power BI Desktop directly. Use **Tabular Editor** (free or paid) connected via XMLA endpoint.

**Steps:**

1. **Publish** the semantic model to a Premium/Fabric workspace.
2. Open **Tabular Editor** and connect to the workspace XMLA endpoint.
3. Define a **role** (or use an existing one).
4. Set the **Object Level Security** property on specific tables or columns:
   - `None` — default, object is visible.
   - `Read` — object is visible to the role.
   - `None` on a table or column effectively hides it if the default is restricted.

In Tabular Editor:

```
Model → Roles → [Role Name] → Table Permissions
  → Set "MetadataPermission" to "None" for restricted objects
```

**Example:** Hide the `Salary` column from the `Employee` table for the "Standard Users" role:
- Open Tabular Editor.
- Navigate to `Roles` → `Standard Users` → `Table Permissions` → `Employee`.
- Set the `Salary` column's `MetadataPermission` to `None`.

### Limitations and Considerations

- OLS is **only supported in Import and DirectQuery modes** (and Direct Lake in Fabric).
- OLS roles must be configured via **XMLA endpoint tools** (Tabular Editor, TMSL scripts).
- If a measure references an OLS-protected column, the measure returns an **error** for users in that role.
- OLS cannot be tested in Power BI Desktop — must test in the Service.
- **Q&A (natural language queries)** does not respect OLS — consider disabling Q&A for models with OLS.
- OLS and RLS can be combined in the same role.

> **📝 Exam Tip:** Know that OLS is configured via XMLA/Tabular Editor, not in Desktop. Understand the interaction between OLS and measures.

---

## 6. Aggregations Management

### Why Aggregations?

Aggregations provide a way to achieve **Import-mode performance** for common queries while keeping detailed data in **DirectQuery** mode. They act as a performance cache layer.

### User-Defined Aggregations

Manually configured aggregation tables that the engine uses transparently.

**Design steps:**

1. **Create an aggregation table** with pre-computed summaries (e.g., `SalesAgg` grouped by Date, ProductCategory, Region with SUM of Revenue).
2. **Set storage mode** of the aggregation table to **Import**.
3. **Configure aggregation mappings** — map each aggregation column to its detail table counterpart.
4. **Hide the aggregation table** from report authors.

**Aggregation mapping example:**

| Aggregation Column | Summarization | Detail Table Column |
|---|---|---|
| `SalesAgg[Revenue]` | Sum | `Sales[Revenue]` |
| `SalesAgg[Quantity]` | Sum | `Sales[Quantity]` |
| `SalesAgg[DateKey]` | GroupBy | `Sales[DateKey]` |
| `SalesAgg[Category]` | GroupBy | `Product[Category]` |
| `SalesAgg[RowCount]` | Count | `Sales[SalesID]` |

**How it works at query time:**
1. The engine analyzes the incoming DAX query.
2. If the query can be satisfied by the aggregation table, it reads from the fast Import table.
3. If not (e.g., query needs detail-level columns not in the aggregation), it falls through to DirectQuery.

### Automatic Aggregations

Microsoft Fabric and Power BI Premium support **automatic aggregations** — the system creates and manages aggregation tables without manual configuration.

**How automatic aggregations work:**
1. A training period analyzes query patterns.
2. The system builds optimized aggregation tables in the background.
3. Queries that match the aggregation grain are served from the cache.
4. No user configuration required beyond enabling the feature.

**Enabling automatic aggregations:**
- Navigate to the semantic model settings in the Fabric portal.
- Enable **Automatic aggregations** under the performance section.
- The system handles the rest.

### Aggregation Tables Design Best Practices

1. **Aggregate at the right granularity** — too fine = no benefit; too coarse = many cache misses.
2. **Include all common grouping columns** — Date (month/quarter level), Category, Region.
3. **Add Count aggregations** — needed for `COUNT` and `COUNTROWS` queries.
4. **Always hide aggregation tables** — they are internal optimization; users should never interact with them directly.
5. **Monitor hit rates** — use Performance Analyzer or Fabric monitoring to check how often aggregations are used.
6. **Combine with composite models** — aggregations pair naturally with composite (Import + DirectQuery) models.

### Performance Benefits

| Metric | Without Aggregations | With Aggregations |
|---|---|---|
| Simple summary query | 5–30 seconds (DirectQuery) | < 1 second (Import) |
| Detail-level query | 5–30 seconds (DirectQuery) | 5–30 seconds (DirectQuery fallthrough) |
| User experience | Inconsistent | Fast for common queries |

---

## 7. Model Refresh Strategies

### Full Refresh vs. Incremental Refresh

| Aspect | Full Refresh | Incremental Refresh |
|---|---|---|
| **Scope** | Reloads all data | Only refreshes new/changed partitions |
| **Duration** | Longer (proportional to data size) | Much faster (only processes delta) |
| **Resource usage** | High memory and CPU | Lower resource consumption |
| **Data source load** | High (full extraction) | Low (only recent data extracted) |
| **Configuration** | Default behavior | Requires policy configuration |

### Incremental Refresh Policies

Incremental refresh uses **RangeStart** and **RangeEnd** parameters in Power Query to partition data by date.

**Configuration steps:**

1. **Create parameters** in Power Query:
   - `RangeStart` (DateTime)
   - `RangeEnd` (DateTime)
2. **Filter the source query** using these parameters.
3. **Define the policy** in Power BI Desktop:
   - **Store rows in the last N years/months/days** — historical window.
   - **Refresh rows in the last N days/months** — refresh window (data that might change).
   - Optionally: **Detect data changes** — only refresh partitions where data changed.
   - Optionally: **Only refresh complete periods** — avoids partial-day data.

**Example policy:**
- Store 3 years of data.
- Refresh the last 10 days.
- Detect data changes using a `MAX(ModifiedDate)` column.

**Result:** The engine creates partitions by date range. Only partitions in the refresh window are reprocessed during scheduled refresh.

```
┌──────────────────────────────────────────────┐
│              Historical Partitions            │
│  (Not refreshed unless policy changes)        │
│  2021-Q1 | 2021-Q2 | ... | 2023-Q3           │
├──────────────────────────────────────────────┤
│        Refresh Window Partitions              │
│  (Refreshed on every scheduled run)           │
│  2024-01 | 2024-02 | 2024-03-01..10          │
└──────────────────────────────────────────────┘
```

### Refresh Scheduling

- **Scheduled refresh** — configure up to 48 refreshes per day in the Fabric/Power BI Service.
- **On-demand refresh** — manually trigger via the UI or API.
- **Dataflow-triggered refresh** — chain model refresh after dataflow completion.
- **Pipeline-triggered refresh** — use Fabric Data Pipelines to orchestrate refresh after ETL.

### XMLA Endpoint for Advanced Refresh

The **XMLA read/write endpoint** (available on Premium/Fabric capacities) enables advanced refresh operations using tools like:

- **SQL Server Management Studio (SSMS)**
- **Tabular Editor**
- **PowerShell (Invoke-ASCmd)**
- **Custom applications using the TOM (Tabular Object Model) library**

**XMLA refresh capabilities:**

| Capability | Description |
|---|---|
| Partition-level refresh | Refresh individual partitions |
| Custom partition management | Create, delete, and merge partitions |
| Parallel processing | Process multiple partitions simultaneously |
| Sequence operations | Define processing order with error handling |

**Example — TMSL refresh command:**

```json
{
  "refresh": {
    "type": "full",
    "objects": [
      {
        "database": "SalesModel",
        "table": "FactSales",
        "partition": "FactSales-2024-03"
      }
    ]
  }
}
```

### Enhanced Refresh API

The **Enhanced Refresh REST API** provides programmatic control over refresh operations.

**Key features:**
- **Asynchronous execution** — submit refresh and poll for status.
- **Granular control** — specify tables, partitions, and refresh types.
- **Commit modes** — `transactional` (all-or-nothing) or `partialBatch` (partial success allowed).
- **Max parallelism** — control the number of concurrent partition refreshes.
- **Apply refresh policy** — leverage incremental refresh policies programmatically.

**Example — REST API call:**

```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/datasets/{datasetId}/refreshes

{
  "type": "Full",
  "commitMode": "transactional",
  "maxParallelism": 4,
  "objects": [
    { "table": "FactSales" },
    { "table": "DimProduct" }
  ]
}
```

**Polling for status:**

```http
GET https://api.powerbi.com/v1.0/myorg/groups/{groupId}/datasets/{datasetId}/refreshes
```

> **📝 Exam Tip:** Know the difference between XMLA endpoint (TMSL/TOM-based, full partition control) and the Enhanced Refresh API (REST-based, async with commitMode options).

---

## 8. Performance Optimization

### Performance Analyzer in Power BI

**Performance Analyzer** is a built-in tool in Power BI Desktop for measuring visual rendering time.

**How to use:**
1. Go to **View** → **Performance Analyzer**.
2. Click **Start recording**.
3. Interact with visuals (or click **Refresh visuals**).
4. Review the results:
   - **DAX query** — time to execute the DAX query sent by the visual.
   - **Direct query** — time for the source SQL query (DirectQuery only).
   - **Other** — rendering, layout, and other overhead.

**What to look for:**
- DAX queries over **500 ms** may benefit from optimization.
- Copy the DAX query and paste it into **DAX Studio** for deeper analysis.

### DAX Studio for Query Analysis

**DAX Studio** is a free, third-party tool for advanced DAX analysis. It connects to semantic models via XMLA endpoint.

**Key features:**

| Feature | Use Case |
|---|---|
| **Query editing** | Write and test DAX queries with syntax highlighting |
| **Server Timings** | Breakdown of Formula Engine (FE) vs. Storage Engine (SE) time |
| **Query Plan** | View logical and physical query plans |
| **VertiPaq Analyzer** | Inspect model size, column cardinality, encoding, and compression |
| **DMV queries** | Query Dynamic Management Views for model metadata |

**Server Timings breakdown:**

| Component | Description | Optimization Target |
|---|---|---|
| **Formula Engine (FE)** | Single-threaded DAX computation | Simplify DAX, reduce iterations |
| **Storage Engine (SE)** | Multi-threaded data retrieval | Reduce cardinality, improve compression |
| **SE Cache** | Cached storage engine queries | Warm cache = faster repeat queries |

**Example — VertiPaq Analyzer insights:**

| Table | Rows | Size (MB) | % of Model |
|---|---|---|---|
| FactSales | 50M | 1,200 | 72% |
| DimProduct | 15K | 8 | 0.5% |
| DimCustomer | 500K | 45 | 2.7% |
| DimDate | 3,650 | 0.2 | 0.01% |

Use this to identify which tables/columns consume the most memory and target optimization efforts.

### Best Practices Analyzer (BPA)

**Best Practices Analyzer** is a feature in **Tabular Editor** that evaluates a semantic model against a set of rules.

**Common BPA rules:**

| Rule | Category | Description |
|---|---|---|
| Avoid bidirectional relationships | Performance | Bidirectional filtering can cause ambiguity and performance issues |
| Remove unused columns | Size | Columns not referenced in any DAX or relationship waste memory |
| Hide foreign key columns | Usability | End users should not see key columns |
| Use DIVIDE instead of `/` | Correctness | DIVIDE handles division by zero gracefully |
| Avoid IFERROR in measures | Performance | IFERROR forces the engine to check for errors row by row |
| No circular dependencies | Correctness | Circular references prevent calculation |
| Format strings for measures | Usability | Measures should have format strings for proper display |
| Summarization set to "None" for non-aggregatable columns | Usability | Prevents accidental aggregation of ID columns |

**Using BPA:**
1. Open the model in **Tabular Editor**.
2. Load the BPA rule set (default or custom `.json` rules).
3. Run the analyzer.
4. Review and fix flagged issues.

> **💡 Tip:** The TE2 community maintains the [BPA rules by SQLBI](https://www.sqlbi.com/tools/bpa-rules/) — a widely adopted rule set.

### Reducing Model Size

| Technique | Impact | How |
|---|---|---|
| **Remove unused columns** | High | Delete columns not used in visuals, DAX, relationships, or RLS |
| **Reduce column cardinality** | High | Round decimals, bin values, use surrogate keys |
| **Use integer keys** | Medium | Replace text keys with integer surrogate keys for relationships |
| **Disable auto date/time** | Medium | File → Options → Data Load → uncheck Auto date/time |
| **Avoid calculated columns** | Medium | Move transformations to Power Query (upstream) |
| **Optimize data types** | Medium | Use the smallest appropriate type (e.g., Int32 instead of Int64) |
| **Split date and time** | Medium | Separate DateTime columns into Date and Time columns |

### Optimizing Relationships

1. **Use integer key columns** — faster joins than text.
2. **Prefer single-direction filtering** — reduces ambiguity and computation.
3. **Limit active relationships** — only one active relationship between two tables; use `USERELATIONSHIP` for alternates.
4. **Avoid unnecessary M:M relationships** — use bridge tables when possible.
5. **Set referential integrity to "Assume"** for DirectQuery — allows inner joins instead of outer joins.

### Query Optimization Techniques

#### DAX Optimization

| Pattern | Instead of | Use |
|---|---|---|
| Division | `A / B` | `DIVIDE(A, B)` |
| Conditional count | `COUNTROWS(FILTER(table, condition))` | `CALCULATE(COUNTROWS(table), condition)` |
| Complex filter | `FILTER(Sales, Sales[Amount] > 100 && Sales[Region] = "West")` | Separate into column predicate + FILTER for non-trivial conditions |
| Avoid nested iterators | `SUMX(FILTER(SUMMARIZE(...)))` | Simplify with SUMMARIZECOLUMNS or pre-filter |
| Variables | Repeated subexpressions | `VAR x = ... RETURN ...` |
| SELECTEDVALUE | `IF(HASONEVALUE(col), VALUES(col))` | `SELECTEDVALUE(col)` |

#### Query Reduction

- Enable **query reduction options** in report settings to prevent unnecessary queries.
- Use **"Apply" buttons on slicers** so filters are only applied when the user clicks Apply.
- Limit the number of visuals per page — each visual generates a DAX query.

#### DirectQuery Optimization

1. **Add indexes on source tables** — especially on columns used in filters and relationships.
2. **Use materialized views** in the source database.
3. **Enable query folding** in Power Query — ensure transformations push down to the source.
4. **Set "Assume Referential Integrity"** on relationships for better SQL generation.
5. **Use aggregation tables** for frequently queried summaries.

---

## Quick Reference: Key DAX Functions for the Exam

| Category | Functions |
|---|---|
| **Aggregation** | `SUM`, `AVERAGE`, `MIN`, `MAX`, `COUNT`, `COUNTROWS`, `DISTINCTCOUNT` |
| **Iterator** | `SUMX`, `AVERAGEX`, `MAXX`, `MINX`, `COUNTX`, `RANKX` |
| **Filter** | `CALCULATE`, `FILTER`, `ALL`, `ALLEXCEPT`, `ALLSELECTED`, `KEEPFILTERS`, `REMOVEFILTERS` |
| **Time Intelligence** | `TOTALYTD`, `TOTALQTD`, `TOTALMTD`, `SAMEPERIODLASTYEAR`, `DATEADD`, `DATESYTD`, `PARALLELPERIOD` |
| **Table** | `SUMMARIZE`, `SUMMARIZECOLUMNS`, `ADDCOLUMNS`, `SELECTCOLUMNS`, `UNION`, `CROSSJOIN`, `TOPN` |
| **Relationship** | `USERELATIONSHIP`, `TREATAS`, `CROSSFILTER`, `RELATED`, `RELATEDTABLE` |
| **Information** | `HASONEVALUE`, `SELECTEDVALUE`, `ISBLANK`, `ISINSCOPE`, `ISFILTERED`, `ISCROSSFILTERED` |
| **Logical** | `IF`, `SWITCH`, `AND`, `OR`, `NOT`, `TRUE`, `FALSE`, `IN` |
| **Text** | `CONCATENATE`, `CONCATENATEX`, `FORMAT`, `LEFT`, `RIGHT`, `MID`, `UPPER`, `LOWER` |

---

## Exam Scenario Practice

### Scenario 1: Choosing Storage Mode

> A company has a 500 GB sales fact table in a Fabric Lakehouse. Reports need to be interactive with sub-second response times for common queries. Detail-level drill-through is required occasionally.

**Answer:** Use **Direct Lake** mode. The data is in a Lakehouse (Delta tables), which is the ideal source for Direct Lake. For the 500 GB size, ensure the Fabric capacity SKU supports the guardrails. If fallback occurs on complex queries, they will route to DirectQuery on the SQL analytics endpoint.

### Scenario 2: Dynamic RLS Implementation

> A multi-national company needs each regional sales manager to see only their region's data. New managers are added frequently.

**Answer:** Implement **dynamic RLS**:
1. Create a `UserRegionMapping` table with `UserEmail` and `Region` columns.
2. Define a role with filter: `[UserEmail] = USERPRINCIPALNAME()`.
3. Relate `UserRegionMapping[Region]` to `DimGeography[Region]` (1:M).
4. Assign all managers to this single role. Add/remove users by updating the mapping table.

### Scenario 3: Performance Optimization

> A report takes 15 seconds to load. Performance Analyzer shows the main visual's DAX query takes 12 seconds. The model is Import mode with 200 million rows.

**Answer:**
1. Copy the DAX query from Performance Analyzer into **DAX Studio**.
2. Analyze **Server Timings** — determine if the bottleneck is Formula Engine or Storage Engine.
3. If FE-bound: simplify DAX, reduce iterations, use variables.
4. If SE-bound: reduce model size, optimize cardinality, remove unused columns.
5. Run **VertiPaq Analyzer** to identify large tables/columns.
6. Consider **aggregation tables** if the query operates on summary-level data.
7. Run **BPA** in Tabular Editor to catch common anti-patterns.

### Scenario 4: Incremental Refresh

> A fact table has 5 years of data (2 billion rows). Full refresh takes 4 hours. The business requires daily refresh of recent data only.

**Answer:**
1. Configure **incremental refresh** with `RangeStart` and `RangeEnd` parameters.
2. Set policy: Store 5 years, refresh last 30 days.
3. Enable **detect data changes** using a watermark column (e.g., `ModifiedDate`).
4. After initial full refresh, subsequent refreshes only process the 30-day window.
5. For finer control, use the **Enhanced Refresh API** or **XMLA endpoint** to manage individual partitions.

---

## Additional Resources

| Resource | Link |
|---|---|
| **DAX Guide (SQLBI)** | [https://dax.guide/](https://dax.guide/) |
| **DAX Patterns** | [https://www.daxpatterns.com/](https://www.daxpatterns.com/) |
| **Microsoft Learn: Semantic Models** | [https://learn.microsoft.com/power-bi/transform-model/](https://learn.microsoft.com/power-bi/transform-model/) |
| **Direct Lake Overview** | [https://learn.microsoft.com/fabric/get-started/direct-lake-overview](https://learn.microsoft.com/fabric/get-started/direct-lake-overview) |
| **Row-Level Security** | [https://learn.microsoft.com/power-bi/enterprise/service-admin-rls](https://learn.microsoft.com/power-bi/enterprise/service-admin-rls) |
| **Incremental Refresh** | [https://learn.microsoft.com/power-bi/connect-data/incremental-refresh-overview](https://learn.microsoft.com/power-bi/connect-data/incremental-refresh-overview) |
| **Enhanced Refresh API** | [https://learn.microsoft.com/power-bi/connect-data/asynchronous-refresh](https://learn.microsoft.com/power-bi/connect-data/asynchronous-refresh) |
| **Best Practices Analyzer Rules** | [https://www.sqlbi.com/tools/bpa-rules/](https://www.sqlbi.com/tools/bpa-rules/) |
| **DAX Studio** | [https://daxstudio.org/](https://daxstudio.org/) |
| **Tabular Editor** | [https://tabulareditor.com/](https://tabulareditor.com/) |

---

> **🎯 Summary:** Domain 3 covers the full lifecycle of semantic models — from design and DAX authoring, through storage mode selection and security configuration, to refresh management and performance tuning. Focus on star schema design, DAX evaluation context, Direct Lake mode, dynamic RLS, and performance analysis tools. These are heavily tested topics on the DP-600 exam.
