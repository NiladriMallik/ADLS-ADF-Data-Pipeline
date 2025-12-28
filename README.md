<h1> Azure Data Pipeline: ADLS -> ADF Data Flow -> Azure SQL</h1>

This is a simple pipeline that is auto-triggered when a new parquet file arrives in "SourceParquet" directory in a data lake container. This data pipeline performs basic data transformations and copies the data into an Azure SQL database inside an Azure SQL Server. After the data is copied to the database, the original parquet file is moved to another "DestinationCSV" directory and stored in CSV format, and the parquet file is deleted 

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
ADF - Copy activity to move Files to DestinationCSV directory
![ADF - Move Files to DestinationCSV directory copy activity](<ADF - Move Files to Processed copy activity.png>)

ADF - Copy activity to move Files to DestinationCSV directory - Source
![ADF - Move Files to DestinationCSV copy activity - Source](<ADF - Move Files to Processed copy activity - Source.png>)

ADF - Copy activity to move Files to DestinationCSV directory - Sink
![ADF - Move Files to DestinationCSV copy activity - Sink](<ADF - Move Files to Processed copy activity - Sink.png>)

File arrival trigger
![ADF - trigger - New parquet file arrival](<ADF - trigger - New parquet file arrival.png>)

New Parquet File Arrival auto triggers the pipeline
![New Parquet File Arrival auto trigger](<ADF - New Parquet File Arrival auto trigger.png>)

Pipeline run status
Pipeline run in progress
![Pipeline run in progress](<ADF - ETL Pipeline monitoring.png>)

The ETL pipeline is completed, data is copied to Azure SQL database. Next, moving the parquet file to DestinationCSV directory in Data Lake container in CSV format.
![ADF - ETL Pipeline success - auto moving file to DestinationCSV directory in ADLS](<ADF - ETL Pipeline success - auto moving file to Processed directory in ADLS.png>)

Copy data activity completed.
![ADF - Move parquet file to DestinationCSV directory in csv format succeeded](<ADF - Move parquet file to processed directory in csv format succeeded.png>)


Parquet file moved to Processed directory in data lake.
![Azure Data Lake storage - Parquet file copied from source directory to csv format in DestinationCSV directory](<Azure Data Lake storage - Parquet file copied from source directory to csv format in processed directory.png>)