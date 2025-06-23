# 🌟 Aeristo Data Integration Case Study & Phased Implementation Plan

---

## 📌 Summary

This document outlines a modern, scalable ETL and data integration strategy for Aeristo, a Dallas-based luxury leather goods company specializing in bespoke interiors for high-end automobiles and private aircraft (e.g., Gulfstream G6, G7). The goal is to integrate legacy data from Microsoft Dynamics AX (pre-2023) with Microsoft Dynamics 365 Business Central (current live data), enabling real-time analytics through Excel, Power BI, and R Shiny applications. The project is structured in three phases—data integration and ETL pipeline setup, reporting and dashboard development, and ongoing optimization—delivering a cloud-native, secure solution as of June 2025.

---

## 📚 Table of Contents

* [Business Problem](#business-problem)
* [Technical Overview](#technical-overview)
* [ETL Strategy & Middleware](#etl-strategy--middleware)
* [Integration Workflow Diagram](#integration-workflow-diagram)
* [Data Consumption for Reporting](#data-consumption-for-reporting)
* [Lightweight Local Storage Option: SQLite](#lightweight-local-storage-option-sqlite)
* [Compatible APIs for R & R Shiny](#compatible-apis-for-r--r-shiny)
* [Phased Implementation Plan](#phased-implementation-plan)
  * [Phase 1: Data Integration and ETL Pipeline Setup](#phase-1-data-integration-and-etl-pipeline-setup)
  * [Phase 2: Reporting and Dashboard Development](#phase-2-reporting-and-dashboard-development)
  * [Phase 3: Optimization and Maintenance](#phase-3-optimization-and-maintenance)
* [Estimated Tooling Costs](#estimated-tooling-costs)
* [Tech Stack](#tech-stack)
* [Conclusion](#conclusion)

---

## 💻 Tech Stack

### 🔧 Microsoft Dynamics AX

![Microsoft Dynamics AX](https://img.shields.io/badge/-Dynamics%20AX-0078D4?logo=microsoft)

### 🔧 Microsoft Dynamics 365 Business Central

![Business Central](https://img.shields.io/badge/-Business%20Central-0078D4?logo=microsoft)

### 🔧 Middleware & Cloud Tools

![Azure Data Factory](https://img.shields.io/badge/-ADF-0078D4?logo=azure)
![Azure SQL](https://img.shields.io/badge/-Azure%20SQL-0061A8?logo=microsoft)
![Azure Synapse](https://img.shields.io/badge/-Azure%20Synapse-0078D4?logo=microsoft)
![SQLite](https://img.shields.io/badge/-SQLite-003B57?logo=sqlite)

### 🔧 Analytics & Reporting

![Power BI](https://img.shields.io/badge/-Power%20BI-F2C811?logo=powerbi)
![Excel](https://img.shields.io/badge/-Excel-217346?logo=microsoft-excel)
![R Shiny](https://img.shields.io/badge/-R%20Shiny-276DC3?logo=r)

### 🔧 R Packages

![shiny](https://img.shields.io/badge/-shiny-276DC3)
![httr](https://img.shields.io/badge/-httr-1f77b4)
![jsonlite](https://img.shields.io/badge/-jsonlite-e31a1c)
![dplyr](https://img.shields.io/badge/-dplyr-17becf)
![ggplot2](https://img.shields.io/badge/-ggplot2-1f77b4)
![forecast](https://img.shields.io/badge/-forecast-2ca02c)
![DBI](https://img.shields.io/badge/-DBI-ff7f0e)
![RSQLite](https://img.shields.io/badge/-RSQLite-6a5acd)

---












