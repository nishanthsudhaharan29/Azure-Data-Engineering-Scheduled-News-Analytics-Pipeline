# Azure Data Engineering ETL Pipeline – Latest News Analytics

## Overview

This project implements an end-to-end Azure Data Engineering pipeline designed to ingest, process, and store real-time news articles related to geopolitical events in the USA, Iran, and Israel. The pipeline uses Azure Databricks for data orchestration and transformation across bronze, silver, and gold layers, and Azure Data Lake Storage (ADLS) for scalable, cost-effective storage.

By automating ingestion from a public news API and structuring the data using the medallion architecture, this solution enables timely insights, reliable data processing, and support for downstream analytics or reporting use cases. It is designed for analysts, humanitarian agencies, and media teams who require clean and structured data for monitoring evolving global conflicts.

---

## Business Case

Access to real-time, structured news is essential during conflict situations for making rapid, informed decisions. Government organizations, non-profits, and intelligence units rely on updated information to assess risks, prioritize responses, and understand public narratives. This pipeline provides daily news updates for high-priority regions, ensuring automation, consistency, and quality in the data. This project enables fast-action to provide help and support people affected by war-damages across Israel, Iran, and United States.

---

## Architecture Overview
![Logo](screenshots/arcc.png)

This pipeline is based on Azure-native services and follows a modular, scalable architecture:

- **Data Ingestion**: Azure Databricks schedules and orchestrates API calls to retrieve daily news data.
- **Data Processing**: Azure Databricks transforms the raw JSON data into structured datasets using bronze, silver, and gold tiers.
- **Data Storage**: Azure Data Lake Storage (ADLS Gen2) serves as the centralized storage system for all layers.

---

## Medallion Architecture

**Bronze Layer**
- Stores raw JSON responses from the news API.
  
**Silver Layer**
- Filters news articles to only include content related to USA, Iran, and Israel.
- Cleans, standardizes, and deduplicates the data.

**Gold Layer**
- Adds derived fields and performs additional transformations.
- Final, curated dataset ready for analysis or dashboard integration.

---

## API Integration

- https://mediastack.com/documentation
- **Target Countries**: United States, Iran, Israel

![Logo](screenshots/2.png)
![Logo](screenshots/3.png)

---

## Key Features

- Full automation via Databricks Workflows
- Scalable ingestion of real-time data
- Country-level filtering and enrichment
- Modular, maintainable architecture
- Structured data storage for downstream analytics

---

## Setup Instructions

### Step 1: Create a Resource Group
### Step 2: Create Azure Resources
![Logo](screenshots/4.png)
1. **Azure Data Lake Storage (ADLS)**
   - Enable hierarchical namespace.
   - Create three containers: `bronze`, `silver`, `gold`.
![Logo](screenshots/5.png)
2. **Azure Databricks**
   - Create a Databricks workspace using the **Standard LTS** tier.
   - Launch the workspace and create a compute cluster.
   - In Storage Accounts, assign `Storage Blob Data Contributor` role to Databricks' Access Connector.
![Logo](screenshots/6.png)
3. **Access Connector Setup**
   - Go to `External Data > Credentials` in Databricks.
   - Create a credential using the Resource ID of the Access Connector.
   - Define external locations for `bronze`, `silver`, and `gold`.
![Logo](screenshots/7.png)
---

## Notebook Structure

Create the following notebooks in Databricks:

1. **Bronze Notebook**
   - Makes the API call
   - Stores raw data in `bronze` container
![Logo](screenshots/8.png)
2. **Silver Notebook**
   - Reads from `bronze`
   - Filters articles by country, removes duplicates, and cleans fields
![Logo](screenshots/9.png)
3. **Gold Notebook**
   - Reads from `silver`
   - Adds metadata fields (e.g., keywords, timestamps)
   - Saves final output to `gold` container
![Logo](screenshots/10.png)
---

## Workflow Automation

Use **Databricks Workflows** to automate the pipeline:

1. Create a job with three tasks:
   - **Bronze Task**
     - Type: Notebook
     - Cluster: Choose the compute cluster
   - **Silver Task**
     - Depends on: Bronze
     - Parameters:
       ```json
       { "bronze_output": "{{tasks.bronze.values.bronze_output}}" }
       ```
   - **Gold Task**
     - Depends on: Silver
     - Parameters:
       ```json
       {
         "silver_output": "{{tasks.silver.values.silver_output}}",
         "bronze_output": "{{tasks.bronze.values.bronze_output}}"
       }
       ```

2. Add a Scheduled Trigger to run every 12 hours.

![Logo](screenshots/11.png)
![Logo](screenshots/12.png)
---

## Output Structure in ADLS

| Layer   | Pa | Description                  |
|---------|------------------------------------|
| Bronze  | Raw JSON from news API             |
| Silver  |Filtered and cleaned articles      |
| Gold    |Enriched and curated final output  |

![Logo](screenshots/13.png)
![Logo](screenshots/14.png)
![Logo](screenshots/15.png)
---

## Possible Enhancements

- Add sentiment analysis using Azure Cognitive Services
- Integrate with Power BI or Azure Synapse Analytics
- Include real-time alerting for breaking news
- Extend to include RSS feeds or other APIs



---



