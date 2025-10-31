# 03. Basic SELECT Queries

## 🎯 Learning Objectives
- Master the SELECT statement
- Learn to filter data with WHERE
- Understand logical operators
- Use comparison operators effectively
- Practice with real-world examples

## 🔍 SELECT Statement Basics

### SELECT Syntax
```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

### SELECT All Columns
```sql
-- Get all columns from a table
SELECT * FROM employees;

-- Using * returns all columns
-- Useful for exploration, but avoid in production code
```

### SELECT Specific Columns
```sql
-- Get only specific columns
SELECT name, email FROM employees;

-- Select multiple columns by separating with commas
SELECT id, name, department, salary FROM employees;
```

### SELECT with Calculations
```sql
-- Perform calculations
SELECT name, salary, salary * 12 as annual_salary FROM employees;

-- Calculate bonuses
SELECT name, salary, salary * 0.1 as bonus FROM employees;

-- Mathematical operations
SELECT 
    product_name,
    price,
    quantity,
    price * quantity as total_value
FROM products;
```

## 🎯 WHERE Clause

The WHERE clause filters rows based on conditions.

### Basic WHERE Examples
```sql
-- Filter by single condition
SELECT * FROM employees WHERE department = 'IT';

-- Filter by numeric comparison
SELECT * FROM employees WHERE salary > 50000;

-- Filter by multiple conditions
SELECT * FROM employees 
WHERE department = 'IT' AND salary > 50000;
```

### Comparison Operators
```sql
-- Equality
WHERE salary = 50000

-- Not equal (different syntaxes)
WHERE salary != 50000    -- Most databases
WHERE salary <> 50000    -- Standard SQL

-- Greater than / Less than
WHERE salary > 50000
WHERE salary < 50000

-- Greater than or equal / Less than or equal
WHERE salary >= 50000
WHERE salary <= 50000
```

## 🔀 Logical Operators

### AND Operator
Both conditions must be true:
```sql
SELECT * FROM employees 
WHERE department = 'IT' AND salary > 55000;
```

### OR Operator
At least one condition must be true:
```sql
SELECT * FROM employees 
WHERE department = 'IT' OR department = 'Sales';
```

### NOT Operator
Negates a condition:
```sql
SELECT * FROM employees 
WHERE department NOT IN ('IT', 'Sales');

-- Equivalent to:
SELECT * FROM employees 
WHERE department <> 'IT' AND department <> 'Sales';
```

### Combining Logical Operators
```sql
SELECT * FROM employees 
WHERE (department = 'IT' OR department = 'Sales')
  AND salary > 50000
  AND hire_date >= '2023-01-01';
```

**Order of Operations:**
1. Parentheses (highest priority)
2. AND
3. OR

## 📋 IN and NOT IN Operators

### IN Operator
Check if value is in a list:
```sql
-- Select employees in specific departments
SELECT * FROM employees 
WHERE department IN ('IT', 'Sales', 'HR');

-- Select employees with specific IDs
SELECT * FROM employees 
WHERE id IN (1, 3, 5, 7);
```

### NOT IN Operator
Exclude values in a list:
```sql
SELECT * FROM employees 
WHERE department NOT IN ('IT', 'Sales');
```

## 🔍 LIKE and Pattern Matching

### LIKE with Wildcards
```sql
-- % matches any sequence of characters
-- _ matches a single character

-- Find names starting with 'A'
SELECT * FROM employees WHERE name LIKE 'A%';

-- Find names ending with 'son'
SELECT * FROM employees WHERE name LIKE '%son';

-- Find names containing 'Smith'
SELECT * FROM employees WHERE name LIKE '%Smith%';

-- Find 4-character names
SELECT * FROM employees WHERE name LIKE '____';

-- Find names with 'a' as second character
SELECT * FROM employees WHERE name LIKE '_a%';
```

### Case Sensitivity
```sql
-- PostgreSQL: Case sensitive by default
WHERE name LIKE 'a%'  -- Lowercase only

WHERE name ILIKE 'a%'  -- Case insensitive

-- MySQL: Case insensitive by default
WHERE name LIKE 'a%'  -- Both lowercase and uppercase

WHERE name LIKE BINARY 'a%'  -- Case sensitive
```

## 🎯 NULL Values

### Checking for NULL
```sql
-- Find rows with NULL values
SELECT * FROM employees WHERE department IS NULL;

-- Find rows without NULL values
SELECT * FROM employees WHERE department IS NOT NULL;

-- Common mistake
SELECT * FROM employees WHERE department = NULL;  -- Won't work!
```

### Handling NULL in Conditions
```sql
-- Get employees with salary info
SELECT * FROM employees 
WHERE salary IS NOT NULL AND salary > 50000;

-- COALESCE to provide default
SELECT 
    name,
    COALESCE(department, 'Unknown') as department
FROM employees;
```

## 📊 Practical Examples

### Example Database: E-Commerce Store
```sql
CREATE TABLE products (
    id INTEGER PRIMARY KEY,
    name VARCHAR(100),
    category VARCHAR(50),
    price DECIMAL(10,2),
    stock INTEGER,
    created_date DATE
);

INSERT INTO products VALUES
(1, 'Laptop Pro 15', 'Electronics', 1299.99, 25, '2024-01-15'),
(2, 'Wireless Mouse', 'Electronics', 29.99, 150, '2024-02-01'),
(3, 'Office Chair', 'Furniture', 299.99, 40, '2024-01-20'),
(4, 'Standing Desk', 'Furniture', 599.99, 15, '2024-03-10'),
(5, 'Coffee Maker', 'Appliances', 89.99, 80, '2024-02-15'),
(6, 'Desk Lamp', 'Furniture', 49.99, 120, '2024-01-05'),
(7, 'Mechanical Keyboard', 'Electronics', 159.99, 60, '2024-02-20');
```

### Query Examples

**1. Get all electronics**
```sql
SELECT * FROM products WHERE category = 'Electronics';
```

**2. Get products under $100**
```sql
SELECT * FROM products WHERE price < 100;
```

**3. Get products between $50 and $200**
```sql
SELECT * FROM products 
WHERE price BETWEEN 50 AND 200;

-- Alternative
SELECT * FROM products 
WHERE price >= 50 AND price <= 200;
```

**4. Get low stock items**
```sql
SELECT * FROM products WHERE stock < 30;
```

**5. Get furniture or appliances**
```sql
SELECT * FROM products 
WHERE category IN ('Furniture', 'Appliances');
```

**6. Find products with 'Pro' in name**
```sql
SELECT * FROM products WHERE name LIKE '%Pro%';
```

**7. Get expensive electronics**
```sql
SELECT * FROM products 
WHERE category = 'Electronics' 
  AND price > 500;
```

**8. Get products added in 2024**
```sql
SELECT * FROM products 
WHERE created_date >= '2024-01-01';
```

## 🧪 Hands-On Practice

### Practice Exercise 1: Employee Database
```sql
CREATE TABLE employees (
    id INTEGER PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(100),
    phone VARCHAR(20),
    department VARCHAR(50),
    position VARCHAR(50),
    salary DECIMAL(10,2),
    hire_date DATE,
    manager_id INTEGER
);

INSERT INTO employees VALUES
(1, 'John', 'Doe', 'john.doe@company.com', '555-0101', 'IT', 'Developer', 75000, '2023-01-15', NULL),
(2, 'Jane', 'Smith', 'jane.smith@company.com', '555-0102', 'IT', 'Manager', 95000, '2022-06-01', NULL),
(3, 'Bob', 'Johnson', 'bob.j@company.com', '555-0103', 'Sales', 'Sales Rep', 55000, '2023-03-20', 5),
(4, 'Alice', 'Williams', 'alice.w@company.com', '555-0104', 'Marketing', 'Analyst', 60000, '2023-07-10', 6),
(5, 'Charlie', 'Brown', 'charlie.b@company.com', '555-0105', 'Sales', 'Manager', 85000, '2022-01-15', NULL),
(6, 'Diana', 'Davis', 'diana.d@company.com', '555-0106', 'Marketing', 'Manager', 88000, '2021-11-01', NULL),
(7, 'Eve', 'Wilson', 'eve.w@company.com', '555-0107', 'IT', 'Developer', 72000, '2023-09-01', 2);
```

**Write queries for:**
1. All IT employees
2. Employees earning more than $70,000
3. Managers (no manager_id)
4. Employees hired in 2023
5. Employees with email containing 'smith'
6. Employees in IT or Sales departments
7. Employees earning between $50,000 and $80,000

### Exercise Solutions

**Solution 1**: All IT employees
```sql
SELECT * FROM employees WHERE department = 'IT';
```

**Solution 2**: High earners
```sql
SELECT * FROM employees WHERE salary > 70000;
```

**Solution 3**: Managers
```sql
SELECT * FROM employees WHERE manager_id IS NULL;
```

**Solution 4**: 2023 hires
```sql
SELECT * FROM employees 
WHERE hire_date >= '2023-01-01' 
  AND hire_date < '2024-01-01';
```

**Solution 5**: Name with 'smith'
```sql
SELECT * FROM employees 
WHERE email LIKE '%smith%';
```

**Solution 6**: IT or Sales
```sql
SELECT * FROM employees 
WHERE department IN ('IT', 'Sales');
```

**Solution 7**: Mid-range salary
```sql
SELECT * FROM employees 
WHERE salary BETWEEN 50000 AND 80000;
```

## 💡 Tips and Best Practices

### 1. Avoid SELECT *
```sql
-- ❌ Bad
SELECT * FROM employees;

-- ✅ Good
SELECT id, name, email FROM employees;
```

**Why?**
- Better performance
- Clearer code intent
- Easier maintenance

### 2. Use Aliases
```sql
-- Make columns readable
SELECT 
    salary * 12 as annual_salary,
    salary * 0.1 as bonus,
    salary + (salary * 0.1) as total_comp
FROM employees;
```

### 3. Be Explicit with AND/OR
```sql
-- Add parentheses for clarity
SELECT * FROM employees 
WHERE (department = 'IT' OR department = 'Sales')
  AND salary > 50000;
```

### 4. Use BETWEEN for Ranges
```sql
-- Better than >= AND <=
WHERE price BETWEEN 100 AND 500

-- Instead of
WHERE price >= 100 AND price <= 500
```

### 5. Handle NULL Carefully
```sql
-- Always check for NULL explicitly
WHERE column IS NULL
WHERE column IS NOT NULL
```

## 🎯 Common Mistakes to Avoid

### Mistake 1: Wrong Operator for NULL
```sql
-- ❌ Won't work
WHERE column = NULL

-- ✅ Correct
WHERE column IS NULL
```

### Mistake 2: Missing Quotes for Strings
```sql
-- ❌ Won't work
WHERE name = John

-- ✅ Correct
WHERE name = 'John'
```

### Mistake 3: AND/OR Precedence
```sql
-- ❌ Might not work as expected
WHERE department = 'IT' OR department = 'Sales' AND salary > 50000

-- ✅ Use parentheses
WHERE (department = 'IT' OR department = 'Sales') AND salary > 50000
```

## 🔗 Next Steps

Mastered basic SELECT queries? Time to learn sorting and limiting results!

---

**Key Takeaways:**
- SELECT retrieves data from tables
- WHERE filters rows based on conditions
- Use AND, OR, NOT for complex conditions
- LIKE for pattern matching
- Always check NULL values explicitly

**Next Tutorial:** [04-Filtering-Data.md](./04-Filtering-Data.md)

