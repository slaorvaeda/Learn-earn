# 08. SQL Joins - Connecting Tables

## 🎯 Learning Objectives
- Understand different types of joins
- Master INNER, LEFT, RIGHT, and FULL joins
- Learn when to use each join type
- Practice complex join scenarios

## 🔗 What are Joins?

Joins combine rows from two or more tables based on a related column.

**Why Join?**
- Data is stored in multiple tables for efficiency
- Joins combine related data
- Avoid data duplication
- Better database design

## 📊 Join Types Overview

```
INNER JOIN:    Only matching rows from both tables
LEFT JOIN:     All rows from left table + matching right
RIGHT JOIN:    All rows from right table + matching left
FULL OUTER:    All rows from both tables
CROSS JOIN:    Cartesian product of all rows
```

## 🎯 INNER JOIN

Returns only rows that have matching values in both tables.

### Basic INNER JOIN Syntax
```sql
SELECT columns
FROM table1
INNER JOIN table2 ON table1.column = table2.column;

-- Using JOIN (same as INNER JOIN)
SELECT columns
FROM table1
JOIN table2 ON table1.column = table2.column;
```

### Example: Employees and Departments
```sql
-- Tables
CREATE TABLE departments (
    dept_id INT PRIMARY KEY,
    dept_name VARCHAR(50)
);

CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    name VARCHAR(50),
    dept_id INT,
    FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
);

-- Sample Data
INSERT INTO departments VALUES
(1, 'IT'), (2, 'Sales'), (3, 'HR');

INSERT INTO employees VALUES
(1, 'Alice', 1),
(2, 'Bob', 1),
(3, 'Charlie', 2),
(4, 'Diana', NULL);

-- INNER JOIN query
SELECT e.name, d.dept_name
FROM employees e
INNER JOIN departments d ON e.dept_id = d.dept_id;

-- Result (Diana excluded because dept_id is NULL)
+---------+-----------+
|  name   | dept_name |
+---------+-----------+
| Alice   | IT        |
| Bob     | IT        |
| Charlie | Sales     |
+---------+-----------+
```

## 👈 LEFT JOIN

Returns all rows from the left table and matching rows from the right table.

### Basic LEFT JOIN
```sql
SELECT columns
FROM table1
LEFT JOIN table2 ON table1.column = table2.column;

-- Equivalent
SELECT columns
FROM table1
LEFT OUTER JOIN table2 ON table1.column = table2.column;
```

### Example: Include All Employees
```sql
SELECT e.name, d.dept_name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id;

-- Result (includes Diana with NULL)
+---------+-----------+
|  name   | dept_name |
+---------+-----------+
| Alice   | IT        |
| Bob     | IT        |
| Charlie | Sales     |
| Diana   | NULL      |
+---------+-----------+
```

### Using LEFT JOIN to Find Unassigned
```sql
-- Find employees without a department
SELECT e.name, e.dept_id
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id
WHERE d.dept_id IS NULL;
```

## 👉 RIGHT JOIN

Returns all rows from the right table and matching rows from the left table.

### Basic RIGHT JOIN
```sql
SELECT columns
FROM table1
RIGHT JOIN table2 ON table1.column = table2.column;

-- Equivalent
SELECT columns
FROM table1
RIGHT OUTER JOIN table2 ON table1.column = table2.column;
```

### Example: Include All Departments
```sql
SELECT e.name, d.dept_name
FROM employees e
RIGHT JOIN departments d ON e.dept_id = d.dept_id;

-- Result (includes HR with no employees)
+---------+-----------+
|  name   | dept_name |
+---------+-----------+
| Alice   | IT        |
| Bob     | IT        |
| Charlie | Sales     |
| NULL    | HR        |
+---------+-----------+
```

## 🔄 FULL OUTER JOIN

Returns all rows from both tables.

### Basic FULL JOIN
```sql
-- PostgreSQL, MySQL 8.0.2+, SQL Server
SELECT columns
FROM table1
FULL OUTER JOIN table2 ON table1.column = table2.column;
```

### Example: All Data
```sql
SELECT e.name, d.dept_name
FROM employees e
FULL OUTER JOIN departments d ON e.dept_id = d.dept_id;

-- Result (includes everything)
+---------+-----------+
|  name   | dept_name |
+---------+-----------+
| Alice   | IT        |
| Bob     | IT        |
| Charlie | Sales     |
| Diana   | NULL      |
| NULL    | HR        |
+---------+-----------+
```

### FULL JOIN in MySQL (older versions)
```sql
-- MySQL alternative using UNION
SELECT e.name, d.dept_name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id
UNION
SELECT e.name, d.dept_name
FROM employees e
RIGHT JOIN departments d ON e.dept_id = d.dept_id;
```

## ❌ CROSS JOIN

Returns Cartesian product (all possible combinations).

```sql
SELECT columns
FROM table1
CROSS JOIN table2;

-- Equivalent
SELECT columns
FROM table1, table2;
```

### Example
```sql
-- Colors and Sizes
CREATE TABLE colors (name VARCHAR(10));
INSERT INTO colors VALUES ('Red'), ('Blue'), ('Green');

CREATE TABLE sizes (size VARCHAR(10));
INSERT INTO sizes VALUES ('S'), ('M'), ('L');

SELECT c.name as color, s.size
FROM colors c
CROSS JOIN sizes s;

-- Result (9 rows)
+-------+------+
| color | size |
+-------+------+
| Red   | S    |
| Red   | M    |
| Red   | L    |
| Blue  | S    |
| Blue  | M    |
| Blue  | L    |
| Green | S    |
| Green | M    |
| Green | L    |
+-------+------+
```

## 🔀 Multiple Joins

### Joining 3+ Tables
```sql
SELECT 
    e.name as employee,
    d.dept_name as department,
    p.project_name as project
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id
LEFT JOIN projects p ON e.emp_id = p.emp_id;
```

### Example: E-Commerce
```sql
-- Tables
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(50)
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

CREATE TABLE order_items (
    item_id INT PRIMARY KEY,
    order_id INT,
    product_name VARCHAR(50),
    quantity INT,
    price DECIMAL(10,2),
    FOREIGN KEY (order_id) REFERENCES orders(order_id)
);

-- Join all three tables
SELECT 
    c.name as customer,
    o.order_date,
    oi.product_name,
    oi.quantity,
    oi.price * oi.quantity as total
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
JOIN order_items oi ON o.order_id = oi.order_id;
```

## 🎯 Common Join Patterns

### Self Join
Join a table with itself.

```sql
-- Employees with their managers
CREATE TABLE employees (
    emp_id INT,
    name VARCHAR(50),
    manager_id INT
);

INSERT INTO employees VALUES
(1, 'Alice', NULL),
(2, 'Bob', 1),
(3, 'Charlie', 1),
(4, 'Diana', 2);

-- Self join
SELECT 
    e.name as employee,
    m.name as manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.emp_id;

-- Result
+----------+----------+
| employee | manager  |
+----------+----------+
| Alice    | NULL     |
| Bob      | Alice    |
| Charlie  | Alice    |
| Diana    | Bob      |
+----------+----------+
```

### Join with WHERE Clause
```sql
SELECT 
    e.name,
    d.dept_name
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id
WHERE d.dept_name = 'IT';
```

### Join with Aggregate
```sql
-- Department employee count
SELECT 
    d.dept_name,
    COUNT(e.emp_id) as employee_count
FROM departments d
LEFT JOIN employees e ON d.dept_id = e.dept_id
GROUP BY d.dept_name;
```

## 💡 Join Best Practices

### 1. Use Aliases
```sql
-- ✅ Good
SELECT e.name, d.dept_name
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id;

-- ❌ Avoid
SELECT employees.name, departments.dept_name
FROM employees
JOIN departments ON employees.dept_id = departments.dept_id;
```

### 2. JOIN Order Matters
```sql
-- Optimize join order for performance
-- Start with most selective table
SELECT ...
FROM small_table s
JOIN large_table l ON ...
WHERE l.column = 'value';
```

### 3. Use Explicit JOINs
```sql
-- ✅ Good
SELECT * FROM table1
JOIN table2 ON table1.id = table2.id;

-- ❌ Avoid (old style)
SELECT * FROM table1, table2
WHERE table1.id = table2.id;
```

## 🧪 Practice Exercises

### Exercise 1: Library Database
```sql
CREATE TABLE books (
    book_id INT PRIMARY KEY,
    title VARCHAR(100),
    author_id INT
);

CREATE TABLE authors (
    author_id INT PRIMARY KEY,
    name VARCHAR(50)
);

CREATE TABLE book_loans (
    loan_id INT PRIMARY KEY,
    book_id INT,
    borrower_name VARCHAR(50),
    loan_date DATE
);

-- Questions:
-- 1. List all books with author names
-- 2. List all books including those without authors
-- 3. Find books that have never been loaned
-- 4. List all authors including those with no books
```

### Exercise Solutions

**1. Books with authors**
```sql
SELECT b.title, a.name as author
FROM books b
JOIN authors a ON b.author_id = a.author_id;
```

**2. All books**
```sql
SELECT b.title, a.name as author
FROM books b
LEFT JOIN authors a ON b.author_id = a.author_id;
```

**3. Unloaned books**
```sql
SELECT b.title
FROM books b
LEFT JOIN book_loans bl ON b.book_id = bl.book_id
WHERE bl.loan_id IS NULL;
```

**4. All authors**
```sql
SELECT a.name, b.title
FROM authors a
LEFT JOIN books b ON a.author_id = b.author_id;
```

## 🔗 Next Steps

Mastered joins? Let's move to subqueries!

---

**Key Takeaways:**
- INNER JOIN: matching rows only
- LEFT JOIN: all left + matching right
- RIGHT JOIN: all right + matching left  
- FULL JOIN: all rows from both
- Use explicit JOIN syntax
- Aliases improve readability

**Next Tutorial:** [09-Subqueries.md](./09-Subqueries.md)
