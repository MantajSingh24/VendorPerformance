# Vendor Performance Analysis

**Power BI + PostgreSQL + Python**

End-to-end project that cleans raw procurement data using Python, loads it into PostgreSQL, and builds an interactive Vendor Performance Dashboard in Power BI.

---

## Interactive Dashboard

[View Live Dashboard](https://app.powerbi.com/view?r=eyJrIjoiODE3NjNhM2ItZTdiZS00NTUzLWI4NmItY2VmY2VmY2IyMTQ1IiwidCI6IjkxYjYwMzQ5LWFjNmItNDM1NS1hMTc1LWJmNzc5MDhmYmVjMiJ9)

Users can click filters, explore visuals, and interact with the dashboard.

---

## Features

- **Python ETL** to clean, standardize, and load CSVs
- **PostgreSQL** relational schema (vendors, purchase_orders, deliveries, invoices, po_line_items)
- **Power BI Dashboard** with KPIs:
  - Total Vendor Spend
  - Average Vendor Rating
  - Average Delivery Delay
  - Average Payment Days
  - Monthly Spend Trend
  - Vendor Comparison Charts

---

## Getting Started

### 1. PostgreSQL Setup

Create database and user:

```sql
CREATE ROLE vendor_user WITH LOGIN PASSWORD 'vendor_pass';
CREATE DATABASE vendor_db OWNER vendor_user;
```

Run the table schema:

```bash
psql -U vendor_user -d vendor_db -f sql/ddl.sql
```

### 2. Install Dependencies

```bash
python -m venv .venv

# Windows
.\.venv\Scripts\activate

# macOS/Linux
source .venv/bin/activate

pip install -r requirements.txt
```

### 3. Configure Environment

Copy the example environment file and update with your credentials:

```bash
cp .env.example .env
```

### 4. Run ETL (Clean + Load Data)

Place your CSV files into `data/raw/`, then run:

```bash
python etl/clean_load.py
```

This will:
- Clean the raw CSVs
- Standardize column names
- Fix data types
- Load everything into PostgreSQL

### 5. Power BI Dashboard

1. Open Power BI Desktop
2. Connect to PostgreSQL
   - Server: `localhost`
   - Database: `vendor_db`
3. Load tables: `vendors`, `purchase_orders`, `deliveries`, `invoices`, `po_line_items`
4. Create DAX measures for KPIs (see `docs/powerbi_dashboard_guide.md`)
5. Build dashboard visuals
6. (Optional) Publish to Power BI Service

### Run Sample Queries

```bash
psql -U vendor_user -d vendor_db -f sql/sample_queries.sql
```

---

## Project Structure

```
vendor-performance/
├── data/
│   ├── raw/              # Original CSV files
│   └── cleaned/          # Processed CSV files
├── docs/
│   ├── insights_outline.md
│   └── powerbi_dashboard_guide.md
├── etl/
│   └── clean_load.py     # ETL pipeline
├── sql/
│   ├── ddl.sql           # Database schema
│   └── sample_queries.sql
├── vendor_performance_Dashboard.pbix
├── requirements.txt
└── README.md
```

---

## Tech Stack

| Component | Technology |
|-----------|------------|
| ETL | Python, Pandas |
| Database | PostgreSQL, SQLAlchemy |
| Dashboard | Power BI |

---

## What This Project Demonstrates

- Python ETL pipeline development
- SQL table design & relational modeling
- Data cleaning & transformation
- Power BI dashboard creation
- KPI-driven vendor analysis

---

## Contact

**Mantaj Singh**  
Data Analytics | Acadia University  
[LinkedIn](https://www.linkedin.com/in/mantaj-s-9448a7271)
