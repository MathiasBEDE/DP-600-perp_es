# Domain 4: Practice Questions — Explore and Analyze Data

> **50 Multiple-Choice Questions** covering Domain 4 of the DP-600 exam (20–25% weight).
> Each question includes a detailed answer explanation.

---

### Question 1

A data analyst needs to allow report consumers to navigate from a summary page to a detail page filtered to a specific product category. The detail page should show only the data relevant to the selected category. What feature should the analyst configure?

- A) Bookmarks
- B) Drill-through
- C) Drill-down
- D) Page navigation buttons

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Drill-through**

**Explanation:** Drill-through in Power BI allows users to right-click a data point on one page and navigate to a detail page that is automatically filtered to the context of the selected data point. This is the standard way to move from a summary view to a detailed view for a specific dimension value such as a product category.

**Why other options are incorrect:**
- A) Bookmarks capture a snapshot of the current report state but do not dynamically filter a target page based on a selected data point.
- C) Drill-down expands within a single visual's hierarchy (e.g., Year → Quarter → Month) rather than navigating to a separate detail page.
- D) Page navigation buttons can link to another page but do not pass filter context from the selected data point automatically.

</details>

---

### Question 2

You are writing a T-SQL query against a Lakehouse SQL analytics endpoint. You need to return the top 10 customers by total revenue for the current year. Which T-SQL clause combination is most appropriate?

- A) `WHERE YEAR(OrderDate) = YEAR(GETDATE())` with `ORDER BY TotalRevenue DESC` and `TOP 10`
- B) `HAVING YEAR(OrderDate) = YEAR(GETDATE())` with `ORDER BY TotalRevenue DESC` and `TOP 10`
- C) `WHERE YEAR(OrderDate) = YEAR(GETDATE())` with `LIMIT 10`
- D) `HAVING COUNT(*) > 10` with `ORDER BY TotalRevenue DESC`

<details>
<summary>Show Answer</summary>

**Correct Answer: A) `WHERE YEAR(OrderDate) = YEAR(GETDATE())` with `ORDER BY TotalRevenue DESC` and `TOP 10`**

**Explanation:** To filter rows before aggregation, you use a WHERE clause. After grouping by customer and computing total revenue, you use ORDER BY TotalRevenue DESC to sort and TOP 10 to limit the result set. This is standard T-SQL syntax supported by the Lakehouse SQL analytics endpoint.

**Why other options are incorrect:**
- B) HAVING is used to filter on aggregated values, not on individual row values like OrderDate before grouping.
- C) LIMIT is not valid T-SQL syntax; it is used in MySQL and PostgreSQL. T-SQL uses TOP instead.
- D) This filters by row count rather than by year and does not return the top 10 by revenue.

</details>

---

### Question 3

A report designer wants to create an interactive experience where users can toggle between two different chart configurations on the same report page. Which Power BI feature is best suited for this requirement?

- A) Drill-through pages
- B) Bookmarks with selection pane
- C) Row-level security
- D) Conditional formatting

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Bookmarks with selection pane**

**Explanation:** Bookmarks capture the state of a report page, including which visuals are visible or hidden. By using the selection pane to control visual visibility and creating bookmarks for each configuration, the designer can add buttons that toggle between views on the same page.

**Why other options are incorrect:**
- A) Drill-through pages navigate to a different page rather than toggling visuals on the same page.
- C) Row-level security restricts data access for specific users and has nothing to do with visual layout toggling.
- D) Conditional formatting changes the appearance of values within a visual but cannot show or hide entire visuals.

</details>

---

### Question 4

You need to query data in a KQL database within Microsoft Fabric to find all events where the response time exceeded 5000 milliseconds in the last 24 hours. Which KQL query is correct?

- A) `Events | where ResponseTime > 5000 and Timestamp > ago(24h)`
- B) `SELECT * FROM Events WHERE ResponseTime > 5000 AND Timestamp > DATEADD(hour, -24, GETDATE())`
- C) `Events | filter ResponseTime > 5000 | where Timestamp > now(-24h)`
- D) `Events | where ResponseTime > 5000 | where Timestamp > datetime(24h)`

<details>
<summary>Show Answer</summary>

**Correct Answer: A) `Events | where ResponseTime > 5000 and Timestamp > ago(24h)`**

**Explanation:** In KQL, the `where` operator filters rows based on a predicate. The `ago()` function returns a datetime value relative to the current time. Combining both conditions with `and` in a single `where` clause is the idiomatic approach in KQL.

**Why other options are incorrect:**
- B) This is T-SQL syntax, not KQL. KQL does not use SELECT/FROM/WHERE keywords.
- C) `filter` is not a valid KQL operator, and `now(-24h)` is not valid KQL syntax for relative time.
- D) `datetime(24h)` is not valid KQL syntax; `ago(24h)` is the correct function for relative time calculations.

</details>

---

### Question 5

A business analyst wants to use natural language to ask questions about a Power BI dataset directly within a report. Which feature should be added to the report?

- A) Smart Narratives visual
- B) Q&A visual
- C) Key Influencers visual
- D) Decomposition Tree visual

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Q&A visual**

**Explanation:** The Q&A visual in Power BI allows users to type natural language questions and receive answers in the form of automatically generated visuals. It interprets user queries against the underlying semantic model and returns relevant charts or values.

**Why other options are incorrect:**
- A) Smart Narratives automatically generates text-based summaries of data but does not accept user-typed natural language questions.
- C) The Key Influencers visual uses AI to analyze which factors influence a specific metric, but it does not respond to free-form natural language questions.
- D) The Decomposition Tree allows interactive exploration of measures across dimensions but does not accept natural language input.

</details>

---

### Question 6

You are using DAX to query a published semantic model. You need to return a table of product categories with their total sales amounts. Which DAX query syntax is correct?

- A) `EVALUATE SUMMARIZECOLUMNS(Products[Category], "TotalSales", SUM(Sales[Amount]))`
- B) `SELECT Category, SUM(Amount) FROM Sales GROUP BY Category`
- C) `CALCULATE(SUM(Sales[Amount]), Products[Category])`
- D) `EVALUATE FILTER(Products, SUM(Sales[Amount]) > 0)`

<details>
<summary>Show Answer</summary>

**Correct Answer: A) `EVALUATE SUMMARIZECOLUMNS(Products[Category], "TotalSales", SUM(Sales[Amount]))`**

**Explanation:** DAX queries use the `EVALUATE` keyword to return a table result. `SUMMARIZECOLUMNS` groups by one or more columns and adds calculated summary columns. This is the standard pattern for querying a semantic model with DAX.

**Why other options are incorrect:**
- B) This is T-SQL syntax, not DAX. DAX queries do not use SELECT, FROM, or GROUP BY.
- C) CALCULATE modifies the filter context for a measure but does not return a table on its own and cannot be used with EVALUATE directly in this form.
- D) FILTER iterates a table and evaluates a condition per row, but SUM(Sales[Amount]) in this context would not correctly aggregate per row of Products.

</details>

---

### Question 7

An organization is implementing deployment pipelines in Microsoft Fabric. Content needs to flow from Development to Test to Production. What is the correct order of operations when promoting content?

- A) Production → Test → Development
- B) Development → Production → Test
- C) Development → Test → Production
- D) Test → Development → Production

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Development → Test → Production**

**Explanation:** Deployment pipelines in Microsoft Fabric follow a standard promotion flow from Development to Test to Production. Content is first created and modified in the Development stage, validated in the Test stage, and then deployed to Production for end users.

**Why other options are incorrect:**
- A) This is the reverse order and would push production content backward into development.
- B) Skipping the Test stage defeats the purpose of validation before production release.
- D) This order would promote test content to development, which is not a valid promotion flow.

</details>

---

### Question 8

A Power BI administrator needs to apply Microsoft Purview sensitivity labels to datasets containing confidential financial data. What is the primary purpose of sensitivity labels in this context?

- A) To restrict who can build reports on top of the dataset
- B) To encrypt and classify data, and enforce information protection policies when data is exported
- C) To improve query performance on the dataset
- D) To automatically refresh the dataset on a schedule

<details>
<summary>Show Answer</summary>

**Correct Answer: B) To encrypt and classify data, and enforce information protection policies when data is exported**

**Explanation:** Sensitivity labels from Microsoft Purview classify and can protect content. When applied to Power BI artifacts, they help ensure that data protection travels with the data when it is exported to supported file formats such as Excel, PDF, or PowerPoint.

**Why other options are incorrect:**
- A) Sensitivity labels classify and protect data but do not directly control who can build reports; that is managed through workspace roles and permissions.
- C) Sensitivity labels have no impact on query performance.
- D) Sensitivity labels are unrelated to data refresh scheduling.

</details>

---

### Question 9

You are using the Anomaly Detection feature in a Power BI line chart. Which statement about this feature is true?

- A) It only works with bar charts and column charts.
- B) It uses machine learning algorithms to automatically flag unexpected data points in a time series.
- C) It requires you to manually define upper and lower thresholds for anomalies.
- D) It replaces the need for any statistical analysis outside Power BI.

<details>
<summary>Show Answer</summary>

**Correct Answer: B) It uses machine learning algorithms to automatically flag unexpected data points in a time series.**

**Explanation:** The Anomaly Detection feature in Power BI uses built-in machine learning to identify unexpected spikes or dips in time series data displayed in a line chart. It automatically determines expected ranges and flags outliers without manual threshold configuration.

**Why other options are incorrect:**
- A) Anomaly detection is available on line charts, not bar or column charts.
- C) The feature automatically calculates expected ranges; manual threshold definition is not required.
- D) While useful, it is a visualization aid and does not replace comprehensive statistical analysis tools or practices.

</details>

---

### Question 10

A team uses Git integration in Microsoft Fabric for source control of their workspace. What is a key benefit of this integration?

- A) It automatically optimizes DAX queries for performance.
- B) It enables version control, branching, and collaboration on workspace items using a Git repository.
- C) It eliminates the need for deployment pipelines.
- D) It automatically applies sensitivity labels to all workspace content.

<details>
<summary>Show Answer</summary>

**Correct Answer: B) It enables version control, branching, and collaboration on workspace items using a Git repository.**

**Explanation:** Git integration in Microsoft Fabric connects a workspace to a Git repository (Azure DevOps or GitHub), enabling teams to track changes, create branches, merge updates, and collaborate using standard source control workflows for Fabric items.

**Why other options are incorrect:**
- A) Git integration manages versioning of artifacts, not DAX query optimization.
- C) Git integration complements deployment pipelines but does not replace them; pipelines handle stage-based promotion.
- D) Sensitivity labels are managed through Microsoft Purview, not through Git integration.

</details>

---

### Question 11

You want to use the Key Influencers AI visual to understand which factors most influence customer churn. What type of field should you place in the "Analyze" well of the visual?

- A) A date field representing the transaction date
- B) The metric or outcome you want to analyze, such as a churn indicator column
- C) A measure that calculates total revenue
- D) A geographic field for map rendering

<details>
<summary>Show Answer</summary>

**Correct Answer: B) The metric or outcome you want to analyze, such as a churn indicator column**

**Explanation:** The Key Influencers visual requires you to place the target outcome field (the metric you want to explain) in the "Analyze" well. The visual then determines which other fields in the "Explain by" well have the most influence on that outcome.

**Why other options are incorrect:**
- A) A date field could be used as an explanatory factor but is not the target outcome for analyzing churn.
- C) Measures can be analyzed but the question specifically asks about customer churn, which is best represented as a categorical or binary column.
- D) Geographic fields are used in map visuals, not as the target field for Key Influencers analysis.

</details>

---

### Question 12

A data engineer needs to query a Lakehouse table using the SQL analytics endpoint to find all orders placed in the last 30 days and calculate average order value by region. Which approach is correct?

- A) Use a KQL query with `summarize avg(OrderValue) by Region`
- B) Use T-SQL: `SELECT Region, AVG(OrderValue) AS AvgOrderValue FROM Orders WHERE OrderDate >= DATEADD(day, -30, GETDATE()) GROUP BY Region`
- C) Use DAX: `EVALUATE CALCULATETABLE(Orders, DATESINPERIOD(Orders[OrderDate], TODAY(), -30, DAY))`
- D) Use Python: `spark.sql("SELECT * FROM Orders").groupBy("Region").avg("OrderValue")`

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use T-SQL: `SELECT Region, AVG(OrderValue) AS AvgOrderValue FROM Orders WHERE OrderDate >= DATEADD(day, -30, GETDATE()) GROUP BY Region`**

**Explanation:** The Lakehouse SQL analytics endpoint supports T-SQL for querying data. This query correctly filters for the last 30 days using DATEADD, groups by Region, and calculates the average order value using the AVG aggregate function.

**Why other options are incorrect:**
- A) KQL is used for querying KQL databases (Real-Time Analytics), not Lakehouse SQL analytics endpoints.
- C) This DAX query would return a filtered table but does not perform the aggregation by region in the required format for a SQL analytics endpoint.
- D) This uses PySpark, which is used in notebooks, not through the SQL analytics endpoint interface.

</details>

---

### Question 13

A report contains a matrix visual showing sales by product and month. A user wants to expand from the product level to the product subcategory level within the same visual. Which interaction does this describe?

- A) Drill-through
- B) Cross-filtering
- C) Drill-down
- D) Bookmarking

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Drill-down**

**Explanation:** Drill-down allows users to navigate deeper into a hierarchy within the same visual. In a matrix with a product hierarchy (Product → Subcategory → Item), clicking the drill-down button expands the view to the next level of the hierarchy.

**Why other options are incorrect:**
- A) Drill-through navigates to a separate detail page, not deeper within the same visual hierarchy.
- B) Cross-filtering highlights or filters data in other visuals on the same page based on a selection, not within the same visual.
- D) Bookmarking saves a report state but does not involve hierarchical navigation within a visual.

</details>

---

### Question 14

You need to monitor the performance of a Power BI semantic model and identify long-running queries. Which tool is most appropriate?

- A) Power BI Capacity Metrics app
- B) Performance Analyzer in Power BI Desktop
- C) DAX Studio
- D) Microsoft Purview Data Catalog

<details>
<summary>Show Answer</summary>

**Correct Answer: C) DAX Studio**

**Explanation:** DAX Studio is an external tool that connects to Power BI semantic models and provides detailed query performance diagnostics, including server timings, query plans, and the ability to identify slow-running DAX queries. It is the most targeted tool for deep query-level performance analysis.

**Why other options are incorrect:**
- A) The Capacity Metrics app monitors capacity-level resource consumption (CPU, memory) but does not provide query-level diagnostics.
- B) Performance Analyzer in Power BI Desktop measures visual rendering times and DAX query durations within a report but provides less detailed diagnostics than DAX Studio for model-level analysis.
- D) Microsoft Purview Data Catalog is for data governance and discovery, not query performance monitoring.

</details>

---

### Question 15

Which Power BI visual type is best suited for showing how different sub-categories contribute to a total while also allowing the user to interactively explore the breakdown using AI-driven analysis?

- A) Pie chart
- B) Treemap
- C) Decomposition Tree
- D) Waterfall chart

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Decomposition Tree**

**Explanation:** The Decomposition Tree is an AI-powered visual that lets users interactively break down a measure across multiple dimensions. It supports AI splits that automatically find the highest or lowest contributing sub-categories, making it ideal for root cause analysis and contribution exploration.

**Why other options are incorrect:**
- A) A pie chart shows proportions of a whole but does not support interactive hierarchical decomposition or AI-driven splits.
- B) A treemap shows hierarchical proportions as nested rectangles but does not offer interactive AI-driven exploration of contributing factors.
- D) A waterfall chart shows sequential contributions to a total (additions and subtractions) but does not support AI-driven interactive decomposition.

</details>

---

### Question 16

An administrator is reviewing the Microsoft Fabric Capacity Metrics app. The CPU utilization has been consistently above 80% during business hours. Which action should the administrator consider first?

- A) Apply sensitivity labels to all datasets
- B) Scale up the capacity SKU or optimize workloads to reduce resource consumption
- C) Disable Git integration for the workspace
- D) Remove all deployment pipelines

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Scale up the capacity SKU or optimize workloads to reduce resource consumption**

**Explanation:** When capacity CPU utilization is consistently high, the appropriate response is to either increase the capacity (scale up to a higher SKU) or optimize the workloads running on it, such as improving query efficiency or staggering refresh schedules. The Capacity Metrics app helps identify the specific workloads consuming the most resources.

**Why other options are incorrect:**
- A) Sensitivity labels are for data classification and protection; they have no impact on CPU utilization.
- C) Git integration has minimal impact on capacity CPU consumption.
- D) Deployment pipelines consume negligible capacity resources during deployment operations and are not the cause of sustained high CPU.

</details>

---

### Question 17

A data analyst writes the following KQL query:

```kql
StormEvents
| summarize EventCount = count() by State
| top 5 by EventCount desc
```

What does this query return?

- A) All storm events grouped by state
- B) The 5 states with the fewest storm events
- C) The 5 states with the most storm events
- D) A single count of all storm events across all states

<details>
<summary>Show Answer</summary>

**Correct Answer: C) The 5 states with the most storm events**

**Explanation:** The query first uses `summarize` to count events per state, then uses `top 5 by EventCount desc` to return only the 5 states with the highest event counts. The `desc` keyword sorts in descending order, so the highest counts appear first.

**Why other options are incorrect:**
- A) The `top 5` operator limits the output to 5 rows, not all states.
- B) The `desc` keyword sorts from highest to lowest, not lowest to highest. Using `asc` would return the fewest.
- D) The `by State` clause in `summarize` groups counts per state rather than producing a single total.

</details>

---

### Question 18

You are creating a Power BI report and need to add a visual that automatically generates a written narrative summarizing key trends and insights from the data on the page. Which visual should you use?

- A) Card visual
- B) Q&A visual
- C) Smart Narratives visual
- D) KPI visual

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Smart Narratives visual**

**Explanation:** The Smart Narratives visual uses natural language generation to automatically create text-based summaries of data and trends visible on the report page. It dynamically updates as filters and slicers change, providing contextual written insights.

**Why other options are incorrect:**
- A) A Card visual displays a single numeric value or measure, not a written narrative summary.
- B) The Q&A visual allows users to ask questions in natural language but does not automatically generate narrative summaries.
- D) A KPI visual shows a key performance indicator against a target but does not generate text-based narratives.

</details>

---

### Question 19

A Power BI workspace contains a semantic model that has been validated and approved by the data governance team. The team wants to indicate to all users that this dataset is trustworthy and recommended for use. What should they do?

- A) Apply a sensitivity label of "Confidential"
- B) Endorse the dataset as "Certified"
- C) Endorse the dataset as "Promoted"
- D) Move the dataset to a personal workspace

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Endorse the dataset as "Certified"**

**Explanation:** Certification is the highest level of endorsement in Power BI and indicates that a dataset has been reviewed and approved by an authorized team. Only users with specific permissions (granted by the tenant admin) can certify content, signaling it as a trusted, authoritative source.

**Why other options are incorrect:**
- A) Sensitivity labels classify data for protection purposes but do not indicate data quality or trustworthiness.
- C) "Promoted" endorsement indicates that the dataset is valuable and ready for use but does not convey formal governance approval like certification does.
- D) Moving to a personal workspace would restrict access and is the opposite of making it broadly available and trusted.

</details>

---

### Question 20

You are using Performance Analyzer in Power BI Desktop to diagnose a slow report page. The results show that a particular visual has a high "DAX query" duration. What does this indicate?

- A) The visual is taking a long time to render in the browser.
- B) The underlying DAX query sent to the semantic model is taking a long time to execute.
- C) The data source connection is slow.
- D) The visual is waiting for a slicer selection.

<details>
<summary>Show Answer</summary>

**Correct Answer: B) The underlying DAX query sent to the semantic model is taking a long time to execute.**

**Explanation:** Performance Analyzer breaks down visual load times into components: DAX query time (time for the engine to execute the query), visual display time, and other processing. A high DAX query duration means the analytical query against the semantic model is the bottleneck.

**Why other options are incorrect:**
- A) Browser rendering time is captured separately as "Visual display" time in Performance Analyzer.
- C) Data source connection issues would appear during refresh operations, not in DAX query execution against an already-loaded semantic model.
- D) Performance Analyzer measures execution time, not wait time for user interaction.

</details>

---

### Question 21

You need to write a DAX query that returns a table of customers who have made more than 10 purchases. Which query is correct?

- A) `EVALUATE FILTER(Customers, COUNTROWS(RELATEDTABLE(Sales)) > 10)`
- B) `EVALUATE CALCULATE(COUNTROWS(Sales), Sales[Count] > 10)`
- C) `SELECT * FROM Customers WHERE PurchaseCount > 10`
- D) `EVALUATE Customers | where PurchaseCount > 10`

<details>
<summary>Show Answer</summary>

**Correct Answer: A) `EVALUATE FILTER(Customers, COUNTROWS(RELATEDTABLE(Sales)) > 10)`**

**Explanation:** The EVALUATE statement returns a table. FILTER iterates the Customers table, and for each customer, RELATEDTABLE(Sales) returns the related rows from Sales. COUNTROWS counts those rows, and only customers with more than 10 related sales rows are returned.

**Why other options are incorrect:**
- B) CALCULATE returns a scalar value, not a table, and the filter syntax `Sales[Count] > 10` is not a valid filter argument in this context.
- C) This is T-SQL syntax, not DAX. DAX queries use EVALUATE, not SELECT/FROM/WHERE.
- D) The pipe operator `|` is KQL syntax, not DAX.

</details>

---

### Question 22

A report page has six visuals. When a user clicks a data point in one visual, all other visuals update to show related data. This behavior is known as:

- A) Drill-through
- B) Cross-filtering and cross-highlighting
- C) Bookmarking
- D) Row-level security

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Cross-filtering and cross-highlighting**

**Explanation:** Cross-filtering and cross-highlighting are the default visual interaction behaviors in Power BI. When a user selects a data point in one visual, other visuals on the same page are automatically filtered or highlighted to reflect the selected context.

**Why other options are incorrect:**
- A) Drill-through navigates to a different report page, not interactions between visuals on the same page.
- C) Bookmarking saves a captured state of the report but is not the mechanism for live inter-visual interactions.
- D) Row-level security restricts data access per user and does not control visual interactions.

</details>

---

### Question 23

A data team wants to set up CI/CD for their Microsoft Fabric workspace. They plan to use Azure DevOps. Which combination of features enables this?

- A) Sensitivity labels and data endorsement
- B) Git integration and deployment pipelines
- C) Capacity Metrics app and Performance Analyzer
- D) Q&A visual and Smart Narratives

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Git integration and deployment pipelines**

**Explanation:** Git integration connects a Fabric workspace to a Git repository in Azure DevOps (or GitHub), enabling source control. Deployment pipelines manage the promotion of content through Development, Test, and Production stages. Together, they form the CI/CD workflow for Fabric.

**Why other options are incorrect:**
- A) Sensitivity labels and data endorsement relate to governance and data classification, not CI/CD processes.
- C) The Capacity Metrics app and Performance Analyzer are monitoring and diagnostics tools, not CI/CD tools.
- D) Q&A visual and Smart Narratives are report visualization features unrelated to CI/CD.

</details>

---

### Question 24

In a KQL query, you need to calculate the 95th percentile of response times grouped by service name. Which query achieves this?

- A) `Requests | summarize p95 = percentile(ResponseTime, 95) by ServiceName`
- B) `Requests | where ResponseTime > percentile(95)`
- C) `SELECT ServiceName, PERCENTILE_CONT(0.95) FROM Requests GROUP BY ServiceName`
- D) `Requests | extend p95 = percentile(ResponseTime, 95) | project ServiceName, p95`

<details>
<summary>Show Answer</summary>

**Correct Answer: A) `Requests | summarize p95 = percentile(ResponseTime, 95) by ServiceName`**

**Explanation:** In KQL, the `percentile()` aggregation function is used within a `summarize` operator. This query groups the data by ServiceName and calculates the 95th percentile of ResponseTime for each group.

**Why other options are incorrect:**
- B) `percentile(95)` in a `where` clause is syntactically incorrect; `percentile` is an aggregation function that requires a column reference and must be used with `summarize`.
- C) This is T-SQL syntax, not KQL.
- D) The `extend` operator adds a column to each row, but `percentile` is an aggregation function that must be used with `summarize`, not `extend`.

</details>

---

### Question 25

A Power BI report is embedded in a Microsoft Teams channel. Users report that the report loads slowly. Which tool should you use first to identify the bottleneck?

- A) DAX Studio
- B) Performance Analyzer in Power BI Desktop
- C) Microsoft Fabric Capacity Metrics app
- D) SQL Server Profiler

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Microsoft Fabric Capacity Metrics app**

**Explanation:** When reports are slow in the Power BI service (including Teams embeds), the Capacity Metrics app is the best first step. It shows whether the capacity is under resource pressure (CPU throttling, memory constraints) that could cause slow load times for all users on that capacity.

**Why other options are incorrect:**
- A) DAX Studio is useful for query-level diagnostics but is a desktop tool that connects directly to models, not for diagnosing service-level capacity issues.
- B) Performance Analyzer runs in Power BI Desktop and measures local rendering performance; it does not capture service-side capacity constraints.
- D) SQL Server Profiler is used for on-premises SQL Server tracing and is not the appropriate tool for Power BI service performance monitoring.

</details>

---

### Question 26

You are configuring a drill-through page in Power BI. Which step is required to enable drill-through functionality on the target page?

- A) Add a bookmark that captures the filter state
- B) Add the drill-through field to the drill-through filter well on the target page
- C) Create a DAX measure that references the source page
- D) Enable cross-filtering between all visuals on the source page

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Add the drill-through field to the drill-through filter well on the target page**

**Explanation:** To configure drill-through, you must add one or more fields to the "Drill through" filter well on the target (detail) page. This tells Power BI which fields can be used as filter context when a user drills through from another page.

**Why other options are incorrect:**
- A) Bookmarks capture report state but are not required for drill-through configuration.
- C) No special DAX measure referencing another page is needed for drill-through; it is configured through the field well.
- D) Cross-filtering is the default visual interaction behavior and is unrelated to drill-through page setup.

</details>

---

### Question 27

An organization has three levels of data endorsement for Power BI content. Which of the following correctly describes the endorsement hierarchy from lowest to highest trust level?

- A) Certified → Promoted → No endorsement
- B) No endorsement → Promoted → Certified
- C) Promoted → No endorsement → Certified
- D) No endorsement → Certified → Promoted

<details>
<summary>Show Answer</summary>

**Correct Answer: B) No endorsement → Promoted → Certified**

**Explanation:** In Power BI, content starts with no endorsement. Content owners can mark items as "Promoted" to indicate they are valuable and ready for use. "Certified" is the highest level, requiring explicit authorization from a tenant administrator, and signals that the content has been formally reviewed and approved.

**Why other options are incorrect:**
- A) This reverses the hierarchy, placing Certified at the lowest level.
- C) This places "No endorsement" between Promoted and Certified, which is incorrect.
- D) This incorrectly places Certified below Promoted in the trust hierarchy.

</details>

---

### Question 28

You are writing a T-SQL query against a Warehouse in Microsoft Fabric. You need to use a window function to calculate a running total of sales by date. Which query is correct?

- A) `SELECT OrderDate, SalesAmount, SUM(SalesAmount) OVER (ORDER BY OrderDate ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS RunningTotal FROM Sales`
- B) `SELECT OrderDate, SalesAmount, RUNNING_TOTAL(SalesAmount) FROM Sales`
- C) `SELECT OrderDate, SalesAmount, SUM(SalesAmount) FROM Sales GROUP BY OrderDate`
- D) `SELECT OrderDate, SalesAmount, CUMSUM(SalesAmount) OVER (ORDER BY OrderDate) FROM Sales`

<details>
<summary>Show Answer</summary>

**Correct Answer: A) `SELECT OrderDate, SalesAmount, SUM(SalesAmount) OVER (ORDER BY OrderDate ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS RunningTotal FROM Sales`**

**Explanation:** The OVER clause with SUM creates a window function that calculates a running total. The ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW specification ensures the sum includes all prior rows up to and including the current row, ordered by OrderDate.

**Why other options are incorrect:**
- B) RUNNING_TOTAL is not a valid T-SQL function.
- C) This GROUP BY query would return the sum per date, not a running total across dates, and would also require aggregating SalesAmount.
- D) CUMSUM is not a valid T-SQL function; Python/pandas uses cumsum, but T-SQL uses SUM with an OVER clause.

</details>

---

### Question 29

A Power BI report uses a slicer to filter data by year. The report designer wants to save a default view where the slicer is set to the current year. Which feature should be used?

- A) Default filter set via persistent filters
- B) Personal bookmarks
- C) Report-level bookmark set as default
- D) Row-level security rule

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Report-level bookmark set as default**

**Explanation:** A report-level bookmark captures the state of the report, including slicer selections. By creating a bookmark with the current year selected and setting it as the default bookmark for the page, all users will see the report with the current year pre-selected when they first open it.

**Why other options are incorrect:**
- A) Persistent filters remember user-specific filter choices but are not designed for setting a designer-controlled default for all users.
- B) Personal bookmarks are per-user and cannot be set as a default for all report consumers.
- D) Row-level security restricts data access per user identity, not slicer defaults.

</details>

---

### Question 30

An analyst notices unexpected spikes in website traffic data displayed in a Power BI line chart. They want to quickly identify possible explanations for these spikes. Which feature should they enable on the line chart?

- A) Forecast
- B) Anomaly Detection with explanations
- C) Trend line
- D) Constant line

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Anomaly Detection with explanations**

**Explanation:** Power BI's Anomaly Detection feature not only flags unexpected data points in a time series but also provides AI-generated explanations suggesting possible causes for the anomalies. Users can click on a flagged point to see factors that may have contributed to the spike.

**Why other options are incorrect:**
- A) Forecast projects future values based on historical trends but does not explain past anomalies.
- C) A trend line shows the overall direction of the data over time but does not flag or explain individual unexpected data points.
- D) A constant line draws a static reference line at a fixed value and provides no analytical insight into data anomalies.

</details>

---

### Question 31

When connecting a Microsoft Fabric workspace to a Git repository, what happens when you commit changes from the workspace?

- A) All workspace items are deleted and recreated from the Git repository.
- B) The workspace item definitions are serialized and pushed to the connected Git branch.
- C) The workspace data, including all table rows, is committed to the Git repository.
- D) A new deployment pipeline stage is automatically created.

<details>
<summary>Show Answer</summary>

**Correct Answer: B) The workspace item definitions are serialized and pushed to the connected Git branch.**

**Explanation:** When committing changes from a Fabric workspace to Git, the item definitions (metadata, configurations, queries, report layouts) are serialized into files and pushed to the connected branch. The actual data stored in tables or datasets is not committed to Git.

**Why other options are incorrect:**
- A) Committing does not delete and recreate items; it syncs item definitions to the repository.
- C) Git stores item definitions, not the underlying data rows, as data volumes would make Git impractical.
- D) Deployment pipelines are configured separately and are not automatically created by Git commits.

</details>

---

### Question 32

A data analyst needs to write a KQL query that joins two tables: Requests and Users. They want to return all requests along with the user name from the Users table based on a matching UserId. Which KQL operator should they use?

- A) `union`
- B) `join kind=inner`
- C) `lookup`
- D) `extend`

<details>
<summary>Show Answer</summary>

**Correct Answer: C) `lookup`**

**Explanation:** The `lookup` operator in KQL is optimized for enriching a fact table (Requests) with dimension data (Users) where you need to add columns from a smaller reference table. It is similar to a left outer join with a dimension table and is efficient for this pattern.

**Why other options are incorrect:**
- A) `union` combines rows from multiple tables vertically (appends) rather than joining columns horizontally based on a key.
- B) `join kind=inner` would also work functionally but `lookup` is the preferred and more efficient operator when enriching a large table with attributes from a smaller table.
- D) `extend` adds calculated columns to a table but does not join data from another table.

</details>

---

### Question 33

You are reviewing a DAX query that another analyst wrote:

```dax
EVALUATE
ADDCOLUMNS(
    VALUES(Products[Category]),
    "Total Sales", [Total Sales],
    "Pct of Grand Total", DIVIDE([Total Sales], CALCULATE([Total Sales], REMOVEFILTERS(Products[Category])))
)
```

What does the `REMOVEFILTERS(Products[Category])` function do in this context?

- A) It removes the Products table entirely from the model.
- B) It clears the filter on Products[Category] so that [Total Sales] calculates the grand total across all categories.
- C) It removes duplicate category values from the result.
- D) It filters Products[Category] to only the current row value.

<details>
<summary>Show Answer</summary>

**Correct Answer: B) It clears the filter on Products[Category] so that [Total Sales] calculates the grand total across all categories.**

**Explanation:** REMOVEFILTERS is a CALCULATE modifier that clears existing filters on the specified column. In this context, it removes the per-category filter so that [Total Sales] in the denominator evaluates across all categories, enabling the percentage of grand total calculation.

**Why other options are incorrect:**
- A) REMOVEFILTERS clears filter context on specified columns, not the table itself from the model.
- C) REMOVEFILTERS does not deduplicate values; it modifies filter context.
- D) REMOVEFILTERS clears the filter, which is the opposite of restricting to the current row value.

</details>

---

### Question 34

A Power BI workspace has multiple reports, datasets, and dataflows. An administrator wants to ensure that when any artifact is exported to Excel or PDF, it carries the "Highly Confidential" protection. What should the administrator do?

- A) Configure row-level security on all datasets.
- B) Apply a "Highly Confidential" sensitivity label to the workspace or individual artifacts.
- C) Endorse all artifacts as "Certified."
- D) Disable export functionality in the tenant settings.

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Apply a "Highly Confidential" sensitivity label to the workspace or individual artifacts.**

**Explanation:** Sensitivity labels from Microsoft Purview can be applied to Power BI artifacts. When content with a sensitivity label is exported to supported formats like Excel or PDF, the protection (such as encryption and access restrictions) defined by the label travels with the exported file.

**Why other options are incorrect:**
- A) Row-level security restricts data visibility per user within Power BI but does not apply protection to exported files.
- C) Endorsement indicates data quality and trustworthiness but does not enforce information protection on exports.
- D) Disabling export entirely would prevent all exports, which is overly restrictive and does not allow controlled sharing of protected content.

</details>

---

### Question 35

Which of the following visual types in Power BI is most appropriate for showing progress toward a single measurable goal?

- A) Scatter chart
- B) Funnel chart
- C) KPI visual
- D) Ribbon chart

<details>
<summary>Show Answer</summary>

**Correct Answer: C) KPI visual**

**Explanation:** The KPI (Key Performance Indicator) visual is specifically designed to display the current value of a metric, its trend over time, and progress toward a defined target or goal. It is the purpose-built visual for tracking goal attainment.

**Why other options are incorrect:**
- A) A scatter chart shows relationships between two or more numeric variables and is not designed for goal tracking.
- B) A funnel chart shows values across stages of a process (e.g., sales pipeline) but is not designed for single-metric goal tracking.
- D) A ribbon chart shows ranking changes across time periods but is not designed for tracking progress toward a specific goal.

</details>

---

### Question 36

You are analyzing a slow-performing report in the Power BI service. The Capacity Metrics app shows that the semantic model's memory footprint is very large. Which optimization strategy would most directly reduce the model's memory consumption?

- A) Add more visuals to the report to distribute the load.
- B) Remove unnecessary columns and reduce cardinality of high-cardinality columns in the semantic model.
- C) Increase the frequency of scheduled refreshes.
- D) Add more bookmarks to the report.

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Remove unnecessary columns and reduce cardinality of high-cardinality columns in the semantic model.**

**Explanation:** The VertiPaq engine compresses data in memory, and model size is primarily driven by the number of columns and the cardinality (number of unique values) in those columns. Removing unused columns and reducing cardinality (e.g., by rounding timestamps or grouping sparse values) directly reduces memory consumption.

**Why other options are incorrect:**
- A) Adding more visuals increases the number of queries against the model, which worsens performance rather than reducing memory consumption.
- C) Increasing refresh frequency does not reduce the model's memory footprint; it may temporarily increase resource usage during refresh operations.
- D) Bookmarks are report-level metadata and have no impact on semantic model memory consumption.

</details>

---

### Question 37

A KQL query needs to render a time chart showing the count of events per hour over the last 7 days. Which query achieves this?

- A) `Events | where Timestamp > ago(7d) | summarize count() by bin(Timestamp, 1h) | render timechart`
- B) `Events | where Timestamp > ago(7d) | count() | render timechart`
- C) `Events | take 7d | summarize count() by Timestamp`
- D) `Events | where Timestamp > ago(7d) | project Timestamp, count()`

<details>
<summary>Show Answer</summary>

**Correct Answer: A) `Events | where Timestamp > ago(7d) | summarize count() by bin(Timestamp, 1h) | render timechart`**

**Explanation:** This query filters events to the last 7 days, groups them into 1-hour bins using `bin(Timestamp, 1h)`, counts events per bin, and renders the results as a time chart. This is the standard KQL pattern for time series visualization.

**Why other options are incorrect:**
- B) `count()` without `summarize` and `by` returns a single row with the total count, not a time series.
- C) `take` returns a random sample of rows, not a time-based filter, and does not accept a time duration.
- D) `count()` is an aggregation function and cannot be used with `project`; it must be used with `summarize`.

</details>

---

### Question 38

A report developer needs to allow users to switch between a chart view and a table view of the same data on a single report page. Which approach requires the least development effort?

- A) Create two separate reports and share links to both.
- B) Use bookmarks to toggle visibility between the chart and the table visual.
- C) Create a drill-through page for the table view.
- D) Use a custom visual from AppSource that combines both views.

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use bookmarks to toggle visibility between the chart and the table visual.**

**Explanation:** The simplest approach is to place both visuals on the page, use the Selection pane to configure visibility states, create two bookmarks (one showing the chart, one showing the table), and assign action buttons to toggle between them. This is a built-in Power BI capability requiring no external tools.

**Why other options are incorrect:**
- A) Creating two separate reports is significantly more effort and provides a fragmented user experience.
- C) Drill-through navigates to a different page and requires user right-click interaction, which is more complex and less intuitive for simple view toggling.
- D) Custom visuals may introduce dependencies and additional complexity compared to the built-in bookmark approach.

</details>

---

### Question 39

When querying a Microsoft Fabric Warehouse using T-SQL, which of the following statements is TRUE about the SQL analytics endpoint?

- A) It supports full DML operations including INSERT, UPDATE, and DELETE.
- B) It provides read-only access to the data using T-SQL SELECT queries.
- C) It requires Apache Spark to execute queries.
- D) It only supports KQL, not T-SQL.

<details>
<summary>Show Answer</summary>

**Correct Answer: B) It provides read-only access to the data using T-SQL SELECT queries.**

**Explanation:** The SQL analytics endpoint for a Lakehouse provides a read-only T-SQL interface for querying data stored in the Lakehouse. It automatically exposes Delta tables as SQL tables that can be queried with standard T-SQL SELECT statements. Note that a Warehouse supports full DML, but the Lakehouse SQL analytics endpoint is read-only.

**Why other options are incorrect:**
- A) The Lakehouse SQL analytics endpoint is read-only; full DML operations (INSERT, UPDATE, DELETE) are supported in the Warehouse, not the Lakehouse SQL analytics endpoint.
- C) The SQL analytics endpoint uses a SQL compute engine, not Apache Spark.
- D) The SQL analytics endpoint supports T-SQL, not KQL. KQL is used with KQL databases in Real-Time Analytics.

</details>

---

### Question 40

A Power BI content creator wants to promote a semantic model so that other users in the organization can easily discover it. The creator does not have certification permissions. What endorsement level can they apply?

- A) Certified
- B) Promoted
- C) Verified
- D) Endorsed

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Promoted**

**Explanation:** Any content owner or member of a workspace can promote content to indicate it is valuable and ready for broader use. Certification, on the other hand, requires specific permissions granted by the tenant administrator. "Promoted" is the endorsement level available without special permissions.

**Why other options are incorrect:**
- A) Certified endorsement requires permissions that must be granted by the tenant admin; a regular content creator cannot certify content.
- C) "Verified" is not a valid Power BI endorsement level.
- D) "Endorsed" is not a specific endorsement level in Power BI; the levels are None, Promoted, and Certified.

</details>

---

### Question 41

You are building a dashboard in the Power BI service. A dashboard tile should show real-time data from a streaming dataset. Which type of tile is most appropriate?

- A) A tile pinned from a paginated report
- B) A custom streaming data tile
- C) A tile pinned from a Q&A question
- D) A tile pinned from an Excel workbook

<details>
<summary>Show Answer</summary>

**Correct Answer: B) A custom streaming data tile**

**Explanation:** Custom streaming data tiles on Power BI dashboards connect directly to streaming datasets and update in real-time as new data arrives. They are specifically designed for real-time monitoring scenarios where data is pushed continuously.

**Why other options are incorrect:**
- A) Tiles pinned from paginated reports display static snapshots and require manual or scheduled refresh, not real-time updates.
- C) A Q&A tile shows a static answer to a natural language question and does not provide real-time streaming capabilities.
- D) Tiles pinned from Excel workbooks are static and require manual refresh.

</details>

---

### Question 42

An analyst writes the following DAX query to analyze sales data:

```dax
EVALUATE
TOPN(
    5,
    SUMMARIZECOLUMNS(
        Products[ProductName],
        "TotalQuantity", SUM(Sales[Quantity])
    ),
    [TotalQuantity], DESC
)
```

What does this query return?

- A) The bottom 5 products by quantity sold
- B) The top 5 products by quantity sold
- C) All products sorted by quantity in descending order
- D) A random sample of 5 products

<details>
<summary>Show Answer</summary>

**Correct Answer: B) The top 5 products by quantity sold**

**Explanation:** TOPN returns the top N rows from a table based on a specified expression and sort order. Here, it takes the top 5 rows from the summarized table, ranked by TotalQuantity in descending order, returning the products with the highest quantities sold.

**Why other options are incorrect:**
- A) The DESC sort order means highest values first, so it returns the top 5, not the bottom 5.
- C) TOPN limits the output to 5 rows; it does not return all products.
- D) TOPN is deterministic based on the sort expression, not random.

</details>

---

### Question 43

A tenant administrator has enabled sensitivity labels for Power BI. A dataset has the label "General" applied. A report built on this dataset has no label. What happens to the report's sensitivity label?

- A) The report inherits the "General" label from the dataset automatically through downstream inheritance.
- B) The report remains unlabeled because labels must be manually applied.
- C) The report automatically gets a "Confidential" label.
- D) An error prevents the report from being created.

<details>
<summary>Show Answer</summary>

**Correct Answer: A) The report inherits the "General" label from the dataset automatically through downstream inheritance.**

**Explanation:** When sensitivity label inheritance is enabled in the Power BI tenant, downstream artifacts (like reports) automatically inherit the sensitivity label from their upstream data source (the dataset). This ensures consistent data protection across dependent items.

**Why other options are incorrect:**
- B) With inheritance enabled, labels propagate automatically; manual application is not required for downstream items.
- C) The report inherits the label of its source dataset ("General"), not an arbitrary higher-level label.
- D) No error occurs; the report is created and the label is inherited.

</details>

---

### Question 44

You are using the Capacity Metrics app and notice frequent "overload" events on your Fabric capacity. Which of the following is NOT a recommended mitigation strategy?

- A) Scaling up to a higher capacity SKU
- B) Distributing workloads across multiple capacities
- C) Adding more sensitivity labels to datasets
- D) Optimizing queries and models to reduce resource consumption

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Adding more sensitivity labels to datasets**

**Explanation:** Sensitivity labels are for data classification and information protection purposes. They have no impact on capacity resource consumption or overload conditions. The other options—scaling up, distributing workloads, and optimizing queries—are all valid strategies for addressing capacity overload.

**Why other options are incorrect:**
- A) Scaling to a larger SKU increases available compute and memory resources, directly addressing overload.
- B) Distributing workloads across multiple capacities reduces the resource demand on any single capacity.
- D) Optimizing queries and models reduces per-operation resource consumption, helping prevent overload.

</details>

---

### Question 45

A data engineer needs to query Eventhouse data in Microsoft Fabric to find the most recent event for each device. Which KQL query pattern is correct?

- A) `DeviceEvents | summarize arg_max(Timestamp, *) by DeviceId`
- B) `DeviceEvents | top 1 by Timestamp desc`
- C) `DeviceEvents | sort by Timestamp desc | take 1`
- D) `DeviceEvents | where Timestamp == max(Timestamp)`

<details>
<summary>Show Answer</summary>

**Correct Answer: A) `DeviceEvents | summarize arg_max(Timestamp, *) by DeviceId`**

**Explanation:** The `arg_max` aggregation function returns the row with the maximum value of the specified column (Timestamp) for each group (DeviceId). The `*` notation includes all other columns from that row. This is the idiomatic KQL pattern for "latest record per group."

**Why other options are incorrect:**
- B) `top 1` returns only a single row for the entire table, not one per device.
- C) `sort` then `take 1` also returns only a single row overall, not one per device.
- D) `max(Timestamp)` is an aggregation function and cannot be used directly in a `where` filter in this manner.

</details>

---

### Question 46

A Power BI report designer is choosing between a clustered bar chart and a stacked bar chart. In which scenario is a stacked bar chart more appropriate?

- A) When the goal is to compare individual category values side-by-side precisely
- B) When the goal is to show the composition of a total and how sub-categories contribute to each category's total
- C) When the goal is to show a trend over time
- D) When there is only one data series to display

<details>
<summary>Show Answer</summary>

**Correct Answer: B) When the goal is to show the composition of a total and how sub-categories contribute to each category's total**

**Explanation:** A stacked bar chart displays segments within each bar, showing how sub-categories combine to form the total for each category. This is ideal for visualizing part-to-whole relationships within categories.

**Why other options are incorrect:**
- A) Precise side-by-side comparison of individual values is better achieved with a clustered bar chart, where each value has its own separate bar.
- C) Trends over time are typically shown with line charts, not stacked bar charts.
- D) With only one data series, a stacked bar chart provides no additional value over a simple bar chart.

</details>

---

### Question 47

A deployment pipeline in Microsoft Fabric has three stages. An analyst deploys content from Development to Test but notices that some data source connection strings need to be different in the Test environment. How should this be handled?

- A) Manually edit the data source connections in the Test workspace after every deployment.
- B) Configure deployment rules in the pipeline to specify parameter or data source changes per stage.
- C) Use sensitivity labels to differentiate environments.
- D) Create separate, unlinked workspaces for each environment.

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Configure deployment rules in the pipeline to specify parameter or data source changes per stage.**

**Explanation:** Deployment pipelines support deployment rules that allow you to configure stage-specific settings such as different data source connection strings or parameter values. These rules are applied automatically during deployment, ensuring each stage connects to the correct environment.

**Why other options are incorrect:**
- A) Manually editing connections after each deployment is error-prone and not scalable; deployment rules automate this.
- C) Sensitivity labels are for data classification and do not control data source connections or environment-specific settings.
- D) Separate unlinked workspaces would break the deployment pipeline flow and require manual content synchronization.

</details>

---

### Question 48

Which of the following is a valid use case for using the EVALUATE statement in a DAX query against a published Power BI semantic model?

- A) Creating a new table permanently in the semantic model
- B) Returning a table of results for ad-hoc analysis without modifying the model
- C) Defining a scheduled refresh for the semantic model
- D) Applying row-level security roles

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Returning a table of results for ad-hoc analysis without modifying the model**

**Explanation:** The EVALUATE statement in DAX is used to execute ad-hoc queries that return table results from a semantic model. It does not modify the model, create tables, or change settings—it is purely for querying and analysis.

**Why other options are incorrect:**
- A) EVALUATE queries are read-only and do not create or modify objects in the semantic model.
- C) Scheduled refresh is configured through the Power BI service settings, not through DAX queries.
- D) Row-level security roles are defined in the model's security configuration, not through EVALUATE queries.

</details>

---

### Question 49

A team is using query diagnostics in Power BI Desktop to troubleshoot a report that takes a long time to load. The diagnostics output shows that a particular visual generates multiple queries. What is the most likely cause?

- A) The visual has a sensitivity label applied.
- B) The visual references multiple measures or has complex interactions that require separate query evaluations.
- C) The workspace is connected to a Git repository.
- D) The report has too many bookmarks.

<details>
<summary>Show Answer</summary>

**Correct Answer: B) The visual references multiple measures or has complex interactions that require separate query evaluations.**

**Explanation:** Power BI may generate multiple queries for a single visual when it uses multiple measures, has conditional formatting, or requires separate query evaluations for subtotals, grand totals, or visual-level filters. Complex visuals with many fields or calculations are the primary cause of multiple query generation.

**Why other options are incorrect:**
- A) Sensitivity labels are metadata for data protection and do not affect query generation or performance.
- C) Git integration manages source control for workspace items and does not influence how visuals generate queries.
- D) Bookmarks save report states but do not cause additional queries to be generated during visual rendering.

</details>

---

### Question 50

An organization requires that only specific, authorized teams can certify Power BI datasets. How is this controlled?

- A) By configuring row-level security on the dataset
- B) By specifying certification permissions through a security group in the Power BI tenant admin settings
- C) By applying a "Confidential" sensitivity label
- D) By assigning the datasets to a Premium capacity

<details>
<summary>Show Answer</summary>

**Correct Answer: B) By specifying certification permissions through a security group in the Power BI tenant admin settings**

**Explanation:** The Power BI tenant administrator controls who can certify content by specifying a security group in the admin portal's endorsement settings. Only members of the designated security group can apply the "Certified" endorsement to datasets and other content.

**Why other options are incorrect:**
- A) Row-level security controls data access within a dataset but has no relation to endorsement or certification permissions.
- C) Sensitivity labels classify data for protection purposes and do not control who can certify content.
- D) Premium capacity provides enhanced performance and features but does not control certification permissions.

</details>

---
