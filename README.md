🤩 Aeristo Data Integration Case Study & Phased Implementation Plan
📌 Summary
This document outlines a modern, scalable ETL and data integration strategy for Aeristo, a Dallas-based luxury leather goods company specializing in bespoke interiors for high-end automobiles and private aircraft (e.g., Gulfstream G6, G7). The goal is to integrate legacy data from Microsoft Dynamics AX (pre-2023) with Microsoft Dynamics 365 Business Central (current live data), enabling real-time analytics through Excel, Power BI, and R Shiny applications. The project is structured in three phases—data integration and ETL pipeline setup, reporting and dashboard development, and ongoing optimization—delivering a cloud-native, secure solution as of June 2025.
📚 Table of Contents

Business Problem
Technical Overview
ETL Strategy & Middleware
Integration Workflow Diagram
Data Consumption for Reporting
Compatible APIs for R & R Shiny
Phased Implementation Plan
Estimated Tooling Costs
Tech Stack
Conclusion

🧭 Business Problem
Aeristo relies on two disconnected systems:

Microsoft Dynamics AX: Legacy ERP (pre-2023) storing customer, vendor, sales, and purchasing history (~500K records).
Microsoft Dynamics 365 Business Central: Cloud-based ERP for real-time operations post-2023.

Key Challenge: The lack of integration between these systems limits Aeristo’s ability to combine historical and live data, hindering data-driven decisions for inventory management, sales forecasting, and customer insights critical to their luxury leather operations.
⚙️ Technical Overview
The solution leverages a modern ETL pipeline to:

Extract historical data from Dynamics AX via SQL Server or OData.
Transform it to align with Business Central’s schema.
Load it incrementally to minimize system impact.

API endpoints enable:

R Shiny dashboards for advanced analytics (e.g., demand forecasting).
Power BI and Excel reports for stakeholder accessibility.

The implementation is divided into three phases:

Phase 1: Data Integration and ETL Pipeline Setup (3-4 months).
Phase 2: Reporting and Dashboard Development (2-3 months).
Phase 3: Optimization and Maintenance (ongoing).

🛠️ ETL Strategy & Middleware
🔄 ETL Process (Incremental)
The ETL process unifies historical and live data:

Extract: Retrieve data from Dynamics AX using SQL Server or OData.
Transform: Align schemas, reformat dates, and tag metadata.
Load: Push transformed data to Business Central via APIs or stage in Azure SQL Database.

🧍️‍⚖️ Middleware: Azure Data Factory

Low-code, cloud-native ETL platform with native connectors for Dynamics AX, Business Central, Azure SQL, and Excel.
Supports incremental loads, automated scheduling, and robust logging.
Integrated with Azure Synapse Analytics for advanced data transformation (optional).

✅ Benefits

Cloud-native: Eliminates on-premises infrastructure needs.
Reporting-ready: Compatible with Power BI, Excel Power Query, and R Shiny.
Secure: Governed by Azure Security Center and audit logging.

🛠️ Phase 1: Data Integration and ETL Pipeline Setup
Objective: Build an incremental ETL pipeline to extract, transform, and load Dynamics AX data into Business Central, with a staging layer for validation.
Estimated Timeframe: 3-4 months (12-16 weeks, 20-30 hours/week for a solutions architect).
Tasks Involved:

Assess Data Sources:
Map Dynamics AX schema (e.g., CustTable, VendTable) to Business Central equivalents.
Analyze data volume (~500K records) and quality (e.g., duplicates, missing fields).


Configure Azure Data Factory (ADF):
Set up pipelines with connectors for Dynamics AX (SQL Server/OData) and Business Central (REST API).
Implement incremental load logic based on ModifiedDateTime.


Set Up Azure SQL Staging Layer:
Create a staging database for raw and transformed data.
Apply schema alignment and data cleansing.


Develop ETL Pipeline:
Extract data from AX via SQL or OData.
Transform data (e.g., date formats, field mappings) in ADF or SQL.
Load to Business Central via REST API or Azure SQL.


Test and Validate:
Execute pilot loads (10% of data) to verify integrity.
Monitor errors using ADF diagnostics.


Enable API Access:
Configure OData V4 and REST APIs in Business Central for reporting.



Commands and Code Snippets:

Extract from Dynamics AX (SQL Server):-- Query customer data from AX
SELECT CustAccount, Name, SalesCurrencyCode, ModifiedDateTime
FROM CustTable
WHERE ModifiedDateTime >= '2022-01-01';


Configure ADF Pipeline (JSON):{
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


Enable OData in Business Central:-- Navigate to Web Services in Business Central
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
This GitHub-compatible diagram illustrates how Aeristo’s historical and live data will be connected for enterprise-grade reporting and visibility.
How Components Work:

Dynamics AX: Stores pre-2023 customer, vendor, sales, and purchasing data.
Azure Data Factory: Extracts, transforms, and loads data in small batches.
D365 Business Central: Blends historical and live data for operational insights.
Azure SQL: Stages data for validation and reporting.
Excel/Power BI/R Shiny: Provides real-time analytics for decision-making.

graph LR
    AX["Dynamics AX<br>Historical Data"]:::system -->|"OData/SQL Server"| ADF["Azure Data Factory<br>Incremental ETL"]:::etl
    ADF -->|"REST API"| BC["D365 Business Central<br>Real-Time ERP"]:::system
    ADF -->|"Load Raw & Transformed Data"| SQL["Azure SQL<br>Staging Layer"]:::db
    SQL -->|"OData V4/JSON"| API["Excel/Power BI/R Shiny<br>Reporting & Analytics"]:::reporting
    BC -->|"REST/OData V4"| API

    classDef system fill:#4B8BBE,color:#fff;
    classDef etl fill:#306998,color:#fff;
    classDef db fill:#1E90FF,color:#fff;
    classDef reporting fill:#228B22,color:#fff;


This diagram, using standard Mermaid syntax, visualizes the data integration workflow and renders correctly on GitHub.

📊 Data Consumption for Reporting
📊 How R Shiny Connects and Adds Value
R Shiny delivers interactive dashboards with advanced analytics capabilities, surpassing Power BI in customization for data scientists.
Benefits for Aeristo:

Real-Time Data: Connects to Business Central APIs (REST/OData V4) for live updates.
Interactive Dashboards: Enables filtering of sales, inventory, or vendor trends.
Advanced Analytics: Supports forecasting and segmentation models.
Deployment: Host on Azure or internal servers for secure access.
Cost-Effective: Open-source, no per-user licensing fees.

Excel/Power BI Integration: Power Query and Power BI connect to Azure SQL or Business Central OData feeds for user-friendly reports.
🛠️ Phase 2: Reporting and Dashboard Development
Objective: Develop dashboards and reports using Power BI, Excel, and R Shiny, leveraging unified data from Phase 1.
Estimated Timeframe: 2-3 months (8-12 weeks).
Tasks Involved:

Design Reporting Requirements:
Identify stakeholder needs (e.g., sales trends, inventory levels).
Define KPIs (e.g., leather stock vs. demand forecast).


Build Power BI Dashboards:
Connect to Business Central OData feed.
Visualize inventory, sales, and vendor performance.


Develop Excel Reports:
Use Power Query to pull data from Azure SQL or Business Central.
Automate refresh schedules.


Create R Shiny Dashboards:
Build interactive apps for analytics (e.g., demand forecasting).
Deploy on Azure App Service or shinyapps.io.


Validate Reports:
Confirm data accuracy with stakeholders.
Optimize query performance.



Commands and Code Snippets:

Power BI OData Connection:Source = OData.Feed("https://api.businesscentral.dynamics.com/v2.0/{tenant-id}/ODataV4/Customer")


Excel Power Query (M):let
    Source = OData.Feed("https://api.businesscentral.dynamics.com/v2.0/{tenant-id}/ODataV4/Customer"),
    Customers = Source{[Name="Customers"]}[Data]
in
    Customers


R Shiny Inventory Dashboard:library(shiny)
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







📱 Compatible APIs for R & R Shiny
🔍 What Are OData V4 and REST APIs?

OData V4: A standardized protocol for structured data access, enabling Excel, Power BI, and R to query Business Central live.
REST API: A lightweight, flexible interface for R Shiny to fetch real-time data, ideal for custom analytics.

These APIs ensure dashboards and reports reflect Aeristo’s current business state without manual exports.
🚀 Deployment with Microsoft Dynamics 365
Business Central’s cloud APIs are enabled by default. Deployment steps include:

Enable Web Services:
Navigate to Web Services in Business Central (Search > Web Services).
Activate OData V4 and REST endpoints for tables like Customers.


Expose API/OData Feed:https://api.businesscentral.dynamics.com/v2.0/{tenant-id}/ODataV4/Customer

GET https://api.businesscentral.dynamics.com/v2.0/{tenant-id}/api/v2.0/companies({company-id})/customers
Authorization: Bearer {access_token}


Authenticate with Azure AD:
Register apps (e.g., R Shiny) in Azure Active Directory.
Grant Business Central API permissions.


Query Data in R:library(httr)
library(jsonlite)
url <- "https://api.businesscentral.dynamics.com/v2.0/{tenant-id}/api/v2.0/companies({company-id})/customers"
res <- GET(url, add_headers(Authorization = paste("Bearer", access_token)))
data <- content(res, "parsed")




This setup delivers a secure, scalable analytics pipeline as of June 2025.

🛠️ Phased Implementation Plan
Phase 3: Optimization and Maintenance
Objective: Monitor, optimize, and maintain the ETL pipeline and reporting tools, adding enhancements as needed.
Estimated Timeframe: Ongoing (post-Phase 2, ~1-2 hours/week initially).
Tasks Involved:

Monitor ETL Pipeline:
Review ADF logs for errors or performance bottlenecks.
Adjust load schedules for efficiency.


Optimize Data Queries:
Index Azure SQL tables for faster reporting.
Cache frequent API calls in Business Central.


Enhance Dashboards:
Add KPIs or visuals based on feedback.
Update R Shiny apps with predictive models.


Train Users:
Document processes for Aeristo’s team.
Train on Power BI and Excel usage.


Security and Compliance:
Rotate Azure AD API tokens.
Ensure GDPR/CCPA compliance for customer data.



Commands and Code Snippets:

Monitor ADF Pipeline (Azure CLI):az datafactory pipeline-run show --factory-name "AeristoADF" --resource-group "AeristoRG" --run-id "1234-5678-9012"


Index Azure SQL Table:CREATE NONCLUSTERED INDEX IX_CustomerNo ON Staging(CustomersCustomerNo);


Update R Shiny App (Forecasting):library(forecast)
output$forecastPlot <- renderPlot({
    ts_data <- ts(data()$OrderQty, frequency = 6)
    fit <- auto.arima(ts_data)
    forecast_data <- forecast(fit, h=6)
    plot(forecast_data, main="6-Month Order Forecast")
})



Phase 3 Tech Stack:









Phase 3 R Packages:




💵 Estimated Tooling Costs (Annualized)



Tool / Service
Purpose
Estimated Annual Cost



Azure Data Factory
ETL pipeline orchestration
$1,200 - $3,000


Azure SQL Database
Staging and transformation layer
$600 - $1,500


Azure Synapse Analytics
Advanced analytics (optional)
$2,000 - $5,000 (optional)


D365 Business Central API
OData/REST API access (included in license)
Bundled with license


R Shiny (open-source)
Dashboard development
Free (internal hosting)


Azure App Service
R Shiny hosting (optional)
~$1,200 (optional)


Power BI
Dashboard visualization
Free - $10/user/month


Excel / Power Query
Reporting integration
Bundled with Office license



Costs reflect 2025 Azure pricing tiers for mid-size data volumes. Actual costs depend on usage and refresh frequency.

💻 Tech Stack
🔧 Microsoft Dynamics AX





🔧 Microsoft Dynamics 365 Business Central





🔧 Middleware & Cloud Tools








🔧 Analytics & Reporting







🔧 R Packages










📩 Conclusion
This modernized solution uses Azure Data Factory and Azure Synapse Analytics (optional) perform incremental ETL operations**, integrating Microsoft Dynamics AX with Business Central via Azure SQL staging. The three-phase approach ensures Aeristo unifies historical and live data seamlessly:

Phase 1 delivers an a robust data pipeline, pipeline.
Phase 2 provides actionable insights through Power BI, Excel, and R Shiny dashboards, dashboards.
Phase 3 guarantees long-term scalability and compliance.

The cloud-native architecture is cloud-based, secure, and cost-effective, enabling real-time analytics via Excel, Power BI, and R Shiny through OData V4 and REST APIs. Designed for executives, data engineers, and architects, this plan ensures scalability and alignment with 2025 data modernization standards.


✅ This case study and plan are optimized for GitHub readability and compatibility with modern cloud-native practices.









