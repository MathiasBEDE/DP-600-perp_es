# Domain 1: Practice Questions — Plan, Implement, and Manage a Solution for Data Analytics

> **50 Multiple-Choice Questions** covering Domain 1 of the DP-600 exam (10–15% weight).
> Each question includes a detailed answer explanation.

---

### Question 1

Your organization is adopting Microsoft Fabric. The data engineering team needs a single, unified storage layer that all Fabric workloads can access without copying data between services. Which component of Microsoft Fabric provides this capability?

- A) Azure Data Lake Storage Gen2
- B) OneLake
- C) Power BI Premium capacity
- D) Azure Synapse workspace

<details>
<summary>Show Answer</summary>

**Correct Answer: B) OneLake**

**Explanation:** OneLake is the unified data lake built into Microsoft Fabric that serves as the single storage layer for all Fabric workloads. It eliminates data silos by providing one copy of data accessible across Data Engineering, Data Warehouse, Data Science, and Power BI experiences. OneLake is built on top of ADLS Gen2 technology but is managed as part of Fabric.

**Why other options are incorrect:**
- A) ADLS Gen2 is a standalone Azure service; while OneLake uses similar technology, ADLS Gen2 itself is not the Fabric-integrated unified layer.
- C) Power BI Premium capacity is a licensing and compute tier, not a storage layer.
- D) Azure Synapse workspace is a separate analytics service that predates Fabric's unified approach.

</details>

---

### Question 2

A compliance officer asks you to ensure that confidential financial reports in a Fabric workspace automatically carry protective labels when shared externally. Which feature should you implement?

- A) Row-level security
- B) Microsoft Purview sensitivity labels
- C) Workspace roles
- D) Microsoft Entra ID Conditional Access

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Microsoft Purview sensitivity labels**

**Explanation:** Microsoft Purview sensitivity labels classify and protect data based on its sensitivity. When applied to Fabric items, these labels persist as the content is shared, exported, or moved, ensuring that confidential information retains its protective classification. Sensitivity labels integrate with Microsoft Information Protection policies for downstream enforcement.

**Why other options are incorrect:**
- A) Row-level security controls which rows a user can see inside a dataset, not the classification or protection of shared content.
- C) Workspace roles control access within a workspace but do not apply persistent protective labels to content.
- D) Conditional Access policies control authentication and access conditions but do not classify or label content.

</details>

---

### Question 3

You manage a Fabric tenant with multiple departments. Each department must be able to manage its own workspace items independently, but a central IT team must retain the ability to govern capacity usage across the organization. What is the recommended approach?

- A) Create one workspace and assign department-level folders
- B) Create separate workspaces per department and assign them to centrally managed capacities
- C) Give every department its own Fabric tenant
- D) Use a single Power BI Pro license for the entire organization

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Create separate workspaces per department and assign them to centrally managed capacities**

**Explanation:** Separate workspaces provide departmental autonomy for managing items, while assigning those workspaces to centrally managed capacities allows IT to control and monitor resource usage across the organization. This model balances self-service analytics with centralized governance.

**Why other options are incorrect:**
- A) A single workspace does not provide departmental independence and creates permission management challenges at scale.
- C) Separate tenants introduce unnecessary complexity, prevent cross-department collaboration, and increase administrative overhead.
- D) A Power BI Pro license does not provide Fabric capacity features and is a per-user license, not an organizational governance model.

</details>

---

### Question 4

A data engineer wants to connect an external Azure Data Lake Storage Gen2 account to OneLake so that data appears as if it is stored within Fabric without physically copying it. Which OneLake feature enables this?

- A) Data pipeline
- B) Shortcut
- C) Dataflow Gen2
- D) Mirrored database

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Shortcut**

**Explanation:** OneLake shortcuts allow you to create references to data stored in external locations such as ADLS Gen2, Amazon S3, or other OneLake locations. The data remains in its original location, but the shortcut makes it appear as a folder within OneLake, enabling all Fabric workloads to access it without data duplication.

**Why other options are incorrect:**
- A) A data pipeline copies or transforms data between sources and destinations; it does not create virtual references.
- C) Dataflow Gen2 is an ETL tool for data transformation, not a virtual reference mechanism.
- D) Mirrored databases replicate operational database data into OneLake, which involves data movement rather than virtual referencing of ADLS Gen2.

</details>

---

### Question 5

Your company requires that all changes to semantic models and reports be tracked in source control before being promoted to production. Which Fabric feature supports this requirement?

- A) Deployment pipelines
- B) Git integration
- C) Workspace monitoring
- D) Data activator

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Git integration**

**Explanation:** Fabric's Git integration connects a workspace to a Git repository (such as Azure DevOps or GitHub), enabling version control of workspace items. Changes are committed to a branch, reviewed, and then synced to other environments, providing full source-control tracking before production deployment.

**Why other options are incorrect:**
- A) Deployment pipelines promote content between stages but do not provide source-control tracking or branching capabilities on their own.
- C) Workspace monitoring tracks usage and performance metrics, not source-code changes.
- D) Data Activator triggers actions based on data conditions and is unrelated to source control.

</details>

---

### Question 6

You need to promote a set of reports and datasets from a Development workspace through Test and into a Production workspace. Which Fabric feature provides a managed multi-stage promotion workflow?

- A) Git integration
- B) Deployment pipelines
- C) Power Automate flows
- D) Azure DevOps release pipelines

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Deployment pipelines**

**Explanation:** Fabric deployment pipelines provide a built-in mechanism to promote content through Development, Test, and Production stages. They support comparison between stages, deployment rules for parameter changes, and selective deployment of items, making them the native tool for managed promotion workflows within Fabric.

**Why other options are incorrect:**
- A) Git integration provides source control but does not natively manage multi-stage promotion within Fabric workspaces.
- C) Power Automate flows can automate tasks but are not the purpose-built tool for Fabric content promotion.
- D) Azure DevOps release pipelines are external CI/CD tools that require additional configuration and are not natively integrated with Fabric's promotion model.

</details>

---

### Question 7

A workspace admin assigns a user the Viewer role in a Fabric workspace. Which action can that user perform?

- A) Create new reports in the workspace
- B) Edit existing semantic models
- C) View and interact with published reports and dashboards
- D) Configure workspace settings

<details>
<summary>Show Answer</summary>

**Correct Answer: C) View and interact with published reports and dashboards**

**Explanation:** The Viewer role is the most restrictive workspace role in Fabric. Users with this role can view and interact with reports and dashboards that have been published to the workspace, but they cannot create, edit, or delete any workspace items or change settings.

**Why other options are incorrect:**
- A) Creating reports requires at least the Contributor role.
- B) Editing semantic models requires at least the Contributor role.
- D) Configuring workspace settings requires the Admin role.

</details>

---

### Question 8

Your organization's Fabric administrator needs to monitor who accessed sensitive data in a lakehouse over the past 30 days. Which tool should they use?

- A) Capacity Metrics app
- B) Microsoft Purview audit logs
- C) Power BI Desktop Performance Analyzer
- D) SQL Profiler

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Microsoft Purview audit logs**

**Explanation:** Microsoft Purview audit logs capture user and admin activities across Microsoft Fabric, including data access events. Administrators can search these logs to identify who accessed specific items, when the access occurred, and what operations were performed, making it the appropriate tool for access auditing over time.

**Why other options are incorrect:**
- A) The Capacity Metrics app monitors capacity utilization and performance, not individual user data access events.
- C) Power BI Desktop Performance Analyzer measures report rendering and query performance locally, not tenant-level data access.
- D) SQL Profiler is a SQL Server tool for tracing database engine events and is not designed for Fabric-level access auditing.

</details>

---

### Question 9

You are configuring a Fabric workspace and need a user to be able to share items and manage permissions for individual items, but not manage workspace settings. Which workspace role should you assign?

- A) Admin
- B) Member
- C) Contributor
- D) Viewer

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Member**

**Explanation:** The Member role in a Fabric workspace allows users to create, edit, and delete workspace items, and also share items and manage permissions on individual items. Members cannot change workspace-level settings, add or remove other members, or modify the workspace's capacity assignment — those actions require the Admin role.

**Why other options are incorrect:**
- A) Admin can manage workspace settings, which exceeds the stated requirements.
- C) Contributor can create and edit items but cannot share items or manage item-level permissions.
- D) Viewer can only view content and cannot share or manage permissions.

</details>

---

### Question 10

An organization wants to track how data flows from source systems through transformation layers to final reports. Which Microsoft capability provides end-to-end visibility into this flow?

- A) Data lineage in Microsoft Fabric
- B) Power BI Q&A
- C) Azure Monitor
- D) Activity log export

<details>
<summary>Show Answer</summary>

**Correct Answer: A) Data lineage in Microsoft Fabric**

**Explanation:** Data lineage in Fabric provides a visual representation of how data moves and transforms from source to destination across workspace items. It shows upstream sources and downstream dependencies, enabling impact analysis and helping organizations understand the complete data journey from ingestion to reporting.

**Why other options are incorrect:**
- B) Power BI Q&A is a natural language query feature for end users, not a lineage visualization tool.
- C) Azure Monitor tracks infrastructure metrics and logs but does not provide Fabric-specific data lineage views.
- D) Activity log export provides event-level audit data, not a visual representation of data flow and dependencies.

</details>

---

### Question 11

A Fabric administrator notices that a specific capacity is consistently throttled during business hours. What should they do first to investigate?

- A) Upgrade every user to a Power BI Premium Per User license
- B) Review the Microsoft Fabric Capacity Metrics app
- C) Delete unused workspaces from the tenant
- D) Disable background data refresh for all semantic models

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Review the Microsoft Fabric Capacity Metrics app**

**Explanation:** The Capacity Metrics app provides detailed insights into capacity utilization, including compute consumption, throttling events, and which workloads are consuming resources. This is the first step to diagnosing throttling because it identifies the root cause before taking any remediation action.

**Why other options are incorrect:**
- A) Upgrading licenses does not resolve capacity throttling and addresses a different concern.
- C) Deleting workspaces without understanding the cause may remove needed resources and does not address the root issue.
- D) Disabling all background refreshes is a drastic action that should only be considered after investigation reveals refresh operations as the cause.

</details>

---

### Question 12

Your company has a Microsoft Fabric F64 capacity. During peak hours, data warehouse queries are slow while data engineering notebook jobs are consuming most of the capacity. What is the most appropriate action?

- A) Move data engineering workloads to a separate capacity
- B) Delete the notebooks that consume the most resources
- C) Switch all users to Power BI Pro licenses
- D) Convert the lakehouse to a SQL Server database

<details>
<summary>Show Answer</summary>

**Correct Answer: A) Move data engineering workloads to a separate capacity**

**Explanation:** Isolating workloads onto separate capacities ensures that resource-intensive data engineering jobs do not starve data warehouse queries. This approach allows each workload to have dedicated compute resources and enables independent scaling, which is the recommended practice for managing competing workloads in Fabric.

**Why other options are incorrect:**
- B) Deleting notebooks is destructive and does not address the underlying resource contention issue.
- C) Power BI Pro licenses do not provide Fabric capacity features and would actually reduce capabilities.
- D) Converting a lakehouse to SQL Server is not a Fabric operation and does not address Fabric capacity contention.

</details>

---

### Question 13

A Fabric tenant administrator wants to prevent users from exporting data from reports to CSV files. Where should this restriction be configured?

- A) Workspace role settings
- B) Tenant settings in the Fabric admin portal
- C) Individual report parameters
- D) OneLake file permissions

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Tenant settings in the Fabric admin portal**

**Explanation:** The Fabric admin portal's tenant settings include export controls that allow administrators to enable or disable data export options such as CSV, Excel, and other formats. These settings apply at the tenant or security group level, providing centralized governance over data export capabilities.

**Why other options are incorrect:**
- A) Workspace roles control access to workspace items, not export format restrictions.
- C) Individual report parameters control report behavior and filtering, not export permissions.
- D) OneLake file permissions govern direct file access in the data lake, not report-level export options.

</details>

---

### Question 14

You are designing a Fabric solution for a multinational company. Data for European customers must remain in the EU region. How should you ensure data residency compliance?

- A) Create workspaces with names prefixed by "EU"
- B) Provision a Fabric capacity in an EU Azure region and assign EU workspaces to it
- C) Apply sensitivity labels marked "EU Only"
- D) Use row-level security to filter EU data

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Provision a Fabric capacity in an EU Azure region and assign EU workspaces to it**

**Explanation:** Fabric capacities are provisioned in specific Azure regions, and the data stored in workspaces assigned to that capacity resides in that region. By creating a capacity in an EU region and assigning all EU-related workspaces to it, you ensure that European customer data remains within EU boundaries to satisfy data residency requirements.

**Why other options are incorrect:**
- A) Naming conventions have no effect on where data is physically stored.
- C) Sensitivity labels classify and protect data but do not control the physical storage region.
- D) Row-level security controls data visibility per user but does not affect where data is physically stored.

</details>

---

### Question 15

A Fabric workspace currently uses Git integration with Azure DevOps. A developer commits changes to the connected branch but the workspace does not reflect the updates. What is the most likely cause?

- A) The workspace has not been synced from the Git repository
- B) The developer's Power BI Pro license has expired
- C) OneLake is experiencing a regional outage
- D) The semantic model is too large for Git

<details>
<summary>Show Answer</summary>

**Correct Answer: A) The workspace has not been synced from the Git repository**

**Explanation:** In Fabric's Git integration, committing changes to a branch does not automatically update the workspace. The workspace must be explicitly synced to pull the latest changes from the Git repository. Until the sync operation is performed, the workspace retains its previous state.

**Why other options are incorrect:**
- B) A Power BI Pro license expiration would prevent workspace access entirely, not cause a selective sync issue.
- C) A OneLake outage would affect broader data operations, not specifically Git-to-workspace synchronization.
- D) Git integration in Fabric handles workspace item definitions; the semantic model data itself is not stored in Git.

</details>

---

### Question 16

You need to assign a security group as workspace admins for 20 Fabric workspaces. What is the most efficient approach?

- A) Add individual users as admins to each workspace manually
- B) Assign the security group the Admin role in each workspace
- C) Grant the security group the Fabric Administrator role in Microsoft Entra ID
- D) Create a Microsoft 365 group for each workspace

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Assign the security group the Admin role in each workspace**

**Explanation:** Assigning a security group to the Admin role in each workspace allows centralized management of workspace administrators. When group membership changes, the workspace permissions update automatically without requiring individual user modifications across all 20 workspaces.

**Why other options are incorrect:**
- A) Adding individual users to each workspace is time-consuming and creates a maintenance burden when personnel changes occur.
- C) The Fabric Administrator role is a tenant-level role that provides global admin capabilities, which exceeds the workspace-level admin requirements.
- D) Creating a Microsoft 365 group per workspace is unnecessary and does not simplify administering the same set of admins across multiple workspaces.

</details>

---

### Question 17

A data analyst in the Contributor role creates a report in a Fabric workspace. They want a colleague outside the workspace to view the report. What must happen?

- A) The analyst can share the report directly because Contributors can share items
- B) A workspace Member or Admin must share the report on behalf of the Contributor
- C) The colleague must be added to the workspace as a Viewer
- D) Either B or C

<details>
<summary>Show Answer</summary>

**Correct Answer: D) Either B or C**

**Explanation:** Contributors cannot share items or manage item permissions. To give the external colleague access, either a Member or Admin must share the report directly, or the colleague must be added to the workspace with at least the Viewer role. Both options satisfy the requirement.

**Why other options are incorrect:**
- A) Contributors do not have sharing permissions in a Fabric workspace.
- B) This is partially correct but incomplete — adding the colleague as a Viewer is also valid.
- C) This is partially correct but incomplete — having a Member or Admin share the report is also valid.

</details>

---

### Question 18

An organization wants to use Fabric deployment pipelines. The pipeline has three stages: Development, Test, and Production. What is the minimum workspace role required to deploy content to the Production stage?

- A) Viewer
- B) Contributor
- C) Member
- D) Admin

<details>
<summary>Show Answer</summary>

**Correct Answer: C) Member**

**Explanation:** To deploy content to a stage in a deployment pipeline, a user must be a Member (or Admin) of the target workspace. Members have sufficient permissions to create and update items in the workspace, which is what the deployment operation performs. Contributors do not have the required deployment permissions in the pipeline context.

**Why other options are incorrect:**
- A) Viewers can only view content and cannot deploy.
- B) Contributors can edit items but do not have sufficient permissions for pipeline deployment operations.
- D) Admin works but is not the minimum required role.

</details>

---

### Question 19

A Fabric administrator wants to restrict which users can create workspaces in the tenant. Where is this setting configured?

- A) Microsoft Entra ID admin center
- B) Fabric admin portal tenant settings
- C) Individual capacity settings
- D) Microsoft 365 admin center groups settings

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Fabric admin portal tenant settings**

**Explanation:** The Fabric admin portal includes a tenant setting that controls which users can create workspaces. Administrators can restrict workspace creation to specific security groups, preventing unrestricted workspace sprawl and maintaining governance over the Fabric environment.

**Why other options are incorrect:**
- A) Microsoft Entra ID admin center manages identities and access policies but does not contain Fabric-specific workspace creation controls.
- C) Capacity settings control resource allocation and performance, not workspace creation permissions.
- D) The Microsoft 365 admin center manages Microsoft 365 group creation, which is related but not the direct control for Fabric workspace creation.

</details>

---

### Question 20

Your organization applies a "Highly Confidential" sensitivity label to a Power BI semantic model. A user exports data from a report connected to this semantic model to an Excel file. What happens to the sensitivity label?

- A) The label is removed during export
- B) The "Highly Confidential" label is automatically inherited by the exported Excel file
- C) The user is blocked from exporting entirely
- D) The Excel file receives a "General" label by default

<details>
<summary>Show Answer</summary>

**Correct Answer: B) The "Highly Confidential" label is automatically inherited by the exported Excel file**

**Explanation:** When sensitivity labels are applied to Power BI content, they flow downstream to exported files. If a user exports data to Excel, the exported file inherits the sensitivity label from the source semantic model, ensuring that data protection persists even outside the Fabric environment. This is a key feature of Microsoft Information Protection integration.

**Why other options are incorrect:**
- A) Labels are designed to persist through export operations, not be removed.
- C) Unless the label policy explicitly blocks export, the export is allowed with the label applied.
- D) The exported file inherits the source's label, not a default "General" label.

</details>

---

### Question 21

A Fabric administrator needs to delegate capacity management for a specific capacity to a department lead without granting full tenant administration. What should the administrator do?

- A) Assign the department lead as a Fabric Administrator in Microsoft Entra ID
- B) Add the department lead as a Capacity Administrator for that specific capacity
- C) Make the department lead a Global Administrator
- D) Give the department lead the Viewer role on all workspaces in the capacity

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Add the department lead as a Capacity Administrator for that specific capacity**

**Explanation:** Fabric allows assigning Capacity Administrators at the individual capacity level. This grants the department lead the ability to manage workspace assignments, monitor performance, and configure settings for that specific capacity without having tenant-wide administrative privileges.

**Why other options are incorrect:**
- A) The Fabric Administrator role provides tenant-wide control, which exceeds the required scope.
- C) Global Administrator is the broadest Microsoft Entra ID role and far exceeds the capacity management requirement.
- D) The Viewer role provides read-only access to workspace content and does not include any capacity management capabilities.

</details>

---

### Question 22

You are designing a data analytics solution in Fabric. The solution must support T-SQL queries for reporting analysts while also allowing Spark-based data engineering jobs. Which combination of Fabric items best supports both requirements within a single workspace?

- A) A lakehouse and a warehouse
- B) Two separate Power BI datasets
- C) An Azure SQL Database and a Spark pool
- D) A KQL database and a dataflow

<details>
<summary>Show Answer</summary>

**Correct Answer: A) A lakehouse and a warehouse**

**Explanation:** A Fabric lakehouse supports both Spark-based engineering and a SQL analytics endpoint for T-SQL queries. A Fabric warehouse provides a full T-SQL data warehousing experience. Together, these items in the same workspace allow data engineers to use Spark notebooks and reporting analysts to use T-SQL, all backed by OneLake.

**Why other options are incorrect:**
- B) Power BI datasets are semantic models for reporting and do not support Spark jobs or direct T-SQL warehousing.
- C) Azure SQL Database and Spark pools are separate Azure services, not native Fabric workspace items.
- D) A KQL database is optimized for log and telemetry analytics using KQL, not standard T-SQL, and a dataflow handles data transformation, not interactive SQL querying.

</details>

---

### Question 23

Your team uses Fabric Git integration. Multiple developers are working on the same workspace items simultaneously. How should they manage concurrent changes to avoid conflicts?

- A) Each developer should work in a separate branch and merge changes through pull requests
- B) Developers should communicate verbally and take turns editing
- C) Lock the Git repository during each developer's session
- D) Disable Git integration and use manual file copying

<details>
<summary>Show Answer</summary>

**Correct Answer: A) Each developer should work in a separate branch and merge changes through pull requests**

**Explanation:** Branching is the standard Git practice for managing concurrent development. Each developer works in an isolated branch, makes and commits changes, then merges through pull requests. This enables code review, conflict resolution, and an auditable history of all changes.

**Why other options are incorrect:**
- B) Verbal coordination does not scale and is error-prone without a systematic branching strategy.
- C) Locking the entire repository prevents concurrent work and defeats the purpose of distributed version control.
- D) Disabling Git integration removes all version control benefits and increases the risk of data loss and untracked changes.

</details>

---

### Question 24

A Fabric administrator observes that a workspace contains orphaned items — items whose original creators have left the organization. What is the recommended practice for managing these items?

- A) Delete all orphaned items immediately
- B) Reassign ownership by having a workspace Admin take over the items
- C) Wait for the items to be automatically removed after 90 days
- D) Export the items to a file share and archive them

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Reassign ownership by having a workspace Admin take over the items**

**Explanation:** When item creators leave the organization, workspace Admins or Members should review and take ownership of orphaned items to ensure continued maintenance and governance. This preserves data continuity and ensures items remain governed under the organization's policies.

**Why other options are incorrect:**
- A) Deleting items without review may remove critical reports and data assets still in use.
- C) Fabric does not automatically remove orphaned items after a set period.
- D) Exporting to a file share removes items from the governance framework and makes them harder to manage.

</details>

---

### Question 25

You need to ensure that data in a Fabric lakehouse is encrypted at rest. What action is required?

- A) Manually enable encryption in the lakehouse settings
- B) No action is required; OneLake encrypts data at rest by default
- C) Upload only pre-encrypted files to the lakehouse
- D) Configure Azure Key Vault integration for each file

<details>
<summary>Show Answer</summary>

**Correct Answer: B) No action is required; OneLake encrypts data at rest by default**

**Explanation:** OneLake automatically encrypts all data at rest using Microsoft-managed keys. This encryption is enabled by default and applies to all data stored in OneLake, including lakehouse files, warehouse data, and other Fabric items. No additional configuration is needed for baseline encryption at rest.

**Why other options are incorrect:**
- A) There is no manual encryption toggle; encryption at rest is automatic and always on.
- C) Uploading pre-encrypted files adds unnecessary complexity since OneLake already handles encryption.
- D) While customer-managed keys via Azure Key Vault can be configured for additional control, it is not required for basic encryption at rest.

</details>

---

### Question 26

A Fabric workspace is connected to a deployment pipeline. You need to apply different parameter values (such as database connection strings) for Test and Production stages. How should you configure this?

- A) Manually edit parameters after each deployment
- B) Use deployment rules to define parameter overrides per stage
- C) Create separate Git branches for each parameter set
- D) Store parameters in a shared OneLake folder

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use deployment rules to define parameter overrides per stage**

**Explanation:** Deployment pipelines support deployment rules that allow you to define parameter value overrides for each stage. For example, you can set a development database connection string for the Test stage and a production connection string for the Production stage. These rules are applied automatically during deployment.

**Why other options are incorrect:**
- A) Manually editing parameters after each deployment is error-prone and does not scale.
- C) Git branches manage code versions, not stage-specific runtime parameter values within deployment pipelines.
- D) Storing parameters in OneLake does not integrate with the deployment pipeline's built-in parameter override mechanism.

</details>

---

### Question 27

You are setting up monitoring for your Fabric environment. The CFO wants a weekly report on capacity consumption costs broken down by workspace. What is the best approach?

- A) Manually check the Azure portal billing page weekly
- B) Use the Microsoft Fabric Capacity Metrics app and create a scheduled report
- C) Ask each workspace owner to self-report their usage
- D) Enable Azure Cost Management alerts for the Fabric resource

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use the Microsoft Fabric Capacity Metrics app and create a scheduled report**

**Explanation:** The Capacity Metrics app provides granular metrics on capacity usage broken down by workspace and workload type. By building a report on top of this data and scheduling its delivery, you can provide the CFO with consistent, automated weekly cost consumption summaries without manual effort.

**Why other options are incorrect:**
- A) Manually checking billing pages does not provide workspace-level breakdown of Fabric capacity consumption.
- C) Self-reporting is unreliable and creates an administrative burden.
- D) Azure Cost Management provides subscription-level cost data but does not break down Fabric capacity consumption at the workspace level with the same granularity.

</details>

---

### Question 28

A data steward needs to understand which downstream reports will be affected if a specific lakehouse table's schema is changed. Which feature should they use?

- A) Impact analysis through data lineage
- B) Power BI Q&A
- C) Workspace access list
- D) Capacity Metrics app

<details>
<summary>Show Answer</summary>

**Correct Answer: A) Impact analysis through data lineage**

**Explanation:** Data lineage in Fabric provides impact analysis capabilities that show all downstream items dependent on a specific data source or item. By examining the lineage of a lakehouse table, the data steward can identify every semantic model, report, and dashboard that would be affected by a schema change.

**Why other options are incorrect:**
- B) Power BI Q&A enables natural language queries against data and does not show item dependencies.
- C) The workspace access list shows who has access to the workspace, not item dependencies.
- D) The Capacity Metrics app monitors performance and consumption, not data dependencies.

</details>

---

### Question 29

An organization has both Microsoft Fabric and an existing Azure Synapse Analytics workspace. They want to query data stored in their Synapse-linked ADLS Gen2 account from Fabric without migrating the data. What should they use?

- A) Create a Fabric data pipeline to copy data nightly
- B) Create a OneLake shortcut pointing to the ADLS Gen2 account
- C) Set up database mirroring from Synapse to Fabric
- D) Export Synapse data to CSV and upload to OneLake

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Create a OneLake shortcut pointing to the ADLS Gen2 account**

**Explanation:** OneLake shortcuts can reference external ADLS Gen2 storage, making the data accessible within Fabric as if it were native OneLake data. This avoids data migration and duplication while enabling Fabric workloads to query the data in place.

**Why other options are incorrect:**
- A) Copying data nightly creates duplication and latency, contradicting the requirement to avoid migration.
- C) Database mirroring replicates operational database data, not ADLS Gen2 files, into Fabric.
- D) Manual CSV export and upload is inefficient, error-prone, and creates data duplication.

</details>

---

### Question 30

A company requires that all Fabric admin actions be retained for seven years for regulatory compliance. How should this be configured?

- A) Enable extended log retention in the Fabric admin portal
- B) Configure audit log retention policies in Microsoft Purview compliance portal and export logs to long-term storage
- C) Take monthly screenshots of the admin portal activity
- D) Assign a user to manually document all admin actions

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Configure audit log retention policies in Microsoft Purview compliance portal and export logs to long-term storage**

**Explanation:** Microsoft Purview audit logs capture all Fabric admin activities. To meet long-term retention requirements, organizations should configure retention policies in the Purview compliance portal and optionally export logs to Azure storage or a SIEM solution for archival beyond the default retention period.

**Why other options are incorrect:**
- A) The Fabric admin portal does not have a built-in seven-year log retention setting.
- C) Screenshots are not auditable, searchable, or acceptable as a compliance control.
- D) Manual documentation is unreliable, not tamper-proof, and does not meet regulatory audit requirements.

</details>

---

### Question 31

Your Fabric tenant has domain-level organization enabled. Which benefit does assigning workspaces to domains provide?

- A) Workspaces in a domain automatically get increased storage quotas
- B) Domains enable logical grouping of workspaces with centralized governance policies and default sensitivity labels
- C) Domains replace the need for workspace roles entirely
- D) Domains allow workspaces to span multiple Azure regions

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Domains enable logical grouping of workspaces with centralized governance policies and default sensitivity labels**

**Explanation:** Fabric domains allow organizations to logically group workspaces by business area (e.g., Finance, Marketing). Domain admins can enforce governance policies, set default sensitivity labels, and manage endorsement settings across all workspaces in the domain, providing consistent governance at the business-unit level.

**Why other options are incorrect:**
- A) Domains do not affect storage quotas; storage is determined by capacity.
- C) Workspace roles remain the primary access control mechanism within individual workspaces; domains supplement, not replace them.
- D) Workspaces are bound to the region of their assigned capacity; domains do not change regional boundaries.

</details>

---

### Question 32

An administrator wants to ensure that only approved semantic models can be used for report creation across the organization. Which Fabric governance feature supports this?

- A) Endorsement (Certified and Promoted)
- B) Row-level security
- C) Workspace viewer permissions
- D) Capacity throttling

<details>
<summary>Show Answer</summary>

**Correct Answer: A) Endorsement (Certified and Promoted)**

**Explanation:** Fabric supports endorsement labels — Certified and Promoted — that mark trusted, approved content. Certified semantic models are verified by designated reviewers, and organizations can configure policies to encourage or require users to build reports on endorsed content, improving data quality and consistency.

**Why other options are incorrect:**
- B) Row-level security restricts data access within a dataset but does not control which datasets are used for reporting.
- C) Viewer permissions control who can see items, not which items are approved for broader use.
- D) Capacity throttling manages compute resources and has no bearing on content approval or endorsement.

</details>

---

### Question 33

You are configuring OneLake data access roles for a lakehouse. You want to grant a team read-only access to a specific folder within the lakehouse while preventing access to other folders. What should you configure?

- A) Workspace-level Viewer role
- B) OneLake data access roles with folder-level read permissions
- C) Row-level security on the SQL analytics endpoint
- D) A sensitivity label on the restricted folders

<details>
<summary>Show Answer</summary>

**Correct Answer: B) OneLake data access roles with folder-level read permissions**

**Explanation:** OneLake data access roles provide fine-grained access control at the folder level within a lakehouse. By creating a role that grants read permissions to a specific folder and assigning the team to that role, you ensure they can only access the designated data while other folders remain inaccessible.

**Why other options are incorrect:**
- A) The Viewer role grants access to all viewable items in the workspace, not specific folders within a lakehouse.
- C) Row-level security filters rows in queries but does not control access to lakehouse file-system folders.
- D) Sensitivity labels classify data but do not enforce folder-level access control within a lakehouse.

</details>

---

### Question 34

Your organization is evaluating Fabric capacity SKUs. The team requires autoscaling so that capacity can temporarily increase during peak processing. What feature should they evaluate?

- A) Fabric capacity bursting and smoothing
- B) Manual capacity SKU upgrades
- C) Adding more Power BI Pro licenses
- D) Creating additional workspaces

<details>
<summary>Show Answer</summary>

**Correct Answer: A) Fabric capacity bursting and smoothing**

**Explanation:** Fabric capacities support bursting, which allows workloads to temporarily consume compute resources beyond the baseline SKU allocation. Consumption smoothing spreads usage over time to avoid unnecessary throttling during short peaks. Together, these features provide elastic handling of peak processing without permanent SKU upgrades.

**Why other options are incorrect:**
- B) Manual SKU upgrades provide permanent increases and are not designed for temporary peak handling.
- C) Power BI Pro licenses are per-user licensing and do not affect capacity compute resources.
- D) Creating additional workspaces does not add compute capacity; workspaces share their assigned capacity.

</details>

---

### Question 35

A developer accidentally commits sensitive connection strings to the Git repository connected to a Fabric workspace. What immediate actions should be taken?

- A) Delete the workspace and recreate it
- B) Rotate the exposed credentials, remove the sensitive data from Git history, and review access logs
- C) Rename the connection string parameters
- D) Add a disclaimer to the repository README

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Rotate the exposed credentials, remove the sensitive data from Git history, and review access logs**

**Explanation:** When credentials are committed to a Git repository, the immediate priority is to rotate (change) the exposed credentials to prevent unauthorized access, purge the sensitive data from the repository's commit history, and review audit logs to determine if the credentials were accessed by unauthorized parties.

**Why other options are incorrect:**
- A) Deleting the workspace does not address the credentials in Git history and causes unnecessary data loss.
- C) Renaming parameters does not revoke the exposed credential values that remain in commit history.
- D) A README disclaimer does not remediate the security exposure.

</details>

---

### Question 36

A Fabric administrator needs to identify which workloads are consuming the most capacity units (CUs) over the past week. Which metric in the Capacity Metrics app should they focus on?

- A) Number of workspace users
- B) CU consumption by workload type and item
- C) Total number of reports published
- D) Number of active Git branches

<details>
<summary>Show Answer</summary>

**Correct Answer: B) CU consumption by workload type and item**

**Explanation:** The Capacity Metrics app provides detailed CU consumption metrics broken down by workload type (e.g., data warehouse, data engineering, Power BI) and individual items. This view directly answers which workloads are consuming the most capacity resources over a given time period.

**Why other options are incorrect:**
- A) The number of workspace users does not indicate resource consumption.
- C) The number of published reports is a count metric that does not reflect compute usage.
- D) Git branches are a development construct and are not tracked as a capacity consumption metric.

</details>

---

### Question 37

Your organization uses Microsoft Fabric and wants to implement a hub-and-spoke workspace architecture. What is the primary purpose of this pattern?

- A) To consolidate all items into a single workspace for simplicity
- B) To centralize shared datasets in a hub workspace while spoke workspaces contain department-specific reports
- C) To distribute storage evenly across all Azure regions
- D) To eliminate the need for workspace roles

<details>
<summary>Show Answer</summary>

**Correct Answer: B) To centralize shared datasets in a hub workspace while spoke workspaces contain department-specific reports**

**Explanation:** The hub-and-spoke pattern places curated, shared semantic models and data assets in a central hub workspace, while individual departments maintain their own spoke workspaces for reports and analysis. This promotes data reuse, consistency, and a single source of truth while allowing departmental autonomy.

**Why other options are incorrect:**
- A) Consolidating everything into one workspace creates permission management challenges and does not scale for large organizations.
- C) Storage distribution across regions is managed through capacity placement, not workspace architecture patterns.
- D) Workspace roles are still required in both hub and spoke workspaces to manage access.

</details>

---

### Question 38

A Fabric workspace admin wants to enable Trusted workspace access so that a workspace can access data in OneLake from a Fabric item with a managed identity. What must be configured?

- A) A service principal in Microsoft Entra ID and a client secret
- B) The workspace identity must be enabled, and the storage account must have a resource instance rule allowing the workspace
- C) A shared access signature (SAS) token on the storage account
- D) A Power BI gateway in the workspace

<details>
<summary>Show Answer</summary>

**Correct Answer: B) The workspace identity must be enabled, and the storage account must have a resource instance rule allowing the workspace**

**Explanation:** Trusted workspace access leverages the workspace's managed identity to authenticate with external resources. The workspace identity must be enabled in Fabric, and the target storage account must be configured with a resource instance rule that trusts the specific Fabric workspace, allowing secure access without shared keys or secrets.

**Why other options are incorrect:**
- A) Service principals with client secrets are a different authentication pattern; trusted workspace access uses the built-in workspace identity.
- C) SAS tokens are shared-secret based and do not leverage the workspace's managed identity.
- D) Power BI gateways are for connecting to on-premises data sources, not for workspace-to-storage identity-based access.

</details>

---

### Question 39

A data engineer creates a OneLake shortcut from a Fabric lakehouse to an Amazon S3 bucket. Which authentication method is used to access the S3 data?

- A) Microsoft Entra ID single sign-on
- B) A connection with a key or role ARN configured through Fabric
- C) Windows Integrated Authentication
- D) OneLake automatically synchronizes credentials with AWS IAM

<details>
<summary>Show Answer</summary>

**Correct Answer: B) A connection with a key or role ARN configured through Fabric**

**Explanation:** When creating a shortcut to Amazon S3, Fabric requires a connection that specifies the authentication to the S3 bucket. This can use an access key and secret key or an IAM role ARN. The connection is configured in Fabric and stores the credentials securely for the shortcut to use.

**Why other options are incorrect:**
- A) Microsoft Entra ID SSO is not supported for cross-cloud authentication to AWS S3 via shortcuts.
- C) Windows Integrated Authentication is for on-premises Windows environments, not cloud-to-cloud storage access.
- D) OneLake does not automatically synchronize with AWS IAM; explicit credential configuration is required.

</details>

---

### Question 40

A compliance team requires that all Power BI reports in the Finance workspace display a "Confidential" watermark when viewed. Which approach achieves this?

- A) Add a text box watermark to every report manually
- B) Apply a Microsoft Purview sensitivity label with visual marking policies
- C) Use a Power BI theme with a watermark background
- D) Configure row-level security to display a watermark row

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Apply a Microsoft Purview sensitivity label with visual marking policies**

**Explanation:** Microsoft Purview sensitivity labels can be configured with content marking policies that include headers, footers, and watermarks. When a "Confidential" label with visual marking is applied to reports, the marking is displayed automatically, ensuring consistent compliance marking without manual intervention.

**Why other options are incorrect:**
- A) Manually adding text boxes is not scalable and can be removed by report editors.
- C) Power BI themes control visual styling but do not enforce compliance-grade watermarks that persist with the content.
- D) Row-level security filters data rows and cannot render visual watermarks on reports.

</details>

---

### Question 41

You are implementing a disaster recovery strategy for critical Fabric workloads. Which approach aligns with Microsoft's recommended practices?

- A) Keep a manual backup of all .pbix files on a local network drive
- B) Use Git integration for version control of workspace item definitions and leverage OneLake's built-in redundancy
- C) Create daily screenshots of all reports for recovery purposes
- D) Rely solely on the recycle bin for item recovery

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use Git integration for version control of workspace item definitions and leverage OneLake's built-in redundancy**

**Explanation:** Git integration provides a recoverable history of workspace item definitions that can be redeployed if items are lost or corrupted. OneLake provides built-in data redundancy through Azure storage replication. Together, these mechanisms form a robust disaster recovery foundation aligned with Microsoft's recommended practices.

**Why other options are incorrect:**
- A) Manual .pbix backups on a network drive are not comprehensive for all Fabric item types and are prone to being outdated.
- C) Report screenshots cannot be used to restore functional reports or data.
- D) The recycle bin has limited retention and does not protect against all disaster scenarios.

</details>

---

### Question 42

An admin wants to prevent users from creating Fabric trial capacities in the tenant. Where should this be disabled?

- A) Azure subscription cost management
- B) Fabric admin portal tenant settings
- C) Microsoft Entra ID license management
- D) Individual user profile settings

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Fabric admin portal tenant settings**

**Explanation:** The Fabric admin portal includes tenant settings that control whether users can start Fabric trial capacities. By disabling this setting (or restricting it to specific security groups), administrators prevent unauthorized trial capacity creation and maintain control over the tenant's capacity landscape.

**Why other options are incorrect:**
- A) Azure cost management tracks spending but does not control Fabric trial capacity creation.
- C) Microsoft Entra ID license management handles license assignment but does not directly control Fabric trial provisioning.
- D) Individual user settings do not include the ability to control trial capacity creation at the tenant level.

</details>

---

### Question 43

A data engineer wants to use a Fabric notebook to read data from a lakehouse in a different workspace. What is required for this cross-workspace access?

- A) The data engineer must be a workspace Admin in both workspaces
- B) The data engineer needs at least read permissions on the lakehouse in the other workspace, and can reference it via its path
- C) Cross-workspace access is not possible in Fabric
- D) The lakehouse must be exported and re-imported into the engineer's workspace

<details>
<summary>Show Answer</summary>

**Correct Answer: B) The data engineer needs at least read permissions on the lakehouse in the other workspace, and can reference it via its path**

**Explanation:** Fabric supports cross-workspace data access. A user with at least read permissions (such as the Viewer role or item-level read permissions) on a lakehouse in another workspace can reference that lakehouse's OneLake path from a notebook, enabling seamless cross-workspace data consumption.

**Why other options are incorrect:**
- A) Admin-level access is not required; read permissions are sufficient for data consumption.
- C) Cross-workspace access is a supported capability in Fabric.
- D) Exporting and re-importing data is unnecessary given Fabric's built-in cross-workspace access capabilities.

</details>

---

### Question 44

Your organization has a Fabric capacity assigned to the East US region. A new project requires a workspace, but the project team is based in West Europe. What should you consider regarding workspace creation?

- A) The workspace will automatically be created in West Europe based on user location
- B) The workspace will reside in the East US region because it inherits the capacity's region; consider provisioning a new capacity in West Europe for lower latency
- C) Workspaces are not tied to any region
- D) The team must use a VPN to access the workspace

<details>
<summary>Show Answer</summary>

**Correct Answer: B) The workspace will reside in the East US region because it inherits the capacity's region; consider provisioning a new capacity in West Europe for lower latency**

**Explanation:** Workspaces in Fabric reside in the Azure region of their assigned capacity. If the capacity is in East US, the workspace's data and compute are in East US, which may result in higher latency for West Europe users. Provisioning a capacity in West Europe and assigning the workspace to it would reduce latency and may help with data residency requirements.

**Why other options are incorrect:**
- A) Workspace region is determined by the capacity, not the user's physical location.
- C) Workspaces are always tied to the region of their assigned capacity.
- D) A VPN does not change the data's physical region or significantly improve cross-region latency for Fabric services.

</details>

---

### Question 45

An organization wants to automate the deployment of Fabric workspace items using CI/CD practices. Which combination of Fabric features best supports this?

- A) Git integration and deployment pipelines
- B) Power BI REST APIs only
- C) Manual export and import of .pbix files
- D) Email-based approval workflows

<details>
<summary>Show Answer</summary>

**Correct Answer: A) Git integration and deployment pipelines**

**Explanation:** Git integration provides source control for workspace items, enabling versioned, reviewable changes. Deployment pipelines automate the promotion of content through Development, Test, and Production stages. Together, they form a complete CI/CD workflow for Fabric, supporting automated, governed content deployment.

**Why other options are incorrect:**
- B) Power BI REST APIs can be used for automation but do not provide the integrated Git-based CI/CD workflow native to Fabric.
- C) Manual file operations are not automated and do not support CI/CD principles.
- D) Email-based approvals are not an automation mechanism and do not integrate with version control or deployment processes.

</details>

---

### Question 46

A tenant has multiple Fabric capacities. An administrator wants to move a workspace from one capacity to another. What consideration is important?

- A) All users will lose access to the workspace permanently
- B) The workspace data will move to the new capacity's region if the capacities are in different regions, potentially affecting data residency
- C) Moving a workspace between capacities is not supported
- D) The workspace must be empty before it can be reassigned

<details>
<summary>Show Answer</summary>

**Correct Answer: B) The workspace data will move to the new capacity's region if the capacities are in different regions, potentially affecting data residency**

**Explanation:** When a workspace is reassigned to a capacity in a different region, the workspace's data migrates to the new region. This is a critical consideration for organizations with data residency requirements, as moving data across regions may violate regulatory obligations.

**Why other options are incorrect:**
- A) Users retain access based on their workspace roles; capacity reassignment does not remove user permissions.
- C) Moving workspaces between capacities is a supported operation in Fabric.
- D) Workspaces can be reassigned to a different capacity regardless of their content.

</details>

---

### Question 47

A Fabric administrator wants to set up alerts when capacity utilization exceeds 80% for more than 30 minutes. What is the recommended approach?

- A) Manually check the Capacity Metrics app every 30 minutes
- B) Configure alerts using the capacity monitoring and alerting capabilities, or use Azure Monitor metrics
- C) Ask users to report slow performance via email
- D) Set the capacity to automatically shut down at 80% utilization

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Configure alerts using the capacity monitoring and alerting capabilities, or use Azure Monitor metrics**

**Explanation:** Fabric capacity metrics can be integrated with Azure Monitor to create alert rules based on utilization thresholds. Administrators can configure alerts to trigger when utilization exceeds 80% for a sustained period, enabling proactive response to capacity pressure before users experience significant performance degradation.

**Why other options are incorrect:**
- A) Manual checking is not scalable or reliable for continuous monitoring.
- C) User-reported alerts are reactive and inconsistent.
- D) Fabric capacities do not have an automatic shutdown feature at a utilization threshold, and shutting down would be disruptive.

</details>

---

### Question 48

You are tasked with ensuring that a specific Fabric workspace follows a naming convention for all items (e.g., all semantic models must start with "SM_"). What is the best way to enforce this?

- A) Configure a built-in Fabric naming rule in workspace settings
- B) Establish a governance policy with documentation and periodic manual reviews, or use automation scripts via APIs
- C) Use sensitivity labels to encode naming conventions
- D) Restrict item creation to only workspace Admins

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Establish a governance policy with documentation and periodic manual reviews, or use automation scripts via APIs**

**Explanation:** Fabric does not currently have a built-in naming convention enforcement feature. The recommended approach is to document naming standards in a governance policy, communicate them to the team, and optionally enforce them through automation using the Fabric REST APIs to audit or rename non-compliant items.

**Why other options are incorrect:**
- A) There is no built-in naming rule feature in Fabric workspace settings.
- C) Sensitivity labels classify data sensitivity, not naming conventions.
- D) Restricting creation to Admins limits productivity and does not inherently enforce naming conventions.

</details>

---

### Question 49

A large enterprise is onboarding to Fabric and wants to ensure that each business unit's data is isolated at the storage level, even within the same tenant. Which approach provides the strongest data isolation?

- A) Assign each business unit its own workspace with different workspace roles
- B) Assign each business unit its own capacity and configure OneLake data access roles for fine-grained control
- C) Place all data in one lakehouse with row-level security
- D) Use separate Power BI Desktop files for each business unit

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Assign each business unit its own capacity and configure OneLake data access roles for fine-grained control**

**Explanation:** Separate capacities provide compute and billing isolation, while OneLake data access roles enforce storage-level access control at the folder and item level. This combination delivers the strongest isolation within a single tenant, ensuring that one business unit's data cannot be accessed by another at the storage layer.

**Why other options are incorrect:**
- A) Workspace roles provide access control but do not offer the same level of storage-level isolation as separate capacities combined with OneLake data access roles.
- C) Row-level security filters query results but does not prevent direct file-level access to the underlying data in OneLake.
- D) Power BI Desktop files are local authoring tools and do not represent a storage isolation strategy in Fabric.

</details>

---

### Question 50

A Fabric administrator needs to review which external users (guest accounts) have access to Fabric workspaces and what roles they hold. What is the most effective method?

- A) Review Microsoft Entra ID guest user list only
- B) Use the Fabric admin portal to review workspace access lists and cross-reference with Microsoft Entra ID guest accounts
- C) Send a survey to all workspace admins asking about external users
- D) Disable all guest access preventively

<details>
<summary>Show Answer</summary>

**Correct Answer: B) Use the Fabric admin portal to review workspace access lists and cross-reference with Microsoft Entra ID guest accounts**

**Explanation:** The Fabric admin portal provides administrators with visibility into workspace membership across the tenant. By reviewing workspace access lists and cross-referencing with Microsoft Entra ID's guest user directory, administrators can compile a comprehensive view of which external users have access and their assigned roles.

**Why other options are incorrect:**
- A) The Microsoft Entra ID guest user list shows who has a guest account but not which Fabric workspaces they can access or their roles.
- C) Surveys are unreliable and time-consuming, with no guarantee of complete or accurate responses.
- D) Disabling all guest access is disruptive and may break legitimate external collaboration workflows.

</details>

---
