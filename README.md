# E-Commerce Data Platform with Microsoft Fabric

**REST API → Medallion Architecture → Delta Lake → Star Schema → Direct Lake → Power BI**

An end-to-end e-commerce data platform built with Microsoft Fabric, starting from REST API ingestion and ending with an interactive Power BI dashboard.

The project focuses on building a modern cloud data pipeline using Fabric Data Pipelines, PySpark, Delta Lake, dimensional modeling, and a Direct Lake semantic model.

## Architecture

```text
DummyJSON API
      ↓
Fabric Data Pipeline
      ↓
Bronze — Raw JSON
      ↓
Fabric Notebook / PySpark
      ↓
Silver — Delta Tables
      ↓
Gold — Star Schema
      ↓
Direct Lake Semantic Model
      ↓
Power BI
```

## Tech Stack

* Microsoft Fabric
* Fabric Data Pipelines
* Fabric Notebooks
* REST API
* Python
* PySpark
* OneLake
* Delta Lake
* Star Schema
* Direct Lake
* DAX
* Power BI

## Data Source

The project uses the DummyJSON REST API:

```text
/products
/users
/carts
```

The ingestion process includes pagination, retry handling, HTTP 429 handling, and request timeouts.

## Medallion Architecture

### Bronze Layer

Raw API responses are stored as JSON files in the Fabric Lakehouse.

```text
Files/
└── bronze/
    ├── products/
    ├── users/
    └── carts/
```

### Silver Layer

The raw JSON data is flattened and transformed using PySpark into Delta tables:

```text
silver_products
silver_users
silver_cart_items
```

The cart data required additional transformation because product information is stored as nested arrays.

### Gold Layer

The Gold layer uses a Star Schema designed for analytics.

```text
             dim_product
                  |
                  v
dim_customer → fact_sales ← dim_date
```

The Gold layer contains:

```text
dim_product
dim_customer
dim_date
fact_sales
```

The final `fact_sales` table contains 800 rows.

## Semantic Model

A Direct Lake semantic model was created in Microsoft Fabric.

The model connects the fact table with the product, customer, and date dimensions using one-to-many relationships.

### Main DAX Measures

```text
Total Sales
Total Orders
Total Quantity
Average Order Value
Total Discount
Average Discount
Total Products Sold
```

## Power BI Dashboard

The dashboard includes:

* Total Sales
* Total Orders
* Total Quantity
* Average Order Value
* Total Discount
* Sales by Category
* Sales by Brand
* Top 10 Products
* Top 10 Customers
* Sales by Gender

### Slicers

* Category
* Brand
* Gender
* Availability Status

## Data Validation

Current validated data:

```text
Products:       194
Customers:      208
Date records:   365
Fact rows:      800
```

The fact table was checked against the product and customer dimensions to make sure the relationships were valid.

## Important Note

The DummyJSON Carts API does not provide a real transaction date.

Because of this, the current `date_key` is based on the data processing timestamp rather than an actual order date. This is documented as a limitation of the source data.

## Future Improvements

* Incremental ingestion
* Pipeline monitoring and logging
* Automated data quality checks
* SCD Type 2
* CI/CD with Fabric Git integration
* Real transactional data with order dates
* More advanced Power BI analytics

## Project Goal

The goal was to build a complete modern data pipeline in Microsoft Fabric, from API ingestion to analytics, while working with cloud data engineering concepts such as Medallion Architecture, Delta Lake, dimensional modeling, semantic models, and Power BI.
