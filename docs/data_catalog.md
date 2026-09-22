# Data Catalog — Gold Layer

This document describes the business-ready tables (views) in the `gold` schema, which form a Star Schema used for analytics and reporting.

---

## 1. `gold.dim_customers`

**Purpose:** Stores customer details enriched with demographic and geographic data, combined from the CRM and ERP source systems.

| Column | Data Type | Description |
|---|---|---|
| `customer_key` | INT | Surrogate key uniquely identifying each customer record in the warehouse. |
| `customer_id` | INT | Original customer identifier from the CRM source system. |
| `customer_number` | NVARCHAR | Alphanumeric customer identifier used for tracing back to source systems. |
| `first_name` | NVARCHAR | Customer's first name. |
| `last_name` | NVARCHAR | Customer's last name. |
| `country` | NVARCHAR | Customer's country of residence (e.g., `Germany`, `United States`, `n/a`). |
| `marital_status` | NVARCHAR | Customer's marital status (`Single`, `Married`, `n/a`). |
| `gender` | NVARCHAR | Customer's gender (`Male`, `Female`, `n/a`). Integrated from CRM (master source) and ERP. |
| `birth_date` | DATE | Customer's date of birth. |
| `create_date` | DATE | Date the customer record was first created in the source system. |

---

## 2. `gold.dim_products`

**Purpose:** Stores current product information (historical/discontinued product versions are excluded) along with category details from the ERP system.

| Column | Data Type | Description |
|---|---|---|
| `product_key` | INT | Surrogate key uniquely identifying each product record in the warehouse. |
| `product_id` | INT | Original product identifier from the CRM source system. |
| `product_number` | NVARCHAR | Alphanumeric product code used for tracing back to source systems and joining with sales data. |
| `product_name` | NVARCHAR | Descriptive name of the product. |
| `category_id` | NVARCHAR | Identifier linking the product to its category. |
| `category` | NVARCHAR | High-level product category (e.g., `Bikes`, `Accessories`). |
| `subcategory` | NVARCHAR | Detailed product classification within the category. |
| `maintenance` | NVARCHAR | Indicates whether the product requires maintenance (`Yes`/`No`). |
| `cost` | INT | Cost of the product in whole currency units. |
| `line` | NVARCHAR | Product line (`Mountain`, `Road`, `Other Sales`, `Touring`, `n/a`). |
| `start_date` | DATE | Date the product (current version) became active. |

---

## 3. `gold.fact_sales`

**Purpose:** Stores transactional sales data for analytical purposes, linking to the customer and product dimensions.

| Column | Data Type | Description |
|---|---|---|
| `order_number` | NVARCHAR | Unique identifier for each sales order. |
| `product_key` | INT | Foreign key linking to `gold.dim_products`. |
| `customer_key` | INT | Foreign key linking to `gold.dim_customers`. |
| `order_date` | DATE | Date the order was placed. |
| `shipping_date` | DATE | Date the order was shipped. |
| `due_date` | DATE | Date the order payment was due. |
| `sales_amount` | INT | Total sales value of the order line (`quantity * price`). |
| `quantity` | INT | Number of units ordered. |
| `price` | INT | Price per unit at the time of sale. |

**Business Rule:** `sales_amount = quantity * price`. This rule is validated and, if violated in the source data, `sales_amount` and/or `price` are recalculated during the Silver layer transformation.
