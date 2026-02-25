🐘 Wildlife Conservation: End-to-End Medallion Lakehouse
📌 Project Overview
This project implements a production-style Lakehouse data pipeline using AWS S3 and Databricks (Delta Lake) to process and analyze wildlife detection data. The system is designed to handle messy sensor logs and transform them into actionable intelligence for ranger deployment and poaching risk detection. 

🏗 Architectural Strategy

The pipeline follows the Medallion Architecture, ensuring high data quality and a clear lineage from raw ingestion to executive-level reporting. 

Staging Layer: Temporary batch loading from AWS S3 to isolate new data and validate schemas.

Bronze (Raw): Immutable source of truth storing raw records with load_date metadata. 

Silver (Cleaned): Application of data quality rules, standardized timestamps, and partitioning by (year, month).

Gold (Aggregated): Business-level Fact Table (fact_wildlife_activity) optimized for high-speed BI querying. 

🔁 Incremental Load & Idempotency
A core focus of this project is cost-efficiency and scalability. Instead of expensive full reloads, the pipeline utilizes: 

Delta MERGE: Ensures idempotent loads by preventing duplicate records in the Bronze and Silver layers. 

Partition-Aware Updates: When new data arrives, the system identifies only the impacted (year, month) partitions in the Gold layer, deletes them, and recalculates—significantly reducing compute costs. 

SHA-256 Surrogate Keys: Implemented to maintain data integrity across distributed datasets. 

📊 Dataset & Business Intelligence
The pipeline processes detection logs containing species (Elephant, Tiger, Deer, Human), confidence scores, and GPS coordinates across a 1-year span. 

Key Insights Enabled:

🚨 Human Intrusion Monitoring: Real-time tracking of unauthorized activity in "Core" forest zones. 

📅 Seasonal Movement: Analyzing how wildlife migration patterns shift across different months. 

📉 Operational Efficiency: Automating detection reporting to reduce manual effort by 80%. 

🛠 Tech Stack & Optimizations
Processing: PySpark, Spark SQL 

Storage: AWS S3 (Data Lake), Delta Lake 

Orchestration: Databricks Job Scheduling 

Performance:

Z-ORDER Clustering: Applied to zone and species for faster filter performance.

Partitioning: Strategically partitioned by (year, month) to optimize large-scale aggregations. 

📂 Project Structure
Bash
wildlife-data-engineering/
├── data_generator/       # Script to simulate 1 year of sensor data
├── notebooks/
│   ├── 01_staging        # S3 Ingestion
│   ├── 02_bronze         # MERGE & Raw Storage
│   ├── 03_silver         # Quality rules & Partitioning
│   └── 04_gold           # Incremental Fact Table updates
├── sql_queries/          # BI/Dashboard queries
└── README.md
