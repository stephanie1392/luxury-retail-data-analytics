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
