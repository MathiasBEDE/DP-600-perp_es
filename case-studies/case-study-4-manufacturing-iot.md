# Case Study 4: Manufacturing IoT Analytics

## Scenario

PrecisionWorks Manufacturing is a global industrial manufacturer operating 8 factories across North America, Europe, and Asia, with a combined fleet of over 2,000 CNC machines, robotic assembly cells, and packaging lines. The company produces precision-engineered components for the automotive and aerospace industries, where product quality tolerances are measured in microns and unplanned downtime costs an estimated $15,000 per hour per production line.

Each factory currently monitors equipment health through a combination of manual inspections, basic SCADA dashboards, and spreadsheet-based maintenance logs. The maintenance team operates on a fixed-schedule preventive maintenance model, replacing parts at calendar-based intervals regardless of actual wear. This results in both unnecessary maintenance costs (estimated $12M annually in premature part replacements) and unexpected breakdowns that still occur 3–4 times per month per factory, causing an average of 6 hours of unplanned downtime per incident.

PrecisionWorks has recently deployed IoT sensors on all critical equipment, capturing telemetry data including temperature, vibration amplitude, spindle speed, hydraulic pressure, power consumption, and coolant flow rate. These sensors transmit data every 2 seconds via Azure IoT Hub, generating approximately 10 million events per day across all factories. The company wants to use Microsoft Fabric to build a predictive maintenance platform that can forecast equipment failures 48–72 hours in advance, optimize maintenance schedules, and provide plant managers with real-time visibility into production line performance.

The VP of Manufacturing also requires cross-factory benchmarking dashboards that compare Overall Equipment Effectiveness (OEE), first-pass yield rates, and energy consumption across all 8 plants. Quality engineers need access to inspection data including high-resolution images from automated optical inspection (AOI) cameras that capture defect images stored in Azure Blob Storage (approximately 500 GB of new images per month).

The IT team consists of 8 data engineers and 3 data analysts. Most are proficient in SQL and Python, with limited experience in streaming analytics. The company uses SAP S/4HANA as its ERP system (on-premises) and a custom Manufacturing Execution System (MES) running on SQL Server 2019. Both systems are behind a corporate firewall and accessible only via an on-premises data gateway.

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        MICROSOFT FABRIC PLATFORM                        │
│                                                                         │
│  ┌──────────────┐   ┌─────────────────────────────────────────────┐    │
│  │  Eventstream  │   │            OneLake (Lakehouse)               │    │
│  │  (IoT        │   │  ┌──────────┐ ┌──────────┐ ┌──────────┐   │    │
│  │   Telemetry) │   │  │  Bronze   │ │  Silver   │ │   Gold    │   │    │
│  └──────┬───────┘   │  │(Raw IoT, │→│(Cleansed, │→│(Aggregated│   │    │
│         │           │  │ MES, SAP) │ │ Joined)   │ │ KPIs)     │   │    │
│         ▼           │  └──────────┘ └──────────┘ └─────┬────┘   │    │
│  ┌──────────────┐   └──────────────────────────────────┼────────┘    │
│  │ KQL Database  │                                      │             │
│  │ (Real-Time    │   ┌──────────────────────────────────▼──────┐     │
│  │  Monitoring)  │   │         Spark Notebooks                  │     │
│  └──────┬───────┘   │  (Predictive Maintenance ML Models,      │     │
│         │           │   Batch Aggregations, Image Processing)   │     │
│         │           └──────────────────────────────┬───────────┘     │
│         │                                          │                  │
│         ▼                                          ▼                  │
│  ┌──────────────┐              ┌───────────────────────────────┐     │
│  │  Real-Time   │              │       Semantic Models          │     │
│  │  Dashboards  │              │  (OEE, Maintenance, Quality)  │     │
│  │  (Factory    │              └──────────────┬────────────────┘     │
│  │   Floor)     │                             │                      │
│  └──────────────┘              ┌──────────────▼────────────────┐     │
│                                │       Power BI Reports         │     │
│                                │  (Plant Manager / Executive)   │     │
│                                └───────────────────────────────┘     │
│                                                                         │
│  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐    │
│  │  Data Pipeline    │  │  Data Pipeline    │  │  Data Pipeline    │    │
│  │  (MES Ingestion)  │  │  (SAP Ingestion)  │  │  (Image Ingest)  │    │
│  └────────┬─────────┘  └────────┬─────────┘  └────────┬─────────┘    │
└───────────┼──────────────────────┼──────────────────────┼──────────────┘
            │                      │                      │
     ┌──────▼───────┐      ┌──────▼───────┐      ┌──────▼───────┐
     │  SQL Server   │      │   SAP S/4HANA │      │  Azure Blob  │
     │  (MES - On    │      │   (ERP - On   │      │  Storage     │
     │   Premises)   │      │    Premises)  │      │  (AOI Images)│
     └──────────────┘      └──────────────┘      └──────────────┘
            │                      │
     ┌──────▼──────────────────────▼───────┐
     │     On-Premises Data Gateway         │
     └─────────────────────────────────────┘
                      ▲
                      │
     ┌────────────────┴────────────────────┐
     │        Azure IoT Hub                 │
     │  (10M events/day from 2000 machines) │
     └────────────────┬────────────────────┘
                      ▲
          ┌───────────┴───────────┐
          │    IoT Sensors         │
          │ (Temp, Vibration,      │
          │  Pressure, Speed)      │
          └───────────────────────┘
```

## Requirements

- **Real-time equipment monitoring** with alerting when sensor readings exceed predefined thresholds (e.g., vibration > 7.1 mm/s, temperature > 85°C)
- **Predictive maintenance ML model** trained on historical failure data to forecast breakdowns 48–72 hours in advance
- **OEE (Overall Equipment Effectiveness)** calculations combining availability, performance, and quality metrics for each machine, line, and factory
- **Cross-factory benchmarking** dashboards comparing KPIs across all 8 plants with drill-down to individual machines
- **Quality control analytics** correlating sensor readings at time of production with downstream inspection results and defect images
- **Batch ingestion** of MES and SAP data via on-premises data gateway at 15-minute intervals
- **Image analytics** pipeline for processing AOI inspection images and linking defect classifications to production parameters
- **Maintenance scheduling optimization** recommending optimal maintenance windows based on predicted failure probabilities and production schedules
- **Role-based access control** ensuring plant managers see only their factory's data while VP-level users see cross-factory views
- **Alerting and notifications** via Microsoft Teams when critical equipment conditions are detected

## Data Sources

| Source | System | Volume | Frequency | Ingestion Method |
|---|---|---|---|---|
| IoT Telemetry | Azure IoT Hub (2000 machines) | ~10M events/day (~120 events/sec) | Real-time (every 2 sec/machine) | Eventstream |
| Production Orders | MES (SQL Server 2019, on-prem) | ~5,000 orders/day | Every 15 minutes | Data Pipeline + Gateway |
| Material/BOM Data | SAP S/4HANA (on-prem) | ~100K records | Daily incremental | Dataflow Gen2 + Gateway |
| Maintenance Logs | MES (SQL Server 2019, on-prem) | ~200 records/day | Every 15 minutes | Data Pipeline + Gateway |
| Inspection Results | MES Quality Module | ~50K inspections/day | Every 15 minutes | Data Pipeline + Gateway |
| Defect Images | Azure Blob Storage (AOI cameras) | ~500 GB/month (~16 GB/day) | Near real-time | Data Pipeline (event trigger) |
| Equipment Master Data | SAP Plant Maintenance | ~2,000 assets | Weekly | Dataflow Gen2 + Gateway |

## Constraints and Considerations

- **Latency Requirements:** Real-time dashboards for factory floor must display sensor data within 30 seconds of capture; anomaly alerts must trigger within 60 seconds; batch dashboards acceptable with 1-hour data freshness
- **Data Volume:** IoT telemetry will grow to approximately 3.6 TB/year of raw data; 3-year retention for raw data, 7-year retention for aggregated metrics required
- **Network:** On-premises systems (MES, SAP) accessible only through on-premises data gateway; IoT Hub is cloud-native; gateway must support concurrent connections from multiple Fabric pipelines
- **Skills:** Team has strong SQL and Python skills but limited Kusto/KQL experience; training plan needed for real-time analytics adoption
- **Cost Optimization:** Raw IoT telemetry should be stored in Lakehouse (low cost); only anomalous readings and hourly aggregates should be stored in KQL Database (higher cost) to optimize capacity consumption
- **Scalability:** Solution must scale to 15 factories and 5,000 machines within 2 years as PrecisionWorks expands
- **Integration:** Maintenance work orders generated by the predictive model should integrate back into the SAP Plant Maintenance module via the gateway
- **Availability:** 99.9% uptime required during factory operating hours (24/5 for NA/EU, 24/7 for Asia plants)

---

## Questions

### Question 1

PrecisionWorks needs to ingest IoT telemetry from Azure IoT Hub into Fabric for both real-time monitoring and historical analysis. The team wants to optimize costs by storing only anomalous readings in the KQL Database while retaining all raw data in the Lakehouse. How should the Eventstream be configured?

- A) Create one Eventstream that sends all data to the KQL Database, then use a scheduled Spark notebook to move non-anomalous data to the Lakehouse and delete it from KQL
- B) Create one Eventstream with the IoT Hub source, add an inline transformation to filter for anomalous readings routing to the KQL Database, and add a second destination routing all unfiltered data to the Lakehouse
- C) Create two separate Eventstreams — one connected to IoT Hub for the KQL Database and another connected to IoT Hub for the Lakehouse — and configure different consumer groups
- D) Send all IoT data to the Lakehouse via Eventstream, then use a Dataflow Gen2 scheduled every minute to copy anomalous readings to the KQL Database

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Create one Eventstream with the IoT Hub source, add an inline transformation to filter for anomalous readings routing to the KQL Database, and add a second destination routing all unfiltered data to the Lakehouse**

**Explanation:** Fabric Eventstream supports multiple destinations from a single source and allows inline transformations (such as filter operations) to route data selectively. By adding a filter transformation that identifies anomalous readings (e.g., vibration > 7.1 mm/s or temperature > 85°C) and routing those to the KQL Database while sending all raw data to the Lakehouse, you get cost-optimized real-time analytics with full historical retention. Option A wastes KQL storage costs and adds operational complexity. Option C requires managing two Eventstreams and consumer groups unnecessarily. Option D introduces latency and is not suitable for real-time alerting on anomalous data.

</details>

---

### Question 2

The data engineering team needs to ingest data from the on-premises MES (SQL Server 2019) and SAP S/4HANA systems into Fabric. Both systems are behind a corporate firewall. What is the correct approach?

- A) Configure Azure ExpressRoute to establish a private connection between the corporate network and Fabric, then use direct connections from Data Pipelines to the on-premises databases
- B) Install an on-premises data gateway, register it in Fabric, create gateway connections for both SQL Server and SAP, and use Data Pipelines and Dataflow Gen2 with these gateway connections
- C) Deploy Azure Data Factory self-hosted integration runtime on-premises and configure Fabric to use it as a linked service for data ingestion
- D) Replicate the MES and SAP databases to Azure SQL Database using Azure Database Migration Service, then connect Fabric directly to the cloud databases

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Install an on-premises data gateway, register it in Fabric, create gateway connections for both SQL Server and SAP, and use Data Pipelines and Dataflow Gen2 with these gateway connections**

**Explanation:** The on-premises data gateway is the standard mechanism in Microsoft Fabric for securely connecting to data sources behind a corporate firewall. Once installed and registered, it enables both Data Pipelines and Dataflow Gen2 to access on-premises SQL Server and SAP systems without exposing them to the public internet. Option A describes ExpressRoute which provides network connectivity but still requires a gateway for Fabric to communicate with on-premises data sources. Option C describes an ADF integration runtime, which is a different product; Fabric uses its own gateway infrastructure. Option D adds unnecessary complexity, cost, and potential data residency issues by replicating entire databases to the cloud.

</details>

---

### Question 3

The predictive maintenance team wants to build an ML model that predicts equipment failures 48–72 hours in advance using historical sensor telemetry and maintenance logs. The team is proficient in Python and scikit-learn. What is the recommended approach in Fabric?

- A) Export historical data to CSV files, build the model locally in Jupyter notebooks on data science workstations, and upload the trained model to a Fabric Lakehouse for manual scoring
- B) Use Fabric Spark notebooks with the MLflow integration to train a scikit-learn model on historical Lakehouse data, track experiments, register the best model, and schedule a scoring notebook to generate predictions daily
- C) Use Power BI's built-in AI insights (Key Influencers visual) to identify the factors that predict equipment failure and publish the results as a dashboard
- D) Build the ML model in Azure Machine Learning, deploy it as a web service, and call the endpoint from a Fabric Data Pipeline for each equipment record

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use Fabric Spark notebooks with the MLflow integration to train a scikit-learn model on historical Lakehouse data, track experiments, register the best model, and schedule a scoring notebook to generate predictions daily**

**Explanation:** Fabric's data science experience natively supports Python, scikit-learn, and MLflow for experiment tracking and model registry. The team can use their existing Python/scikit-learn skills directly in Fabric notebooks, train on the historical telemetry and maintenance data stored in the Lakehouse, compare model versions through MLflow experiment tracking, and register the best-performing model. A scheduled notebook can then apply the model daily to generate 48–72 hour failure predictions. Option A lacks MLflow tracking, collaboration features, and automation. Option C provides visual analytics but is not a proper machine learning solution for predictive maintenance. Option D introduces unnecessary external dependencies and costs when Fabric provides built-in ML capabilities.

</details>

---

### Question 4

Plant managers at each factory need Power BI dashboards showing their factory's OEE metrics, but the VP of Manufacturing needs a cross-factory benchmarking view. All users access reports from a single Power BI app. How should security be implemented?

- A) Create separate semantic models for each factory and a combined model for the VP, publishing 9 separate reports
- B) Implement row-level security (RLS) in the semantic model with dynamic rules using `USERPRINCIPALNAME()`, mapping each plant manager to their factory, and assigning the VP role with no factory filter
- C) Create a single workspace per factory with workspace viewer permissions for each plant manager, and give the VP access to all workspaces
- D) Use object-level security (OLS) to hide factory-specific tables from plant managers who should not see other factories' data

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Implement row-level security (RLS) in the semantic model with dynamic rules using `USERPRINCIPALNAME()`, mapping each plant manager to their factory, and assigning the VP role with no factory filter**

**Explanation:** Dynamic RLS using `USERPRINCIPALNAME()` allows a single semantic model and report to serve all users while automatically filtering data based on the logged-in user. A security mapping table links each user's email to their assigned factory. Plant managers see only their factory's data, while the VP role has no factory filter applied, enabling full cross-factory visibility. This approach is scalable and maintains a single version of truth. Option A creates maintenance overhead with 9 separate models. Option C uses workspace-level security which doesn't provide row-level data filtering within a shared report. Option D (OLS) hides tables/columns, not rows, so it cannot filter by factory.

</details>

---

### Question 5

The team needs to calculate OEE metrics by combining IoT telemetry (machine run/stop states) from the Lakehouse with production order data from the MES system. OEE is calculated as Availability × Performance × Quality. The data arrives at different intervals — IoT telemetry is near real-time and MES production data refreshes every 15 minutes. What is the best approach?

- A) Build the OEE calculation entirely in the semantic model using DAX measures that combine both data sources through relationships in the model
- B) Create a Spark notebook scheduled every 15 minutes that reads both IoT telemetry and MES data from the Lakehouse, computes OEE metrics, and writes results to a Gold layer table that the semantic model reads
- C) Use Dataflow Gen2 to merge the IoT and MES data streams in real-time and output the calculated OEE metrics directly to Power BI
- D) Calculate OEE in the KQL Database using KQL queries that join real-time telemetry with MES data

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Create a Spark notebook scheduled every 15 minutes that reads both IoT telemetry and MES data from the Lakehouse, computes OEE metrics, and writes results to a Gold layer table that the semantic model reads**

**Explanation:** Since MES data refreshes every 15 minutes, the effective refresh rate for OEE calculations is limited to that interval regardless of how frequently IoT data arrives. A Spark notebook scheduled at 15-minute intervals aligns with this data availability, performs the complex OEE computation (joining machine states with production orders and quality data), and materializes the results in a Gold table for efficient reporting. Option A would push complex calculations to DAX, which is less suitable for large-scale joins across telemetry and production data. Option C overstates Dataflow Gen2's capabilities for this type of calculation. Option D could work for real-time telemetry queries but joining with MES data (which is in the Lakehouse, not the KQL Database) adds complexity.

</details>

---

### Question 6

An anomaly alert must trigger within 60 seconds when a machine's vibration exceeds 7.1 mm/s and send a notification to the maintenance team's Microsoft Teams channel. How should this be implemented?

- A) Use a Spark Structured Streaming notebook that continuously monitors the Lakehouse for new IoT records and calls the Microsoft Teams webhook when thresholds are exceeded
- B) Configure a Data Activator (Reflex) alert on the Eventstream that monitors the vibration field, triggers when the value exceeds 7.1 mm/s, and sends a notification action to the Teams channel
- C) Create a Power Automate flow that queries the KQL Database every 30 seconds for threshold violations and posts a message to Teams
- D) Build a scheduled Data Pipeline that runs every minute, queries the Lakehouse for recent anomalous readings, and uses a Web Activity to call the Teams webhook

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Configure a Data Activator (Reflex) alert on the Eventstream that monitors the vibration field, triggers when the value exceeds 7.1 mm/s, and sends a notification action to the Teams channel**

**Explanation:** Data Activator (Reflex) in Fabric is specifically designed for real-time alerting on streaming data. It can connect directly to an Eventstream, evaluate conditions on incoming events (such as vibration > 7.1 mm/s), and trigger actions including Teams notifications — all within the 60-second latency requirement. Option A adds unnecessary complexity with a continuously running Spark job and depends on data reaching the Lakehouse first. Option C introduces polling overhead and depends on data being in KQL first. Option D has a minimum 1-minute delay from the pipeline schedule plus execution time, making it unreliable for the 60-second SLA.

</details>

---

### Question 7

The quality engineering team needs to correlate defect images from AOI cameras (stored in Azure Blob Storage) with sensor telemetry at the time of production. They want to identify which machine parameters (temperature, vibration, pressure) are associated with higher defect rates. What approach should be used?

- A) Store the image file paths as references in the Lakehouse, join with telemetry data using timestamps and machine IDs in a Spark notebook, and use the pandas DataFrame to generate correlation analysis and visualizations
- B) Import all defect images directly into the Lakehouse as binary columns alongside the telemetry data for unified analysis
- C) Build a Power BI report that displays images from Blob Storage alongside charts of telemetry readings, allowing quality engineers to visually identify patterns
- D) Use Azure Cognitive Services Computer Vision to analyze images and store results in a separate Azure SQL Database, then import the results into Fabric via a Data Pipeline

<details>
<summary>Show Answer</summary>

**Correct Answer: A) Store the image file paths as references in the Lakehouse, join with telemetry data using timestamps and machine IDs in a Spark notebook, and use the pandas DataFrame to generate correlation analysis and visualizations**

**Explanation:** This approach efficiently links defect classifications from AOI inspection records with corresponding machine telemetry by joining on timestamp and machine ID. Storing image paths (not the images themselves) as references in the Lakehouse keeps storage efficient while enabling drill-down to specific images when needed. The Spark notebook can compute statistical correlations between machine parameters and defect rates using Python libraries like pandas, scipy, and matplotlib. Option B is impractical because storing large binary images as Lakehouse columns is inefficient and not how image data should be managed. Option C provides only visual inspection without quantitative correlation analysis. Option D introduces unnecessary external dependencies when the correlation analysis can be performed within Fabric.

</details>

---

### Question 8

PrecisionWorks plans to scale from 8 factories (2,000 machines) to 15 factories (5,000 machines) within 2 years, increasing IoT events from 10M to 25M per day. How should the Fabric solution be designed to handle this growth?

- A) Deploy a dedicated F64 Fabric capacity per factory to ensure isolation, with a separate F128 capacity for the centralized cross-factory analytics
- B) Design Lakehouse tables partitioned by factory and date, use Fabric capacity auto-scaling, implement data lifecycle management to archive old raw data to cold storage, and use shortcuts for cross-factory queries
- C) Migrate to Azure Synapse Analytics, which offers better scalability for IoT workloads than Microsoft Fabric
- D) Increase the current Fabric capacity to F128 now and add more capacity units as new factories come online

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Design Lakehouse tables partitioned by factory and date, use Fabric capacity auto-scaling, implement data lifecycle management to archive old raw data to cold storage, and use shortcuts for cross-factory queries**

**Explanation:** Proper data partitioning by factory and date ensures query performance scales with data volume by enabling partition pruning. Fabric capacity auto-scaling adjusts resources based on actual demand, avoiding over-provisioning during off-peak hours. Data lifecycle management addresses the retention requirements (3 years raw, 7 years aggregated) by archiving older data to lower-cost storage tiers. Shortcuts enable cross-factory queries without centralizing all data. Option A is extremely expensive and creates operational complexity with many separate capacities. Option C is not appropriate as Fabric is the strategic platform. Option D doesn't address the data architecture concerns (partitioning, lifecycle management) needed for sustainable scale.

</details>

---

### Question 9

The team has limited KQL experience but needs to build real-time monitoring dashboards showing current machine status across the factory floor. The dashboards must show the latest sensor reading for each machine and highlight machines in alarm state. What is the most practical approach given the team's skill set?

- A) Skip KQL entirely and use Spark Structured Streaming with a Delta Live Table that maintains the latest state per machine, building Power BI DirectQuery reports against the Lakehouse
- B) Use the KQL Database with Copilot assistance to write queries, leverage the built-in KQL queryset templates for IoT scenarios, and build Real-Time Dashboards using the visual query builder where possible
- C) Hire a dedicated KQL expert before starting the real-time analytics workstream, as the existing team cannot deliver the solution without specialized expertise
- D) Use T-SQL views in a Fabric Warehouse against mirrored KQL data, since the team is already proficient in SQL

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use the KQL Database with Copilot assistance to write queries, leverage the built-in KQL queryset templates for IoT scenarios, and build Real-Time Dashboards using the visual query builder where possible**

**Explanation:** KQL is optimized for time-series and IoT telemetry queries, making it the right choice despite the team's limited experience. Fabric provides several tools to lower the learning curve: Copilot can help generate KQL queries from natural language descriptions, KQL querysets include templates for common IoT patterns, and Real-Time Dashboards offer visual builders for basic queries. The team can incrementally build KQL skills while delivering results. Option A adds significant complexity and doesn't provide the same real-time query performance as KQL for streaming data. Option C unnecessarily delays the project. Option D doesn't work because T-SQL against mirrored KQL data doesn't provide the same real-time performance characteristics.

</details>

---

### Question 10

The predictive maintenance model generates daily predictions indicating which machines have a high probability of failure within 48–72 hours. The maintenance team needs these predictions to automatically create work orders in the SAP Plant Maintenance module. How should this integration be implemented?

- A) Email a daily CSV report of predicted failures to the maintenance team, who manually create work orders in SAP
- B) After the scoring notebook writes predictions to the Lakehouse, trigger a Data Pipeline that reads high-probability failure predictions, transforms them into SAP work order format, and uses a Web Activity to call the SAP API through the on-premises data gateway
- C) Use Power Automate with a Fabric connector to read predictions from the Lakehouse and create work orders in SAP via the SAP connector
- D) Build a custom Python application hosted on an Azure VM that polls the Lakehouse for new predictions and writes directly to the SAP database

<details>
<summary>Show Answer</summary>

**Correct Answer: B) After the scoring notebook writes predictions to the Lakehouse, trigger a Data Pipeline that reads high-probability failure predictions, transforms them into SAP work order format, and uses a Web Activity to call the SAP API through the on-premises data gateway**

**Explanation:** This approach keeps the integration within the Fabric ecosystem using Data Pipelines for orchestration. After the ML scoring notebook writes predictions to the Lakehouse, a downstream pipeline activity reads predictions exceeding the probability threshold, formats them according to the SAP BAPI/RFC interface specification, and calls the SAP API to create Plant Maintenance work orders. The on-premises data gateway enables secure communication with the SAP system behind the corporate firewall. Option A is manual and defeats the purpose of automation. Option C could work but Power Automate may have limitations with complex SAP BAPI calls and lacks the data transformation capabilities of a Data Pipeline. Option D introduces unnecessary infrastructure and bypasses Fabric's orchestration capabilities.

</details>
