# 13. Window Functions - Advanced Analytics

## 🎯 Learning Objectives
- Understand window functions vs aggregate functions
- Master ROW_NUMBER, RANK, DENSE_RANK
- Learn LAG/LEAD for time-series analysis
- Use cumulative functions for running totals
- Practice ranking and partitioning

## 🪟 What are Window Functions?

Window functions perform calculations across a set of rows related to the current row.

**Key Difference:**
- **Aggregate functions** collapse rows into one
- **Window functions** return a value for each row

## 📊 Window Function Syntax

```sql
SELECT 
    column1,
    column2,
    function() OVER (
        PARTITION BY column3
        ORDER BY column4
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) as window_result
FROM table_name;
```

## 🏆 Ranking Functions

### ROW_NUMBER()
Assigns a unique sequential number to each row.

```sql
SELECT 
    name,
    salary,
    ROW_NUMBER() OVER (ORDER BY salary DESC) as rank
FROM employees;

-- Result
+---------+--------+------+
|  name   | salary | rank |
+---------+--------+------+
| Bob     | 80000  |  1   |
| Alice   | 75000  |  2   |
| Charlie | 60000  |  3   |
+---------+--------+------+
```

### RANK()
Assigns rank with gaps for ties.

```sql
SELECT 
    name,
    salary,
    RANK() OVER (ORDER BY salary DESC) as rank
FROM employees;

-- Result (notice gaps)
+---------+--------+------+
|  name   | salary | rank |
+---------+--------+------+
| Bob     | 80000  |  1   |
| Alice   | 80000  |  1   |
| Charlie | 60000  |  3   |
+---------+--------+------+
```

### DENSE_RANK()
Assigns rank without gaps.

```sql
SELECT 
    name,
    salary,
    DENSE_RANK() OVER (ORDER BY salary DESC) as rank
FROM employees;

-- Result (no gaps)
+---------+--------+------+
|  name   | salary | rank |
+---------+--------+------+
| Bob     | 80000  |  1   |
| Alice   | 80000  |  1   |
| Charlie | 60000  |  2   |
+---------+--------+------+
```

### NTILE()
Divides rows into buckets.

```sql
-- Divide employees into 3 salary groups
SELECT 
    name,
    salary,
    NTILE(3) OVER (ORDER BY salary DESC) as salary_group
FROM employees;
```

## 🔄 LAG and LEAD

### LAG()
Access data from previous row.

```sql
SELECT 
    month,
    sales,
    LAG(sales) OVER (ORDER BY month) as prev_sales,
    sales - LAG(sales) OVER (ORDER BY month) as change
FROM monthly_sales;

-- Result
+-------+-------+------------+--------+
| month | sales | prev_sales | change |
+-------+-------+------------+--------+
|  1    | 1000  |   NULL     |  NULL  |
|  2    | 1200  |   1000     |  200   |
|  3    | 1100  |   1200     |  -100  |
+-------+-------+------------+--------+
```

### LEAD()
Access data from next row.

```sql
SELECT 
    employee,
    hire_date,
    LEAD(hire_date) OVER (ORDER BY hire_date) as next_hire
FROM employees;
```

## 📈 Cumulative Functions

### Running Totals
```sql
SELECT 
    date,
    amount,
    SUM(amount) OVER (ORDER BY date) as running_total
FROM transactions;

-- Result
+------------+--------+---------------+
|    date    | amount | running_total |
+------------+--------+---------------+
| 2024-01-01 |  100   |     100       |
| 2024-01-02 |  200   |     300       |
| 2024-01-03 |  150   |     450       |
+------------+--------+---------------+
```

### Moving Averages
```sql
-- 3-day moving average
SELECT 
    date,
    price,
    AVG(price) OVER (
        ORDER BY date 
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ) as moving_avg
FROM stock_prices;
```

## 🎯 PARTITION BY

Partition divides rows into groups.

```sql
-- Rank within each department
SELECT 
    name,
    department,
    salary,
    RANK() OVER (PARTITION BY department ORDER BY salary DESC) as dept_rank
FROM employees;

-- Result
+---------+------------+--------+-----------+
|  name   | department | salary | dept_rank |
+---------+------------+--------+-----------+
| Alice   | IT         | 80000  |    1      |
| Bob     | IT         | 60000  |    2      |
| Charlie | Sales      | 50000  |    1      |
| Diana   | Sales      | 45000  |    2      |
+---------+------------+--------+-----------+
```

## 💰 Advanced Examples

### Top 3 Per Department
```sql
SELECT name, department, salary
FROM (
    SELECT 
        name,
        department,
        salary,
        ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) as rn
    FROM employees
) ranked
WHERE rn <= 3;
```

### Month-over-Month Growth
```sql
SELECT 
    month,
    sales,
    LAG(sales) OVER (ORDER BY month) as prev_month,
    (sales - LAG(sales) OVER (ORDER BY month)) * 100.0 / 
        LAG(sales) OVER (ORDER BY month) as pct_change
FROM monthly_sales;
```

### Cumulative by Group
```sql
SELECT 
    category,
    month,
    sales,
    SUM(sales) OVER (PARTITION BY category ORDER BY month) as cumulative
FROM sales_data;
```

## 🔗 Next Steps

Window functions mastered? Let's learn CTEs!

---

**Key Takeaways:**
- ROW_NUMBER, RANK, DENSE_RANK for ranking
- LAG/LEAD for time-series
- PARTITION BY for grouping
- Running totals and moving averages
- Powerful for analytics

**Next Tutorial:** [14-CTEs.md](./14-CTEs.md)
