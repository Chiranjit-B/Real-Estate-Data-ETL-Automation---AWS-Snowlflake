# 📊 Real Estate Data Pipeline Project with AWS, Snowflake, and Power BI 📈


![AWS Badge](https://img.shields.io/badge/Service-AWS-FF9900)
![ETL Automation Badge](https://img.shields.io/badge/Process-ETL%20Automation-purple)
![Data Engineering Badge](https://img.shields.io/badge/Role-Data%20Engineering-green)
![Cloud Computing Badge](https://img.shields.io/badge/Domain-Cloud%20Computing-blue)
![Apache Airflow Badge](https://img.shields.io/badge/Tool-Apache%20Airflow-lightblue)
![AWS S3 Bucket Badge](https://img.shields.io/badge/Storage-AWS%20S3%20Bucket-darkgreen)

![image](https://github.com/user-attachments/assets/da1665fa-80e3-4841-a409-03ccfe26f04e)

Welcome to an interactive look at a powerful, cloud-native data pipeline for real estate analytics! This article walks through how we built an end-to-end pipeline using **AWS**, **Snowflake**, and **Power BI**, enabling near-real-time insights into real estate trends from **Redfin data**. Leveraging **Apache Airflow** for orchestration and **Snowpipe** for automated data loading, this project showcases modern data engineering practices.


---

## 🌟 Project Overview

In today’s data-driven world, real estate professionals need fast, reliable insights to stay competitive. This project solves that challenge by transforming raw real estate data into actionable insights. Using a combination of **AWS services**, automated workflows, and visualization tools, we can process Redfin data, standardize it, and make it accessible for real-time decision-making. 

**Key Objectives:**
1. Extract data from Redfin and load it into AWS S3.
2. Transform raw data for analytic readiness.
3. Automate data ingestion into Snowflake using Snowpipe.
4. Visualize insights in Power BI for intuitive decision-making.

---

## 🏗️ Architecture & Workflow

Below is a high-level overview of our pipeline:

### 1. **Data Extraction from Redfin** 🏠
   - Data is sourced from Redfin’s market tracker, accessible [here](https://redfin-public-data.s3.us-west-2.amazonaws.com/redfin_market_tracker/city_market_tracker.tsv000.gz).
   - A custom Python script fetches data from this source, pulling metrics like property prices, inventory levels, and sales trends, all staged for processing.

### 2. **Loading Raw Data to AWS S3** ☁️
   - We store the extracted data in an **AWS S3** bucket titled "Raw Data," where it remains in its original form for easy reference.
   - By centralizing this in AWS, we create a scalable and reliable storage point.

### 3. **Data Transformation** 🔄
   - Using a Jupyter notebook, we process and clean data, managing inconsistencies, filling null values, and standardizing formats.
   - **Apache Airflow** orchestrates this transformation, ensuring the data is ready for analytics. 

### 4. **Automated Data Loading into Snowflake with Snowpipe** ❄️

**Why Snowflake and Snowpipe?**  
Snowflake is known for its scalable, high-performance data warehousing, making it a great choice for this project. By storing data in Snowflake, we benefit from:
   - **Elastic Scalability**: Snowflake scales resources up or down based on load, making it ideal for handling varying data volumes.
   - **Separation of Storage and Compute**: This enables efficient resource allocation, improving both performance and cost-efficiency.
   - **Integrated Data Sharing and Secure Access**: Snowflake allows easy data sharing while maintaining strong data security.

**Advantages of Snowpipe**  
Snowpipe is Snowflake’s managed data ingestion service, automating the loading of files from AWS S3 to Snowflake as they arrive. Key advantages include:
   - **Real-Time Data Loading**: New data is ingested as soon as it arrives in S3, maintaining real-time data availability.
   - **Serverless**: Snowpipe operates as a managed service, reducing infrastructure maintenance.
   - **Easy to Set Up**: With just a few configurations, Snowpipe connects seamlessly with S3, simplifying data workflows.

**How Snowpipe and Triggers Helped**  
In this project, **Snowpipe** and its **automated trigger** keep our data pipeline continuously up-to-date. The trigger, configured as an event notification on the S3 bucket, activates Snowpipe to ingest any new or updated files in the Transformed Data bucket, ensuring that Snowflake always has the latest data.

### Trigger Mechanism  
- **Trigger Configuration**: An S3 event notification is set up to detect new data in the "Transformed Data" bucket. When a new file arrives, it sends an event to Snowpipe, which then ingests the file into Snowflake.
- **Immediate Ingestion**: As soon as data is available, it is automatically loaded into Snowflake, eliminating delays in data availability.
- **Hands-Off Automation**: With Snowpipe and triggers, the ingestion process is fully automated, saving time and reducing the need for manual monitoring.

### 5. **Data Storage & Visualization** 🗄️📊
   - Once in Snowflake, data is available for analytic queries. This structured data is segmented by city, state, price, and more.
   - **Power BI** connects to Snowflake, providing dynamic and visually rich dashboards that reveal real estate trends and market patterns.

---

## 🔧 Key Technologies & Tools

To achieve this seamless, automated pipeline, we used a combination of powerful tools. Here’s a breakdown of each component:

### AWS S3
Our raw data resides in AWS S3, which ensures data reliability, scalability, and security for both storage and retrieval. It’s also Snowpipe-compatible, making it a natural choice for this project.

### Apache Airflow
Airflow orchestrates ETL (Extract, Transform, Load) processes, providing a workflow for efficient data handling. It schedules and manages the data transformation notebook, making sure data flows smoothly through each pipeline stage.

### Snowflake & Snowpipe
Snowflake serves as our data warehouse, and **Snowpipe** automates data ingestion. When transformed data arrives in S3, Snowpipe instantly loads it into Snowflake. 

Here’s the code snippet that sets up Snowpipe:

```sql
CREATE OR REPLACE PIPE my_snowpipe
AUTO_INGEST = TRUE
AS
COPY INTO my_table
FROM @my_s3_stage
FILE_FORMAT = (TYPE = 'CSV');
```

With this, Snowpipe automates data loading, ensuring Snowflake remains up-to-date.

### Power BI
Using **Power BI** dashboards, users can visually explore data with various filters and visualizations, diving into metrics like median sale price, property type, and inventory by region.

---

## 🔄 End-to-End Workflow in Detail

### 1. **Data Extraction** 🏠
The pipeline begins with our data extraction script. Using Python’s `requests` and `pandas`, this script downloads and processes Redfin data, formatting it as a CSV for S3 storage.

### 2. **Environment Setup** 🖥️
We configured a virtual environment, ensuring compatibility with Python 3.10. Packages like `boto3` (for AWS integration) and `apache-airflow` are installed, preparing the system for the full pipeline.

```bash
# Setting up environment
sudo apt update
sudo apt install python3-pip python3.10-venv
python3 -m venv redfin_venv
source redfin_venv/bin/activate
pip install pandas boto3 apache-airflow
```

### 3. **Data Loading to S3** ☁️
Once transformed, data is uploaded to an AWS S3 bucket. This serves as the central point for all stages that follow.

### 4. **Transformation and Automation with Airflow** 🔄
Airflow DAGs automate the transformation phase, handling data cleaning and consistency checks. Here’s a sample Airflow DAG that schedules the transformation notebook:

```python
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from datetime import datetime

def transform_data():
    # Code to clean and transform data

dag = DAG('data_transformation', start_date=datetime(2023, 1, 1))

transform_task = PythonOperator(
    task_id='transform_data',
    python_callable=transform_data,
    dag=dag
)
```

### 5. **Automated Ingestion with Snowpipe** ❄️
Snowpipe watches for new files in the "Transformed Data" S3 bucket, loading them directly into Snowflake as they arrive. The trigger configuration below ensures Snowpipe starts automatically whenever new data arrives:

```sql
CREATE NOTIFICATION INTEGRATION my_s3_notification
    TYPE = QUEUE
    ENABLED = TRUE
    NOTIFY_HOST = '<s3_bucket>';

CREATE PIPE my_pipe
    AUTO_INGEST = TRUE
    AS COPY INTO my_table FROM @my_s3_stage FILE_FORMAT = (TYPE = 'CSV');
```

### 6. **Real-Time Insights in Power BI** 📊
Finally, with data centralized in Snowflake, Power BI dashboards visualize trends, using filters for various metrics like state, region type, and property type.

---

## 📊 Business Value

This pipeline empowers stakeholders to make timely, data-driven decisions. Key business benefits include:

1. **Real-Time Trend Monitoring**: With Snowpipe, Snowflake, and Power BI, teams have immediate access to real estate trends, updated in real time.
2. **Efficient Data Processing**: With Airflow’s automated ETL capabilities, data transformation is fast and efficient, reducing manual effort.
3. **Insightful Visualizations**: Power BI turns raw data into digestible visuals, making it easy for users to spot patterns and make strategic choices.

---

## 🔒 Security & Best Practices

- **Environment Variables**: Use environment variables for sensitive details, like AWS and Snowflake credentials.
- **Access Controls**: Implement strict access controls for data in Snowflake and S3 to ensure data governance.
- **Regular Monitoring**: Check pipeline components regularly, especially Snowpipe triggers, to maintain reliable data flow.

---

## 🎉 Conclusion

This project showcases a modern approach to data engineering, combining AWS, Airflow, Snowflake, and Power BI to provide valuable real estate insights. With automation and cloud-native tools, it’s
