# Big Data Analysis
1. Data validation focusing on primary and foreign keys, as well as important metrics such as price and rating.

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

2. 
