# Case Study 5: Education Analytics Platform

## Scenario

PacificState University System (PSUS) is a public higher-education system comprising eight campuses spread across the state, serving approximately 120,000 students and employing over 15,000 faculty and staff. Each campus currently maintains its own data silos — separate Student Information Systems, Learning Management Systems, financial aid databases, and facilities management tools — making system-wide reporting a labor-intensive, error-prone process that often takes weeks to compile.

The Office of Institutional Research (OIR), reporting to the System Chancellor, has been tasked with building a **unified analytics platform** on Microsoft Fabric to support three strategic priorities: **student success and retention**, **enrollment management**, and **institutional research for accreditation**. The state legislature has mandated that PSUS improve its six-year graduation rate from 58% to 68% within five years, and the Board of Trustees requires quarterly dashboards tracking progress toward that goal.

Currently, enrollment funnel data is compiled manually from spreadsheets submitted by each campus admissions office. Retention risk indicators are calculated once per semester using static SQL queries against the Banner SIS, missing real-time signals from Canvas LMS activity. Financial aid packaging relies on PeopleSoft batch processes that run nightly, and there is no cross-campus view of how aid dollars correlate with retention outcomes. Research grant data lives in SharePoint lists maintained by individual principal investigators, making overhead-rate calculations for federal compliance unreliable. Classroom utilization data from IoT occupancy sensors is collected but never analyzed beyond the facilities department.

PSUS has a small central IT analytics team of six people (two data engineers, two analysts, one BI developer, and one data governance specialist). Each campus also has one or two local analysts who need self-service access to curated datasets. The platform must comply with the **Family Educational Rights and Privacy Act (FERPA)**, which restricts access to personally identifiable student information. Deans and department chairs should see data only for their own college or department, while the Chancellor's office and OIR need a cross-campus view. The system must also support accreditation reporting cycles for the Western Association of Schools and Colleges (WASC), which occur on a seven-year schedule with mid-cycle reviews.

The IT team has selected **Microsoft Fabric** as the platform and has an E5 license with Fabric capacity (F64). They need a phased rollout: Phase 1 covers enrollment and retention dashboards for Fall semester, Phase 2 adds financial aid and course performance analytics for Spring, and Phase 3 integrates research grants and facilities utilization by the following Fall. Deployment pipelines must support semester-based promotion of content from development to test to production workspaces.

---

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         DATA SOURCES (Ingestion Layer)                      │
├──────────────┬──────────────┬─────────────┬──────────────┬─────────────────┤
│ Banner SIS   │ Canvas LMS   │ PeopleSoft  │ SharePoint   │ IoT Sensors     │
│ (Oracle DB)  │ (REST API)   │ (On-prem    │ Lists        │ (Classroom      │
│              │              │  SQL Server) │ (Grants)     │  Utilization)   │
└──────┬───────┴──────┬───────┴──────┬──────┴──────┬───────┴────────┬────────┘
       │              │              │             │                │
       ▼              ▼              ▼             ▼                ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                     MICROSOFT FABRIC - OneLake (Lakehouse)                  │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │                        Bronze Layer (Raw)                             │  │
│  │  SIS_Raw  │  LMS_Raw  │  FinAid_Raw  │  Grants_Raw  │  Facility_Raw │  │
│  └───────────────────────┬───────────────────────────────────────────────┘  │
│                          │  Data Pipelines & Dataflows Gen2                 │
│  ┌───────────────────────▼───────────────────────────────────────────────┐  │
│  │                       Silver Layer (Cleansed)                         │  │
│  │  Students  │  Enrollments  │  Courses  │  FinAid  │  Facilities      │  │
│  │  (PII Tagged, FERPA-classified)                                      │  │
│  └───────────────────────┬───────────────────────────────────────────────┘  │
│                          │  Notebooks (Spark) & Stored Procedures           │
│  ┌───────────────────────▼───────────────────────────────────────────────┐  │
│  │                        Gold Layer (Curated)                           │  │
│  │  Retention_Fact  │  Enrollment_Funnel  │  Course_Perf  │  Aid_Alloc  │  │
│  │  Grant_Overhead  │  Classroom_Util     │  Accreditation_Metrics      │  │
│  └───────────────────────┬───────────────────────────────────────────────┘  │
│                          │                                                  │
│  ┌───────────────────────▼──────────────────┐  ┌────────────────────────┐  │
│  │         Warehouse (SQL Endpoint)          │  │   ML Model Endpoint    │  │
│  │  Institutional Research Ad-Hoc Queries    │  │   (Retention           │  │
│  │  Accreditation Reporting Views            │  │    Prediction - MLflow)│  │
│  └───────────────────────┬──────────────────┘  └───────────┬────────────┘  │
│                          │                                  │               │
│  ┌───────────────────────▼──────────────────────────────────▼────────────┐  │
│  │                  Semantic Models (Power BI)                            │  │
│  │  ┌─────────────┐  ┌──────────────┐  ┌─────────────────────────────┐  │  │
│  │  │ Enrollment & │  │ Financial    │  │ Facilities & Research       │  │  │
│  │  │ Retention    │  │ Aid & Course │  │ Grants                      │  │  │
│  │  │ Model        │  │ Perf Model   │  │ Model                       │  │  │
│  │  │ (RLS by      │  │ (RLS by      │  │ (RLS by campus/dept)        │  │  │
│  │  │  college)    │  │  college)    │  │                             │  │  │
│  │  └─────────────┘  └──────────────┘  └─────────────────────────────┘  │  │
│  │  Sensitivity Labels: FERPA-Confidential, FERPA-Directory, Internal   │  │
│  └──────────────────────────────────────────────────────────────────────┘  │
│                          │                                                  │
│  ┌───────────────────────▼──────────────────────────────────────────────┐  │
│  │               Deployment Pipelines (Dev → Test → Prod)               │  │
│  │         Semester-based promotions aligned to academic calendar        │  │
│  └──────────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         CONSUMERS                                           │
│  Chancellor's Office │ Deans │ Dept Chairs │ Campus Analysts │ OIR Staff   │
│  (Power BI Reports, Paginated Reports for Accreditation, Mobile App)       │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Requirements

### Functional Requirements
- **Student Retention Prediction**: ML model trained on historical enrollment, GPA, LMS engagement, and financial aid data to produce per-student risk scores each week
- **Enrollment Funnel Dashboards**: Real-time view from prospect → applicant → admitted → enrolled → retained, broken down by campus, college, and demographics
- **Course Performance Analytics**: Grade distributions, DFW (D/F/Withdraw) rates, section fill rates, and instructor effectiveness indicators
- **Financial Aid Allocation Optimization**: Cross-campus analysis of aid packaging strategies correlated with retention and graduation outcomes
- **Accreditation Reporting**: Pre-built paginated reports aligned to WASC standards with historical trend data spanning at least 10 years
- **Classroom Utilization**: IoT-driven dashboards showing room occupancy vs. scheduled capacity to optimize space allocation

### Technical Requirements
- Lakehouse architecture with Bronze/Silver/Gold medallion layers in OneLake
- Warehouse SQL endpoint for institutional research ad-hoc queries
- Semantic models with Row-Level Security (RLS) so deans see only their college's data
- Microsoft Purview sensitivity labels applied to all datasets containing student PII
- Deployment pipelines with Dev/Test/Prod workspaces, promoted on a semester schedule
- Data refresh: SIS and LMS data refreshed every 4 hours; financial aid nightly; IoT sensor data every 15 minutes
- Capacity management on F64 SKU with autoscale consideration during peak registration periods

---

## Data Sources

| Source | System | Connection | Volume | Refresh Frequency |
|---|---|---|---|---|
| Student Records | Banner SIS (Oracle 19c) | On-premises data gateway | ~5M records (10 years) | Every 4 hours |
| Learning Activity | Canvas LMS (Cloud) | REST API via Dataflow Gen2 | ~50M events/semester | Every 4 hours |
| Financial Aid | PeopleSoft (SQL Server 2019) | On-premises data gateway | ~2M records/year | Nightly batch |
| Research Grants | SharePoint Online Lists | Direct connector | ~5,000 active grants | Daily |
| Classroom Sensors | Azure IoT Hub | Event Stream / KQL Database | ~1M readings/day | Every 15 minutes |
| HR/Payroll | Workday (Cloud) | REST API | ~15,000 employee records | Weekly |
| External Benchmarks | IPEDS Federal Data | Flat CSV files | Annual snapshots | Annually |

---

## Constraints and Considerations

### Compliance (FERPA)
- All student PII (name, student ID, grades, financial aid) is classified as **FERPA-protected** and requires explicit consent for disclosure
- **Directory information** (name, enrollment status, major) may be shared more broadly but students can opt out
- Sensitivity labels must distinguish between FERPA-Confidential and FERPA-Directory data
- Audit logs must be retained for 7 years to satisfy federal records requirements
- Data sharing with external entities (e.g., state legislature) requires de-identification or aggregation to cohorts of ≥ 5

### Data Governance
- Microsoft Purview integration for data lineage tracking, data classification, and sensitivity labeling
- Data steward assigned per domain: Registrar (enrollment), Financial Aid Office (aid), Provost Office (academic), Facilities (IoT)
- Endorsement and certification of datasets — only **certified** semantic models may be used for accreditation reports
- Column-level security on fields like SSN, date of birth, and financial need analysis

### Service Level Agreements
- Dashboard availability: 99.5% during business hours (7 AM – 10 PM campus local time)
- Data freshness: SIS/LMS ≤ 6 hours stale; financial aid ≤ 24 hours stale
- Peak load: Registration periods (2 weeks each in November and April) see 3× normal query volume
- Accreditation report generation must complete within 30 minutes for any 10-year range

### Team Composition
| Role | Count | Responsibility |
|---|---|---|
| Lead Data Engineer | 1 | Pipeline architecture, lakehouse design, capacity planning |
| Data Engineer | 1 | Pipeline development, data quality monitoring |
| BI Developer | 1 | Semantic models, RLS, reports, deployment pipelines |
| Data Analyst (OIR) | 2 | Ad-hoc analysis, accreditation metrics, self-service content |
| Data Governance Specialist | 1 | FERPA compliance, sensitivity labels, Purview policies |
| Campus Analysts (distributed) | 8–16 | Self-service reporting, local data validation |

---

## Questions

### Question 1

PacificState University needs to ensure that the Dean of the College of Engineering on the Northridge campus can only view student retention data for Engineering students at Northridge, while the Chancellor's Office can view data for all colleges across all campuses. Which approach should the BI developer implement in the semantic model?

- A) Create separate semantic models for each college and campus combination, and distribute links to each dean
- B) Implement Row-Level Security (RLS) with DAX filter expressions that reference a campus-college mapping table linked to the user's Azure AD identity
- C) Use object-level security (OLS) to hide non-Engineering tables from the Dean of Engineering
- D) Create a Power BI app with separate navigation experiences for each college, relying on app-level permissions

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Implement Row-Level Security (RLS) with DAX filter expressions that reference a campus-college mapping table linked to the user's Azure AD identity**

**Explanation:** Row-Level Security (RLS) is the correct mechanism for restricting data visibility at the row level based on user identity. The BI developer should create a security table mapping Azure AD user principal names (UPNs) to their campus and college, then define RLS roles with DAX expressions like `[College] = LOOKUPVALUE(SecurityTable[College], SecurityTable[UPN], USERPRINCIPALNAME())`. Users in the Chancellor's Office would be assigned a role with no filter (or not assigned to any restrictive role if using dynamic RLS). Option A is impractical at scale (8 campuses × multiple colleges). Option C (OLS) hides columns or tables, not rows. Option D does not actually restrict data access — users could still access underlying data through other means.

</details>

---

### Question 2

The data engineering team needs to ingest Canvas LMS learning activity data (approximately 50 million events per semester) via REST API into the Fabric lakehouse. The API returns paginated JSON responses with a rate limit of 3,000 requests per hour. What is the most appropriate ingestion approach?

- A) Use a Dataflow Gen2 with a custom Power Query connector that handles pagination and writes directly to the Gold layer
- B) Use a Data Pipeline with a Copy Activity configured with REST as the source and the lakehouse Bronze layer as the sink, with pagination rules and a scheduled trigger every 4 hours
- C) Write a Spark notebook that uses the `requests` Python library to call the API and save results as Delta tables in the Silver layer
- D) Use an Event Stream to capture real-time events from the Canvas LMS webhook endpoint

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use a Data Pipeline with a Copy Activity configured with REST as the source and the lakehouse Bronze layer as the sink, with pagination rules and a scheduled trigger every 4 hours**

**Explanation:** Data Pipelines with Copy Activity are the recommended approach in Microsoft Fabric for ingesting data from REST APIs at scale. Copy Activity natively supports REST pagination patterns (RFC 5988 link headers, response body pagination), handles throttling with retry policies, and can write to the lakehouse Bronze layer as raw JSON or Parquet files. Landing data in the Bronze layer follows the medallion architecture pattern — raw data is ingested first, then cleansed in subsequent transformations. Option A (Dataflow Gen2) can connect to REST APIs but is optimized for smaller, transformation-heavy workloads, not high-volume ingestion. Option C would work but requires custom code for pagination, throttling, and error handling that Copy Activity provides out of the box. Option D (Event Stream) would require Canvas to push events via webhooks, which is a different integration pattern than polling a REST API.

</details>

---

### Question 3

The data governance specialist needs to apply FERPA compliance controls in the Fabric environment. Student Social Security numbers (SSNs), dates of birth, and financial need analysis data must be visible only to authorized Financial Aid Office staff, while the semantic model still needs these columns for DAX calculations. What combination of features should be used?

- A) Row-Level Security (RLS) to filter out rows containing sensitive data for unauthorized users
- B) Object-Level Security (OLS) on sensitive columns in the semantic model, combined with Microsoft Purview sensitivity labels on the underlying lakehouse tables
- C) Remove sensitive columns from the semantic model and create a separate Direct Lake model for Financial Aid staff only
- D) Use Power BI data masking to replace sensitive values with asterisks for unauthorized viewers

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Object-Level Security (OLS) on sensitive columns in the semantic model, combined with Microsoft Purview sensitivity labels on the underlying lakehouse tables**

**Explanation:** Object-Level Security (OLS) restricts visibility of specific columns (or tables) in a semantic model based on role membership. This allows DAX measures to reference SSN or financial need columns for aggregation calculations (e.g., average aid by need level) while hiding the raw column values from unauthorized users. Microsoft Purview sensitivity labels complement OLS by classifying the underlying data at the lakehouse/warehouse layer, enabling data loss prevention (DLP) policies, and providing audit trail for FERPA compliance. Option A (RLS) filters rows, not columns — it cannot hide specific fields. Option C works but creates maintenance overhead with duplicate models and doesn't allow a single model to serve both audiences. Option D (data masking) is not a native Power BI semantic model feature in the way described.

</details>

---

### Question 4

The OIR team needs to build a student retention prediction model that generates weekly risk scores. The model should be trained on 10 years of historical data (GPA, credit hours, LMS engagement, financial aid status) and produce a probability of dropping out for each currently enrolled student. Which Fabric capability should be used?

- A) Create a Dataflow Gen2 with calculated columns using Power Query M to compute risk scores based on business rules
- B) Build a machine learning model using a Fabric Spark notebook with MLflow tracking, train on Gold layer data, and write predictions back to a lakehouse table that the semantic model references
- C) Use a DAX calculated column in the semantic model that applies a weighted scoring formula to retention risk factors
- D) Export data to Azure Machine Learning workspace, train the model there, and import predictions via a REST API call in a Data Pipeline

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Build a machine learning model using a Fabric Spark notebook with MLflow tracking, train on Gold layer data, and write predictions back to a lakehouse table that the semantic model references**

**Explanation:** Microsoft Fabric's Data Science experience supports end-to-end ML workflows using Spark notebooks with integrated MLflow experiment tracking. The data engineer can train a classification model (e.g., logistic regression, gradient boosting) on the Gold layer historical data, register the model in MLflow, and schedule a notebook to generate weekly predictions written to a Delta table in the lakehouse. The semantic model then references this predictions table via Direct Lake mode. Option A (Dataflow Gen2/Power Query) is not designed for ML model training. Option C (DAX calculated column) can implement simple rule-based scoring but cannot perform actual ML training and prediction. Option D would work but introduces unnecessary complexity and external dependencies when Fabric natively supports the entire ML lifecycle.

</details>

---

### Question 5

PSUS needs to deploy analytics content across three workspaces (Dev, Test, Prod) on a semester-based schedule. The BI developer must ensure that data source connections are updated correctly during promotion, and that accreditation reports in Production use only certified semantic models. What is the correct approach?

- A) Use Git integration to manage version control, and manually update connection strings in each workspace after deployment
- B) Configure Fabric deployment pipelines with parameterization rules for data source connections, and use endorsement (Certified) on the Production semantic model after validation
- C) Create separate Power BI .pbix files for each environment and publish them independently to each workspace
- D) Use Azure DevOps CI/CD pipelines with Fabric REST APIs to programmatically deploy content and update connection strings

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Configure Fabric deployment pipelines with parameterization rules for data source connections, and use endorsement (Certified) on the Production semantic model after validation**

**Explanation:** Fabric deployment pipelines are purpose-built for promoting content across Dev → Test → Prod workspaces. Parameterization rules allow automatic substitution of data source connections (e.g., pointing to a production lakehouse or warehouse SQL endpoint) during promotion, eliminating manual connection string updates. The endorsement feature (with "Certified" status) allows the data governance team to mark the Production semantic model as validated and approved — the requirement states that only certified models may be used for accreditation reports. Option A (Git integration) handles version control but doesn't provide the promotion workflow or parameterization. Option C creates maintenance overhead and doesn't ensure consistency. Option D is technically possible but over-engineers the solution when Fabric has native deployment pipelines.

</details>

---

### Question 6

The enrollment funnel dashboard must show near-real-time data during peak registration periods (November and April), when query volume triples. The current F64 capacity sometimes becomes unresponsive during these peaks. What is the recommended approach to maintain performance?

- A) Upgrade permanently to an F128 SKU to handle peak load year-round
- B) Enable Fabric capacity autoscale (smoothing and bursting) and configure scale-up rules triggered by CPU utilization thresholds during registration periods
- C) Implement Import mode for all semantic models to reduce query load on the lakehouse during peak periods
- D) Schedule all data refreshes outside of registration hours and rely on cached data during peak periods

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Enable Fabric capacity autoscale (smoothing and bursting) and configure scale-up rules triggered by CPU utilization thresholds during registration periods**

**Explanation:** Microsoft Fabric supports capacity autoscale, which allows the capacity to burst above its baseline SKU during peak demand periods and smooth usage spikes over a 24-hour window. This is the most cost-effective approach for handling predictable, time-limited peak loads (two 2-week windows per year). The team can also use capacity metrics to monitor utilization and set alerts. Option A (permanent upgrade) wastes budget during the ~48 weeks of normal load. Option C (Import mode) would require scheduled refreshes and increase storage costs while losing the near-real-time benefit of Direct Lake. Option D (deferring refreshes) directly contradicts the requirement for near-real-time data during registration periods.

</details>

---

### Question 7

The institutional research team needs to run complex ad-hoc SQL queries against 10 years of enrollment and retention data for accreditation reporting. These queries involve multi-table joins, window functions, and aggregations that must complete within 30 minutes. The data is currently stored in the lakehouse Gold layer as Delta tables. What is the best approach?

- A) Query the lakehouse SQL analytics endpoint directly using T-SQL
- B) Create a Fabric Warehouse and load curated accreditation-specific tables from the Gold layer using stored procedures, then query the warehouse
- C) Build a composite semantic model on top of the Gold layer and use DAX queries in Power BI to generate the reports
- D) Export the Gold layer data to an external Azure SQL Database optimized for complex queries

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Create a Fabric Warehouse and load curated accreditation-specific tables from the Gold layer using stored procedures, then query the warehouse**

**Explanation:** The Fabric Warehouse is optimized for complex T-SQL workloads including multi-table joins, window functions, and large aggregations — exactly the patterns needed for institutional research ad-hoc queries. By creating accreditation-specific tables (e.g., pre-joined fact/dimension tables spanning 10 years), the team can optimize schema design, add indexes and statistics, and use stored procedures for repeatable accreditation queries. The 30-minute SLA is more reliably met with a warehouse's dedicated compute than with the lakehouse SQL endpoint, which is read-only and has limited optimization capabilities. Option A (lakehouse SQL endpoint) provides T-SQL access but is read-only with no ability to create indexes or optimize query plans. Option C (DAX queries) is not suitable for complex ad-hoc SQL workloads. Option D introduces unnecessary external dependencies.

</details>

---

### Question 8

Classroom utilization data from IoT sensors generates approximately 1 million readings per day. The facilities team needs a dashboard showing real-time occupancy compared to scheduled capacity, with the ability to analyze trends over the past 30 days. What is the recommended architecture for this data stream in Fabric?

- A) Ingest IoT data into the lakehouse Bronze layer via a Data Pipeline scheduled every 15 minutes, transform through Silver/Gold layers, and use Direct Lake mode
- B) Use Fabric Event Streams to ingest IoT data into a KQL Database (Eventhouse) for real-time analytics and short-term trend analysis, with a periodic copy to the lakehouse for long-term historical analysis
- C) Write IoT data directly to a Fabric Warehouse table using streaming inserts and query with T-SQL
- D) Store all IoT data in Azure Blob Storage and use a lakehouse shortcut to make it available in Fabric

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use Fabric Event Streams to ingest IoT data into a KQL Database (Eventhouse) for real-time analytics and short-term trend analysis, with a periodic copy to the lakehouse for long-term historical analysis**

**Explanation:** Fabric's Real-Time Intelligence capabilities (Event Streams + KQL Database/Eventhouse) are designed for streaming data scenarios like IoT sensor ingestion. Event Streams can ingest from Azure IoT Hub, and KQL (Kusto Query Language) is optimized for time-series data analysis — ideal for real-time occupancy dashboards and 30-day trend analysis. For long-term historical storage and integration with the broader analytics platform, a periodic copy (e.g., daily) to the lakehouse ensures the data is available in the Gold layer for cross-domain reporting. Option A (15-minute batch) introduces latency and doesn't provide true real-time analytics. Option C (Warehouse streaming inserts) is not a supported pattern in Fabric Warehouse. Option D (Blob Storage shortcuts) doesn't address the real-time dashboard requirement.

</details>

---

### Question 9

The data governance specialist needs to implement an audit trail showing who accessed FERPA-protected student data, when they accessed it, and what data they viewed. This audit trail must be retained for 7 years. Which combination of Fabric and Microsoft 365 features should be configured?

- A) Enable Power BI activity logging via the Admin Portal and export logs to a lakehouse table on a daily schedule using the Power BI REST API, with a 7-year retention policy on the lakehouse
- B) Configure Microsoft Purview audit (Standard) in the Microsoft 365 compliance center, which automatically captures Fabric/Power BI access events, and set the audit log retention policy to 7 years using Purview Audit (Premium)
- C) Create a custom event-tracking solution using Power Automate flows triggered by Power BI report views
- D) Rely on Azure Active Directory sign-in logs, which capture all Fabric access events with a default 30-day retention

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Configure Microsoft Purview audit (Standard) in the Microsoft 365 compliance center, which automatically captures Fabric/Power BI access events, and set the audit log retention policy to 7 years using Purview Audit (Premium)**

**Explanation:** Microsoft Purview Audit captures Power BI and Fabric activity events (report views, dataset queries, data exports, admin operations) as part of the unified Microsoft 365 audit log. Purview Audit (Premium), available with E5 licensing (which PSUS has), supports custom retention policies up to 10 years, satisfying the 7-year FERPA requirement. This provides a tamper-resistant, compliance-grade audit trail. Option A (REST API export) works but requires custom engineering effort and the self-managed lakehouse storage is not as compliance-hardened as Purview Audit. Option C (Power Automate) would miss many access patterns (e.g., direct dataset queries via XMLA endpoints). Option D (Azure AD sign-in logs) captures authentication events but not the granular data access events needed for FERPA auditing, and the default retention is only 30 days.

</details>

---

### Question 10

PSUS is preparing for its WASC accreditation mid-cycle review. The accreditation team needs a paginated report that shows 10-year trends in graduation rates, student-to-faculty ratios, and financial metrics, broken down by campus and college. The report must use exact formatting required by WASC, including specific page layouts, headers, and footers. The data must come from the certified Production semantic model. What is the recommended approach?

- A) Build a Power BI interactive report with multiple pages and use the "Export to PDF" feature to generate the accreditation document
- B) Create a Power BI paginated report (.rdl) connected to the certified Production semantic model, with precise layout control for WASC-required formatting, and publish it to the Production workspace
- C) Export the data from the warehouse to Excel and use Excel templates to format the accreditation report manually
- D) Use a Power BI dashboard with pinned tiles showing each required metric and take screenshots for the accreditation submission

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Create a Power BI paginated report (.rdl) connected to the certified Production semantic model, with precise layout control for WASC-required formatting, and publish it to the Production workspace**

**Explanation:** Power BI paginated reports (based on SQL Server Reporting Services / .rdl technology) are specifically designed for pixel-perfect, print-ready documents with precise control over page layout, headers, footers, page breaks, and formatting — exactly what accreditation reports require. Paginated reports can connect directly to a Power BI semantic model as a data source, ensuring they use the certified Production model as required. They handle large datasets efficiently (10 years of trend data) and can be scheduled for delivery via email subscriptions. Option A (interactive report + PDF export) does not provide the precise page layout control needed for regulatory documents. Option C (Excel) introduces manual processes, data currency risks, and breaks the single-source-of-truth principle. Option D (dashboard screenshots) is not a professional approach for accreditation submissions.

</details>
