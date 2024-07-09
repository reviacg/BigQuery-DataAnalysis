# BigQuery-DataAnalysis
CREATE OR REPLACE TABLE kimia_farma.kf_analysis_table1 AS
SELECT
  t.transaction_id,
  t.date,
  c.branch_id,
  c.branch_name,
  c.kota,
  c.provinsi,
  c.rating AS rating_cabang,
  t.customer_name,
  p.product_id,
  p.product_name,
  p.price AS actual_price,
  t.discount_percentage,
  CASE
    WHEN p.price <= 50000 THEN 0.10
    WHEN p.price > 50000 AND p.price <= 100000 THEN 0.15
    WHEN p.price > 100000 AND p.price <= 300000 THEN 0.20
    WHEN p.price > 300000 AND p.price <= 500000 THEN 0.25
    WHEN p.price > 500000 THEN 0.30
  END AS percentage_gross_laba,
  (p.price - (p.price * t.discount_percentage)) AS nett_sales,
    CASE
    WHEN p.price <= 50000 THEN p.price * 0.10
    WHEN p.price > 50000 AND p.price <= 100000 THEN p.price * 0.15
    WHEN p.price > 100000 AND p.price <= 300000 THEN p.price * 0.20
    WHEN p.price > 300000 AND p.price <= 500000 THEN p.price * 0.25
    WHEN p.price > 500000 THEN p.price * 0.30
  END AS nett_profit,
  t.rating AS rating_transaksi,
FROM
  kimia_farma.kf_final_transaction t
JOIN
  kimia_farma.kf_kantor_cabang c ON t.branch_id = c.branch_id
JOIN
  kimia_farma.kf_product p ON t.product_id = p.product_id
JOIN
  kimia_farma.kf_inventory i ON t.product_id = i.product_id AND t.branch_id = i.branch_id;
