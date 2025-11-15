# LakeToWarehouse – AWS S3 → Glue → Redshift Pipeline  
End-to-end AWS data pipeline that ingests raw data from Amazon S3, catalogs it using AWS Glue Crawler, queries with Amazon Athena, transforms through AWS Glue ETL jobs, and loads the processed data into Amazon Redshift for analytics and reporting.

## Table of Contents  
- [Overview](#overview)  
- [Architecture](#architecture)  
- [Features](#features)  
- [Prerequisites](#prerequisites)  
- [Setup & Deployment](#setup-deployment)  
  - [1. S3 Source Data](#1-s3-source-data)  
  - [2. Glue Crawler & Catalog](#2-glue-crawler-and-catalog)  
  - [3. Athena Query Layer](#3-athena-query-layer)  
  - [4. Glue ETL Job](#4-glue-etl-job)  
  - [5. Redshift Data Warehouse](#5-redshift-data-warehouse)  
- [Usage](#usage)  
- [Directory Structure](#directory-structure)  
- [Data Model](#data-model)  
- [Contributing](#contributing)  
- [License](#license)  

## Overview  
This project demonstrates a production-style data pipeline on AWS:  
- Raw data lands in an S3 bucket.  
- A Glue Crawler crawls that data and builds a Glue Data Catalog.  
- You optionally query the raw data via Athena.  
- A Glue ETL job transforms/cleans/enriches the data.  
- The transformed data is loaded into a Redshift cluster for analytics and reporting.  
- This architecture supports scalable, serverless (or near-serverless) data ingestion, transformation and warehousing.

## Architecture  
![Image](https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2020/01/23/S3SpendwithGlueRedshift2.png)

**Key components:**  
- **Amazon S3** – Source raw data lake.  
- **AWS Glue Crawler** – Detects schema, builds Data Catalog.  
- **AWS Glue ETL** – A PySpark job (or similar) to transform data.  
- **Amazon Athena** – (Optional) Query raw or curated data in S3.  
- **Amazon Redshift** – Data warehouse for analytics, BI and reporting.  
- **Glue Data Catalog** – Metadata store used by both ETL and Athena.

## Features  
- Fully automated pipeline from raw ingestion to analytics-ready tables.  
- Modular architecture: separate raw, curated, warehouse layers.  
- Minimal manual intervention: once set up, new data ingestion is seamless.  
- Scalable using AWS managed services.  
- Clean separation of concerns: ingestion, cataloging, transformation, warehousing.  
- Example notebooks included for Redshift connection and ETL logic.

## Prerequisites  
Before you begin, ensure you have:  
- An AWS account with permissions for S3, Glue, Athena, Redshift, IAM.  
- AWS CLI or AWS Console access.  
- Input data (for example CSV/Parquet) to ingest into S3.  
- (Optional) Basic knowledge of PySpark/Glue scripts if you’ll modify the ETL.  
- (Optional) A BI tool connected to Redshift for reporting.

## Setup & Deployment  

### 1. S3 Source Data  
- Create an S3 bucket (e.g., `my-data-lake-raw`).  
- Upload your raw dataset into a folder (for example `s3://my-data-lake-raw/source/`).  
- Ensure proper folder structure, naming conventions and file formats (CSV, Parquet, JSON) as expected.

### 2. Glue Crawler and Data Catalog  
- Create a Glue Crawler that points to the S3 bucket/folder you created.  
- Configure the crawler with a database name in Glue Data Catalog (e.g., `raw_data_db`).  
- Run the crawler so it builds table definitions.  
- Confirm in Glue Console that the tables exist with correct schema.

### 3. Athena Query Layer  
- In AWS Athena, set the Query Result location (e.g., `s3://my-data-lake-raw/athena_results/`).  
- Run sample SQL queries on the raw tables (e.g., `SELECT * FROM raw_data_db.table LIMIT 10;`).  
- Use this layer for ad-hoc exploration and validation.

### 4. Glue ETL Job  
- Locate the notebook (e.g., `ETL-Code.ipynb`) in this repository.  
- Modify as necessary: input source table(s), transformation logic, output table or S3/Redshift target.  
- Configure a Glue Job: assign IAM role, set Worker type, N.  
- In the job, read from Glue catalog (raw), apply transformations, write to Redshift or curated S3 bucket.  
- Example code includes connection setup, schema enforcement, partitioning etc.

### 5. Redshift Data Warehouse  
- Provision a Redshift cluster (e.g., `dc2.large`, with VPC, subnet group, security group).  
- Create a database schema (e.g., `analytics_db`) and database user.  
- Create tables matching your transformed data model (see [Data Model](#data-model)).  
- Connect the Glue job to Redshift: supply JDBC URL, user, password, and S3 temporary bucket for COPY if using UNLOAD/IMPORT.  
- After job runs, verify that tables are populated with expected rows.  
- Connect a BI tool (e.g., Tableau, QuickSight, Power BI) to Redshift for analysis.

## Usage  
1. Drop new raw files into the S3 source folder (maintaining naming conventions).  
2. Run the Glue Crawler (or schedule it via AWS Glue Trigger).  
3. Run the Athena queries (if desired) to validate schema and data.  
4. Trigger the Glue ETL job (manually or via trigger).  
5. Verify the data in Redshift.  
6. Use a BI tool to build dashboards, reports, or further analytics on Redshift tables.

## Directory Structure  
```text
/  
├─ Data/  
│   ├─ (raw sample files)  
│   ├─ Dimenstion.drawio.pdf  
│   ├─ Model.drawio.png  
├─ ETL-Code.ipynb        # Notebook showing ETL logic  
├─ redshift-connection.ipynb  # Notebook to test Redshift connectivity  
├─ README.md  
