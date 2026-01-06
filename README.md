# 🛍️ Retail / HCP Sales Analytics Project

## 📌 Project Overview
This project builds an **analytics-ready sales data model** using **Apache Spark**, designed to support both **customer-level ranking** and **geo-level performance analysis**. The pipeline produces **two fact tables** optimized for different analytical use cases and pushes the final outputs to **AWS S3**.

The solution follows **dimensional modeling best practices**, enabling aggregation across **geographic hierarchies** and **customer types**.

**Terminology**
- Please note 'HCP' refers to Health Care Provider, or the customer.
- Base sales are NBRx (New to Brand Prescriptions), NRx (New Prescriptions), TRx (Total Prescriptions)

Please note for the purpose of this project we are drawing data from fictional intermediate tables to avoid using confidential healthcare data.

---

## 🧱 Data Model Overview
The data model consists of the following tables:

- **Customer-level ranked sales fact**
- **Geo-level aggregated sales fact**
- **Customer Dimension**
- **Segmentation Dimension**
- **Geography Dimension**
- **Product Dimension**

Both fact tables reference shared dimension tables to ensure consistency across reporting layers.

---

## 🧾 Fact Tables

### 1️⃣ HCP-Level Ranked Sales Fact
**Output:** `df_hcp_geo_sales_final` (pushed to S3)

**Grain:**
- One row per **HCP × product × geography × time period**

**Purpose:**
- Enables **HCP-level performance analysis**
- Supports **ranking customers based on NBRx**
- Used for targeting, segmentation, and sales effectiveness analysis

**Key Metrics:**
- NBRx (New Brand Prescriptions)
- Market share (Brand sales/Market sales)
- HCP rank within geography
- Contribution to geo-level performance

**Ranking Logic:**
- HCPs are ranked by **NBRx volume**
- Ranking is calculated **within geographic partitions**
- Implemented using **window functions** to ensure consistent ordering

---

### 2️⃣ Geo-Level Aggregated Sales Fact
**Output:** `df_agg_sales_all` (pushed to S3)

**Grain:**
- One row per **geography × product × time period x customer segmentation**

**Purpose:**
- Provides a **rolled-up view of sales performance**
- Optimized for dashboards and executive reporting
- Enables comparison across territories, regions, and national levels

**Key Metrics:**
- Brand volume and Market Volume for NBRx, NRx, TRx
- Market share (Brand sales/Market sales)
- Sales growth ((Curr sales - Prev sales)/Curr sales)
- Aggregated sales by geography (Nation, Region, Area, District, Territory)

---

## Aggregation Across Hierarchies

### Geographic Hierarchy
Sales data supports roll-ups and drill-downs across multiple geo levels:

```text
National
 └── Region
     └── Area
         └── District
             └── Territory
---

### 🔄 Data Pipeline Summary
- Ingest sales data from AWS S3 into Databricks
- Clean and standardize schemas
- Create referenced dimension tables
- Apply business rules
- Build HCP-level ranked fact using window functions
- Aggregate customer-level metrics into geo-level fact
- Push final fact tables to S3 for downstream consumption
