# Modern E-Commerce Data Platform with Microsoft Fabric

**REST API → Medallion Architecture → Delta Lake → Star Schema → Direct Lake → Power BI**

An end-to-end e-commerce data platform built with Microsoft Fabric, starting from REST API ingestion and ending with an interactive Power BI dashboard.

The project focuses on building a modern data pipeline using Python, Microsoft Fabric Data Pipelines, PySpark, Delta Lake, dimensional modeling, and a Direct Lake semantic model.

## Architecture

![Architicture](screenshot/Architicture.png)

## Tech Stack

* Python
* Jupyter Notebook
* Microsoft Fabric
* Fabric Data Pipelines
* OneLake
* Lakehouse
* PySpark
* Delta Lake
* Star Schema
* DAX
* Power BI
* GitHub

## Data Source

The project uses the DummyJSON REST API as the source system.

Main datasets:

* Products
* Users
* Carts

## Medallion Architecture

### Bronze Layer

Raw API data is ingested into the Fabric Lakehouse using Fabric Data Pipelines.

```text
Files/
└── bronze/
    ├── products/
    ├── users/
    └── carts/
```

### Silver Layer

The Silver layer was implemented using PySpark in Microsoft Fabric.

Main transformations:

* JSON flattening
* Exploding nested arrays
* Selecting required columns
* Creating derived measures
* Adding processing timestamps
* Writing cleaned data as Delta tables

Main Silver tables:

```text
silver_products
silver_users
silver_cart_items
```

### Gold Layer

The Gold layer was also implemented using PySpark in Microsoft Fabric.

A star schema was created with:

```text
dim_product
dim_customer
dim_date
fact_sales
```

Surrogate keys are used to connect the fact table with the dimension tables.

## Semantic Model

A Direct Lake semantic model was created in Microsoft Fabric.

Main relationships:

```text
dim_product  1 ─── *  fact_sales
dim_customer 1 ─── *  fact_sales
dim_date     1 ─── *  fact_sales
```
![Star_Scheema](screenshot/Star_Scheema.png)

### Main DAX Measures

```DAX
Total Sales =
SUM(fact_sales[discounted_total])
```

```DAX
Total Quantity =
SUM(fact_sales[quantity])
```

```DAX
Total Orders =
DISTINCTCOUNT(fact_sales[cart_id])
```

```DAX
Average Order Value =
DIVIDE(
    [Total Sales],
    [Total Orders]
)
```

```DAX
Total Discount =
SUM(fact_sales[discount_amount])
```

## Power BI Dashboard

The final Power BI report includes:

* Total Sales
* Total Orders
* Total Quantity
* Average Order Value
* Total Discount
* Sales by Category
* Sales by Brand
* Top 10 Products by Sales
* Top 10 Customers by Sales
* Sales by Customer Gender
![Dashboard](screenshot/Dashboard.png)

### Slicers

* Category
* Brand
* Gender
* Availability Status

## Data Validation

The final data model was validated using record counts, null checks, distinct key checks, and relationship validation.

Key results:

* 194 products
* 208 customers
* 365 date records
* 800 sales fact records
* 189 distinct products sold
* 208 distinct customers in the fact table
* No null product keys
* No null customer keys

## Project Files

```text
ecommerce-data-platform/
├── README.md
└── notebooks/
    ├── fabric_platform.ipynb
    ├── silver_layer.ipynb
    └── gold_layer.ipynb
```

`fabric_platform.ipynb` contains the Python REST API ingestion work developed in VS Code/Jupyter.

`silver_layer.ipynb` and `gold_layer.ipynb` contain the PySpark transformations developed in Microsoft Fabric.

## Important Note

DummyJSON does not provide a true transaction date for the cart data.

Therefore, the `date_key` used in the fact table is derived from the Silver layer processing timestamp rather than an actual business transaction date.

This limitation is documented intentionally as part of the project's data modeling decisions.


