# SQL Quick Reference Guide - Zero to Master

## 📚 Complete SQL Cheat Sheet

This is your comprehensive SQL reference covering all essential topics from beginner to advanced.

## 🏁 Beginner SQL

### Basic SELECT
```sql
SELECT * FROM table_name;
SELECT column1, column2 FROM table_name;
SELECT DISTINCT column FROM table_name;
```

### WHERE Clause
```sql
SELECT * FROM table_name WHERE condition;
SELECT * FROM table_name WHERE age > 25;
SELECT * FROM table_name WHERE name = 'John';
SELECT * FROM table_name WHERE age BETWEEN 20 AND 30;
SELECT * FROM table_name WHERE name IN ('John', 'Jane');
SELECT * FROM table_name WHERE name LIKE '%John%';
SELECT * FROM table_name WHERE name IS NULL;
SELECT * FROM table_name WHERE name IS NOT NULL;
```

### Logical Operators
```sql
SELECT * FROM table WHERE age > 25 AND salary > 50000;
SELECT * FROM table WHERE dept = 'IT' OR dept = 'Sales';
SELECT * FROM table WHERE NOT age < 25;
```

### Sorting and Limiting
```sql
SELECT * FROM table ORDER BY column ASC;
SELECT * FROM table ORDER BY column DESC;
SELECT * FROM table ORDER BY column1, column2;
SELECT * FROM table LIMIT 10;
SELECT * FROM table LIMIT 10 OFFSET 20;
```

## 🚀 Intermediate SQL

### Aggregate Functions
```sql
SELECT COUNT(*) FROM table;
SELECT SUM(column) FROM table;
SELECT AVG(column) FROM table;
SELECT MIN(column) FROM table;
SELECT MAX(column) FROM table;
```

### GROUP BY
```sql
SELECT department, COUNT(*) 
FROM employees 
GROUP BY department;

SELECT department, AVG(salary) 
FROM employees 
GROUP BY department;
```

### HAVING Clause
```sql
SELECT department, COUNT(*) 
FROM employees 
GROUP BY department 
HAVING COUNT(*) > 5;
```

### Joins
```sql
-- INNER JOIN
SELECT columns
FROM table1
INNER JOIN table2 ON table1.id = table2.id;

-- LEFT JOIN
SELECT columns
FROM table1
LEFT JOIN table2 ON table1.id = table2.id;

-- RIGHT JOIN
SELECT columns
FROM table1
RIGHT JOIN table2 ON table1.id = table2.id;

-- FULL OUTER JOIN (PostgreSQL)
SELECT columns
FROM table1
FULL OUTER JOIN table2 ON table1.id = table2.id;

-- Multiple Joins
SELECT e.name, d.department_name, m.manager_name
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id
LEFT JOIN managers m ON e.manager_id = m.manager_id;
```

### UNION
```sql
SELECT column FROM table1
UNION
SELECT column FROM table2;

SELECT column FROM table1
UNION ALL
SELECT column FROM table2;
```

### Subqueries
```sql
-- Subquery in WHERE
SELECT * FROM employees 
WHERE salary > (SELECT AVG(salary) FROM employees);

-- Subquery in SELECT
SELECT name, (SELECT COUNT(*) FROM orders WHERE customer_id = c.id) as order_count
FROM customers c;

-- EXISTS
SELECT * FROM employees e
WHERE EXISTS (
    SELECT 1 FROM projects p WHERE p.manager_id = e.id
);
```

## 🎓 Advanced SQL

### Window Functions
```sql
-- ROW_NUMBER
SELECT 
    name, 
    salary,
    ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) as rank
FROM employees;

-- RANK
SELECT 
    name, 
    salary,
    RANK() OVER (ORDER BY salary DESC) as salary_rank
FROM employees;

-- DENSE_RANK
SELECT 
    name, 
    salary,
    DENSE_RANK() OVER (ORDER BY salary DESC) as salary_rank
FROM employees;

-- LAG/LEAD
SELECT 
    name,
    salary,
    LAG(salary, 1) OVER (ORDER BY hire_date) as prev_salary,
    LEAD(salary, 1) OVER (ORDER BY hire_date) as next_salary
FROM employees;

-- Cumulative Sum
SELECT 
    name,
    salary,
    SUM(salary) OVER (ORDER BY hire_date) as cumulative_salary
FROM employees;
```

### Common Table Expressions (CTEs)
```sql
WITH department_stats AS (
    SELECT 
        department,
        AVG(salary) as avg_salary,
        COUNT(*) as emp_count
    FROM employees
    GROUP BY department
)
SELECT * FROM department_stats WHERE avg_salary > 60000;

-- Recursive CTE
WITH RECURSIVE hierarchy AS (
    SELECT id, name, manager_id, 1 as level
    FROM employees WHERE manager_id IS NULL
    
    UNION ALL
    
    SELECT e.id, e.name, e.manager_id, h.level + 1
    FROM employees e
    JOIN hierarchy h ON e.manager_id = h.id
)
SELECT * FROM hierarchy;
```

### CASE Statement
```sql
SELECT 
    name,
    salary,
    CASE 
        WHEN salary > 80000 THEN 'High'
        WHEN salary > 50000 THEN 'Medium'
        ELSE 'Low'
    END as salary_level
FROM employees;
```

## 🗄️ Data Modification

### INSERT
```sql
INSERT INTO table_name (column1, column2) VALUES (value1, value2);
INSERT INTO table_name VALUES (value1, value2, value3);
INSERT INTO table_name SELECT * FROM another_table;
```

### UPDATE
```sql
UPDATE table_name SET column1 = value1 WHERE condition;
UPDATE employees SET salary = salary * 1.1 WHERE department = 'IT';
UPDATE table_name SET column1 = value1, column2 = value2 WHERE condition;
```

### DELETE
```sql
DELETE FROM table_name WHERE condition;
DELETE FROM employees WHERE id = 123;
DELETE FROM table_name WHERE column1 IS NULL;
```

## 🔄 Advanced Features

### Transactions
```sql
BEGIN TRANSACTION;
INSERT INTO accounts VALUES (1, 1000);
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;

-- Rollback
BEGIN TRANSACTION;
DELETE FROM employees WHERE id = 123;
ROLLBACK;
```

### Stored Procedures
```sql
-- MySQL
DELIMITER //
CREATE PROCEDURE GetEmployeeCount(IN dept VARCHAR(50))
BEGIN
    SELECT COUNT(*) FROM employees WHERE department = dept;
END //
DELIMITER ;

CALL GetEmployeeCount('IT');

-- PostgreSQL
CREATE OR REPLACE FUNCTION GetEmployeeCount(dept VARCHAR(50))
RETURNS INTEGER AS $$
BEGIN
    RETURN (SELECT COUNT(*) FROM employees WHERE department = dept);
END;
$$ LANGUAGE plpgsql;

SELECT GetEmployeeCount('IT');
```

### Triggers
```sql
-- MySQL
CREATE TRIGGER update_timestamp
BEFORE UPDATE ON employees
FOR EACH ROW
SET NEW.updated_at = CURRENT_TIMESTAMP;

-- PostgreSQL
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = NOW();
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER update_employees_timestamp
BEFORE UPDATE ON employees
FOR EACH ROW
EXECUTE FUNCTION update_updated_at_column();
```

### Views
```sql
CREATE VIEW active_employees AS
SELECT * FROM employees WHERE status = 'Active';

SELECT * FROM active_employees;

CREATE OR REPLACE VIEW department_summary AS
SELECT 
    department,
    COUNT(*) as employee_count,
    AVG(salary) as avg_salary
FROM employees
GROUP BY department;
```

## ⚡ Performance Optimization

### Indexes
```sql
CREATE INDEX idx_name ON table_name(column_name);
CREATE INDEX idx_name ON table_name(column1, column2);
CREATE UNIQUE INDEX idx_name ON table_name(column_name);

-- Show indexes
SHOW INDEXES FROM table_name;

-- Drop index
DROP INDEX idx_name ON table_name;
```

### Query Optimization Tips
```sql
-- Use WHERE to filter early
SELECT * FROM large_table WHERE column = 'value' LIMIT 10;

-- Use proper JOINs instead of subqueries
-- ❌ Bad
SELECT * FROM employees WHERE id IN (SELECT id FROM managers);

-- ✅ Good
SELECT e.* FROM employees e JOIN managers m ON e.id = m.id;

-- Use EXISTS instead of IN for large datasets
-- ❌ Bad
SELECT * FROM orders WHERE customer_id IN (SELECT id FROM customers WHERE status = 'Active');

-- ✅ Good
SELECT * FROM orders o WHERE EXISTS (
    SELECT 1 FROM customers c WHERE c.id = o.customer_id AND c.status = 'Active'
);

-- Avoid SELECT *
SELECT id, name, email FROM users;  -- ✅ Good

-- Use LIMIT for large result sets
SELECT * FROM large_table LIMIT 1000;
```

## 🎯 Common Patterns

### Running Totals
```sql
SELECT 
    date,
    amount,
    SUM(amount) OVER (ORDER BY date) as running_total
FROM transactions;
```

### Moving Averages
```sql
SELECT 
    date,
    price,
    AVG(price) OVER (ORDER BY date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) as moving_avg
FROM stock_prices;
```

### Top N Per Group
```sql
SELECT department, name, salary
FROM (
    SELECT 
        department, 
        name, 
        salary,
        ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) as rn
    FROM employees
) ranked
WHERE rn <= 3;
```

### Remove Duplicates
```sql
-- Using ROW_NUMBER
DELETE FROM table_name
WHERE id IN (
    SELECT id FROM (
        SELECT id, ROW_NUMBER() OVER (PARTITION BY column ORDER BY id) as rn
        FROM table_name
    ) ranked
    WHERE rn > 1
);
```

## 📊 Database System Differences

### MySQL
- Most popular, easy setup
- Good for web applications
- `LIMIT` supported
- Case-insensitive by default

### PostgreSQL
- Advanced features
- Best for complex queries
- `LIMIT` and `OFFSET` supported
- Case-sensitive unless quoted

### SQL Server
- Enterprise features
- Windows ecosystem
- `TOP N` instead of LIMIT
- T-SQL dialect

### Oracle
- Enterprise-grade
- Advanced features
- `ROWNUM` and `FETCH` for limiting
- PL/SQL language

## 🔗 More Resources

- **Practice**: HackerRank, LeetCode, SQLBolt
- **Documentation**: Official docs for your chosen database
- **Community**: Stack Overflow, Reddit r/SQL
- **Courses**: Mode Analytics, Codecademy

---

**Keep this guide handy while learning SQL!** 🚀
