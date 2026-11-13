# Brazilian E-Commerce Data Pipeline (Databricks Medallion Architecture)

## 📌 Project Overview
This project is an end-to-end data engineering pipeline built on **Databricks**, designed to process and analyze the Olist Brazilian E-Commerce dataset. The pipeline strictly follows the **Medallion Architecture (Bronze, Silver, Gold)**, transforming raw, unstructured CSV files into clean, strongly-typed, business-ready Delta tables optimized for Business Intelligence (BI) dashboards.

## 🏗️ Architecture & Data Flow

This project leverages **Unity Catalog** for data governance and utilizes a Serverless compute environment. The data flows through three distinct Delta Lake tiers:

1. **Bronze Tier (Raw Ingestion):** 
   - **Source:** 9 raw Olist CSV files uploaded to a governed Unity Catalog Volume (`raw_landing`).
   - **Process:** A dynamic PySpark loop iteratively reads the CSVs and writes them as managed Delta tables.
   - **Governance:** Data is ingested "as-is" to maintain an immutable history. Modern Unity Catalog audit columns (`ingestion_timestamp` and `_metadata.file_path`) are appended for data lineage and debugging.

2. **Silver Tier (Cleansing & Conformance):**
   - **Process:** Core fact and dimension tables (`orders`, `order_items`) are refined using PySpark.
   - **Transformations:** 
     - Dropped orphaned records (null primary keys).
     - Deduplicated rows to prevent double-counting revenue.
     - Enforced strict schema types (e.g., casting string dates to `TimestampType`, and financial metrics to `DoubleType`).

3. **Gold Tier (Business Aggregations):**
   - **Process:** Transformed the cleansed Silver data into highly optimized aggregates using **Spark SQL**.
   - **Transformations:** Joined the `orders` and `order_items` tables to pre-compute a `daily_sales_revenue` dataset, minimizing downstream compute costs for BI tools like Power BI or Tableau.

## 🛠️ Tech Stack & Skills Demonstrated
* **Compute & Environment:** Databricks Free Edition (Serverless)
* **Data Processing:** PySpark (DataFrame API), Spark SQL
* **Storage & Formatting:** Delta Lake (ACID transactions, Time Travel)
* **Data Governance:** Unity Catalog (Three-level namespace schema isolation, Managed Volumes)
* **Key Concepts:** Idempotency (`CREATE OR REPLACE`, `.mode("overwrite")`), Metadata extraction, Type casting, CTAS statements.

## 📊 Visualizing the Gold Tier
*(![Gold Tier Daily Revenue](revenue_chart.png))*
> *A native Databricks visualization of the Gold tier `daily_sales_revenue` table, demonstrating the final metric ready for stakeholder consumption.*

## 📂 Repository Structure
* `01_setup_and_bronze_ingestion.ipynb`: DDL for Unity Catalog schema/volume creation and dynamic PySpark metadata-driven ingestion.
* `02_silver_orders_processing.ipynb`: PySpark scripts for deduplication, null handling, and explicit data type casting.
* `03_gold_business_aggregations.ipynb`: Spark SQL CTAS statements for joining and aggregating business metrics.

## 🚀 How to Run
1. Provision a Databricks workspace with Unity Catalog enabled.
2. Download the [Olist Dataset](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) from Kaggle.
3. Import the notebooks into your Databricks workspace.
4. Execute `01_setup_and_bronze_ingestion` to provision the schemas and volume, then upload the CSVs via the UI to the volume path.
5. Run the remaining notebooks sequentially to build the Medallion architecture.

---
*Developed by [Stephen Njoroge](https://www.linkedin.com/in/stephen-njoroge-aa0b63318/) | [Portfolio](https://github.com/SteveRoge8)*