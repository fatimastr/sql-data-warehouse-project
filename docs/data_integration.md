# Data Integration (How Source Tables Are Related)

This diagram shows which columns connect the CRM and ERP tables to each other. These keys are what the Gold layer uses to join the tables together, for example when building `dim_customers` and `dim_products`.

```mermaid
flowchart LR
    subgraph CRM["CRM"]
        sales["crm_sales_details<br/>prd_key, cst_id"]
        prd["crm_prd_info<br/>prd_key"]
        cust["crm_cust_info<br/>cst_id, cst_key"]
    end

    subgraph ERP["ERP"]
        cat["erp_px_cat_g1v2<br/>id"]
        az["erp_cust_az12<br/>cid"]
        loc["erp_loc_a101<br/>cid"]
    end

    sales ---|"prd_key"| prd
    sales ---|"cst_id"| cust
    prd ---|"prd_key = id"| cat
    cust ---|"cst_key = cid"| az
    cust ---|"cst_key = cid"| loc
```

## Join Keys

| Table A | Table B | Joined on | Business meaning |
|---|---|---|---|
| `crm_sales_details` | `crm_prd_info` | `prd_key` | Which product was sold |
| `crm_sales_details` | `crm_cust_info` | `cst_id` | Which customer bought |
| `crm_prd_info` | `erp_px_cat_g1v2` | `prd_key = id` | Product category details |
| `crm_cust_info` | `erp_cust_az12` | `cst_key = cid` | Customer birthdate (and gender) |
| `crm_cust_info` | `erp_loc_a101` | `cst_key = cid` | Customer country |
