🤩 Aeristo Data Integration Case Study & Phased Implementation Plan

📌 Summary
This document presents a cost-effective and scalable ETL and data integration strategy for Aeristo, a luxury leather goods company specializing in bespoke interiors for luxury automobiles and private aircraft (e.g., G6, G7). The objective is to integrate legacy data from Microsoft Dynamics AX (pre-2023) with Microsoft Dynamics 365 Business Central (current live data) and enable real-time analytics via Excel, R, and R Shiny applications. The implementation is structured in three phases—data integration and ETL pipeline setup, reporting and dashboard development, and ongoing optimization—to ensure a seamless, scalable solution.
📚 Table of Contents

Business Problem
Technical Overview
ETL Strategy & Middleware
Integration Workflow Diagram
Data Consumption for Reporting
Compatible APIs for R & RShiny
Phased Implementation Plan
Estimated Tooling Costs
Tech Stack
Conclusion

🧭 Business Problem
Aeristo operates two parallel systems:

Microsoft Dynamics AX: Legacy system used pre-2023 for customer/vendor records, historical sales, and purchasing history.
Microsoft D365 Business Central: Current real-time ERP system, the source of operational truth post-2023.

Key Challenge: These systems do not communicate, limiting data-driven decision-making and report creation, which hinders Aeristo’s ability to leverage historical data for inventory management, sales forecasting, and customer insights in their luxury leather operations.
⚙️ Technical Overview
The proposed solution involves an ETL pipeline to:

Extract historical data from Dynamics AX.
Transform it into a schema-aligned format for Business Central.
Load it incrementally to avoid system overload.

Additionally, the solution exposes API endpoints for:

R and R Shiny dashboards for advanced analytics.
Excel-based reporting for stakeholder accessibility.

The project is divided into three phases:

Phase 1: Data Integration and ETL Pipeline Setup (3-4 months).
Phase 2: Reporting and Dashboard Development (2-3 months).
Phase 3: Optimization and Maintenance (ongoing).

🛠️ ETL Strategy & Middleware
🔄 ETL Process (Incremental)
The ETL process is designed to unify historical and live data efficiently:

Extract: Pull data from Dynamics AX via SQL Server or OData.
Transform: Align schemas, reformat dates, and tag metadata.
Load: Transfer transformed data to Business Central via APIs or an intermediary Azure SQL database.

🧍️‍⚖️ Middleware: Azure Data Factory

Cost-effective, low-code cloud ETL tool.
Native connectors for Dynamics AX, D365 Business Central, Azure SQL, and Excel.
Supports incremental loads, scheduling, and logging.

✅ Benefits

No local infrastructure required.
Compatible with Excel/Power Query for reporting.
Governed via Azure Security & Logging for compliance.

🛠️ Phase 1: Data Integration and ETL Pipeline Setup
Objective: Establish an incremental ETL pipeline to extract historical data from Dynamics AX, transform it, and load it into Business Central, with a staging layer for data validation.
Estimated Timeframe: 3-4 months (12-16 weeks, assuming 20-30 hours/week for a contract solutions architect).
Tasks Involved:

Assess Data Sources:
Map Dynamics AX schema (customer, vendor, sales, purchasing) to Business Central schema.
Identify data volume (e.g., ~500K records) and quality issues (e.g., missing fields, duplicates).


Configure Azure Data Factory (ADF):
Set up ADF pipeline with connectors for Dynamics AX (SQL Server/OData) and Business Central (REST API).
Define incremental load logic (e.g., based on last modified date).


Set Up Azure SQL Staging Layer:
Create a staging database to store raw and transformed data.
Implement schema alignment and data cleansing.


Develop ETL Pipeline:
Extract data from AX via SQL queries or OData.
Transform data (e.g., date formats, field mappings) using ADF or SQL.
Load transformed data into Business Central via REST API or Azure SQL.


Test and Validate:
Run pilot loads (e.g., 10% of data) to verify data integrity.
Log errors using ADF monitoring tools.


Enable API Access:
Configure OData V4 and REST APIs in Business Central for future reporting.



Commands and Code Snippets:

Extract from Dynamics AX (SQL Server):-- Query customer data from AX
SELECT CustAccount, Name, SalesCurrencyCode, ModifiedDateTime
FROM CustTable
WHERE ModifiedDateTime >= '2022-01-01';


Configure ADF Pipeline (JSON Definition):{
  "name": "AX_to_BC_Pipeline",
  "properties": {
    "activities": [
      {
        "name": "CopyAXtoAzureSQL",
        "type": "Copy",
        "inputs": [{ "referenceName": "AX_SQLSource", "type": "DatasetReference" }],
        "outputs": [{ "referenceName": "AzureSQLStaging", "type": "DatasetReference" }],
        "typeProperties": {
          "source": { "type": "SqlServerSource", "query": "SELECT * FROM CustTable" },
          "sink": { "type": "SqlSink" }
        }
      },
      {
        "name": "TransformData",
        "type": "DataFlow",
        "typeProperties": {
          "sources": [{ "dataset": { "referenceName": "AzureSQLStaging" } }],
          "sinks": [{ "dataset": { "referenceName": "AzureSQLTransformed" } }],
          "transformations": [
            { "name": "MapFields", "type": "Select", "mapping": { "CustAccount": "CustomerNo", "Name": "CustomerName" } }
          ]
        }
      },
      {
        "name": "LoadToBC",
        "type": "Copy",
        "inputs": [{ "referenceName": "AzureSQLTransformed", "type": "DatasetReference" }],
        "outputs": [{ "referenceName": "BC_RESTSink", "type": "DatasetReference" }],
        "typeProperties": {
          "source": { "type": "SqlSource" },
          "sink": {
            "type": "RestSink",
            "requestMethod": "POST",
            "httpRequest": {
              "url": "https://api.businesscentral.dynamics.com/v2.0/{tenant-id}/api/v2.0/companies({company-id})/customers",
              "headers": { "Authorization": "Bearer {access_token}" }
            }
          }
        }
      }
    ]
  }
}


Create Azure SQL Staging Table:CREATE TABLE Staging_Customers (
    CustomerNo NVARCHAR(50),
    CustomerName NVARCHAR(100),
    SalesCurrencyCode NVARCHAR(10),
    ModifiedDateTime DATETIME,
    LoadDate DATETIME DEFAULT GETDATE()
);


Enable OData in Business Central (via UI or AL code):-- Navigate to Web Services in Business Central
-- Publish OData endpoint: /ODataV4/Customer


Test API Access in R:library(httr)
library(jsonlite)
url <- "https://api.businesscentral.dynamics.com/v2.0/{tenant-id}/sandbox/ODataV4/Customer"
res <- GET(url, add_headers(Authorization = paste("Bearer", access_token)))
data <- content(res, "parsed")



Phase 1 Tech Stack:
Phase 1 R Packages:
🧬 Integration Workflow Diagram
🔍 Executive Overview
This diagram illustrates how Aeristo’s historical and live business data will be seamlessly connected, enabling enterprise-grade reporting and data visibility across departments.
How Each Component Works:

Dynamics AX (Historical Data): Stores pre-2023 customer, vendor, sales, and purchasing history—valuable data that cannot be discarded.
Azure Data Factory: Acts as the data pipeline, extracting data from AX, reformatting it, and loading it in small batches to avoid operational disruptions.
D365 Business Central (Live ERP): The core system for daily operations, where historical data is blended with current data for complete visibility.
Azure SQL (Staging Layer): A temporary holding area for testing and cleaning data before it reaches Business Central.
Excel / R API Reporting: Enables real-time access to unified data for decision-making, forecasting, and dashboard creation.

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


This diagram visualizes the incremental data integration workflow across Dynamics AX, Azure Data Factory, Business Central, Azure SQL, and API-driven reporting layers.

📊 Data Consumption for Reporting
📊 How R Shiny Connects and Adds Value
R Shiny enables data analysts and scientists to build interactive dashboards and visual reports, offering greater customization and statistical depth than tools like Tableau or PowerBI.
Benefits for Aeristo:

Live Data Access: Connects directly to Business Central’s APIs (REST or OData V4) for real-time data without manual exports.
Interactive Dashboards: Allows users to filter and explore trends (e.g., sales, supply chain, vendor spend).
Custom Analytics: Supports advanced models like demand forecasting and customer segmentation.
Deployment Flexibility: Apps can be hosted on Aeristo’s intranet or Azure, accessible to executives and production leads.
Cost-Effective: Open-source, avoiding per-seat licensing costs.

Excel Integration: Power Query connects to Azure SQL or Business Central OData feeds for stakeholder-friendly reports.
🛠️ Phase 2: Reporting and Dashboard Development
Objective: Develop interactive dashboards and reports using PowerBI, Excel, and R Shiny, leveraging the unified data from Phase 1.
Estimated Timeframe: 2-3 months (8-12 weeks).
Tasks Involved:

Design Reporting Requirements:
Gather stakeholder needs (e.g., sales trends, inventory levels).
Define KPIs (e.g., hides available vs. demand forecast).


Build PowerBI Dashboards:
Connect PowerBI to Business Central OData feed.
Create visuals for inventory, sales, and vendor performance.


Develop Excel Reports:
Use Power Query to pull data from Azure SQL or Business Central.
Automate refresh schedules.


Create R Shiny Dashboards:
Build interactive apps for advanced analytics (e.g., demand forecasting).
Deploy on internal server or shinyapps.io.


Validate Reports:
Ensure data accuracy with stakeholders.
Optimize query performance.



Commands and Code Snippets:

PowerBI OData Connection (via PowerBI Desktop):Source = OData.Feed("https://api.businesscentral.dynamics.com/v2.0/{tenant-id}/ODataV4/Customer")


Excel Power Query (M Query):let
    Source = OData.Feed("https://api.businesscentral.dynamics.com/v2.0/{tenant-id}/ODataV4/Customer"),
    Customers = Source{[Name="Customers"]}[Data]
in
    Customers


R Shiny App for Inventory Dashboard:library(shiny)
library(httr)
library(jsonlite)
library(dplyr)
library(ggplot2)

# Mock Business Central data
get_bc_data <- function() {
    data <- data.frame(
        CustomerNo = c("C001", "C002", "C003"),
        LeatherType = c("Black Calf", "Navy Suede", "Red Aniline"),
        OrderQty = c(100, 150, 80),
        DeliveryDate = as.Date(c("2025-07-20", "2025-08-01", "2025-08-15"))
    )
    return(data)
}

# UI
ui <- fluidPage(
    titlePanel("Aeristo Inventory Dashboard"),
    sidebarLayout(
        sidebarPanel(
            selectInput("leatherType", "Select Leather Type:", choices = c("All", "Black Calf", "Navy Suede", "Red Aniline"))
        ),
        mainPanel(
            plotOutput("inventoryPlot")
        )
    )
)

# Server
server <- function(input, output) {
    data <- reactive({
        df <- get_bc_data()
        if (input$leatherType != "All") {
            df <- filter(df, LeatherType == input$leatherType)
        }
        df
    })

    output$inventoryPlot <- renderPlot({
        ggplot(data(), aes(x = DeliveryDate, y = OrderQty, fill = LeatherType)) +
            geom_bar(stat = "identity") +
            theme_minimal() +
            labs(title = "Leather Order Quantities")
    })
}

# Run app
shinyApp(ui, server)



Phase 2 Tech Stack:
Phase 2 R Packages:
📱 Compatible APIs for R & RShiny
🔍 What Are OData V4 and REST APIs — and Why They Matter
To enable real-time reporting, we leverage OData V4 and REST APIs:

OData V4: A structured protocol allowing Excel, R, or PowerBI to access Business Central data live, like a secure pass to the ERP system.
REST API: A lightweight, flexible method for R Shiny to retrieve specific data in real time, ideal for custom analytics.

These APIs eliminate manual data exports, ensuring reports and dashboards reflect the current state of Aeristo’s business.
🚀 Deployment with Microsoft Dynamics 365
Both APIs are enabled by default in Business Central (cloud). The deployment process includes:

Enable Web Services:
Navigate to Web Services in Business Central (Search > Web Services).
Enable OData and SOAP endpoints for relevant tables or queries.
Use standard or custom API pages for REST.


Expose API/OData Feed:https://api.businesscentral.dynamics.com/v2.0/{tenant-id}/sandbox/ODataV4/Customer

GET https://api.businesscentral.dynamics.com/v2.0/{tenant-id}/sandbox/api/v2.0/companies({company-id})/customers
Authorization: Bearer <access_token>


Authenticate Using Azure AD:
Register R Shiny apps or Excel workbooks as Azure Active Directory (AAD) applications.
Grant permissions to access Business Central APIs.


Query Data from R:library(httr)
library(jsonlite)
url <- "https://api.businesscentral.dynamics.com/v2.0/{tenant-id}/sandbox/api/v2.0/companies({company-id})/customers"
res <- GET(url, add_headers(Authorization = paste("Bearer", access_token)))
data <- content(res, "parsed")




This setup ensures a modern, secure, and scalable pipeline for analytics without disrupting operations.

🛠️ Phased Implementation Plan
Phase 3: Optimization and Maintenance
Objective: Monitor, optimize, and maintain the ETL pipeline and reporting tools, adding enhancements as needed.
Estimated Timeframe: Ongoing (post-Phase 2, ~1-2 hours/week initially).
Tasks Involved:

Monitor ETL Pipeline:
Check ADF logs for errors or performance issues.
Adjust incremental load schedules if needed.


Optimize Data Queries:
Index Azure SQL tables for faster reporting.
Cache frequent API calls in Business Central.


Enhance Dashboards:
Add new KPIs or visuals based on stakeholder feedback.
Update R Shiny apps with features like predictive models.


Train Users:
Document processes for Aeristo’s team.
Conduct training on PowerBI and Excel reports.


Security and Compliance:
Rotate API tokens in Azure AD.
Ensure GDPR/CCPA compliance for customer data.



Commands and Code Snippets:

Monitor ADF Pipeline (Azure CLI):az datafactory pipeline-run show --factory-name "AeristoADF" --resource-group "AeristoRG" --run-id "1234-5678-9012"


Index Azure SQL Table:CREATE NONCLUSTERED INDEX IX_CustomerNo ON Staging_Customers (CustomerNo);


Update R Shiny App (Add Forecasting):library(forecast)
# Add forecasting to Shiny server
output$forecastPlot <- renderPlot({
    ts_data <- ts(data()$OrderQty, frequency = 12)
    fit <- auto.arima(ts_data)
    forecast_data <- forecast(fit, h = 6)
    plot(forecast_data, main = "6-Month Order Forecast")
})



Phase 3 Tech Stack:
Phase 3 R Packages:
💵 Estimated Tooling Costs (Annualized)



Tool / Service
Purpose
Estimated Annual Cost



Azure Data Factory
ETL pipeline for AX → BC integration
$1,200 - $3,000


Azure SQL (Staging Layer)
Temporary storage and transformation layer
$600 - $1,500


Microsoft Dynamics 365 BC API
OData/REST API access (included in D365 license)
Bundled with license


RStudio + R Shiny (open-source)
Dashboard & analytics engine
Free (hosted internally)


Optional: R Shiny Server Pro
Managed R Shiny deployment (for IT support)
~$10,000 (optional enterprise)


Excel / Power Query
Excel integration via OData
Bundled with Office license



These estimates are based on average Azure pricing tiers and assume a mid-size data volume. Final costs may vary based on refresh frequency and storage footprint.

💻 Tech Stack
🔧 Microsoft Dynamics AX

🔧 Microsoft Dynamics 365 Business Central

🔧 Middleware & Cloud Tools

🔧 Analytics & Reporting

🔧 R Packages

📾 Conclusion
This solution uses Azure Data Factory to perform incremental ETL operations from Microsoft Dynamics AX into Business Central, with schema-aligned transformations and staging via Azure SQL. The phased implementation ensures Aeristo unifies historical and live data without disrupting operations. Phases 1 and 2 deliver a robust data pipeline and actionable reporting tools, while Phase 3 ensures long-term scalability and reliability. The architecture is cloud-based, secure, and cost-effective, enabling real-time reporting via Excel, PowerBI, and R Shiny through OData V4 and REST APIs.

✅ This case study and plan are designed for executive decision-makers, senior data engineers, and software architects, ensuring enterprise scalability and compatibility with modern data science stacks.







