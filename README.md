# CASE STUDY: Scalable Data Warehouse Architecture for Urban Mobility
> **High-Performance ELT & Dimensional Modeling on Google Cloud Platform**

## 1. PROJECT EXECUTIVE SUMMARY
- **Objective:** Transformation of 100M+ raw Uber trip records into a high-efficiency Medallion Architecture.
- **Tech Stack:** Mage AI, Google Cloud Storage (GCS), BigQuery, Python, SQL, Apache Parquet.
- **Outcome:** Engineered a production-ready Star Schema that reduced query data processing costs by ~60% and improved analytical response times by over 80%.

---

## 2. THE ENGINEERING CHALLENGE
- **Legacy Bottleneck:** Raw data was stored in a monolithic, unindexed flat table, forcing full-table scans for every analytical request.
- **Cost Inefficiency:** High "Data Processed" metrics in BigQuery resulted in excessive operational expenditure (OpEx).
- **Extraction Barrier:** Encountered the 1GB single-object export limit in BigQuery when attempting to migrate data to a cold-storage Data Lake.
- **Data Latency:** Temporal calculations (Hour, Weekday, Month) were being performed at runtime, causing dashboard lag during peak usage.

---

## 3. ARCHITECTURAL DECISIONS & IMPLEMENTATION



### A. Data Lake Ingestion (Bronze Layer)
- **Orchestration:** Utilized **Mage AI** to decouple the ingestion logic from the storage layer.
- **Storage Strategy:** Migrated raw data to GCS using **Apache Parquet**; leveraged columnar storage to enable predicate pushdown and Snappy compression.
- **Parallel Sharding:** Resolved the 1GB export limit by implementing a **Wildcard URI strategy** (`rides_*.parquet`), parallelizing the export process across multiple compute nodes.

### B. Warehouse Transformation (Silver/Gold Layer)
- **Dimensional Modeling:** Deconstructed the flat-file into a **Star Schema** to isolate metrics from context.
- **Datetime Dimension:** Pre-calculated all temporal features (24-hour cycle, day-of-week, etc.) to eliminate redundant SQL `EXTRACT` functions.
- **Identity Management:** Generated **UUID-based surrogate keys** to ensure referential integrity and shield the warehouse from source-system schema drift.

---

## 4. TECHNICAL TROUBLESHOOTING LOG
- **Authentication:** Resolved `403 Forbidden` errors by provisioning a dedicated **GCP Service Account** with granular IAM roles (`Storage Object Admin`).
- **Schema Mapping:** Identified and corrected column naming inconsistencies (e.g., `RatecodeID` vs `rate_code`) through a comprehensive audit of the `INFORMATION_SCHEMA`.
- **Idempotency:** Designed SQL scripts with `CREATE OR REPLACE` logic to ensure the pipeline is repeatable and prevents data duplication upon failure.

---

## 5. REVENUE IMPACT & BUSINESS VALUE
- **Cost Optimization:** By querying normalized dimension tables rather than the raw 100M-row table, the scanning cost per query was significantly minimized.
- **Speed to Insight:** "Time-to-Insight" was reduced from minutes to sub-3-second execution times for standard operational reports.
- **Enterprise Scalability:** The architecture is designed to handle TB-scale datasets without modification to the underlying pipeline logic.

---

## 6. CORE BUSINESS ANALYTICS CAPABILITIES



- **Operational Efficiency:** Identifying the "Fare-to-Distance" ratio by pickup location to optimize driver incentives.
- **Customer Segmentation:** Analyzing group-ride behavior (passenger counts) versus trip duration for vehicle class optimization.
- **Financial Reconciliation:** Detailed breakdown of payment types across vendors for tax and merchant fee auditing.

---

##
