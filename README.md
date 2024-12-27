# NYC Taxi Data Engineering Project | Modern Data Engineering Azure Project

## Introduction
This project leverages modern data engineering practices on Microsoft Azure to analyze NYC Taxi data. Key tools include Azure Data Lake, Databricks for ETL, Delta Lake for data warehousing, and Power BI for visualization. The project aims to build a scalable and efficient data pipeline for processing and analyzing taxi trip records.

## Architecture
![image](https://github.com/user-attachments/assets/0a8f8475-8456-4488-af67-3d274dff7270)

This architecture diagram illustrates a data engineering pipeline designed for processing and analyzing NYC Taxi data using Azure services, Databricks, and Power BI. Here's the breakdown of the components and workflow:

### **1. Source**
- The data originates from a **Taxi data source**, potentially a live API, structured files, or a public dataset.
### **2. Ingestion (Azure Data Factory)**
- **Azure Data Factory (ADF)** serves as the ingestion layer, fetching data from the source.
- It dynamically orchestrates and transfers raw data into the **Bronze Layer** of the **Data Lake Gen2** storage.
- **Bronze Layer** stores the data in its raw format, typically in **Parquet files**, ensuring all ingested data is preserved.
### **3. Transformation (Databricks)**
- **Azure Databricks** is used for data transformation. This process includes:
  - Cleaning, validating, and enriching raw data stored in the Bronze Layer.
  - Using **PySpark** for complex transformations and business logic.
- **Silver Layer** (Transformed Data):
  - The cleaned and structured data is written to this layer in **Parquet format**.
  - This layer serves as an intermediate step, focusing on usability and quality.
- **Gold Layer** (Serving Data):
  - The final processed data is written in Delta Tables in the **Data Lake Gen2**.
  - The **Delta Lake** enables advanced features such as:
    - **Data versioning**: Track historical changes.
    - **Time travel**: Query historical states of the data.
    - **Tombstoning**: Handle deleted records and retention policies.

### **4. Security**
- The architecture incorporates robust security measures:
  - **Azure Active Directory (AAD)**: Manages authentication and access control for the services.
  - **Service Principal**: Enables secure access for reading and writing data.
  - **Key Vault**: Protects sensitive configuration keys and credentials.

### **5. Reporting (Power BI)**
- **Power BI** connects to the Gold Layer (Delta Tables) for advanced analytics and visualization.
- Dashboards provide insights such as trip patterns, revenue analysis, and other metrics derived from the taxi data.


## Technology Used
1. **Programming Language**: Python, SQL
2. **Microsoft Azure Services**:
   - Azure Data Lake Storage (Gen2)
   - Azure Databricks
   - Azure Data Factory
   - Power BI
   - Azure Delta Lake
3. **Tools and Libraries**:
   - PySpark
   - Delta Lake

## Dataset Used
**NYC TLC Trip Record Data**  
The dataset includes yellow and green taxi trip records with fields for pick-up/drop-off dates and times, locations, trip distances, itemized fares, rate types, payment types, and passenger counts.

- **Dataset Source**: [NYC TLC Trip Record Data](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page)  


## Data Model
A relational model encompassing multiple tables:
- **Trip Data Table**: Contains trip records (e.g., trip ID, pick-up/drop-off times, locations).
- **Zone Data Table**: Maps location IDs to geographic zones.
- **Trip Type Table**: Includes Trip details

## ETL Pipeline
1. **Extract**: Raw data ingested from Azure Data Lake.
2. **Transform**: Data processed using Azure Databricks with PySpark for cleaning, joining, and transformations.
3. **Load**: Data stored in Delta Lake format for efficient querying and analysis.

## Visualization
Power BI dashboards provide insights into data

## Challenges Faced  
During the data ingestion process, handling data specific to **(10th month)** required implementing specialized logic to ensure accurate processing and loading into the pipeline.

---

## Solution Implemented  
To address this, the following approach was adopted:

1. **Conditional Logic Using If Activity**:
   - Integrated an **If Condition Activity** in Azure Data Factory (ADF) to evaluate whether the incoming data corresponds to the **10th month**.
   - The condition dynamically checks metadata or a timestamp column from the source data.

2. **True and False Paths with Copy Activities**:
   - **True Path**:
     - If the condition is evaluated as true, the pipeline triggers a **Copy Activity** to ingest data into the **Bronze Layer**.
     - Additional validations and transformations were applied as needed.
   - **False Path**:
     - If the condition is false, the pipeline skips or redirects the data to another process, ensuring irrelevant data is excluded for the 10th-specific requirement.
![Screenshot 2024-12-26 162706](https://github.com/user-attachments/assets/a32124d8-03e1-4321-9035-4397d22c5080)

## Benefits of the Solution  
- **Dynamic Handling**: The pipeline dynamically processes month-specific data without requiring manual intervention.  
- **Efficiency**: Automated validation minimized errors during ingestion for the specified month.  
- ![Screenshot 2024-12-26 162706](https://github.com/user-attachments/assets/b030d195-98a6-4f01-b365-d7d6d0ffb05f)

- ## Challenge Faced: Integration Issue Between Azure Data Lake Storage and Databricks

The error `[SQL_CONF_NOT_FOUND] The SQL config "fs.azure.account.key.nyctaxistrgeac.dfs.core.windows.net" cannot be found` occurred during the integration of Azure Data Lake Storage (ADLS Gen2) with Databricks. This issue impacted the ability to access data stored in ADLS for further transformations and reporting in Power BI. The problem is specifically related to missing or misconfigured authentication credentials required to connect Databricks to ADLS.

---

### Root Cause
1. **Missing Configuration**: 
   - The configuration for `fs.azure.account.key.nyctaxistrgeac.dfs.core.windows.net` was not properly set in the Databricks Spark session.
   - Without this key, Databricks cannot authenticate and access the Azure Data Lake Storage account.

2. **Impact on Data Flow**:
   - This error halted the data ingestion and transformation processes.
   - Consequently, it prevented the pipeline from loading transformed data into the **Gold Layer** (Delta Tables), which is essential for generating reports in Power BI.

3. **Power BI Dependency**:
   - Power BI relies on the Delta Tables in the **Gold Layer** for real-time and historical data insights.
   - Without access to the data, Power BI reporting was disrupted.

---

### Solution Implemented

1. **Setting Up the Correct Configuration**:
   - Verified that the **Azure Data Lake Storage (ADLS) account key** is valid and properly configured in the Databricks environment.
   ```pythON code
   spark.conf.set("fs.azure.account.key.nyctaxistrgeac.dfs.core.windows.net", "<YOUR_STORAGE_ACCOUNT_KEY>")

  
  






