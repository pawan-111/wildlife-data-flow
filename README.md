🐘 Wildlife Conservation — End-to-End Data Engineering Pipeline
📌 Project Overview
This project implements a production-style Lakehouse data pipeline using AWS S3 and Databricks (Delta Lake) to process wildlife detection data.
The pipeline follows the Medallion Architecture (Staging → Bronze → Silver → Gold) and supports fully incremental data processing using Delta MERGE and partition-based updates.
The system enables seasonal wildlife activity analysis, zone-wise monitoring, and human intrusion tracking.
🏗 Architecture
AWS S3 (Raw CSV Files)
        ↓
Staging Layer (Temporary Batch Load)
        ↓
Bronze Layer (Raw Delta Storage)
        ↓
Silver Layer (Cleaned & Partitioned Data)
        ↓
Gold Layer (Aggregated Fact Table)
        ↓
Dashboard / SQL Analytics
📂 Project Structure
wildlife-data-engineering/
│
├── data_generator/
│   └── generate_wildlife_dataset.py
│
├── notebooks/
│   ├── 01_staging_ingestion
│   ├── 02_bronze_merge
│   ├── 03_silver_transformation
│   ├── 04_gold_incremental_update
│
├── sql_queries/
│   └── dashboard_queries.sql
│
└── README.md
📊 Dataset Description
Raw wildlife detection logs contain:
Column	Description
image_id	Unique detection ID
species	Detected species (Elephant, Tiger, Deer, Human)
confidence	Model confidence score
latitude	GPS latitude
longitude	GPS longitude
timestamp	Detection timestamp
camera_id	Camera source
zone	Forest zone (Core, Buffer, Edge)
The dataset spans 1 full year (2025) with seasonal patterns built into the data.
🟤 Medallion Architecture Implementation
🟡 1. Staging Layer
Loads new CSV batch from AWS S3
Adds load_date
Temporary holding area
Enables controlled batch processing
Purpose:
Validate schema
Isolate new data
Prevent corrupt data from entering Bronze
🟤 2. Bronze Layer (Raw Storage)
Stores raw records as Delta table
Uses MERGE on image_id
Preserves ingestion history
Characteristics:
No transformations
Source of truth
Supports reprocessing
🟠 3. Silver Layer (Cleaned & Structured)
Data quality rules applied:
Remove NULL confidence
Remove confidence < 0.6
Handle invalid timestamps using try_to_timestamp
Standardize species names
Derive:
event_time
activity_date
year
month
Partitioned by:
(year, month)
Purpose:
Analytics-ready dataset
Optimized for aggregation
🟡 4. Gold Layer (Business Fact Table)
Table: fact_wildlife_activity
| year | month | zone | species | detection_count |
Aggregation logic:
GROUP BY year, month, zone, species
COUNT(*)
Partitioned by:
(year, month)
This table powers dashboards and analytical queries.
🔁 Incremental Load Strategy
The pipeline supports fully incremental processing:
New data lands in S3
Loaded into Staging
MERGE into Bronze (no duplicates)
Cleaned and MERGE into Silver
Identify impacted (year, month) partitions
Delete only impacted partitions in Gold
Recalculate and insert updated aggregates
Clear staging
✔ No full reload
✔ Partition-aware processing
✔ Delta Lake compliant
✔ Efficient and scalable
📈 Sample Business Insights Enabled
The Gold fact table enables:
📅 Monthly wildlife activity trend
🗺 Zone-wise activity comparison
🐯 Species seasonality analysis
🚨 Human intrusion monitoring
📊 High-risk zone identification
Use Cases:
Ranger deployment planning
Poaching risk detection
Seasonal wildlife movement analysis
Human-wildlife conflict prevention
⚙️ Technologies Used
AWS S3 (Data Lake Storage)
Databricks
Apache Spark (PySpark)
Delta Lake
SQL
Medallion Architecture
Partition-based incremental processing
🚀 Performance Optimization
Partitioning by (year, month)
Delta MERGE for idempotent loads
OPTIMIZE with ZORDER
Auto-compaction enabled
Correlated DELETE using EXISTS
🎯 Key Engineering Concepts Demonstrated
Lakehouse Architecture
Incremental ETL Processing
Data Quality Handling
Partition-Based Aggregation
Delta Lake Limitations & Workarounds
Idempotent Pipeline Design
Production-Level Workflow
