# Cloud Data Analytics Portfolio: Luxury E-Commerce Demand & Inventory Optimization

**Candidate:** Stephanie Katara
**Academic Background:** B.Sc. IT | Master's in Fashion Luxury Goods Management (Paris)  
**Target Roles:** Junior Data Analyst, Retail Analyst, E-Commerce Operations Coordinator

---

## 1. Project Overview & Business Scenario
This project simulates an end-to-end data pipeline for an international luxury fashion house facing two critical operational challenges: 
1. **Targeted CRM Marketing:** Identifying high-value VIP customer segments across global markets.
2. **Supply Chain Efficiency:** Isolating stagnant, slow-moving warehouse inventory (dead stock) that ties up operational capital.

### Data Architecture
`Raw Transactional CSV Data` ➔ `Google BigQuery Cloud Warehouse` ➔ `Google Connected Sheets BI Dashboard`

---

## 2. Phase 1: Business Intelligence Dashboarding (Google Connected Sheets)
Using a live Google BigQuery data connector, raw transactional data streams directly into an agile spreadsheet environment. Text data anomalies (case mismatches and trailing whitespaces) were engineered using `TRIM()` and `PROPER()` functions. A dynamic Pivot Table and interactive conditional Slicers were deployed to calculate and filter global luxury revenue metrics.

![Spreadsheet Dashboard](spreadsheet_dashboard.png)

### Business Outcome
Brand directors can filter regional marketplace performance on-demand without needing to write backend queries, allowing for rapid commercial strategy pivots.

---

## 3. Phase 2: Data Segmentation & Inventory Balancing (GoogleSQL)

### Query 1: High-Value Customer Segmentation (VIP Tracking)
**Objective:** Calculate Customer Lifetime Value (CLV) to isolate international buyers who have spent a cumulative total exceeding €2,000.

```sql
SELECT 
    customer_name,
    country,
    SUM(order_amount) AS total_lifetime_spend
FROM `luxury_retail_analysis.sales_transactions`
GROUP BY customer_name, country
HAVING total_lifetime_spend > 2000
ORDER BY total_lifetime_spend DESC;
```

![VIP Customer Query Results](sql_vip_customers.png)

**Business Outcome:** Extracted a high-priority customer tier list containing 5 key international shoppers. This allows luxury brand management teams to deploy exclusive VIP loyalty outreach initiatives.

---

### Query 2: Warehouse Dead Stock Analysis
**Objective:** Evaluate supply chain indicators to identify products where current physical stock levels are critically saturated (>100 units) but global marketplace demand is stagnant (<10 units sold).

```sql
SELECT 
    product_name,
    category,
    stock_left,
    SUM(quantity_sold) AS total_units_sold
FROM `luxury_retail_analysis.sales_transactions`
WHERE stock_left > 100
GROUP BY product_name, category, stock_left
HAVING total_units_sold < 10
ORDER BY stock_left DESC;
```

![Dead Stock Query Results](sql_dead_stock.png)

**Business Outcome:** Successfully isolated "Suede Ankle Boots" as a high-risk operational liability (115 items in warehouse stock with only 4 historical units sold). This automated script triggers clear signals for procurement managers to halt production and apply e-commerce markdown strategies.

---

## 4. Technical Skillsets Validated
* **Cloud Platforms:** Google Cloud Platform (GCP), Google BigQuery Console.
* **Database Languages:** GoogleSQL (Data Aggregation, Filtering, Joins, Aliases).
* **Business Intelligence Tools:** Connected Sheets, Advanced Pivot Tables, Slicers, Data Cleaning.

* ---

## 5. Phase 3: Large-Scale Retail Production Database Audit (Full 10-Question Script)
**Author:** Stephanie Katara  
**Database Volume:** 99,441 Real Transactional Records  
**Environment:** Google BigQuery (GoogleSQL)

Below is the complete, production-ready script executing 10 critical commercial data requests across the retail infrastructure.

---- Q1. Total Orders
-- Business Question:
-- How many total orders are there?
  ==========================================

-- syntax:
```
SELECT COUNT(*) AS total_orders
FROM `project-05bd422f-db01-475b-9b2.retail_analysis.orders`;
```

* ** Result:
-- 99,441


--------------------------------------------------


  ==========================================
-- Q2. Orders by Status
-- Business Question:
-- How many orders are in each status?
  ==========================================

-- syntax:

```
SELECT
    order_status,
    COUNT(*) AS total_orders
FROM `project-05bd422f-db01-475b-9b2.retail_analysis.orders`
GROUP BY order_status
ORDER BY total_orders DESC;
```

-- Result:
-- delivered   96478
-- shipped     1107
-- canceled    625
-- unavailable 609
-- invoiced    314
-- processing  301
-- created     5
-- approved    2

  ==========================================
-- Q3. Overall Business Summary
-- Business Question:
-- What is the overall performance of the business?
  ==========================================

Purpose:
Get key business metrics in one query.

Tables and columns Used:
- orders → order_id, customer_id
- order_items → price, freight_value

--syntax:

```
SELECT
    count(DISTINCT o.order_id) as total_orders,
    count(DISTINCT customer_id) as unique_customers,
    sum(oi.price),
    sum(oi.freight_value),
    avg(oi.price)
FROM `project-05bd422f-db01-475b-9b2.retail_analysis.orders` AS o
JOIN `project-05bd422f-db01-475b-9b2.retail_analysis.order_items` AS oi
ON o.order_id = oi.order_id
```

Functions Used:
- COUNT(DISTINCT order_id) → Counts unique orders
- COUNT(DISTINCT customer_id) → Counts unique customers
- SUM(price) → Total sales
- SUM(freight_value) → Total shipping cost
- AVG(price) → Average item price

JOIN:
orders and order_items joined using:
order_id

Important Note:
After JOIN, one order can appear multiple times because
one order can contain multiple items.

Example:
Order 1001
- Item 1
- Item 2
- Item 3

COUNT(*) would count 3 rows,
but there is only 1 order.

Use:
COUNT(DISTINCT order_id)
to count actual orders.

-- Result:
-- total_orders       98666 
-- unique_customers   98666
-- total_sales        13591643.70
-- total_freight      2251909.53
-- average_item_price 120.65

  ==========================================
-- Q4. Sales by Order Status
-- Business Question:
-- How much sales revenue comes from each order status?
  ==========================================

--syntax:

```
SELECT 
    o.order_status,
    count(DISTINCT o.order_id) as total_orders,
    sum(oi.price),
    avg(oi.price),
FROM `project-05bd422f-db01-475b-9b2.retail_analysis.orders` AS o
JOIN `project-05bd422f-db01-475b-9b2.retail_analysis.order_items` AS oi
ON o.order_id = oi.order_id
group by o.order_status
```

Purpose:
Analyze orders and revenue by different order statuses.

Tables Used:
- orders → order_id, order_status
- order_items → price

SQL Concepts Used:
- JOIN → Combine orders and order_items using order_id
- GROUP BY → Create separate groups for each order status
- COUNT(DISTINCT order_id) → Count unique orders
- SUM(price) → Calculate total sales
- AVG(price) → Calculate average item price

Important Note:

GROUP BY creates separate groups:
- delivered
- canceled
- shipped
- etc.

Then aggregate functions calculate values inside each group.

Example:
COUNT(DISTINCT order_id)
counts unique orders within each status group.


|-- Result:

| order_status | total_orders | total_sales  | average_item_price |
|--------------|-------------:|------------: |-------------------:|
| approved     | 2            | 209.60       | 69.87              |
| canceled     | 461          | 95,235.27    | 175.71             |
| delivered    | 96,478       | 13,221,498.11| 119.98             |
| invoiced     | 312          | 61,526.37    | 171.38             |
| processing   | 301          | 60,439.22    | 169.30             |
| shipped      | 1,106        | 150,727.44   | 127.20             |
| unavailable  | 6            | 2,007.69     | 286.81             |

-- Insight:
-- Delivered orders generate the highest sales revenue.
-- Canceled orders have fewer orders but a higher average item price.
-- INNER JOIN may exclude orders without matching order_items.

  ==========================================
-- Q5. Top-Selling Products
-- Business Question:
-- Which products generate the most sales?
  ==========================================


-- Purpose:
-- Identify products with the highest revenue
-- and understand which products perform best.


-- Table Used:
-- order_items
--
-- Columns Used:
-- product_id → Identifies each product
-- order_item_id → Counts items sold
-- price → Calculates sales revenue


-- SQL Concepts Used:
-- COUNT() → Counts number of items
-- SUM() → Calculates total sales
-- GROUP BY → Creates groups for each product
-- ORDER BY → Sorts results
-- DESC → Shows highest values first
-- LIMIT → Shows top results only


-- SQL Syntax:

```
SELECT
    product_id,
    COUNT(DISTINCT order_item_id) AS items_sold,
    SUM(price) AS total_sales
FROM table_name
GROUP BY product_id
ORDER BY total_sales DESC
LIMIT 10;
```

-- Result:

| Rank | Product ID | Items Sold | Total Sales |
|------|------------|------------|-------------|
| 1 | bb50f2e236e5eea0100680137654686c | 5 | 63,885.00 |
| 2 | 6cdd53843498f92890544667809f1595 | 3 | 54,730.20 |
| 3 | d6160fb7873f184099d9bc95e30376af | 1 | 48,899.34 |
| 4 | d1c427060a0f73f6b889a5c7c61f2ac4 | 3 | 47,214.51 |
| 5 | 99a4788cb24856965c36a24e339b6058 | 3 | 43,025.56 |
| 6 | 3dd2a17168ec895c781a9191c1e95ad7 | 6 | 41,082.60 |
| 7 | 25c38557cf793876c5abdd5931f922db | 2 | 38,907.32 |
| 8 | 5f504b3a1c75b73d6151be81eb05bdc9 | 2 | 37,733.90 |
| 9 | 53b36df67ebb7c41585e8d54d6772e08 | 5 | 37,683.42 |
| 10 | aca2eb7d00ea1a7b8ebd4e68314663af | 4 | 37,608.90 |


  ===========================================
-- Q5. Top-Selling Products
-- Business Question:
-- Which products generate the most sales?
  ===========================================


-- Purpose:
-- Identify products with the highest revenue
-- and understand which products perform best.


-- Table Used:
-- order_items
--
-- Columns Used:
-- product_id → Identifies each product
-- order_item_id → Counts items sold
-- price → Calculates sales revenue


-- SQL Concepts Used:
-- COUNT() → Counts number of items
-- SUM() → Calculates total sales
-- GROUP BY → Creates groups for each product
-- ORDER BY → Sorts results
-- DESC → Shows highest values first
-- LIMIT → Shows top results only


== SQL Syntax:

```
SELECT
    product_id,
    COUNT(DISTINCT order_item_id) AS items_sold,
    SUM(price) AS total_sales
FROM table_name
GROUP BY product_id
ORDER BY total_sales DESC
LIMIT 10;
```

-- Result:

| Rank | Product ID | Items Sold | Total Sales |
|------|------------|------------|-------------|
| 1 | bb50f2e236e5eea0100680137654686c | 5 | 63,885.00 |
| 2 | 6cdd53843498f92890544667809f1595 | 3 | 54,730.20 |
| 3 | d6160fb7873f184099d9bc95e30376af | 1 | 48,899.34 |
| 4 | d1c427060a0f73f6b889a5c7c61f2ac4 | 3 | 47,214.51 |
| 5 | 99a4788cb24856965c36a24e339b6058 | 3 | 43,025.56 |
| 6 | 3dd2a17168ec895c781a9191c1e95ad7 | 6 | 41,082.60 |
| 7 | 25c38557cf793876c5abdd5931f922db | 2 | 38,907.32 |
| 8 | 5f504b3a1c75b73d6151be81eb05bdc9 | 2 | 37,733.90 |
| 9 | 53b36df67ebb7c41585e8d54d6772e08 | 5 | 37,683.42 |
| 10 | aca2eb7d00ea1a7b8ebd4e68314663af | 4 | 37,608.90 |


-- Insight:
-- The highest-selling product generated 63,885 in sales.
-- A product does not need the highest quantity sold
-- to generate the highest revenue.
-- Sales depend on both quantity and product price.


-- Important Note:
-- ORDER BY total_sales DESC ranks products
-- from highest revenue to lowest revenue.
-- LIMIT 10 shows only the top 10 products.

  ==========================================
-- Q6. Product Category Analysis
-- Business Question:
-- Which product categories generate the most sales?
  ==========================================


-- Purpose:
-- Identify the best-performing product categories
-- based on revenue generated.


-- Tables Used:

-- products
-- product_id → Used for joining tables
-- product_category_name → Product category

-- order_items
-- product_id → Used for joining tables
-- order_item_id → Counts items sold
-- price → Calculates sales


-- SQL Concepts Used:
-- JOIN → Combines products and order_items tables
-- COUNT() → Counts items sold
-- SUM() → Calculates total sales
-- AVG() → Calculates average price
-- GROUP BY → Calculates metrics per category
-- ORDER BY → Ranks categories by sales

== SQL Syntax:

```
SELECT
    p.product_category_name,
    COUNT(DISTINCT oi.order_item_id) AS items_sold,
    SUM(oi.price) AS total_sales,
    AVG(oi.price) AS average_price

FROM products AS p

JOIN order_items AS oi
ON p.product_id = oi.product_id

GROUP BY p.product_category_name

ORDER BY total_sales DESC;
```

-- Result:

|Rank|  Category               |Items Sold |  Total Sales  |Average Price|
|----|-------------------------|-----------|---------------|-------------|
| 1  | beleza_saude            | 21        | 1,258,681.34  | 130.16 |
| 2  | relogios_presentes      | 12        | 1,205,005.68  | 201.14 |
| 3  | cama_mesa_banho         | 11        | 1,036,988.68  | 93.30  |
| 4  | esporte_lazer           | 7         | 988,048.97    | 114.34 |
| 5  | informatica_acessorios  | 20        | 911,954.32    | 116.51 |
| 6  | moveis_decoracao        | 15        | 729,762.49    | 87.56  |
| 7  | cool_stuff              | 7         | 635,290.85    | 167.36 |
| 8  | utilidades_domesticas   | 12        | 632,248.66    | 90.79  |
| 9  | automotivo              | 20        | 592,720.11    | 139.96 |
| 10 | ferramentas_jardim      | 15        | 485,256.46    | 111.63 |
 

-- Insight:

-- Beleza_saude generated the highest revenue
-- with approximately 1.26 million in sales.

-- Relogios_presentes had the highest average price,
-- showing that higher-priced products can generate
-- high revenue with fewer items sold.
-- Revenue depends on both quantity sold and product price.


  ===========================================
-- Q7. Top Customers by Spending
-- Business Question:
-- Which customers spend the most money?
 ============================================


-- Purpose:
-- Identify the highest-value customers based on
-- total amount spent.


-- Tables Used:

-- orders
-- customer_id → Customer identifier
-- order_id → Used to join tables

-- order_items
-- order_id → Used to join tables
-- price → Calculates customer spending


-- SQL Concepts Used:
-- JOIN → Combines orders and order_items
-- COUNT(DISTINCT) → Counts unique orders
-- SUM() → Calculates total spending
-- GROUP BY → Groups data by customer
-- ORDER BY → Ranks customers by spending


== SQL Syntax:

```
SELECT
    o.customer_id,
    COUNT(DISTINCT o.order_id) AS total_orders,
    SUM(oi.price) AS total_spent

FROM orders AS o

JOIN order_items AS oi
ON o.order_id = oi.order_id

GROUP BY o.customer_id

ORDER BY total_spent DESC;
```

--Result:

| Rank | Customer ID                      | Total Orders | Total Spent |
| ---- | -------------------------------- | -----------: | ----------: |
| 1    | 1617b1357756262bfa56ab541c47bc16 |            1 |   13,440.00 |
| 2    | ec5b2ba62e574342386871631fafd3fc |            1 |    7,160.00 |
| 3    | c6e2731c5b391845f6800c97401a43a9 |            1 |    6,735.00 |
| 4    | f48d464a0baaea338cb25f816991ab1f |            1 |    6,729.00 |
| 5    | 3fd6777bbce08a352fddd04e4a7cc8f6 |            1 |    6,499.00 |
| 6    | 05455dfa7cd02f13d132aa7a6a9729c6 |            1 |    5,934.60 |
| 7    | df55c14d1476a9a3467f131269c2477f |            1 |    4,799.00 |
| 8    | 24bbf5fd2f2e1b359ee7de94defc4a15 |            1 |    4,690.00 |
| 9    | e0a2412720e9ea4f26c1ac985f6a7358 |            1 |    4,599.90 |
| 10   | 3d979689f636322c62418b6346b1c6d2 |            1 |    4,590.00 |

--Insight:

-- The highest-spending customer spent 13,440.00 in a single order.
-- The top 10 customers each placed one order, but those orders had a high purchase value.
-- This suggests that large one-time purchases contributed significantly to the highest customer spending.

 ==========================================
-- Q8. Top Sellers by Sales
-- Business Question:
-- Which sellers generate the highest sales?
==========================================


-- Purpose:
-- Identify the top-performing sellers based on
-- total sales revenue.


-- Tables Used:

-- order_items
-- seller_id → Seller identifier
-- order_item_id → Counts items sold
-- price → Calculates total sales


-- SQL Concepts Used:
-- COUNT()
-- SUM()
-- GROUP BY
-- ORDER BY
-- LIMIT


== SQL Syntax:

```
SELECT
    seller_id,
    COUNT(order_item_id) AS items_sold,
    SUM(price) AS total_sales
FROM order_items
GROUP BY seller_id
ORDER BY total_sales DESC
LIMIT 10;
```

--Results:

| Rank | Seller ID   | Items Sold | Total Sales |
| ---- | ----------- | ---------: | ----------: |
| 1    | 4869f7a5... |       1156 |  229,472.63 |
| 2    | 53243585... |        410 |  222,776.05 |
| 3    | 4a3ca931... |       1987 |  200,472.92 |
| 4    | fa1c13f2... |        586 |  194,042.03 |
| 5    | 7c67e144... |       1364 |  187,923.89 |
| 6    | 7e93a43e... |        340 |  176,431.87 |
| 7    | da8622b1... |       1551 |  160,236.57 |
| 8    | 7a67c85e... |       1171 |  141,745.53 |
| 9    | 1025f0e2... |       1428 |  138,968.55 |
| 10   | 955fee92... |       1499 |  135,171.70 |

--Insight
--The top seller generated approximately 229,472.63 in total sales.
--Sellers with fewer items sold can still rank highly if they sell higher-value products.
--Total sales depend on both sales volume and product price, not just the number of items sold.

======================================================================
--Q9. Average Delivery Time
--Business Question
--What is the average delivery time for delivered orders?
--Purpose
--Measure how long it takes customers to receive their orders after purchase.
======================================================================

--Tables Used
--orders
--order_purchase_timestamp → Purchase date
--order_delivered_customer_date → Delivery date
--order_status → Filter only delivered orders

--SQL Concepts Used
--AVG()
--DATE_DIFF()
--WHERE

== SQL Syntax:

```
SELECT
    AVG(
        DATE_DIFF(
            order_delivered_customer_date,
            order_purchase_timestamp,
            DAY
        )
    ) AS average_delivery_days
FROM orders
WHERE order_status = 'delivered';
```

--Result:
--Average Delivery Days
--12.09 days

--Insight
--On average, delivered orders reached customers in about 12.09 days.
--This metric can be used to monitor delivery performance and identify opportunities to improve shipping speed


============================================
-- Q10. Monthly Sales Trend
-- Business Question:
-- How do total sales change month by month?
============================================

-- Purpose:
-- Analyze monthly sales performance.
-- Identify high- and low-performing months.
-- Observe monthly sales trends.

-- Tables Used:
-- orders
-- order_id → Join with order_items
-- order_purchase_timestamp → Extract purchase month

-- order_items
-- order_id → Join with orders
-- price → Calculate total monthly sales

--SQL Concepts Used:
-- JOIN
-- EXTRACT()
-- SUM()
-- GROUP BY
-- ORDER BY

== SQL Syntax:

```
SELECT
    EXTRACT(MONTH FROM o.order_purchase_timestamp) AS month,
    SUM(oi.price) AS total_monthly_sales
FROM orders AS o
JOIN order_items AS oi
ON o.order_id = oi.order_id
GROUP BY month
ORDER BY month ASC;
```

--Results:

Month | Total Monthly Sales
1     |   1,070,343.23
2     |   1,091,481.73
3     |   1,357,557.74
4     |   1,356,574.98
5     |   1,502,588.82
6     |   1,298,162.91
7     |   1,393,538.70
8     |   1,428,658.01
9     |   624,814.05
10    |   713,727.09
11    |   1,010,271.37
12    |   743,925.07

--Insights:
-- May (Month 5) generated the highest total sales (≈ 1.50 million).
-- September (Month 9) recorded the lowest total sales (≈ 624.8 thousand).
-- Sales remained relatively strong from March to August.
-- Sales declined in September and October before recovering in November.


