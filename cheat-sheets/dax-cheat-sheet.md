# DAX Cheat Sheet — DP-600 Exam Quick Reference

> **Quick reference guide** covering the DAX functions, concepts, and patterns most
> likely to appear on the Microsoft DP-600 (Implementing Analytics Solutions Using
> Microsoft Fabric) exam.

---

## Table of Contents

1. [Evaluation Context](#1-evaluation-context)
2. [Filter Functions](#2-filter-functions)
3. [Aggregation Functions](#3-aggregation-functions)
4. [Iterator (X) Functions](#4-iterator-x-functions)
5. [Time Intelligence](#5-time-intelligence-functions)
6. [Table Functions](#6-table-functions)
7. [Relationship Functions](#7-relationship-functions)
8. [Context Transition](#8-context-transition)
9. [Common Patterns](#9-common-patterns)
10. [Exam Tips](#10-exam-tips)

## 1. Evaluation Context

Every DAX expression is evaluated inside **two kinds of context**.

### Row Context vs Filter Context

| Context Type | Row Context | Filter Context |
|---|---|---|
| **Created by** | Iterators (`SUMX`, `FILTER`, calculated columns, etc.) | Slicers, filters, `CALCULATE`, visual structure |
| **What it does** | Points to a single row in a table | Restricts the rows visible to the entire model |
| **Scope** | One table at a time | Propagates through relationships |
| **Nested?** | Yes — each iterator adds a new row context | Yes — `CALCULATE` can layer or replace filters |

**Key rule:** Row context does **not** propagate through relationships.
Use `RELATED` (many→one) or `RELATEDTABLE` (one→many) to follow relationships.

### Quick Visual

```
FILTER CONTEXT  (slicers, CALCULATE args)
  └─ ROW CONTEXT  (current row in an iterator)
       Sales[Quantity] * Sales[Price]
```

> **Exam tip:** If a question asks "Which context does a calculated column
> evaluate in?" — the answer is **row context** (of the table it belongs to),
> plus whatever filter context exists at query time.

---

## 2. Filter Functions

### Core Filter Functions

| Function | Syntax | Purpose |
|---|---|---|
| `CALCULATE` | `CALCULATE(<expression>, <filter1>, …)` | Evaluates an expression under modified filter context |
| `CALCULATETABLE` | `CALCULATETABLE(<table_expr>, <filter1>, …)` | Same as CALCULATE but returns a table |
| `FILTER` | `FILTER(<table>, <condition>)` | Returns a table with rows that satisfy the condition (iterator) |
| `ALL` | `ALL(<table_or_column>)` | Removes all filters from a table or column |
| `ALLEXCEPT` | `ALLEXCEPT(<table>, <col1>, …)` | Removes all filters from a table **except** the listed columns |
| `ALLSELECTED` | `ALLSELECTED(<table_or_column>)` | Removes filters inside the visual but keeps external slicer/filter context |
| `REMOVEFILTERS` | `REMOVEFILTERS(<table_or_column>)` | Alias for `ALL` when used as a CALCULATE modifier |
| `KEEPFILTERS` | `KEEPFILTERS(<filter_expr>)` | Adds a filter that **intersects** with (rather than replaces) existing filters |

### Value-Inspection Functions

| Function | Returns | Use Case |
|---|---|---|
| `VALUES(<column>)` | Distinct values visible in current filter context (includes blank for invalid refs) | Iterate or check visible members |
| `DISTINCT(<column>)` | Distinct values visible in current filter context (no extra blank) | Similar to VALUES without the blank row |
| `HASONEVALUE(<column>)` | `TRUE` / `FALSE` | Check if exactly one value is in filter context |
| `SELECTEDVALUE(<column>, <alt>)` | The single value in context, or `<alt>` | Safe replacement for `IF(HASONEVALUE(…), VALUES(…))` |

### CALCULATE — Deep Dive

`CALCULATE` is the most important DAX function. It:

1. **Clones** the current filter context.
2. **Applies** each filter argument (replacing or intersecting).
3. **Performs context transition** — converts row context into filter context.

```dax
-- Basic: override filter on Category
Total Electronics Sales =
CALCULATE(
    SUM(Sales[Amount]),
    Products[Category] = "Electronics"
)

-- Remove all filters on a column
All Product Sales =
CALCULATE(
    SUM(Sales[Amount]),
    REMOVEFILTERS(Products[Category])
)

-- KEEPFILTERS: intersect instead of replace
Filtered Sales =
CALCULATE(
    SUM(Sales[Amount]),
    KEEPFILTERS(Products[Category] = "Electronics")
)
```

### ALL vs ALLEXCEPT vs ALLSELECTED

```dax
-- ALL: ignores every filter on the table
Pct of Grand Total =
DIVIDE(
    SUM(Sales[Amount]),
    CALCULATE(SUM(Sales[Amount]), ALL(Sales))
)

-- ALLEXCEPT: keep only the Year filter
Pct Within Year =
DIVIDE(
    SUM(Sales[Amount]),
    CALCULATE(SUM(Sales[Amount]), ALLEXCEPT(Sales, 'Date'[Year]))
)

-- ALLSELECTED: respects slicers outside the visual
Pct of Visible Total =
DIVIDE(
    SUM(Sales[Amount]),
    CALCULATE(SUM(Sales[Amount]), ALLSELECTED(Sales))
)
```

> **Exam tip:** `REMOVEFILTERS` is functionally identical to `ALL` when used
> inside `CALCULATE`. Microsoft recommends `REMOVEFILTERS` for readability when
> the intent is to clear filters.

---

## 3. Aggregation Functions

These are the "simple" aggregators — they work on a **single column** and
respect the current filter context.

| Function | Description | Blank Handling |
|---|---|---|
| `SUM(<column>)` | Sum of all values | Ignores blanks |
| `AVERAGE(<column>)` | Arithmetic mean | Ignores blanks |
| `COUNT(<column>)` | Count of non-blank values | Ignores blanks |
| `COUNTA(<column>)` | Count of non-blank values (any type) | Ignores blanks |
| `COUNTBLANK(<column>)` | Count of blank/empty values | Counts blanks |
| `COUNTROWS(<table>)` | Number of rows in a table | Counts all rows |
| `DISTINCTCOUNT(<column>)` | Count of distinct non-blank values | Ignores blanks |
| `MIN(<column>)` | Minimum value | Ignores blanks |
| `MAX(<column>)` | Maximum value | Ignores blanks |

```dax
Total Revenue   = SUM(Sales[Amount])
Avg Order Value = AVERAGE(Sales[Amount])
Order Count     = COUNTROWS(Sales)
Unique Products = DISTINCTCOUNT(Sales[ProductID])
```

> **Exam tip:** `COUNTROWS` requires a **table** argument, not a column.
> `COUNT` requires a **column**. Mixing them up is a common exam trap.

---

## 4. Iterator (X) Functions

Iterators scan a table **row by row**, evaluate an expression for each row
(creating a row context), then aggregate the results.

| Function | Aggregation | Syntax |
|---|---|---|
| `SUMX` | Sum | `SUMX(<table>, <expression>)` |
| `AVERAGEX` | Average | `AVERAGEX(<table>, <expression>)` |
| `COUNTX` | Count (non-blank) | `COUNTX(<table>, <expression>)` |
| `MAXX` | Maximum | `MAXX(<table>, <expression>)` |
| `MINX` | Minimum | `MINX(<table>, <expression>)` |
| `RANKX` | Rank | `RANKX(<table>, <expression>, [value], [order], [ties])` |
| `PRODUCTX` | Product | `PRODUCTX(<table>, <expression>)` |
| `CONCATENATEX` | Text join | `CONCATENATEX(<table>, <expression>, <delimiter>)` |

```dax
-- Simple aggregator — works when the value is already in a column
Total Sales = SUM(Sales[Amount])

-- Iterator — needed when the value must be calculated per row
Total Sales = SUMX(Sales, Sales[Quantity] * Sales[UnitPrice])
```

### RANKX

```dax
Product Rank =
RANKX(
    ALL(Products[ProductName]),       -- table to rank over
    CALCULATE(SUM(Sales[Amount])),    -- expression to evaluate per row
    ,                                 -- value (blank = current)
    DESC,                             -- order
    DENSE                             -- tie-breaking: DENSE or SKIP
)
```

> **Exam tip:** `RANKX` uses **context transition** internally because
> `CALCULATE` is evaluated for each row. Understand that the first argument
> defines the "universe" of items being ranked.

---

## 5. Time Intelligence Functions

**Prerequisite:** A contiguous **Date table** marked as a date table, with a
column of data type Date containing no gaps.

### Totals-to-Date

| Function | Period | Syntax |
|---|---|---|
| `TOTALYTD` | Year-to-date | `TOTALYTD(<expression>, <dates>, [year_end])` |
| `TOTALQTD` | Quarter-to-date | `TOTALQTD(<expression>, <dates>)` |
| `TOTALMTD` | Month-to-date | `TOTALMTD(<expression>, <dates>)` |
| `DATESYTD` | Returns YTD dates table | `DATESYTD(<dates>, [year_end])` |
| `DATESQTD` | Returns QTD dates table | `DATESQTD(<dates>)` |
| `DATESMTD` | Returns MTD dates table | `DATESMTD(<dates>)` |

```dax
YTD Sales = TOTALYTD(SUM(Sales[Amount]), 'Date'[Date])

-- Equivalent using CALCULATE + DATESYTD
YTD Sales =
CALCULATE(
    SUM(Sales[Amount]),
    DATESYTD('Date'[Date])
)

-- Fiscal year ending June 30
Fiscal YTD Sales = TOTALYTD(SUM(Sales[Amount]), 'Date'[Date], "6/30")
```

### Period Shifting

| Function | Shift | Example Result (from June 2024) |
|---|---|---|
| `DATEADD(<dates>, <n>, <interval>)` | Shift by N intervals | `DATEADD('Date'[Date], -1, YEAR)` → June 2023 |
| `SAMEPERIODLASTYEAR(<dates>)` | Same period, prior year | June 2023 |
| `PARALLELPERIOD(<dates>, <n>, <interval>)` | Full parallel period | `PARALLELPERIOD('Date'[Date], -1, QUARTER)` → Full Q1 2024 |
| `PREVIOUSMONTH(<dates>)` | Prior month | May 2024 |
| `PREVIOUSQUARTER(<dates>)` | Prior quarter | Q1 2024 (Jan–Mar) |
| `PREVIOUSYEAR(<dates>)` | Prior year | Full 2023 |
| `NEXTMONTH(<dates>)` | Next month | July 2024 |
| `NEXTQUARTER(<dates>)` | Next quarter | Q3 2024 |
| `NEXTYEAR(<dates>)` | Next year | Full 2025 |

```dax
Sales LY = CALCULATE(SUM(Sales[Amount]), SAMEPERIODLASTYEAR('Date'[Date]))

Sales Prev Month = CALCULATE(SUM(Sales[Amount]), PREVIOUSMONTH('Date'[Date]))
```

### DATEADD vs SAMEPERIODLASTYEAR vs PARALLELPERIOD

| | DATEADD | SAMEPERIODLASTYEAR | PARALLELPERIOD |
|---|---|---|---|
| Flexibility | Any interval/offset | Fixed: -1 year | Any interval/offset |
| Returns | Same grain as context | Same grain as context | Always **full** period |

> **Exam tip:** `PARALLELPERIOD` always returns a **complete** period (full
> month/quarter/year) regardless of the dates in the current filter context.
> `DATEADD` preserves the same date range, just shifted.

---

## 6. Table Functions

Table functions return tables and are used inside `CALCULATE`, iterators,
or variable assignments.

### Transformation Functions

| Function | Purpose | Syntax |
|---|---|---|
| `ADDCOLUMNS` | Adds calculated columns to a table | `ADDCOLUMNS(<table>, <name>, <expr>, …)` |
| `SELECTCOLUMNS` | Creates a new table with selected/calculated columns | `SELECTCOLUMNS(<table>, <name>, <expr>, …)` |
| `SUMMARIZE` | Groups by columns (from related tables OK) | `SUMMARIZE(<table>, <groupBy_col>, …)` |
| `SUMMARIZECOLUMNS` | Groups + filters + measures (top-level only) | `SUMMARIZECOLUMNS(<groupBy_col>, …, <filter>, …, <name>, <expr>)` |

```dax
-- ADDCOLUMNS: extend a table with a computed column
ADDCOLUMNS(
    Products,
    "Total Sales", CALCULATE(SUM(Sales[Amount]))
)

-- SELECTCOLUMNS: project only what you need
SELECTCOLUMNS(
    Customers,
    "Customer", Customers[Name],
    "City", Customers[City]
)

-- SUMMARIZE: group by
SUMMARIZE(
    Sales,
    Products[Category],
    "Category Sales", SUM(Sales[Amount])
)

-- SUMMARIZECOLUMNS: preferred for top-level queries
SUMMARIZECOLUMNS(
    Products[Category],
    'Date'[Year],
    "Total", SUM(Sales[Amount])
)
```

### Set Operations

| Function | Description |
|---|---|
| `UNION(<table1>, <table2>, …)` | Combines rows from two or more tables (duplicates kept) |
| `EXCEPT(<table1>, <table2>)` | Rows in table1 that are **not** in table2 |
| `INTERSECT(<table1>, <table2>)` | Rows common to **both** tables |
| `CROSSJOIN(<table1>, <table2>)` | Cartesian product of two tables |
| `GENERATE(<table1>, <table_expr>)` | For each row in table1, evaluate table_expr (inner join behavior) |
| `GENERATEALL(<table1>, <table_expr>)` | Same as GENERATE but keeps rows even when table_expr is empty (left join) |

```dax
-- New customers this year (not in last year)
New Customers =
COUNTROWS(
    EXCEPT(
        VALUES(Sales[CustomerID]),                    -- this year
        CALCULATETABLE(
            VALUES(Sales[CustomerID]),
            SAMEPERIODLASTYEAR('Date'[Date])
        )                                             -- last year
    )
)
```

### TREATAS

`TREATAS` applies column values as a virtual relationship (filter) without
needing a physical relationship in the model.

```dax
Budget by Product =
CALCULATE(
    SUM(Budget[Amount]),
    TREATAS(VALUES(Products[ProductID]), Budget[ProductID])
)
```

> **Exam tip:** `SUMMARIZE` should not include measure expressions in
> production code (use `ADDCOLUMNS` + `SUMMARIZE` instead). However,
> `SUMMARIZECOLUMNS` is safe and recommended for top-level queries.

---

## 7. Relationship Functions

| Function | Direction | Use Case |
|---|---|---|
| `RELATED(<column>)` | Many → One (lookup) | Get a value from the "one" side in row context |
| `RELATEDTABLE(<table>)` | One → Many | Get related rows from the "many" side in row context |
| `USERELATIONSHIP(<col1>, <col2>)` | Activates an inactive relationship | Use inside CALCULATE for alternate date keys, etc. |
| `CROSSFILTER(<col1>, <col2>, <direction>)` | Changes filter direction at query time | `NONE`, `ONEWAY`, `BOTH` |

```dax
-- RELATED: get category from Products table while iterating Sales
Sales with Category =
ADDCOLUMNS(
    Sales,
    "Category", RELATED(Products[Category])
)

-- USERELATIONSHIP: query using ShipDate instead of OrderDate
Shipped Sales =
CALCULATE(
    SUM(Sales[Amount]),
    USERELATIONSHIP(Sales[ShipDate], 'Date'[Date])
)

-- CROSSFILTER: enable bidirectional filtering at query time
Customers With Sales =
CALCULATE(
    COUNTROWS(Customers),
    CROSSFILTER(Sales[CustomerID], Customers[CustomerID], BOTH)
)
```

> **Exam tip:** `USERELATIONSHIP` can only activate a relationship that
> **already exists** (but is inactive) in the model. It cannot create new
> relationships.

---

## 8. Context Transition

Context transition occurs when `CALCULATE` is used **inside a row context**.
It converts every column value of the current row into an equivalent filter.

### How It Works

```
Row Context:  Sales[ProductID] = 42, Sales[Date] = 2024-03-15, …
                          ↓  CALCULATE  ↓
Filter Context: ProductID = 42 AND Date = 2024-03-15 AND …
```

### Practical Example

```dax
-- Calculated column on Products table
Products[Total Sales] =
    CALCULATE(SUM(Sales[Amount]))
    -- No explicit filter! Context transition converts the current
    -- Products row into a filter that propagates to Sales.
```

### Performance Considerations

- Context transition on small tables → negligible impact.
- Context transition inside large iterators → can be expensive.
- Nested `CALCULATE` inside `SUMX` over millions of rows → consider alternatives.

### Rules to Remember

1. Context transition converts **all columns** of the current row into filters.
2. It only fires when `CALCULATE` or `CALCULATETABLE` is present.
3. Measures always wrap their expression in an implicit `CALCULATE`, so calling
   a measure inside an iterator **always** triggers context transition.
4. Context transition propagates through relationships (unlike plain row context).

> **Exam tip:** A common exam question: "What happens when you reference a
> measure inside SUMX?" — Answer: Context transition fires automatically
> because measures implicitly use CALCULATE.

---

## 9. Common Patterns

### 9.1 Year-over-Year (YoY) Growth

```dax
Sales LY =
CALCULATE(
    SUM(Sales[Amount]),
    SAMEPERIODLASTYEAR('Date'[Date])
)

YoY Growth =
VAR CurrentSales = SUM(Sales[Amount])
VAR PriorSales   = [Sales LY]
RETURN
    DIVIDE(CurrentSales - PriorSales, PriorSales)

YoY Growth % = FORMAT([YoY Growth], "0.0%")
```

### 9.2 Running Total

```dax
Running Total =
VAR LastDate = MAX('Date'[Date])
RETURN
CALCULATE(
    SUM(Sales[Amount]),
    'Date'[Date] <= LastDate,
    ALL('Date'[Date])
)
```

### 9.3 Percentage of Total

```dax
-- % of Grand Total
Pct of Total =
DIVIDE(
    SUM(Sales[Amount]),
    CALCULATE(SUM(Sales[Amount]), ALL(Sales))
)

-- % of Parent (keeps all filters except Category)
Pct of Parent =
DIVIDE(
    SUM(Sales[Amount]),
    CALCULATE(SUM(Sales[Amount]), ALLEXCEPT(Sales, Products[Category]))
)

-- % of Visible Total (respects external slicers)
Pct of Visible =
DIVIDE(
    SUM(Sales[Amount]),
    CALCULATE(SUM(Sales[Amount]), ALLSELECTED(Sales))
)
```

### 9.4 Dynamic Ranking

```dax
Product Rank =
RANKX(
    ALL(Products[ProductName]),
    CALCULATE(SUM(Sales[Amount])),
    ,
    DESC,
    DENSE
)

-- Top N filtering (e.g., Top 10 products)
Top 10 Flag =
IF([Product Rank] <= 10, 1, 0)
```

### 9.5 New vs Returning Customers

```dax
New Customers =
COUNTROWS(
    EXCEPT(
        VALUES(Sales[CustomerID]),
        CALCULATETABLE(
            VALUES(Sales[CustomerID]),
            SAMEPERIODLASTYEAR('Date'[Date])
        )
    )
)

Returning Customers =
DISTINCTCOUNT(Sales[CustomerID]) - [New Customers]
```

### 9.6 Moving Average

```dax
Moving Avg 3M =
AVERAGEX(
    DATESINPERIOD('Date'[Date], MAX('Date'[Date]), -3, MONTH),
    CALCULATE(SUM(Sales[Amount]))
)
```

### 9.7 Virtual Relationship (TREATAS)

```dax
Budget vs Actual =
VAR Actual = SUM(Sales[Amount])
VAR Budget =
    CALCULATE(
        SUM(Budget[Amount]),
        TREATAS(VALUES(Products[ProductID]), Budget[ProductID])
    )
RETURN Actual - Budget
```

---

## 10. Exam Tips

### Function Selection Cheat Table

| Scenario | Recommended Function(s) |
|---|---|
| Modify filter context | `CALCULATE` / `CALCULATETABLE` |
| Remove all filters | `ALL` or `REMOVEFILTERS` |
| Remove all filters except one | `ALLEXCEPT` |
| Keep slicer context in visual | `ALLSELECTED` |
| Row-by-row calculation | `SUMX`, `AVERAGEX`, or other iterator |
| YoY comparison | `SAMEPERIODLASTYEAR` or `DATEADD(…, -1, YEAR)` |
| Year-to-date | `TOTALYTD` or `DATESYTD` |
| Full prior period | `PARALLELPERIOD` or `PREVIOUSMONTH/QUARTER/YEAR` |
| Lookup from many-to-one | `RELATED` |
| Lookup from one-to-many | `RELATEDTABLE` |
| Activate inactive relationship | `USERELATIONSHIP` |
| Virtual relationship | `TREATAS` |
| Rank items | `RANKX` |
| Safe single-value extraction | `SELECTEDVALUE` |
| Group and aggregate (top-level) | `SUMMARIZECOLUMNS` |

### Common Exam Traps

| Trap | What to Watch For |
|---|---|
| Missing date table | Time intelligence functions **require** a marked date table |
| `CALCULATE` filter replacement | Default behavior **replaces** column filters; use `KEEPFILTERS` to intersect |
| `SUMMARIZE` with measures | Avoid — use `ADDCOLUMNS(SUMMARIZE(…), …)` instead |
| `ALL` inside vs outside CALCULATE | Inside CALCULATE → removes filters; standalone → returns full table |
| `PARALLELPERIOD` grain | Always returns a **complete** period, not the same date range shifted |
| Context transition in iterators | Calling a measure inside `SUMX` triggers implicit CALCULATE |
| `DISTINCTCOUNT` vs `COUNTROWS(DISTINCT(…))` | Functionally similar, but `DISTINCTCOUNT` ignores blanks |
| `COUNTROWS` vs `COUNT` argument | `COUNTROWS` takes a table; `COUNT` takes a column |
| `RELATED` vs `RELATEDTABLE` direction | `RELATED` = many→one; `RELATEDTABLE` = one→many |
| `REMOVEFILTERS` vs `ALL` | Identical inside CALCULATE; `REMOVEFILTERS` is preferred for clarity |

### Variables Best Practice

Always use `VAR` / `RETURN` for readability and performance:

```dax
YoY Change =
VAR CurrentSales = SUM(Sales[Amount])
VAR PriorSales   =
    CALCULATE(
        SUM(Sales[Amount]),
        SAMEPERIODLASTYEAR('Date'[Date])
    )
RETURN
    DIVIDE(CurrentSales - PriorSales, PriorSales)
```

**Why?** Variables are evaluated **once**, improve readability, and appear
frequently on the exam.

### CALCULATE Argument Evaluation Order

1. Filter arguments are evaluated in the **old** filter context.
2. All filter arguments are applied **simultaneously** (not sequentially).
3. Context transition happens **after** filter arguments are applied.

---

*Last updated: 2025 · Aligned with DP-600 exam objectives*
