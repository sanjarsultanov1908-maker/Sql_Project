# Retail Sales Analysis (SQL Project)

A SQL project that walks through a typical data analyst workflow on retail sales data: creating the database, cleaning the data, exploring it, and answering business questions with SQL queries.

## Project Overview

- **Project:** Retail Sales Analysis
- **Database:** `sql_project_p2`
- **Tool:** SQL (PostgreSQL)
- **Level:** Beginner

## Objectives

1. **Set up a retail sales database:** create the database and the `retail_sales` table, then load the data.
2. **Data cleaning:** find and remove records with missing values.
3. **Exploratory data analysis (EDA):** get a basic understanding of the dataset (number of sales, unique customers, categories).
4. **Business analysis:** answer 10 business questions using SQL.

## Project Structure

### 1. Database Setup

```sql
CREATE DATABASE sql_project_p2;

DROP TABLE IF EXISTS retail_sales;
CREATE TABLE retail_sales
(
    transaction_id INT PRIMARY KEY,
    sale_date DATE,
    sale_time TIME,
    customer_id INT,
    gender VARCHAR(15),
    age INT,
    category VARCHAR(15),
    quantity INT,
    price_per_unit FLOAT,
    cogs FLOAT,
    total_sale FLOAT
);
```

### 2. Data Exploration & Cleaning

- **Record count:** total number of records in the dataset.
- **Customer count:** number of unique customers.
- **Category list:** all unique product categories.
- **Null check:** find records with missing values and delete them.

```sql
SELECT COUNT(*) FROM retail_sales;
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;
SELECT DISTINCT category FROM retail_sales;

SELECT * FROM retail_sales
WHERE transaction_id IS NULL OR sale_date IS NULL OR sale_time IS NULL
   OR gender IS NULL OR category IS NULL OR quantity IS NULL
   OR cogs IS NULL OR total_sale IS NULL;

DELETE FROM retail_sales
WHERE transaction_id IS NULL OR sale_date IS NULL OR sale_time IS NULL
   OR gender IS NULL OR category IS NULL OR quantity IS NULL
   OR cogs IS NULL OR total_sale IS NULL;
```

### 3. Data Analysis & Business Questions

**Q1. Retrieve all columns for sales made on `2022-11-05`.**
```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

**Q2. Retrieve all transactions where the category is `Clothing` and the quantity sold is 4 or more in November 2022.**
```sql
SELECT *
FROM retail_sales
WHERE category = 'Clothing'
  AND TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
  AND quantity >= 4;
```

**Q3. Calculate the total sales for each category.**
```sql
SELECT
    category,
    SUM(total_sale) AS net_sale,
    COUNT(*) AS total_orders
FROM retail_sales
GROUP BY 1;
```

**Q4. Find the average age of customers who purchased items from the `Beauty` category.**
```sql
SELECT ROUND(AVG(age), 2) AS avg_age
FROM retail_sales
WHERE category = 'Beauty';
```

**Q5. Find all transactions where `total_sale` is greater than 1000.**
```sql
SELECT *
FROM retail_sales
WHERE total_sale > 1000;
```

**Q6. Find the total number of transactions made by each gender in each category.**
```sql
SELECT
    category,
    gender,
    COUNT(*) AS total_trans
FROM retail_sales
GROUP BY category, gender
ORDER BY 1;
```

**Q7. Calculate the average sale for each month and find the best-selling month in each year.**
```sql
SELECT year, month, avg_sale
FROM (
    SELECT
        EXTRACT(YEAR FROM sale_date) AS year,
        EXTRACT(MONTH FROM sale_date) AS month,
        AVG(total_sale) AS avg_sale,
        RANK() OVER (
            PARTITION BY EXTRACT(YEAR FROM sale_date)
            ORDER BY AVG(total_sale) DESC
        ) AS rank
    FROM retail_sales
    GROUP BY 1, 2
) AS t1
WHERE rank = 1;
```

**Q8. Find the top 5 customers based on the highest total sales.**
```sql
SELECT
    customer_id,
    SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;
```

**Q9. Find the number of unique customers who purchased items from each category.**
```sql
SELECT
    category,
    COUNT(DISTINCT customer_id) AS cnt_unique_cs
FROM retail_sales
GROUP BY category;
```

**Q10. Create shifts and count the orders in each (Morning < 12, Afternoon 12-17, Evening > 17).**
```sql
WITH hourly_sale AS (
    SELECT *,
        CASE
            WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
            WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
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

## SQL Concepts Used

- `CREATE`, `DROP`, `DELETE`
- `SELECT`, `WHERE`, `ORDER BY`, `LIMIT`
- Aggregate functions: `COUNT`, `SUM`, `AVG`, `ROUND`
- `GROUP BY`, `COUNT(DISTINCT ...)`
- Date and time functions: `EXTRACT`, `TO_CHAR`
- Subqueries and CTEs (`WITH`)
- Window functions: `RANK() OVER (PARTITION BY ...)`
- `CASE WHEN` for conditional logic

## Findings

- **Customers:** the dataset covers customers of different ages and genders across several product categories.
- **High-value sales:** several transactions exceed 1000 in total sale, showing a premium customer segment.
- **Sales trends:** the monthly analysis shows the best-selling month in each year.
- **Customer insights:** the analysis highlights the top-spending customers and the most popular categories.

## How to Use

1. **Clone the repository:**
   ```bash
   git clone <your-repository-url>
   ```
2. **Set up the database:** run the table creation script from `sql_query.sql` in PostgreSQL.
3. **Load the data:** import the dataset into the `retail_sales` table.
4. **Run the queries:** execute the analysis queries one by one and check the results.

## Repository Structure

```
├── README.md
├── sql_query.sql        # all SQL queries (setup, cleaning, analysis)
└── SQL - Retail Sales Analysis_utf.csv   # dataset (if included)
```

## Author

**<Your Name>**
Telegram: <your-channel-link>
GitHub: <your-github-link>

If you find this project useful, give it a star ⭐
