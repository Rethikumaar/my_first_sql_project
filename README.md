# 🚀 Retail Sales Data Analysis with SQL

## Project Summary

This project demonstrates a comprehensive analysis of retail transactional data using SQL. It covers the full lifecycle of a data analysis task: database setup, essential data cleaning, exploratory data analysis (EDA), and solving **10 critical business questions** using advanced SQL techniques like aggregation, subqueries, Common Table Expressions (CTEs), and Window Functions.

| Detail | Description |
| :--- | :--- |
| **Database** | `sql_query_p1` |
| **Goal** | Extract actionable insights into sales performance, customer behavior, and time-based trends. |
| **SQL Skills Demonstrated** | `CREATE`, `DELETE`, `GROUP BY`, `SUM`, `AVG`, `COUNT(DISTINCT)`, `DATE\_FORMAT`, `LIMIT`, `CASE`, `WITH` (CTE), `RANK()` (Window Function). |

-----

## 1\. ⚙️ Database Setup & Schema

The project utilizes a single table, `retail_sales`, with the following structure:

```sql
-- create DB
CREATE DATABASE sql_query_p1;

-- use the DB
USE sql_query_p1;

-- create table schema
CREATE TABLE retail_sales
(
    transactions_id INT PRIMARY KEY,
    sale_date DATE,	
    sale_time TIME,
    customer_id INT,	
    gender VARCHAR(10),
    age INT,
    category VARCHAR(35),
    quantity INT,
    price_per_unit FLOAT,	
    cogs FLOAT,
    total_sale FLOAT
);
```

-----

## 2\. 🧹 Data Cleaning and Exploration (EDA)

Initial checks were performed to understand the dataset size and ensure data quality by handling null values.

### 2.1. Null Value Check & Deletion

The following queries were executed to identify and permanently remove records with missing data from the critical sales columns.

```sql
-- Identify records with NULL values
SELECT * FROM retail_sales
WHERE
    transactions_id IS NULL OR sale_date IS NULL OR sale_time IS NULL OR
    gender IS NULL OR category IS NULL OR quantity IS NULL OR
    cogs IS NULL OR total_sale IS NULL;

-- Delete the NULL records 
SET SQL_SAFE_UPDATES = 0; -- Temporarily disable safe updates
DELETE FROM retail_sales
WHERE
    transactions_id IS NULL OR sale_date IS NULL OR sale_time IS NULL OR
    gender IS NULL OR category IS NULL OR quantity IS NULL OR
    cogs IS NULL OR total_sale IS NULL;
-- SET SQL_SAFE_UPDATES = 1; -- Re-enable safe updates (optional)
```

### 2.2. Core Data Metrics

| Metric | SQL Query |
| :--- | :--- |
| **Total Sales Records** | `SELECT COUNT(*) AS total_sale FROM retail_sales;` |
| **Unique Customers** | `SELECT COUNT(DISTINCT customer_id) AS total_sale FROM retail_sales;` |
| **Unique Product Categories** | `SELECT DISTINCT category FROM retail_sales;` |

-----

## 3\. 📊 Business Analysis — 10 Key Queries

The following SQL queries were developed to derive insights from the retail data.

### 3.1. Query 1: Sales on a Specific Date

**Objective:** Retrieve all sales records for a specific day ($\text{2022-11-05}$).

```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

### 3.2. Query 2: Filtered Transactions

**Objective:** Isolate transactions for 'Clothing' category with $\text{quantity} > 4$ during November 2022.

```sql
SELECT 
    *
FROM retail_sales
WHERE 
    category = 'Clothing'
    AND DATE_FORMAT(sale_date, '%Y-%m') = '2022-11' -- Filter by month
    AND quantity > 4;
```

### 3.3. Query 3: Total Sales and Orders by Category

**Objective:** Calculate the **Net Sales (Total Revenue)** and the **Total Orders** for each product category.

```sql
SELECT 
    category,
    SUM(total_sale) AS net_sale,
    COUNT(*) AS total_orders
FROM retail_sales
GROUP BY category;
```

### 3.4. Query 4: Average Customer Age

**Objective:** Find the average age of customers purchasing items from the 'Beauty' category.

```sql
SELECT
    ROUND(AVG(age), 2) AS avg_age
FROM retail_sales
WHERE category = 'Beauty';
```

### 3.5. Query 5: High-Value Transactions

**Objective:**  Identify all individual transactions where the total_sale exceeded 1000.
```sql
SELECT *
FROM retail_sales
WHERE total_sale > 1000;
```

### 3.6. Query 6: Transactions by Category and Gender

**Objective:** Count the total transactions, grouped by product category and gender, to understand purchasing demographics.

```sql
SELECT 
    category,
    gender,
    COUNT(*) AS total_trans
FROM retail_sales
GROUP BY category, gender
ORDER BY category;
```

### 3.7. Query 7: Best-Selling Month per Year (Window Function)

**Objective:** Identify the month in each year with the **highest average sales**, utilizing a **Subquery** and the **`RANK()` Window Function**.

```sql
SELECT 
    year,
    month,
    avg_sale
FROM 
(
    SELECT 
        YEAR(sale_date) AS year,
        MONTH(sale_date) AS month,
        AVG(total_sale) AS avg_sale,
        RANK() OVER (PARTITION BY YEAR(sale_date) ORDER BY AVG(total_sale) DESC) AS rnk
    FROM retail_sales
    GROUP BY YEAR(sale_date), MONTH(sale_date)
) AS t1
WHERE rnk = 1; -- Selects only the highest ranked month for each year
```

### 3.8. Query 8: Top 5 Customers by Total Spending

**Objective:** Identify the **Top 5 High-Value Customers (HVCs)** based on their cumulative total sales.

```sql
SELECT 
    customer_id,
    SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;
```

### 3.9. Query 9: Unique Customer Count per Category

**Objective:** Find the number of unique customers engaging with each product category.

```sql
SELECT 
    category,
    COUNT(DISTINCT customer_id) AS cnt_unique_cs
FROM retail_sales
GROUP BY category;
```

### 3.10. Query 10: Orders by Time-of-Day Shift (CTE)

**Objective:** Categorize transactions into operational shifts (Morning, Afternoon, Evening) and count total orders per shift using a **Common Table Expression (CTE)**.

```sql
WITH hourly_sale AS
(
    SELECT *,
        CASE
            WHEN HOUR(sale_time) < 12 THEN 'Morning'
            WHEN HOUR(sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
            ELSE 'Evening'
        END AS shift
    FROM retail_sales
)
SELECT 
    shift,
    COUNT(*) AS total_orders
FROM hourly_sale
GROUP BY shift;
```

-----

## 4\. 💡 Key Insights & Conclusion

This analysis provides the foundation for several strategic business decisions:

  * **Peak Demand:** The time-of-day analysis (Query 10) indicates which shifts experience the highest order volume, allowing for optimal **staff scheduling** and resource allocation.
  * **Customer Segmentation:** Identifying the **Top 5 spending customers** (Query 8) enables the creation of targeted loyalty and retention programs.
  * **Category Performance:** Metrics on **Net Sales and Unique Customers** per category (Queries 3 and 9) confirm the highest-performing product lines, guiding inventory and marketing focus.
  * **Seasonal Planning:** The identification of best-selling months (Query 7) is crucial for planning **seasonal promotions** and managing inventory fluctuations.

This project successfully demonstrates the ability to execute end-to-end data analysis, translating raw data and business requirements into effective SQL solutions and actionable insights.

-----

## 5\. 🧑‍💻 Author

GitHub	https://github.com/Rethikumaar
LinkedIn	https://linkedin.com/in/rethi-kumaar
