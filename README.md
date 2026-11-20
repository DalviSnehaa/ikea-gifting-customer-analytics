# IKEA Gifting – Customer Analytics & RFM Segmentation

End-to-end customer analytics portfolio project using **PostgreSQL, Python (Pandas), and Power BI**.

## Project objective

Analyze historical online retail transactions to understand customer purchasing behaviour, identify valuable customer segments, and flag customers with no purchase activity in the trailing 90 days.

The project follows this workflow:

1. **PostgreSQL:** import, consolidate, validate, and clean the two yearly transaction datasets.
2. **Python / Jupyter Notebook:** calculate Recency, Frequency, and Monetary (RFM), score customers, assign business segments, and prepare the final analytical dataset.
3. **Power BI:** build a two-page customer analytics report: Business Overview and RFM Analysis.

## Dataset

This project is designed for the **Online Retail II** dataset from the UCI Machine Learning Repository. It contains two yearly worksheets covering December 2009 to December 2011.

Download the dataset from:
https://archive.ics.uci.edu/dataset/502/online+retail+ii

The source data is not included in this repository. It is large and should be downloaded separately. Keep the original workbook in `data/raw/` locally; do not commit large raw data files to GitHub.

Expected source columns:

- `Invoice`
- `StockCode`
- `Description`
- `Quantity`
- `InvoiceDate`
- `Price`
- `Customer ID`
- `Country`

> The exact column names may vary after import. The SQL scripts and notebook use normalized snake_case names. Rename columns during import or adjust the scripts to match your imported column names.

## Repository structure

```text
ikea-gifting-customer-analytics/
├── data/
│   ├── raw/                 # Put the downloaded source workbook here (not committed)
│   └── processed/           # Export cleaned and RFM-enriched CSVs here (not committed)
├── sql/
│   ├── 01_create_schema.sql
│   ├── 02_create_staging_tables.sql
│   ├── 03_combine_years.sql
│   ├── 04_clean_and_validate.sql
│   └── 05_data_quality_checks.sql
├── notebooks/
│   └── ikea_customer_rfm_analysis.ipynb
├── power_bi/
│   ├── README.md
│   └── measures.dax
├── docs/
│   └── IKEA_Gifting_BRD.docx
├── reports/                 # Add exported report screenshots / findings
├── requirements.txt
├── .gitignore
└── README.md
```

## Tools

- PostgreSQL
- pgAdmin 4
- Python 3
- Jupyter Notebook
- Pandas and NumPy
- Matplotlib and Seaborn
- Microsoft Power BI Desktop

## PostgreSQL setup

1. Install PostgreSQL and pgAdmin 4.
2. Create a database named `ikea_customer_analytics`.
3. Open Query Tool connected to that database.
4. Run the SQL scripts in numerical order from the `sql/` folder.
5. Import each yearly worksheet into its matching staging table:
   - `retail.year_2009_2010`
   - `retail.year_2010_2011`
6. Import staging columns as text initially where practical, to reduce import failures caused by inconsistent source values.
7. Run the combine, cleaning, and validation scripts.

### Import note

pgAdmin's CSV import workflow expects a table to exist first. Create the staging tables with `02_create_staging_tables.sql`, then import each year's CSV into its corresponding table. If importing from the Excel workbook, export each worksheet to CSV first.

## Data cleaning approach

The SQL workflow includes:

- Consolidation of the two yearly datasets using `UNION ALL`.
- Type conversion for quantity, invoice date, price, and customer identifier.
- Checks for missing customer identifiers.
- Checks for zero or negative quantities.
- Checks for missing descriptions and prices.
- Exact duplicate detection.

The scripts create a typed table and a quality-check report. Review the results before deciding which records to exclude from customer-level analysis. Negative quantities can represent returns or credit notes; they should not automatically be deleted without documenting the treatment.

## Python / RFM analysis

Open `notebooks/ikea_customer_rfm_analysis.ipynb` after exporting the cleaned SQL table to:

`data/processed/retail_cleaned.csv`

The notebook:

1. Loads and inspects the cleaned transaction data.
2. Applies documented analysis filters.
3. Calculates customer-level Recency, Frequency, and Monetary metrics.
4. Scores customers and assigns business segments.
5. Flags churn using a 90-day inactivity threshold.
6. Merges customer-level RFM attributes back into the transaction-level data.
7. Exports `retail_rfm_final.csv` for Power BI.

The notebook contains configurable assumptions for the analysis reference date, transaction inclusion, and RFM score direction. Review these settings and document the final choices you use.

## Power BI report

Build the report using `data/processed/retail_rfm_final.csv`.

### Page 1 – Business Overview

Suggested KPIs and visuals:
- Total Revenue
- Total Customers
- Total Transactions
- Monthly revenue trend
- Year-over-year revenue
- Country-wise revenue
- Customer purchasing trends

### Page 2 – RFM Analysis

Suggested KPIs and visuals:
- Churned Percentage
- Recency
- Frequency
- Monetary
- Customer Value
- Customer count by segment
- Revenue contribution by segment
- Segment and customer-level drill-down

See `power_bi/README.md` and `power_bi/measures.dax` for implementation guidance and starter measures.

## Business definitions

- **Recency:** number of days since the customer's most recent purchase.
- **Frequency:** number of distinct purchase transactions made during the analysis period.
- **Monetary:** total revenue generated by a customer during the analysis period.
- **Churn:** no recorded purchase activity in the trailing 90 days, as defined in the project BRD.
- **Customer Value:** define the exact KPI calculation in the report glossary. A useful starting point is customer-level Monetary value, but do not label it as a separate lifetime-value model unless one is actually built.

## Important project integrity notes

- This repository is a project starter package, not a claim that the analysis has already been executed.
- Add actual row counts, data-quality results, segment sizes, revenue findings, and screenshots only after running the project.
- Do not invent accuracy, business impact, or performance metrics.
- The BRD describes fixed-threshold churn flagging; predictive churn modelling is outside the stated scope.
- Keep the raw workbook and generated CSV files out of Git unless you have checked dataset redistribution terms and repository size.

## Resume project title

**IKEA Gifting – Customer Analytics & RFM Segmentation**

**Technologies:** PostgreSQL, Python, Pandas, NumPy, Jupyter Notebook, Power BI.

## Suggested GitHub upload

```bash
git init
git add .
git commit -m "Add IKEA customer analytics project"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/ikea-gifting-customer-analytics.git
git push -u origin main
```

Replace `YOUR-USERNAME` with your GitHub username after creating an empty repository on GitHub.
