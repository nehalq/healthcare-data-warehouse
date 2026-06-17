# Healthcare Data Warehouse System

> A Python-based ETL pipeline and Star Schema data warehouse for global healthcare analytics, integrating CSV datasets and live REST APIs into a centralized SQLite analytical platform.

---

## Table of Contents

- [Overview](#overview)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Features](#features)
- [Data Sources](#data-sources)
- [Database Schema](#database-schema)
- [Getting Started](#getting-started)
- [Usage](#usage)
- [Results](#results)
- [Future Work](#future-work)
- [Team](#team)

---

## Overview

Healthcare organizations collect vast amounts of data from hospitals, surveys, and public health systems — but this data often resides in multiple incompatible systems. This project solves that problem by building a **Healthcare Data Warehouse (DWH)** that integrates, standardizes, and structures heterogeneous healthcare data for efficient querying and analysis.

Key capabilities:
- Extracts data from 1,000,000-row CSV files and a live COVID-19 REST API (231 countries)
- Cleans, validates, and transforms raw data using a Python ETL pipeline
- Loads structured data into a **Star Schema** SQLite database
- Supports BI dashboards (Power BI, Tableau), SQL analytics, and ML model training
- Applies clustering and anomaly detection to uncover global health trends

---

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Python 3 |
| ETL & Data Processing | Pandas, Requests |
| Data Warehouse | SQLite |
| Notebook | Jupyter (etl.ipynb) |
| Visualization | Matplotlib, Plotly, Power BI |
| ML Applications | Scikit-learn (clustering, anomaly detection) |
| Data Sources | CSV, REST API |

**Keywords:** Data Warehousing, ETL Pipeline, Star Schema, Business Intelligence, SQLite, Python, Pandas, Healthcare Analytics, Dimensional Modeling, COVID-19 API

---

## Project Structure

```
healthcare-data-warehouse/
│
├── data/
│   └── global-health-statistics.csv      # Primary data source (1M rows)
│
├── data_warehouse/
│   ├── global_health_datawarehouse.db    # SQLite data warehouse
│   └── exports/                          # Query result exports
│
├── logs/                                 # ETL execution logs
│
├── etl.ipynb                             # Main ETL Jupyter notebook
└── README.md
```

---

## Features

- **Centralized Storage** — Combines CSV and API sources into a single unified warehouse
- **Data Quality & Validation** — Automated profiling, missing-value handling (median imputation), and duplicate removal
- **Star Schema Design** — Optimized for fast analytical queries and BI tool integration
- **Scalable ETL Pipeline** — Modular Python pipeline with logging and metadata tracking at each phase
- **ML-Ready Data** — Cleaned, structured output ready for clustering and anomaly detection models
- **BI Integration** — Compatible with Power BI, Tableau, QlikView, and Python visualization libraries

---

## Data Sources

| Source | Description | Key Attributes |
|---|---|---|
| CSV — Global Health Statistics | International healthcare and disease indicators | country, disease_name, prevalence_rate, mortality_rate, healthcare_access, doctors_per_1000 |
| API — COVID-19 (disease.sh) | Live COVID-19 statistics for 231 countries | country, cases, deaths, recovered, population, continent |
| Synthetic / Derived | Generated during transformation for demographics and time | age_group, gender, year |

---

## Database Schema

This project uses a **Star Schema** with one central fact table and five dimension tables.

```
                    ┌──────────────────┐
                    │  DimCountry      │
                    └────────┬─────────┘
                             │
┌──────────────┐    ┌────────▼──────────┐    ┌──────────────────┐
│  DimDisease  ├────►  FactHealthOutcomes◄────┤  DimDemographics │
└──────────────┘    └────────┬──────────┘    └──────────────────┘
                             │
              ┌──────────────┴──────────────┐
              │                             │
     ┌────────▼────────┐          ┌─────────▼──────────┐
     │    DimTime      │          │   DimCovidStats     │
     └─────────────────┘          └────────────────────┘
```

**Fact Table:** `FactHealthOutcomes` — prevalence_rate, mortality_rate, incidence_rate, and foreign keys to all dimensions

**Dimension Tables:**
- `DimCountry` — country ID, name, socioeconomic attributes
- `DimDisease` — disease ID, name, category, treatment type
- `DimDemographics` — age group and gender categories
- `DimTime` — year ID and year
- `DimCovidStats` — COVID-19 statistics per country

---

## Getting Started

### Prerequisites

```bash
pip install pandas requests jupyter matplotlib plotly scikit-learn
```

### Clone the Repository

```bash
git clone https://github.com/your-username/healthcare-data-warehouse.git
cd healthcare-data-warehouse
```

### Add Data

The dataset is too large to host on GitHub. Download it from Google Drive and place it inside the `data/` directory:

📁 **[Download Dataset from Google Drive](https://drive.google.com/drive/folders/10tRF5HseN08iyeqw84hudK40D1KDTWuX?usp=sharing)**

> Download `global-health-statistics.csv` and place it at `data/global-health-statistics.csv` before running the notebook.

### Run the ETL Pipeline

```bash
jupyter notebook etl.ipynb
```

Run all cells in order. The notebook will:
1. Extract data from the CSV and COVID-19 API
2. Transform and clean all datasets
3. Load structured data into the SQLite data warehouse at `data_warehouse/global_health_datawarehouse.db`

---

## Usage

### Query the Warehouse

```python
import sqlite3
import pandas as pd

conn = sqlite3.connect("data_warehouse/global_health_datawarehouse.db")

# Example: Top 10 diseases by average mortality rate
query = """
    SELECT d.disease_name, AVG(f.mortality_rate) AS avg_mortality
    FROM FactHealthOutcomes f
    JOIN DimDisease d ON f.disease_id = d.disease_id
    GROUP BY d.disease_name
    ORDER BY avg_mortality DESC
    LIMIT 10
"""
df = pd.read_sql_query(query, conn)
print(df)
```

### Connect to Power BI / Tableau

Point your BI tool directly at `data_warehouse/global_health_datawarehouse.db` using an ODBC or SQLite connector to build interactive dashboards.

---

## Results

After running the full ETL pipeline, the warehouse contains:

| Table | Row Count |
|---|---|
| FactHealthOutcomes | 1,000,000 |
| DimCountry | 20 |
| DimDisease | 1,760 |
| DimDemographics | 12 |
| DimTime | 25 |
| DimCovidStats | 231 |

Sample analytics produced from the warehouse:
- Top 10 diseases by average mortality rate
- Countries with highest DALYs (disability-adjusted life years)
- COVID-19 trends correlated with healthcare access indicators

---

## Future Work

- **Real-time integration** — Streaming data sources via message queues or change-data-capture (CDC)
- **Cloud migration** — Move warehouse to Snowflake, BigQuery, or AWS Redshift for elastic scalability
- **Advanced ML** — Deep learning models for outbreak prediction and health pattern detection
- **Natural language querying** — Integrate generative AI to allow plain-English SQL queries over the warehouse

---

## Team

**Course:** Data Warehousing and Business Intelligence (CT-472)  
**Department:** Computer Science & Technology

| Name | Student ID |
|---|---|
| Neha Laeeq | DT-22002 |
| Talal Khan | DT-22043 |
| Shayan | DT-22037 |
| Ahmed Hassan | DT-22041 |

---

## License

This project was developed for academic purposes as part of CT-472.
