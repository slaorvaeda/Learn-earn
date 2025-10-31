# 02. Database Fundamentals

## 🎯 Learning Objectives
- Understand relational database concepts
- Learn about tables, rows, and columns
- Understand primary keys and foreign keys
- Learn about data types
- Create your first database and table

## 📊 What is a Relational Database?

A **relational database** organizes data into tables that can be linked together.

**Key Characteristics:**
- Data stored in **tables** (rows and columns)
- Tables can **relate** to each other
- Follows **rules** to ensure data integrity
- Uses **SQL** to query data

### Analogy
Think of a relational database like a library:
- Each **table** = a book shelf
- Each **row** = a book on the shelf
- Each **column** = information about the book (title, author, ISBN)

## 🗂️ Core Concepts

### Tables
A table stores data in a structured format:
```
+----+---------+--------+-----------+
| ID |  Name   |  Age   |  Department|
+----+---------+--------+-----------+
| 1  | Alice   |  25    |  IT       |
| 2  | Bob     |  30    |  Sales    |
| 3  | Charlie |  28    |  IT       |
+----+---------+--------+-----------+
```

**Components:**
- **Rows**: Individual records (Alice, Bob, Charlie)
- **Columns**: Fields/attributes (ID, Name, Age, Department)
- **Cells**: Intersection of row and column

### Primary Key
A unique identifier for each row in a table.

**Characteristics:**
- Unique: No two rows can have the same primary key
- Not NULL: Cannot be empty
- Stable: Shouldn't change over time
- One per table

```sql
CREATE TABLE students (
    student_id INTEGER PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100)
);
```

### Foreign Key
A field that references the primary key of another table.

**Purpose:**
- Create relationships between tables
- Enforce referential integrity
- Prevent orphaned records

```sql
CREATE TABLE enrollments (
    enrollment_id INTEGER PRIMARY KEY,
    student_id INTEGER,
    course_id INTEGER,
    FOREIGN KEY (student_id) REFERENCES students(student_id),
    FOREIGN KEY (course_id) REFERENCES courses(course_id)
);
```

## 📐 Data Types

### Numeric Types
```sql
INTEGER         -- Whole numbers: -2,147,483,648 to 2,147,483,647
BIGINT          -- Large whole numbers
DECIMAL(10,2)   -- Fixed precision: DECIMAL(precision, scale)
NUMERIC(10,2)   -- Same as DECIMAL
FLOAT           -- Floating point numbers
DOUBLE          -- Double precision floating point
```

### String Types
```sql
CHAR(n)         -- Fixed length string (padded with spaces)
VARCHAR(n)      -- Variable length string, max n characters
TEXT            -- Unlimited length text
```

### Date/Time Types
```sql
DATE            -- Date only (YYYY-MM-DD)
TIME            -- Time only (HH:MM:SS)
DATETIME        -- Date and time
TIMESTAMP       -- Date and time with timezone
```

### Boolean Type
```sql
BOOLEAN         -- TRUE or FALSE
BIT             -- 0 or 1 (some databases)
```

## 🏗️ Creating Your First Database

### Step 1: Create Database
```sql
-- SQLite
CREATE DATABASE my_database;

-- MySQL
CREATE DATABASE my_database;

-- PostgreSQL
CREATE DATABASE my_database;

-- SQL Server
CREATE DATABASE my_database;
```

### Step 2: Use Database
```sql
-- MySQL, PostgreSQL, SQL Server
USE my_database;

-- SQLite (file-based, no USE needed)
-- Just open the database file
```

### Step 3: Create Table
```sql
CREATE TABLE employees (
    employee_id INTEGER PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE,
    phone VARCHAR(20),
    hire_date DATE,
    salary DECIMAL(10,2),
    department VARCHAR(50)
);
```

## 📋 Constraints

Constraints enforce rules on data.

### NOT NULL
Requires a value:
```sql
first_name VARCHAR(50) NOT NULL
```

### UNIQUE
Ensures unique values:
```sql
email VARCHAR(100) UNIQUE
```

### PRIMARY KEY
Unique identifier:
```sql
employee_id INTEGER PRIMARY KEY
```

### FOREIGN KEY
References another table:
```sql
FOREIGN KEY (manager_id) REFERENCES employees(employee_id)
```

### CHECK
Validates data against condition:
```sql
age INTEGER CHECK (age >= 0 AND age <= 150),
salary DECIMAL(10,2) CHECK (salary > 0)
```

### DEFAULT
Provides default value:
```sql
hire_date DATE DEFAULT CURRENT_DATE,
status VARCHAR(20) DEFAULT 'Active'
```

## 🔗 Relationships Between Tables

### One-to-One Relationship
One record in Table A relates to one record in Table B:

```sql
CREATE TABLE users (
    user_id INTEGER PRIMARY KEY,
    username VARCHAR(50) UNIQUE,
    email VARCHAR(100)
);

CREATE TABLE user_profiles (
    profile_id INTEGER PRIMARY KEY,
    user_id INTEGER UNIQUE,
    full_name VARCHAR(100),
    bio TEXT,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

### One-to-Many Relationship
One record in Table A relates to many records in Table B:

```sql
CREATE TABLE departments (
    department_id INTEGER PRIMARY KEY,
    department_name VARCHAR(50)
);

CREATE TABLE employees (
    employee_id INTEGER PRIMARY KEY,
    first_name VARCHAR(50),
    department_id INTEGER,
    FOREIGN KEY (department_id) REFERENCES departments(department_id)
);
```

### Many-to-Many Relationship
Requires a junction table:

```sql
CREATE TABLE students (
    student_id INTEGER PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE courses (
    course_id INTEGER PRIMARY KEY,
    course_name VARCHAR(100)
);

-- Junction table
CREATE TABLE enrollments (
    enrollment_id INTEGER PRIMARY KEY,
    student_id INTEGER,
    course_id INTEGER,
    enrollment_date DATE,
    FOREIGN KEY (student_id) REFERENCES students(student_id),
    FOREIGN KEY (course_id) REFERENCES courses(course_id)
);
```

## 🎯 Complete Example: Library Database

```sql
-- Create Library Database
CREATE DATABASE library_db;

USE library_db;

-- Authors Table
CREATE TABLE authors (
    author_id INTEGER PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    birth_date DATE,
    nationality VARCHAR(50)
);

-- Books Table
CREATE TABLE books (
    book_id INTEGER PRIMARY KEY,
    title VARCHAR(200) NOT NULL,
    author_id INTEGER,
    isbn VARCHAR(20) UNIQUE,
    publication_year INTEGER,
    genre VARCHAR(50),
    pages INTEGER,
    FOREIGN KEY (author_id) REFERENCES authors(author_id)
);

-- Members Table
CREATE TABLE members (
    member_id INTEGER PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE,
    phone VARCHAR(20),
    join_date DATE DEFAULT CURRENT_DATE
);

-- Loans Table
CREATE TABLE loans (
    loan_id INTEGER PRIMARY KEY,
    member_id INTEGER,
    book_id INTEGER,
    loan_date DATE DEFAULT CURRENT_DATE,
    return_date DATE,
    due_date DATE,
    FOREIGN KEY (member_id) REFERENCES members(member_id),
    FOREIGN KEY (book_id) REFERENCES books(book_id)
);

-- Insert Sample Data
INSERT INTO authors VALUES
(1, 'J.K.', 'Rowling', '1965-07-31', 'British'),
(2, 'Stephen', 'King', '1947-09-21', 'American'),
(3, 'Agatha', 'Christie', '1890-09-15', 'British');

INSERT INTO books VALUES
(1, 'Harry Potter and the Philosopher Stone', 1, '978-0747532699', 1997, 'Fantasy', 223),
(2, 'The Shining', 2, '978-0307743657', 1977, 'Horror', 447),
(3, 'Murder on the Orient Express', 3, '978-0062693662', 1934, 'Mystery', 288);

INSERT INTO members VALUES
(1, 'Alice', 'Johnson', 'alice.j@example.com', '555-0101', '2024-01-15'),
(2, 'Bob', 'Smith', 'bob.s@example.com', '555-0102', '2024-02-01'),
(3, 'Charlie', 'Brown', 'charlie.b@example.com', '555-0103', '2024-02-20');

INSERT INTO loans VALUES
(1, 1, 1, '2024-01-20', NULL, '2024-02-03'),
(2, 2, 2, '2024-02-15', '2024-03-01', '2024-03-01'),
(3, 3, 3, '2024-03-10', NULL, '2024-03-24');
```

## 🧪 Hands-On Practice

### Exercise: Create Online Shop Database

**Requirements:**
1. Create tables for customers, products, and orders
2. Use appropriate data types
3. Add primary keys and foreign keys
4. Include constraints (NOT NULL, UNIQUE, CHECK)

```sql
-- Your solution here
CREATE TABLE customers (
    customer_id INTEGER PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    phone VARCHAR(20),
    registration_date DATE DEFAULT CURRENT_DATE
);

CREATE TABLE products (
    product_id INTEGER PRIMARY KEY,
    product_name VARCHAR(100) NOT NULL,
    description TEXT,
    price DECIMAL(10,2) NOT NULL CHECK (price > 0),
    stock_quantity INTEGER DEFAULT 0 CHECK (stock_quantity >= 0),
    category VARCHAR(50)
);

CREATE TABLE orders (
    order_id INTEGER PRIMARY KEY,
    customer_id INTEGER,
    order_date DATE DEFAULT CURRENT_DATE,
    total_amount DECIMAL(10,2),
    status VARCHAR(20) DEFAULT 'Pending',
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);
```

## 🎓 Key Takeaways

✅ **Tables** store data in rows and columns  
✅ **Primary keys** uniquely identify rows  
✅ **Foreign keys** link tables together  
✅ **Data types** define what can be stored  
✅ **Constraints** enforce data rules  
✅ **Relationships** connect related data  

## 🔗 Next Steps

Now you understand database structure! Let's start querying data.

---

**Next Tutorial:** [03-Basic-Queries.md](./03-Basic-Queries.md)

