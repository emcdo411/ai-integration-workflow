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

![Aeristo ETL Workflow Diagram](https://via.placeholder.com/1200x800.png?text=Aeristo+ETL+Workflow+Diagram)

> *This diagram visualizes the incremental data integration workflow across Dynamics AX, Azure Data Factory, Business Central, Azure SQL, and API-driven reporting layers.*

---

## 📊 Data Consumption for Reporting

* **Excel**: Power Query connects to either Azure SQL Staging or Business Central OData feeds
* **R / RShiny**: Consumes Business Central data via OData V4 API or REST endpoints

---

## 📱 Compatible APIs for R & RShiny

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
