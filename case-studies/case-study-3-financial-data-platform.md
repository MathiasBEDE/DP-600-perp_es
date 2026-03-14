# Case Study 3: Financial Data Platform

## Scenario

GlobalEdge Financial is a mid-tier financial services company managing approximately $50 billion in assets across equities, fixed income, derivatives, and alternative investments. The firm employs 3,200 staff across trading desks in New York, London, and Singapore and processes an average of 4.5 million trades per day across all asset classes.

The company's current analytics infrastructure relies on a legacy on-premises data warehouse running on Oracle Exadata, supplemented by dozens of Excel-based risk models maintained by individual trading desks. Regulatory reporting for Basel III capital requirements and SEC filings is a largely manual process that takes the compliance team two weeks each quarter to compile. Senior leadership has mandated a migration to Microsoft Fabric to consolidate analytics, enable real-time trading insights, and automate regulatory reporting.

The Chief Risk Officer has identified several critical pain points: traders lack real-time visibility into portfolio risk exposures, the fraud detection system generates excessive false positives (currently 85%), and the firm recently received a regulatory finding for late submission of a Volcker Rule compliance report. The CTO wants to implement a unified data platform that can handle both real-time streaming analytics for the trading floor and batch processing for end-of-day risk calculations and regulatory submissions.

GlobalEdge's data engineering team consists of 15 engineers, most experienced with Python and Spark. The firm also has a dedicated compliance technology team of 5 analysts who build regulatory reports. The trading technology group manages the real-time market data infrastructure and connectivity to exchanges. All three teams will collaborate on the Fabric implementation, but each team needs distinct workspaces with appropriate access controls.

The firm's CISO requires that all data be classified using Microsoft Purview sensitivity labels, with trading position data labeled as "Highly Confidential" and restricted to specific trading desks using row-level security. The deployment must follow a strict Dev → Test → Prod pipeline with change management approvals, and all environments must maintain SOX audit trails. The platform must achieve 99.95% uptime during market hours (9:30 AM–4:00 PM ET on business days).

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        MICROSOFT FABRIC PLATFORM                        │
│                                                                         │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────────────────┐    │
│  │  Eventstream  │   │  Eventstream  │   │     OneLake (Lakehouse)  │    │
│  │  (Market Data)│   │  (Trade Exec) │   │  ┌────────┐ ┌────────┐ │    │
│  └──────┬───────┘   └──────┬───────┘   │  │ Bronze │ │ Silver │ │    │
│         │                   │           │  │ (Raw)  │→│(Curated│ │    │
│         ▼                   ▼           │  └────────┘ └───┬────┘ │    │
│  ┌──────────────────────────────┐      │              ┌───▼────┐ │    │
│  │       KQL Database           │      │              │ Gold   │ │    │
│  │   (Real-Time Analytics)      │      │              │(Served)│ │    │
│  └──────────────┬───────────────┘      │              └────────┘ │    │
│                 │                       └──────────────────┬─────┘    │
│                 ▼                                          │          │
│  ┌──────────────────────┐    ┌────────────────────────────▼────┐     │
│  │  Real-Time Dashboard │    │      Spark Notebooks            │     │
│  │  (Trading Floor)     │    │  (Risk Models / Basel III Calc) │     │
│  └──────────────────────┘    └────────────────┬───────────────┘     │
│                                                │                     │
│                 ┌──────────────────────────────▼──────────┐          │
│                 │         Semantic Model                   │          │
│                 │  (Risk Analytics / Regulatory Reports)   │          │
│                 └──────────────────────┬─────────────────┘          │
│                                        │                             │
│                 ┌──────────────────────▼──────────┐                  │
│                 │       Power BI Reports           │                  │
│                 │  (Exec Dashboards / SEC Reports) │                  │
│                 └─────────────────────────────────┘                  │
└─────────────────────────────────────────────────────────────────────────┘
         ▲              ▲                ▲                ▲
         │              │                │                │
   ┌─────┴─────┐ ┌─────┴──────┐  ┌─────┴──────┐  ┌─────┴──────────┐
   │ Event Hub  │ │  Oracle DB  │  │ Python/    │  │  Dynamics 365  │
   │ (Market   │ │  (Trading   │  │ Spark Risk │  │  (Customer     │
   │  Data Feed)│ │   System)   │  │  Models    │  │   Data / KYC)  │
   └───────────┘ └────────────┘  └────────────┘  └────────────────┘
```

## Requirements

- **Real-time ingestion** of streaming market data (quotes, Level 2 order book) from Azure Event Hub into Fabric Eventstreams
- **Near real-time trade capture** from the Oracle trading system with latency under 30 seconds
- **End-of-day batch processing** for risk calculations (VaR, stress testing) using Spark notebooks with existing Python risk models
- **Basel III regulatory reports** generated automatically on a daily and quarterly basis
- **Fraud detection pipeline** that reduces false positive rate from 85% to below 20%
- **Executive dashboards** showing firm-wide P&L, risk exposures, and compliance status
- **Trading desk dashboards** with real-time position and risk data, refreshing every 10 seconds
- **Row-level security** ensuring each trading desk sees only its own positions
- **Sensitivity labels** applied automatically based on data classification (Public, Internal, Confidential, Highly Confidential)
- **Deployment pipelines** for Dev → Test → Prod with automated testing and approval gates
- **SOX-compliant audit trails** for all data transformations and report generation
- **Cross-region disaster recovery** with RPO < 1 hour and RTO < 4 hours

## Data Sources

| Source | System | Volume | Frequency | Ingestion Method |
|---|---|---|---|---|
| Market Data | Exchange feeds via Event Hub | ~500M events/day | Real-time streaming | Eventstream |
| Trade Executions | Oracle 19c Trading System | ~4.5M trades/day | Near real-time (CDC) | Dataflow Gen2 / Mirroring |
| Risk Model Outputs | Python/Spark batch jobs | ~200GB/day | End-of-day batch | Spark Notebooks |
| Customer / KYC Data | Dynamics 365 | ~2M records | Daily incremental | Dataflow Gen2 |
| Reference Data | Internal REST APIs | ~50K instruments | Hourly | Data Pipeline |
| Regulatory Filings | SEC EDGAR / Internal | ~500 documents/quarter | Quarterly batch | Manual + Pipeline |

## Constraints and Considerations

- **Regulatory Compliance:** SOX Section 404 controls required for all financial reporting data; SEC Rule 17a-4 requires immutable record retention for 6 years; Basel III calculations must follow standardized approach
- **Data Residency:** US trading data must remain in US East region; EU data (London desk) must comply with GDPR; Singapore data subject to MAS regulations
- **Performance SLAs:** Real-time dashboards must refresh within 10 seconds; end-of-day risk batch must complete by 8:00 PM ET; regulatory reports must be available by 6:00 AM ET on submission days
- **Security:** All Fabric items must have sensitivity labels; workspace access restricted by team function; service principal authentication for automated pipelines; all API calls logged to Azure Monitor
- **Capacity:** F64 capacity for Production; F32 for Test; F16 for Development; capacity auto-scaling enabled during market hours
- **Data Quality:** Trade data requires reconciliation against source systems with less than 0.01% variance tolerance; risk model outputs must be validated against prior-day results

---

## Questions

### Question 1

GlobalEdge needs to ingest real-time market data from Azure Event Hub into Fabric for the trading floor dashboards. The dashboards must display current market prices with no more than 5-second latency. Which combination of Fabric components should the data engineering team use?

- A) Use a Data Pipeline with a scheduled trigger every 5 seconds to copy data from Event Hub to a Lakehouse, then build Power BI reports on the Lakehouse tables
- B) Use Eventstream to ingest from Event Hub, route data to a KQL Database for real-time querying, and build Real-Time Dashboards using KQL queries
- C) Use Dataflow Gen2 to connect to Event Hub, transform the data, and load it into a warehouse table with DirectQuery reports
- D) Use a Spark Structured Streaming notebook to read from Event Hub and write to a Lakehouse Delta table, then use Power BI with DirectQuery

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use Eventstream to ingest from Event Hub, route data to a KQL Database for real-time querying, and build Real-Time Dashboards using KQL queries**

**Explanation:** Eventstream is the native Fabric component for real-time data ingestion from sources like Azure Event Hub. Routing the data to a KQL Database enables sub-second querying of streaming data using Kusto Query Language. Real-Time Dashboards in Fabric are specifically designed for low-latency visualization of KQL data and can auto-refresh at intervals as short as a few seconds. Option A would introduce unacceptable latency through scheduled pipeline triggers. Option C is incorrect because Dataflow Gen2 is designed for batch/micro-batch ETL, not real-time streaming. Option D could work technically but adds unnecessary complexity and higher latency compared to the native Eventstream → KQL path.

</details>

---

### Question 2

The compliance team needs to implement row-level security (RLS) so that traders on the Equities desk can only see equity positions, and Fixed Income traders can only see bond positions. The data is served through a semantic model in Power BI. How should this be implemented?

- A) Create separate semantic models for each trading desk and distribute different Power BI apps to each team
- B) Define RLS roles in the semantic model with DAX filter expressions on the TradingDesk column, and map Azure AD security groups for each desk to the appropriate roles
- C) Use workspace-level permissions to restrict each trading desk to a separate workspace containing only their data
- D) Implement column-level security in the Lakehouse to hide the TradingDesk column from unauthorized users

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Define RLS roles in the semantic model with DAX filter expressions on the TradingDesk column, and map Azure AD security groups for each desk to the appropriate roles**

**Explanation:** Row-level security (RLS) in Power BI semantic models is implemented by defining roles with DAX filter expressions that restrict which rows a user can see. By creating roles such as "EquitiesDesk" with a filter like `[TradingDesk] = "Equities"` and mapping the corresponding Azure AD security group, each desk automatically sees only its own positions. Option A would create maintenance overhead with duplicate models. Option C restricts workspace access but doesn't provide row-level data filtering within a shared model. Option D addresses column-level security, not row-level security, and is configured in the Lakehouse rather than the semantic model where the reports are built.

</details>

---

### Question 3

The risk team runs end-of-day Value at Risk (VaR) calculations using existing Python models that leverage NumPy and SciPy. These calculations must process all trades from the day (approximately 4.5 million records) and complete by 8:00 PM ET. What is the best approach to integrate these models into Fabric?

- A) Rewrite the Python risk models in DAX and implement them as calculated measures in the semantic model
- B) Use Spark notebooks in Fabric with the existing Python code, leveraging PySpark for distributed processing of the trade data stored in the Lakehouse
- C) Export data from the Lakehouse to an external Azure VM running the Python scripts, then import results back via a Data Pipeline
- D) Use Dataflow Gen2 with custom Python transformations to run the VaR calculations during the data refresh

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use Spark notebooks in Fabric with the existing Python code, leveraging PySpark for distributed processing of the trade data stored in the Lakehouse**

**Explanation:** Fabric's Spark notebooks natively support Python and can leverage libraries like NumPy and SciPy. The existing Python risk models can be migrated with minimal changes and benefit from distributed Spark processing to handle the 4.5 million daily trades within the time window. The Lakehouse's Delta tables are directly accessible from Spark. Option A is impractical because DAX is not designed for complex statistical calculations like Monte Carlo simulations used in VaR. Option C introduces unnecessary complexity, data movement costs, and additional infrastructure to manage. Option D is limited because Dataflow Gen2's Python support is restricted and not suitable for heavy computational workloads like VaR calculations.

</details>

---

### Question 4

GlobalEdge must implement deployment pipelines for their Fabric content following a Dev → Test → Prod promotion process with SOX-compliant change management. Which approach best meets these requirements?

- A) Manually export .pbix files from Dev and import them into Test and Prod workspaces after email approval from the change management board
- B) Use Fabric deployment pipelines with three stages mapped to Dev, Test, and Prod workspaces, configure deployment rules for connection strings, and integrate with Azure DevOps for approval gates
- C) Use Git integration to maintain all Fabric items in a repository, with separate branches for each environment, and promote changes via pull requests only
- D) Create identical items in each workspace independently and apply changes to all three environments simultaneously to ensure consistency

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use Fabric deployment pipelines with three stages mapped to Dev, Test, and Prod workspaces, configure deployment rules for connection strings, and integrate with Azure DevOps for approval gates**

**Explanation:** Fabric deployment pipelines provide a native mechanism for promoting content across Dev, Test, and Prod stages. Deployment rules allow automatic adjustment of data source connection strings for each environment (e.g., pointing to test vs. production databases). Integration with Azure DevOps enables formal approval gates that satisfy SOX change management requirements with audit trails. Option A is error-prone and lacks automation or audit trails. Option C (Git integration) is complementary and useful for version control but doesn't directly provide the deployment pipeline with environment-specific parameter substitution. Option D creates significant maintenance burden and risk of environment drift.

</details>

---

### Question 5

The fraud detection team wants to reduce false positives from 85% to below 20%. They plan to use historical trade data and known fraud cases to build a predictive model. The model must score new trades in near real-time. Which Fabric approach is most appropriate?

- A) Build a machine learning model using Spark MLlib in a Fabric notebook, train on historical Lakehouse data, register it in the ML model registry, and use the model for batch scoring via a scheduled notebook
- B) Create complex DAX measures in the semantic model that implement fraud detection rules and flag suspicious trades in Power BI reports
- C) Build a machine learning model in a Fabric notebook, register it in the ML model registry, and deploy it using PREDICT function in a Spark Structured Streaming pipeline reading from Eventstream
- D) Export all trade data to Azure Machine Learning, build and deploy a model there, and use a REST API call from a Fabric Data Pipeline to score each trade

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Build a machine learning model in a Fabric notebook, register it in the ML model registry, and deploy it using PREDICT function in a Spark Structured Streaming pipeline reading from Eventstream**

**Explanation:** This approach combines ML model training on historical data in the Lakehouse with near real-time scoring using Spark Structured Streaming. The PREDICT function in Fabric allows applying a registered ML model to incoming streaming data without leaving the Fabric environment. This satisfies both the accuracy improvement goal (ML vs. rules) and the near real-time scoring requirement. Option A provides only batch scoring, not near real-time. Option B uses rule-based logic rather than machine learning, which is unlikely to achieve the false positive reduction target. Option D introduces unnecessary external dependencies and latency from REST API calls for each trade.

</details>

---

### Question 6

The compliance team must ensure that all trading position data is labeled as "Highly Confidential" and that sensitivity labels propagate correctly when data is exported to Excel or PDF. Which configuration is required?

- A) Apply sensitivity labels manually to each Power BI report and dashboard at the time of publication
- B) Configure Microsoft Purview Information Protection with default sensitivity labels for the Fabric workspace, enable mandatory labeling policy, and enable label inheritance so downstream items inherit labels from Lakehouse tables
- C) Use Azure Key Vault to encrypt all trading data at rest and in transit, which automatically applies sensitivity classifications
- D) Create a custom Data Pipeline that scans all Fabric items nightly and applies metadata tags for sensitivity classification

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Configure Microsoft Purview Information Protection with default sensitivity labels for the Fabric workspace, enable mandatory labeling policy, and enable label inheritance so downstream items inherit labels from Lakehouse tables**

**Explanation:** Microsoft Purview sensitivity labels integrate natively with Microsoft Fabric. By setting a default label for the workspace and enabling mandatory labeling, all new items automatically receive the appropriate classification. Label inheritance ensures that when data flows from a Lakehouse table to a semantic model to a Power BI report, the "Highly Confidential" label propagates downstream. When users export labeled content to Excel or PDF, the sensitivity label and its associated protection (e.g., encryption, watermarks) travel with the exported file. Option A is error-prone and doesn't scale. Option C addresses encryption but not data classification labeling. Option D is a custom solution that doesn't leverage native Purview integration.

</details>

---

### Question 7

GlobalEdge needs to ingest trade data from their Oracle 19c trading system into the Fabric Lakehouse with latency under 30 seconds. The Oracle system processes approximately 4.5 million trades per day. Which ingestion approach meets the latency requirement?

- A) Use Dataflow Gen2 with a scheduled refresh every 30 seconds to incrementally load new trades from Oracle
- B) Use database mirroring in Fabric to replicate Oracle data changes in near real-time to the Lakehouse
- C) Configure a Data Pipeline with a tumbling window trigger set to 30-second intervals, using a Copy Activity to pull from Oracle
- D) Use Fabric Eventstream with an Oracle CDC connector to capture changes and stream them into the Lakehouse

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use database mirroring in Fabric to replicate Oracle data changes in near real-time to the Lakehouse**

**Explanation:** Fabric's database mirroring provides near real-time replication of database changes into OneLake, making it the most suitable option for achieving sub-30-second latency from an operational database. Mirroring continuously captures changes and replicates them without requiring scheduled triggers. Option A would be unreliable at 30-second intervals because Dataflow Gen2 has startup overhead that makes such frequent refreshes impractical. Option C has similar overhead issues with pipeline trigger intervals. Option D describes an Eventstream approach, but Eventstream is designed for event streaming sources (like Event Hub, Kafka) rather than direct database CDC from Oracle. Note: If Oracle mirroring is not supported, an alternative would be to use Oracle GoldenGate or a similar CDC tool to publish changes to Event Hub, then use Eventstream.

</details>

---

### Question 8

The data engineering team needs to implement a medallion architecture (Bronze/Silver/Gold) in the Lakehouse for trade data. The Bronze layer stores raw data, Silver contains cleansed and conformed data, and Gold contains aggregated data for reporting. What is the recommended implementation in Fabric?

- A) Create three separate Lakehouses (Bronze, Silver, Gold), use Spark notebooks to transform data between layers, and use shortcuts in the Gold Lakehouse to reference Silver tables when needed
- B) Create a single Lakehouse with three schemas (bronze, silver, gold) and use stored procedures for transformations between layers
- C) Create three separate Warehouses (Bronze, Silver, Gold) and use T-SQL stored procedures to move data between them
- D) Create a single Lakehouse and use folder naming conventions (bronze/, silver/, gold/) with unmanaged Delta tables for each layer

<details>
<summary>Show Answer</summary>

**Correct Answer: A) Create three separate Lakehouses (Bronze, Silver, Gold), use Spark notebooks to transform data between layers, and use shortcuts in the Gold Lakehouse to reference Silver tables when needed**

**Explanation:** Using separate Lakehouses for each medallion layer is a Fabric best practice that provides clear separation of concerns, independent access control for each layer, and easier lifecycle management. Spark notebooks handle the transformations between layers using the full power of PySpark. Shortcuts allow the Gold layer to reference Silver data without data duplication. Option B is incorrect because Lakehouse does not natively support schemas in the same way as a Warehouse. Option C would work but using a Warehouse for raw Bronze data storage is less cost-effective and doesn't leverage the Lakehouse's optimized storage for large-scale raw data. Option D works but makes access control and governance more difficult compared to separate Lakehouses.

</details>

---

### Question 9

The Singapore trading desk reports that their real-time dashboards are experiencing high latency during the Asian market open (9:00 AM SGT). The Fabric capacity is F64 hosted in the US East region. What should the platform team do to address this?

- A) Increase the capacity from F64 to F128 to handle the additional load from the Singapore trading desk
- B) Create a separate Fabric capacity in the Southeast Asia region for the Singapore desk, replicate the necessary KQL databases, and configure Real-Time Dashboards against the regional KQL endpoint
- C) Schedule the Singapore dashboards to pre-render during off-peak hours and cache the results for the Asian market open
- D) Optimize the KQL queries used by the Singapore dashboards to reduce data scanned and improve response time

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Create a separate Fabric capacity in the Southeast Asia region for the Singapore desk, replicate the necessary KQL databases, and configure Real-Time Dashboards against the regional KQL endpoint**

**Explanation:** The latency issue is primarily caused by geographic distance between the Singapore users and the US East Fabric capacity. Network round-trip time across the Pacific significantly impacts the responsiveness of real-time dashboards that refresh every 10 seconds. Deploying a regional capacity in Southeast Asia with replicated KQL databases addresses the root cause. Option A adds compute capacity but doesn't address network latency. Option C defeats the purpose of real-time dashboards. Option D may provide marginal improvement but doesn't address the fundamental network latency issue. This also aligns with the MAS data residency requirements mentioned in the constraints.

</details>

---

### Question 10

GlobalEdge needs to produce Basel III regulatory capital reports on a daily basis. The reports combine trade positions, risk model outputs, and counterparty exposure data from multiple Lakehouse tables. The reports must be available by 6:00 AM ET each business day and must maintain a complete audit trail. How should this be implemented?

- A) Create a Power BI paginated report connected to the semantic model with a scheduled refresh at 5:00 AM ET, and enable Power BI audit logging
- B) Build an orchestration Data Pipeline that triggers after the end-of-day risk batch completes, runs a Spark notebook to calculate Basel III metrics, writes results to a Gold Lakehouse table with full lineage tracking, generates a paginated report, and logs all steps to a pipeline audit table
- C) Use Dataflow Gen2 to combine the source tables, calculate the regulatory metrics, and output the report to a SharePoint document library by 6:00 AM ET
- D) Schedule a Spark notebook at 5:00 AM ET to query all source tables, compute the Basel III metrics, and email the results as a CSV attachment to the compliance team

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Build an orchestration Data Pipeline that triggers after the end-of-day risk batch completes, runs a Spark notebook to calculate Basel III metrics, writes results to a Gold Lakehouse table with full lineage tracking, generates a paginated report, and logs all steps to a pipeline audit table**

**Explanation:** This approach provides end-to-end orchestration with proper dependency management (triggered after risk batch completion rather than a fixed time), combines Spark's computational power for complex Basel III calculations, persists results in the Gold Lakehouse for auditability, and maintains a complete audit trail through pipeline logging. SOX compliance requires that every step in regulatory report generation be traceable. Option A relies on a fixed time schedule without ensuring upstream data is ready and provides limited audit trail. Option C lacks the computational power for complex regulatory calculations. Option D produces only a CSV without proper audit trail, lineage tracking, or integration with the broader reporting infrastructure.

</details>
