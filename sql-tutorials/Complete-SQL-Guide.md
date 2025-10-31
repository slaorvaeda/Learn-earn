# Complete SQL Guide - All Topics from Zero to Master

## 📚 Comprehensive SQL Reference

This guide covers all SQL topics from beginner to advanced level.

---

## 🏁 Part 1: Beginner SQL (Read First)

### ✅ Already Covered in Separate Files:
- **01-Introduction-SQL.md**: SQL basics and setup
- **02-Database-Fundamentals.md**: Tables, keys, relationships
- **03-Basic-Queries.md**: SELECT, WHERE, filtering

### Additional Beginner Topics:

#### Filtering with Complex WHERE
```sql
-- BETWEEN
SELECT * FROM employees WHERE salary BETWEEN 50000 AND 80000;

-- LIKE with patterns
SELECT * FROM employees WHERE name LIKE 'A%';
SELECT * FROM employees WHERE email LIKE '%@company.com';

-- IN operator
SELECT * FROM employees WHERE department IN ('IT', 'Sales', 'HR');
```

#### Sorting and Limiting
```sql
-- ORDER BY
SELECT * FROM employees ORDER BY salary DESC;
SELECT * FROM employees ORDER BY department, salary DESC;

-- LIMIT (MySQL, PostgreSQL)
SELECT * FROM employees ORDER BY salary DESC LIMIT 10;

-- TOP (SQL Server)
SELECT TOP 10 * FROM employees ORDER BY salary DESC;

-- ROWNUM (Oracle)
SELECT * FROM (
    SELECT * FROM employees ORDER BY salary DESC
) WHERE ROWNUM <= 10;
```

#### String Functions
```sql
-- CONCAT (combine strings)
SELECT CONCAT(first_name, ' ', last_name) as full_name FROM employees;

-- UPPER/LOWER
SELECT UPPER(name), LOWER(email) FROM employees;

-- SUBSTRING/SUBSTR
SELECT SUBSTRING(name, 1, 5) FROM employees;

-- LENGTH/LEN
SELECT LENGTH(name) as name_length FROM employees;

-- TRIM
SELECT TRIM('  name  ') FROM dual;
```

#### Math Functions
```sql
-- Basic math
SELECT ROUND(salary * 1.1, 2) as new_salary FROM employees;
SELECT CEIL(salary) FROM employees;
SELECT FLOOR(salary) FROM employees;
SELECT ABS(balance) FROM accounts;
SELECT POWER(salary, 2) FROM employees;
```

---

## 🚀 Part 2: Intermediate SQL (Read Next)

### ✅ Already Covered:
- **08-Joins.md**: INNER, LEFT, RIGHT, FULL joins

### Aggregate Functions
```sql
-- COUNT
SELECT COUNT(*) FROM employees;
SELECT COUNT(DISTINCT department) FROM employees;

-- SUM
SELECT SUM(salary) FROM employees;

-- AVG
SELECT AVG(salary) FROM employees;

-- MIN/MAX
SELECT MIN(salary), MAX(salary) FROM employees;
```

### GROUP BY and HAVING
```sql
-- Group by department
SELECT department, COUNT(*) as emp_count
FROM employees
GROUP BY department;

-- Having clause (filter groups)
SELECT department, AVG(salary) as avg_salary
FROM employees
GROUP BY department
HAVING AVG(salary) > 60000;

-- Multiple GROUP BY columns
SELECT department, position, COUNT(*)
FROM employees
GROUP BY department, position;
```

### Subqueries
```sql
-- Subquery in WHERE
SELECT * FROM employees 
WHERE salary > (SELECT AVG(salary) FROM employees);

-- Subquery in SELECT
SELECT 
    name,
    salary,
    (SELECT AVG(salary) FROM employees) as avg_salary
FROM employees;

-- EXISTS
SELECT * FROM employees e
WHERE EXISTS (
    SELECT 1 FROM projects p WHERE p.manager_id = e.id
);

-- ANY/ALL
SELECT * FROM employees
WHERE salary > ANY (SELECT salary FROM managers);

SELECT * FROM employees
WHERE salary > ALL (SELECT salary FROM managers);
```

### Views
```sql
-- Create view
CREATE VIEW high_salary_employees AS
SELECT * FROM employees WHERE salary > 70000;

-- Query view
SELECT * FROM high_salary_employees;

-- Update view
CREATE OR REPLACE VIEW active_employees AS
SELECT * FROM employees WHERE status = 'Active';

-- Drop view
DROP VIEW high_salary_employees;
```

### Data Modification
```sql
-- INSERT
INSERT INTO employees (name, department, salary) 
VALUES ('John Doe', 'IT', 75000);

-- INSERT multiple rows
INSERT INTO employees (name, department, salary) VALUES
('Alice', 'IT', 80000),
('Bob', 'Sales', 55000);

-- INSERT from query
INSERT INTO employees_backup 
SELECT * FROM employees WHERE hire_date > '2023-01-01';

-- UPDATE
UPDATE employees 
SET salary = salary * 1.1 
WHERE department = 'IT';

UPDATE employees 
SET department = 'Engineering', salary = 90000 
WHERE id = 123;

-- DELETE
DELETE FROM employees WHERE id = 123;
DELETE FROM employees WHERE department = 'IT' AND salary < 50000;

-- TRUNCATE (faster than DELETE)
TRUNCATE TABLE temp_data;
```

### Transactions
```sql
-- Transaction example
BEGIN TRANSACTION;

UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;

-- If everything is ok
COMMIT;

-- If error occurs
ROLLBACK;

-- Savepoint
SAVEPOINT before_update;
UPDATE employees SET salary = 90000 WHERE id = 123;
-- If needed
ROLLBACK TO before_update;
```

---

## 🎓 Part 3: Advanced SQL (Read After Intermediate)

### ✅ Already Covered:
- **13-Window-Functions.md**: ROW_NUMBER, RANK, LAG/LEAD

### Common Table Expressions (CTEs)
```sql
-- Basic CTE
WITH department_stats AS (
    SELECT 
        department,
        COUNT(*) as emp_count,
        AVG(salary) as avg_salary
    FROM employees
    GROUP BY department
)
SELECT * FROM department_stats 
WHERE avg_salary > 60000;

-- Multiple CTEs
WITH 
dept_count AS (
    SELECT department, COUNT(*) as cnt FROM employees GROUP BY department
),
dept_avg AS (
    SELECT department, AVG(salary) as avg_sal FROM employees GROUP BY department
)
SELECT 
    dc.department,
    dc.cnt,
    da.avg_sal
FROM dept_count dc
JOIN dept_avg da ON dc.department = da.department;

-- Recursive CTE
WITH RECURSIVE employee_hierarchy AS (
    -- Base case
    SELECT emp_id, name, manager_id, 1 as level
    FROM employees WHERE manager_id IS NULL
    
    UNION ALL
    
    -- Recursive case
    SELECT e.emp_id, e.name, e.manager_id, eh.level + 1
    FROM employees e
    JOIN employee_hierarchy eh ON e.manager_id = eh.emp_id
)
SELECT * FROM employee_hierarchy;
```

### Stored Procedures
```sql
-- MySQL
DELIMITER //
CREATE PROCEDURE GetDepartmentEmployees(IN dept_name VARCHAR(50))
BEGIN
    SELECT * FROM employees WHERE department = dept_name;
END //
DELIMITER ;

CALL GetDepartmentEmployees('IT');

-- PostgreSQL
CREATE OR REPLACE FUNCTION GetDepartmentCount(dept_name VARCHAR)
RETURNS INTEGER AS $$
DECLARE
    emp_count INTEGER;
BEGIN
    SELECT COUNT(*) INTO emp_count
    FROM employees WHERE department = dept_name;
    
    RETURN emp_count;
END;
$$ LANGUAGE plpgsql;

SELECT GetDepartmentCount('IT');

-- SQL Server
CREATE PROCEDURE UpdateEmployeeSalary
    @EmployeeID INT,
    @NewSalary DECIMAL(10,2)
AS
BEGIN
    UPDATE employees
    SET salary = @NewSalary
    WHERE id = @EmployeeID;
END;

EXEC UpdateEmployeeSalary @EmployeeID = 123, @NewSalary = 80000;
```

### Triggers
```sql
-- MySQL
CREATE TRIGGER update_timestamp
BEFORE UPDATE ON employees
FOR EACH ROW
SET NEW.updated_at = CURRENT_TIMESTAMP;

-- PostgreSQL
CREATE OR REPLACE FUNCTION update_timestamp()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = NOW();
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER employees_timestamp
BEFORE UPDATE ON employees
FOR EACH ROW
EXECUTE FUNCTION update_timestamp();
```

---

## 🏆 Part 4: Master Level SQL

### Database Design - Normalization

#### First Normal Form (1NF)
- Each column contains atomic values
- No repeating groups

#### Second Normal Form (2NF)
- 1NF + every non-key attribute fully depends on primary key

#### Third Normal Form (3NF)
- 2NF + no transitive dependencies

### Indexes
```sql
-- Create index
CREATE INDEX idx_salary ON employees(salary);
CREATE INDEX idx_dept_salary ON employees(department, salary);

-- Unique index
CREATE UNIQUE INDEX idx_email ON employees(email);

-- Composite index
CREATE INDEX idx_name_email ON employees(first_name, last_name, email);

-- Drop index
DROP INDEX idx_salary ON employees;

-- Show indexes
SHOW INDEXES FROM employees;
```

### Query Optimization
```sql
-- Use WHERE to filter early
SELECT * FROM large_table WHERE category = 'A';

-- Use indexes
-- Make sure indexed columns are in WHERE clause

-- Avoid SELECT *
SELECT id, name, email FROM users;  -- Better

-- Use JOIN instead of subqueries when possible
-- ❌ Bad
SELECT * FROM orders 
WHERE customer_id IN (SELECT id FROM customers WHERE status = 'A');

-- ✅ Good
SELECT o.* FROM orders o
JOIN customers c ON o.customer_id = c.id
WHERE c.status = 'A';

-- Use UNION ALL instead of UNION when duplicates don't matter
SELECT * FROM table1
UNION ALL
SELECT * FROM table2;

-- EXPLAIN/EXECUTION PLAN
EXPLAIN SELECT * FROM employees WHERE salary > 50000;
```

### Advanced Joins
```sql
-- Full outer join simulation in MySQL
SELECT e.*, d.* 
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id

UNION

SELECT e.*, d.*
FROM employees e
RIGHT JOIN departments d ON e.dept_id = d.dept_id;

-- Self-join for hierarchies
SELECT 
    e.name as employee,
    m.name as manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.emp_id;
```

### Recursive Queries
```sql
-- Employee hierarchy
WITH RECURSIVE org_chart AS (
    SELECT emp_id, name, manager_id, 0 as level
    FROM employees WHERE manager_id IS NULL
    
    UNION ALL
    
    SELECT e.emp_id, e.name, e.manager_id, oc.level + 1
    FROM employees e
    JOIN org_chart oc ON e.manager_id = oc.emp_id
)
SELECT * FROM org_chart ORDER BY level;
```

---

## 🎯 Practical Projects

### Project 1: E-Commerce Database
```sql
-- Design complete e-commerce system
-- Tables: customers, products, orders, order_items, categories
-- Implement: joins, aggregations, reporting queries
```

### Project 2: Library Management
```sql
-- Design library system
-- Tables: books, authors, members, loans, fines
-- Implement: transaction handling, views, procedures
```

### Project 3: Employee Management
```sql
-- HR system design
-- Tables: employees, departments, projects, evaluations
-- Implement: hierarchies, window functions, reporting
```

---

## 🔥 Interview Questions & Answers

### Easy Questions
**Q: What is a primary key?**
A: A unique identifier for each row in a table.

**Q: What is a foreign key?**
A: A field that references another table's primary key.

**Q: What is the difference between WHERE and HAVING?**
A: WHERE filters rows, HAVING filters groups after aggregation.

### Medium Questions
**Q: What is the difference between INNER and LEFT JOIN?**
A: INNER returns only matching rows, LEFT returns all left rows + matching right.

**Q: Explain the difference between RANK and DENSE_RANK.**
A: RANK leaves gaps after ties, DENSE_RANK doesn't.

**Q: What is normalization?**
A: Database design process to reduce redundancy and improve data integrity.

### Hard Questions
**Q: How would you find the second highest salary?**
A: 
```sql
SELECT MAX(salary) FROM employees 
WHERE salary < (SELECT MAX(salary) FROM employees);
```

**Q: Write a query to find duplicates.**
A:
```sql
SELECT column1, column2, COUNT(*)
FROM table_name
GROUP BY column1, column2
HAVING COUNT(*) > 1;
```

**Q: Delete duplicates keeping only one.**
A:
```sql
DELETE FROM table_name
WHERE id NOT IN (
    SELECT MIN(id) FROM table_name
    GROUP BY column1, column2
);
```

---

## 🎓 Certification Path

### Recommended Certifications:
1. **Microsoft SQL Server Certification**
2. **Oracle Database Certification**
3. **MySQL Certification**
4. **PostgreSQL Certification**
5. **Google Data Analytics Certificate**

---

## 📚 Additional Resources

### Practice Platforms:
- **HackerRank SQL**
- **LeetCode Database Problems**
- **Mode Analytics SQL Tutorial**
- **SQLBolt**

### Books:
- **Learning SQL** by Alan Beaulieu
- **SQL Queries for Mere Mortals**
- **The Art of SQL**
- **SQL Performance Explained**

---

**Congratulations! You now have complete SQL knowledge from Zero to Master!** 🎉

Keep practicing and building projects to master SQL!
