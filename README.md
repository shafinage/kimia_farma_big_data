## Big Data Analysis using Big Query and Visualisation with Looker Studio
### 1. Data validation focusing on primary and foreign keys, as well as important metrics such as price and rating.
```sql
SELECT
  COUNT(*) AS ft_total_rows,
  COUNTIF(transaction_id IS NULL) AS null_transaction_id,
  COUNTIF(product_id IS NULL) AS null_product_id,
  COUNTIF(branch_id IS NULL) AS null_branch_id,
  COUNTIF(price IS NULL) AS null_price,
  COUNTIF(price <= 0) AS invalid_price,
  COUNTIF(date IS NULL) AS null_date,
  COUNTIF(rating IS NULL) AS null_rating,
  COUNTIF(rating < 1 OR rating > 5) AS invalid_rating,
FROM kimia_farma.kf_final_transaction;

SELECT
  COUNT(*) AS p_total_rows,
  COUNTIF(product_id IS NULL) AS null_product_id,
  COUNTIF(price IS NULL) AS null_price,
  COUNTIF(price <= 0) AS invalid_price,
FROM kimia_farma.kf_products;

SELECT
  COUNT(*) AS c_total_rows,
  COUNTIF(branch_id IS NULL) AS null_branch_id,
  COUNTIF(rating IS NULL) AS null_rating,
  COUNTIF(rating < 1 OR rating > 5) AS invalid_rating,
FROM kimia_farma.kf_kantor_cabang;

SELECT
  COUNT(*) AS i_total_rows,
  COUNTIF(inventory_id IS NULL) AS null_inventory_id,
  COUNTIF(branch_id IS NULL) AS null_branch_id,
  COUNTIF(product_id IS NULL) AS null_product_id,
FROM kimia_farma.kf_inventory;
```

### 2. Analysis table, including aggregate
#### a. To have the full joined tables displayed, to have in check which columns belong to which table
  ```sql
SELECT *
FROM kimia_farma.kf_final_transaction fintrans
LEFT JOIN kimia_farma.kf_products prod
on fintrans.product_id=prod.product_id
left join kimia_farma.kf_kantor_cabang cabang
on cast(fintrans.branch_id as string)=cast(cabang.branch_id as string)
limit 5;
```
#### b. find which columns are to be created from calculated fields. Then use CTEs to reduce repetition of queries.
    
```sql

create table kimia_farma.tabel_analisa as
with avail_columns as ( #CTE 1:putting all ready columns mandated
  select 
    fintrans.transaction_id,
    fintrans.date,
    fintrans.branch_id,
    cabang.branch_name,
    cabang.kota,
    cabang.provinsi,
    cabang.rating as rating_cabang,
    fintrans.customer_name,
    fintrans.product_id,
    prod.product_name,
    fintrans.price as actual_price,
    fintrans.discount_percentage,
    fintrans.rating as rating_transaksi
  FROM kimia_farma.kf_final_transaction fintrans
  LEFT JOIN kimia_farma.kf_products prod
  on fintrans.product_id=prod.product_id
  left join kimia_farma.kf_kantor_cabang cabang
  on cast(fintrans.branch_id as string)=cast(cabang.branch_id as string)
  ),

   
calc_field as ( #CTE 2: calling CTE 1, and add the calculated columns
  select *, #calling all column in CTE avail_columns
    case    #adding the calculated fields
     when actual_price <= 50000 then 0.1
     when actual_price < 50000 and actual_price <= 100000 then 0.15
     when actual_price > 100000 and actual_price <= 300000 then 0.20
     when actual_price > 300000 and actual_price <= 500000 then 0.25
     when actual_price > 500000 then 0.3
    end as presentase_gross_laba,

  actual_price * (1 - discount_percentage) as nett_sales

  from avail_columns)

select 
  * except (rating_transaksi),
  nett_sales * presentase_gross_laba AS nett_profit,
  rating_transaksi
from calc_field;
```

### Result Table
<img width="1623" height="605" alt="Screenshot 2026-04-10 215719" src="https://github.com/user-attachments/assets/f1ece53d-5ed5-4093-a700-d605f0ebfe4b" />

### Dashboard
<img width="1274" height="901" alt="Screenshot 2026-04-10 221924" src="https://github.com/user-attachments/assets/8d0e254a-a373-40ce-81e0-de16bf7bd846" />



