# 🤩 Aeristo Data Integration Case Study & Proposal

## 📌 Summary

This document presents a **cost-effective and scalable ETL and data integration strategy** for Aeristo, a luxury leather goods company specializing in bespoke interiors for luxury automobiles and private aircraft (e.g., G6, G7). The objective is to integrate legacy data from **Microsoft Dynamics AX (pre-2023)** with **Microsoft Dynamics 365 Business Central (current live data)** and ensure future compatibility with Excel-based reporting, R, and RShiny applications for real-time analytics.

---

## 📚 Table of Contents

1. [Business Problem](#business-problem)
2. [Technical Overview](#technical-overview)
3. [ETL Strategy & Middleware](#etl-strategy--middleware)
4. [Integration Workflow Diagram](#integration-workflow-diagram)
5. [Data Consumption for Reporting](#data-consumption-for-reporting)
6. [Compatible APIs for R & RShiny](#compatible-apis-for-r--rshiny)
7. [Tech Stack](#tech-stack)
8. [Conclusion](#conclusion)

---

## 🧭 Business Problem

Aeristo operates two parallel systems:

* **Microsoft Dynamics AX**: Legacy system used pre-2023 for **customer/vendor records, historical sales, and purchasing history**.
* **Microsoft D365 Business Central**: Current real-time ERP system, source of operational truth post-2023.

**Key Challenge**: These systems do not communicate, limiting data-driven decision-making and report creation.

---

## ⚙️ Technical Overview

We propose an **ETL pipeline** that will:

* Extract historical data from Dynamics AX
* Transform it into schema-aligned format for Business Central
* Load it incrementally (not one-swoop due to system load concerns)

Additionally, we will **expose API endpoints** for:

* R & RShiny dashboards
* Excel-based reporting

---

## 🛠️ ETL Strategy & Middleware

### 🔄 ETL Process (Incremental)

1. **Extract**: AX data via SQL Server or OData
2. **Transform**: Schema conversion, date alignment, metadata tagging
3. **Load**: To Business Central via APIs or intermediary Azure SQL DB

### 🧍️‍⚖️ Middleware: Azure Data Factory

* **Cost-effective, low-code cloud ETL tool**
* Native connectors for Dynamics AX, D365 BC, Azure SQL, Excel
* Supports **incremental loads, scheduling, and logging**

### ✅ Benefits

* No local infrastructure needed
* Compatible with Excel/Power Query
* Governed via Azure Security & Logging

---

## 🧬 Integration Workflow Diagram

### 🔍 Executive Overview

This diagram illustrates how Aeristo's historical and live business data will be seamlessly connected, allowing for enterprise-grade reporting and data visibility across departments.

**Here’s how each component works:**

* **Dynamics AX (Historical Data)**: This is where Aeristo’s customer, vendor, sales, and purchasing history lived before 2023. While it's no longer the active system, this data is too valuable to discard.

* **Azure Data Factory**: Think of this as the data pipeline. It smartly extracts the data from AX, transforms it so it looks like the current system’s format, and loads it where it’s needed. It does this in small batches, so there's no disruption to your operations.

* **D365 Business Central (Live ERP)**: This is the heart of Aeristo’s day-to-day business. Once transformed, the historical data lands here and is blended with your current data, enabling complete visibility for leadership, sales, and finance teams.

* **Azure SQL (Staging Layer)**: This acts like a temporary holding pen. It’s useful for testing and cleaning up data before it reaches Business Central, or for storing specific reports.

* **Excel / R API Reporting**: This is where the value becomes visible. Whether a manager prefers Excel or your data scientists work in R and Shiny, both teams will have real-time access to updated data for decision-making, forecasting, and dashboard creation.

```mermaid
graph LR
  AX["Dynamics AX<br/>(Historical Data)"]:::system -->|"OData / SQL Server"| ADF["Azure Data Factory<br/>(Incremental ETL)"]:::etl
  ADF -->|"REST API"| BC["D365 Business Central<br/>(Real-Time ERP)"]:::system
  ADF -->|"Load Raw + Transformed Data"| SQL["Azure SQL<br/>(Staging Layer)"]:::db
  SQL -->|"OData V4 / JSON"| API["Excel / R API<br/>Reporting & Shiny Apps"]:::reporting
  BC -->|"Direct REST / OData"| API

  classDef system fill:#4B8BBE,color:#fff;
  classDef etl fill:#306998,color:#fff;
  classDef db fill:#1E90FF,color:#fff;
  classDef reporting fill:#228B22,color:#fff;
```

> *This diagram visualizes the incremental data integration workflow across Dynamics AX, Azure Data Factory, Business Central, Azure SQL, and API-driven reporting layers.*

---

## 📊 Data Consumption for Reporting

### 📊 How RShiny Connects and Adds Value

**RShiny** is a powerful tool used by data analysts and scientists to build interactive dashboards, applications, and visual reports—similar to what you'd find in tools like Tableau or PowerBI, but with far greater customization and statistical depth.

Here's how it fits into the Aeristo solution:

* **Live Access to Data**: RShiny connects directly to Business Central’s APIs (REST or OData V4). That means any dashboard or visualization is pulling live, real-time data—no exports or stale spreadsheets.

* **Fully Interactive Dashboards**: Whether you're tracking sales performance, supply chain movement, or vendor spend, RShiny allows you to click, filter, and zoom in on trends just like a premium enterprise BI tool.

* **Custom Reporting & Forecasting**: R and RShiny allow data scientists to apply advanced models—like demand forecasting, anomaly detection, and customer segmentation—right inside the dashboards.

* **Deployment on Internal Network or Azure**: Once built, these apps can be deployed on Aeristo’s intranet or through an Azure service, allowing department heads, executives, or production leads to see tailored insights instantly.

* **Cost-Effective**: RShiny is open-source, which means no per-seat licensing like other enterprise tools. It’s powerful, secure, and budget-friendly.

In short, RShiny allows Aeristo to move from static reports to **dynamic, self-service intelligence**—tailored to your industry, powered by real-time ERP data.

* **Excel**: Power Query connects to either Azure SQL Staging or Business Central OData feeds
* **R / RShiny**: Consumes Business Central data via OData V4 API or REST endpoints

---

## 📱 Compatible APIs for R & RShiny

### 🔍 What Are OData V4 and REST APIs — and Why They Matter

To connect RShiny dashboards and Excel reports to Business Central in real-time, we rely on two powerful technologies: **OData V4** and **REST APIs**.

* **OData V4 (Open Data Protocol)**: Think of this as a structured language that allows Excel, R, or Power BI to talk directly to Business Central. It’s like giving your reporting tools a secure pass to go into your ERP and grab the latest data—live and on demand.

* **REST API (Representational State Transfer)**: REST is a modern, lightweight way for apps (like RShiny) to get very specific pieces of data, securely and in real time. REST APIs are flexible, fast, and highly compatible with advanced analytics tools. If OData is like Excel-friendly language, REST is what developers and data scientists use for full customization.

These technologies mean we don’t need to export files or do manual uploads. Everything updates in real time, so reports and dashboards always reflect the current state of your business.

### 🚀 How They're Deployed with Microsoft Dynamics 365

Both APIs are already **enabled by default** in Microsoft Dynamics 365 Business Central (cloud). Here's how they are typically deployed:

#### Step 1: Enable Web Services in Business Central

Within Business Central:

* Navigate to **Web Services** (`Search > Web Services`)
* Enable the OData and SOAP endpoints for the relevant tables or queries
* For REST, use the available standard or custom API pages

#### Step 2: Expose an API or OData Feed

You can publish an OData feed like this:

```plaintext
https://api.businesscentral.dynamics.com/v2.0/{tenant-id}/sandbox/ODataV4/Customer
```

Or call a REST endpoint for customers:

```http
GET https://api.businesscentral.dynamics.com/v2.0/{tenant-id}/sandbox/api/v2.0/companies({company-id})/customers
Authorization: Bearer <access_token>
```

These are the URLs and tokens your R scripts or Excel Power Query tools will use.

#### Step 3: Authenticate Using Azure AD

* Register your RShiny app or Excel workbook as an **Azure Active Directory (AAD)** application
* Grant it permissions to access Business Central APIs

#### Step 4: Query Data from R

Here’s an example of pulling live customer data in R:

```r
library(httr)
url <- "https://api.businesscentral.dynamics.com/v2.0/{tenant-id}/sandbox/api/v2.0/companies({company-id})/customers"
res <- GET(url, add_headers(Authorization = paste("Bearer", access_token)))
data <- content(res)
```

This ensures your apps and reports are updated in real time—without moving or exporting data manually.

> With this setup, Aeristo gains a modern, secure, and scalable pipeline for analytics and dashboards without disrupting core operations.

To connect RShiny dashboards and Excel reports to Business Central in real-time, we rely on two powerful technologies: **OData V4** and **REST APIs**.

* **OData V4 (Open Data Protocol)**: Think of this as a structured language that allows Excel, R, or Power BI to talk directly to Business Central. It’s like giving your reporting tools a secure pass to go into your ERP and grab the latest data—live and on demand.

* **REST API (Representational State Transfer)**: REST is a modern, lightweight way for apps (like RShiny) to get very specific pieces of data, securely and in real time. REST APIs are flexible, fast, and highly compatible with advanced analytics tools. If OData is like Excel-friendly language, REST is what developers and data scientists use for full customization.

These technologies mean we don’t need to export files or do manual uploads. Everything updates in real time, so reports and dashboards always reflect the current state of your business.

---

### 💵 Estimated Tooling Costs (Annualized)

| Tool / Service                 | Purpose                                          | Estimated Annual Cost            |
| ------------------------------ | ------------------------------------------------ | -------------------------------- |
| Azure Data Factory             | ETL pipeline for AX → BC integration             | \$1,200 - \$3,000                |
| Azure SQL (Staging Layer)      | Temporary storage and transformation layer       | \$600 - \$1,500                  |
| Microsoft Dynamics 365 BC API  | OData/REST API access (included in D365 license) | Bundled with license             |
| RStudio + RShiny (open-source) | Dashboard & analytics engine                     | Free (hosted internally)         |
| Optional: RShiny Server Pro    | Managed RShiny deployment (for IT support)       | \~\$10,000 (optional enterprise) |
| Excel / Power Query            | Excel integration via OData                      | Bundled with Office license      |

> These estimates are based on average Azure pricing tiers and assume a mid-size data volume. Final costs may vary based on refresh frequency and storage footprint.

### 🔗 OData V4

* Native to D365 BC
* Easy integration with `httr`, `tidyverse`, or `RODBC`

### 🔗 REST APIs

* For more complex authentication scenarios (OAuth 2.0)
* Supports `httr::GET()` and `jsonlite::fromJSON()` for dashboard ingestion

### 🔗 R Packages

![httr](https://img.shields.io/badge/-httr-1f77b4)
![jsonlite](https://img.shields.io/badge/-jsonlite-ff7f0e)
![RODBC](https://img.shields.io/badge/-RODBC-2ca02c)
![shiny](https://img.shields.io/badge/-shiny-17becf)
![tidyverse](https://img.shields.io/badge/-tidyverse-d62728)

---

## 💻 Tech Stack

### 🔧 Microsoft Dynamics AX

![SQL Server](https://img.shields.io/badge/-SQL%20Server-CC2927?logo=microsoftsqlserver)
![.NET](https://img.shields.io/badge/-.NET-512BD4?logo=dotnet)
![OData](https://img.shields.io/badge/-OData-0072C6?logo=microsoft)

### 🔧 Microsoft Dynamics 365 Business Central

![D365 BC](https://img.shields.io/badge/-D365%20Business%20Central-0078D4?logo=microsoft)
![OData V4](https://img.shields.io/badge/-OData%20v4-005B9E?logo=odata)
![REST API](https://img.shields.io/badge/-REST%20API-008080)

### 🔧 Middleware & Cloud Tools

![Azure Data Factory](https://img.shields.io/badge/-Azure%20Data%20Factory-0078D4?logo=microsoftazure)
![Azure SQL](https://img.shields.io/badge/-Azure%20SQL-00BFFF?logo=microsoftsqlserver)
![Power Query](https://img.shields.io/badge/-Power%20Query-217346?logo=microsoftexcel)

---

## 📾 Conclusion

The proposed solution uses **Azure Data Factory** to perform **incremental ETL operations** from Microsoft Dynamics AX into Business Central using schema-aligned transformations and staging via Azure SQL. This allows Aeristo to unify historical and live data without disrupting current operations.

Once in place, this pipeline enables real-time reporting via **Excel**, **R**, and **RShiny**, leveraging **OData V4 and REST APIs** directly from Business Central. The architecture is cloud-based, **scalable, secure, and designed to minimize infrastructure costs**.

---

> ✅ This proposal is designed for executive decision-makers, senior data engineers, and software architects. It ensures enterprise scalability and developer-friendly tools without compromising cost-efficiency or compatibility with modern data science stacks.





