# Data Flow (Lineage)

This diagram shows how every table travels from the source files to the final Gold views: which Bronze table feeds which Silver table, and which Silver tables are combined to build each Gold view. It answers the question "where did this number come from?".

```mermaid
flowchart LR
    subgraph SRC["Sources"]
        CRM["CRM<br/>(CSV files)"]
        ERP["ERP<br/>(CSV files)"]
    end

    subgraph BRONZE["🥉 Bronze Layer"]
        b1["crm_cust_info"]
        b2["crm_prd_info"]
        b3["crm_sales_details"]
        b4["erp_cust_az12"]
        b5["erp_loc_a101"]
        b6["erp_px_cat_g1v2"]
    end

    subgraph SILVER["🥈 Silver Layer"]
        s1["crm_cust_info"]
        s2["crm_prd_info"]
        s3["crm_sales_details"]
        s4["erp_cust_az12"]
        s5["erp_loc_a101"]
        s6["erp_px_cat_g1v2"]
    end

    subgraph GOLD["🥇 Gold Layer"]
        g1["dim_customers"]
        g2["dim_products"]
        g3["fact_sales"]
    end

    CRM --> b1 & b2 & b3
    ERP --> b4 & b5 & b6

    b1 --> s1
    b2 --> s2
    b3 --> s3
    b4 --> s4
    b5 --> s5
    b6 --> s6

    s1 --> g1
    s4 --> g1
    s5 --> g1

    s2 --> g2
    s6 --> g2

    s3 --> g3
```

## How Gold views are built

| Gold view | Built from (Silver tables) |
|---|---|
| `gold.dim_customers` | `crm_cust_info` + `erp_cust_az12` + `erp_loc_a101` |
| `gold.dim_products` | `crm_prd_info` + `erp_px_cat_g1v2` |
| `gold.fact_sales` | `crm_sales_details` (joined with the two dimensions to look up `product_key` and `customer_key`) |

Between Bronze and Silver every table maps one-to-one. Tables are only combined in the Gold layer.
