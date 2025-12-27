<h1> Azure Data Pipeline: ADLS -> ADF Data Flow -> Azure SQL</h1>

<h2>Architecture Diagram</h2>
* ADLS - Source Parquet folder <br>
* Azure Data Factory<br>
* Azure SQL Database (Serverless) <br>
* Event-based triggers (Event Grid) <br>
* Mapping Data Flows

<h2>ADLS Folder structure</h2>

```
report-etl/
   ├── SourceParquet/
   └── DestinationCSV/
```

<h2>Storage Account configuration</h2>

Storage Account - Overview
![Storage account overview](<Storage Account - Overview.png>)

Storage Account - Networking configuration
![Storage account network configuration](<Storage Account - Networking configuration.png>)

Storage Account - Resource Access settings
![Storage Account - Resource Access settings](<Storage Account - Resource Access settings.png>)

Data lake container in storage account containing source and target directories
![alt text](<nycdatalake container in storage account.jpg>)

Role Assignments
![Storage Account - Role Assignments](<Storage Account - Role Assignments.png>)

Server configuration NYC_TAXI_DB
![SQL - database compute + storage configuration - 1](<SQL - database compute + storage configuration - 1.png>)
![SQL - database compute + storage configuration - 2](<SQL - database compute + storage configuration - 2.png>)

Network settings
![SQL - database server network settings](<SQL - database server network settings.png>)

SQL - database server overview
![SQL - database server overview](<SQL - database server overview.png>)

NYC_TAXI_DATA table
![SQL table - NYC_TAXI_DATA](<SQL table - NYC_TAXI_DATA.png>)

<h2>Azure Data Factory connections and datasets</h2>

<h3>Connections</h3>

Linked service to Azure Data Lake Gen 2.
![Linked service to Azure Data Lake Gen 2.](<ADLS linked service.png>)

Linked service to SQL DB.
![Linked service to SQL DB.](<Database SQL linked service.png>)

<h3>Datasets</h3>

Dataset of source directory containing parquet files
![Dataset of source directory containing .parquet files](<Dataset - SourceParquet.png>)

Dataset of destination directory to store data as CSV files.
![Dataset of destination directory to store data as CSV files](<Dataset - ProcessedCSV.png>)

Dataset of destination SQL table
![Dataset of destination SQL table](<Dataset - NYC_TAXI_DATA table.png>)

<h3>Data flow in ETL_Daily_Data_Refresh_NYC pipeline</h3>

#### ETL pipeline

Complete pipeline
![Data pipeline](<ADF pipeline.png>)

#### ADLS to SQL Table data flow

Source
![alt text](<ADF - ADLS to SQL table data flow - Source.png>)
![ADLS to SQL Table data flow](<ADF - ADLS to SQL table data flow - Source options.png>)

Column transformations
1. Transformation 1 - Surcharge Tax
![alt text](<ADF - ADLS to SQL table data flow - Surcharge Tax derived column.png>)

2. Transformation 2 - Final Amount = Total Amount + Surcharge Tax
![alt text](<ADF - ADLS to SQL table data flow - Final Amount.png>)

3. Date Updated column = currentTimestamp()
![alt text](<ADF - ADLS to SQL table data flow - Date Updated.png>)

Sink
![Azure SQL table sink](<ADF - ADLS to SQL table data flow - Sink.png>)

#### Copy data flow
