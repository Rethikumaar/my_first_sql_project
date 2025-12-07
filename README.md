# 1. Retail Data Insights Using SQL

## 2. Project Overview
This project demonstrates how SQL can be used to analyze retail sales data effectively.  
It covers database creation, data cleaning, exploratory data analysis, and answering 10 business-related questions using SQL.

**Database:** `p1_retail_db`  
**Skills Demonstrated:** SQL, Data Cleaning, Aggregation, Window Functions, EDA

---

## 3. Project Objectives
1. Create and structure a retail sales SQL database  
2. Clean and validate raw data  
3. Explore data using SQL queries  
4. Answer 10 business questions using SQL  
5. Generate insights for trends, customer behavior, and sales performance  

---

# 4. Database Setup

## 4.1 Create Database & Table
```sql
CREATE DATABASE p1_retail_db;

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
5. Data Exploration & Cleaning
5.1 Total Records
sql
Copy code
SELECT COUNT(*) FROM retail_sales;
5.2 Unique Customers
sql
Copy code
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;
5.3 Unique Categories
sql
Copy code
SELECT DISTINCT category FROM retail_sales;
5.4 Identify NULL Records
sql
Copy code
SELECT *
FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR
    gender IS NULL OR age IS NULL OR category IS NULL OR
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;
5.5 Delete NULL Records
sql
Copy code
DELETE FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR
    gender IS NULL OR age IS NULL OR category IS NULL OR
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;
6. Business Analysis — SQL Queries (All 10)
Below are the 10 business questions and their SQL solutions.

6.1 Query 1 — Retrieve all sales made on 2022-11-05
sql
Copy code
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
6.2 Query 2 — Clothing transactions with quantity > 4 in Nov 2022
sql
Copy code
SELECT *
FROM retail_sales
WHERE 
    category = 'Clothing'
    AND DATE_FORMAT(sale_date, '%Y-%m') = '2022-11'
    AND quantity > 4;
6.3 Query 3 — Total sales & order count per category
sql
Copy code
SELECT 
    category,
    SUM(total_sale) AS net_sale,
    COUNT(*) AS total_orders
FROM retail_sales
GROUP BY category;
6.4 Query 4 — Average age of Beauty product customers
sql
Copy code
SELECT 
    ROUND(AVG(age), 2) AS avg_age
FROM retail_sales
WHERE category = 'Beauty';
6.5 Query 5 — Transactions where total_sale > 1000
sql
Copy code
SELECT *
FROM retail_sales
WHERE total_sale > 1000;
6.6 Query 6 — Total transactions by gender for each category
sql
Copy code
SELECT 
    category,
    gender,
    COUNT(*) AS total_trans
FROM retail_sales
GROUP BY category, gender
ORDER BY category;
6.7 Query 7 — Best-selling month of each year (highest avg sale)
sql
Copy code
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
WHERE rnk = 1;
6.8 Query 8 — Top 5 customers by total spending
sql
Copy code
SELECT 
    customer_id,
    SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;
6.9 Query 9 — Unique customer count for each category
sql
Copy code
SELECT 
    category,
    COUNT(DISTINCT customer_id) AS cnt_unique_cs
FROM retail_sales
GROUP BY category;
6.10 Query 10 — Create shift categories & count orders
sql
Copy code
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
7. Key Insights
Clothing and Beauty categories show strong sales performance

Several customers consistently make high-value purchases

Evening hours record higher order activity

Seasonal variations indicate high-performing months

Top customers significantly impact revenue

8. Conclusion
This project demonstrates hands-on capability in:

SQL database creation

Data cleaning & preprocessing

Aggregations and filtering

Window functions

Solving real business questions

Trend identification & customer insights

A strong addition to a Data Analyst portfolio.

9. Author
P. Rethi Kumaar
Information Science Engineering | CGPA: 7.8
Aspiring Data Analyst & Developer

GitHub: https://github.com/JackieRK
LinkedIn: https://linkedin.com/in/rethi-kumaar
