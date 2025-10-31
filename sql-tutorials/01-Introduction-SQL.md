# 01. Introduction to SQL

## 🎯 Learning Objectives
- Understand what SQL is and why it's important
- Learn about different database systems
- Set up your first database environment
- Write your first SQL query
- Understand basic database concepts

## 🌟 What is SQL?

**SQL** stands for **Structured Query Language**.

SQL is a programming language designed for:
- Managing and manipulating data in relational databases
- Retrieving information from databases
- Inserting, updating, and deleting data
- Creating and modifying database structures

### Why SQL Matters

SQL is everywhere:
- **95% of the data** in the world is stored in SQL databases
- Used by **major tech companies**: Google, Amazon, Facebook, Microsoft
- **High demand skill**: Consistently ranked in top 10 tech skills
- **Universal**: Works across all major databases

## 🗄️ Understanding Databases

### What is a Database?
A database is an organized collection of data stored electronically.

**Analogy**: Think of a database like a digital filing cabinet where you store information in an organized way.

### What is a Table?
A table is a collection of related data organized in rows and columns.

**Example**: A "Students" table
```
+----+---------+--------+-------+
| ID |  Name   |  Age   | Grade |
+----+---------+--------+-------+
| 1  | Alice   |  20    |  A    |
| 2  | Bob     |  19    |  B    |
| 3  | Charlie |  21    |  A    |
+----+---------+--------+-------+
```

- **Rows**: Individual records (Alice, Bob, Charlie)
- **Columns**: Fields or attributes (ID, Name, Age, Grade)

## 📊 Database Systems

### Popular SQL Databases

#### 1. **MySQL**
- **Type**: Open-source, free
- **Best For**: Web applications, small to medium businesses
- **Pros**: Easy to use, fast, widely supported
- **Used By**: Facebook, Twitter, YouTube

#### 2. **PostgreSQL**
- **Type**: Open-source, free
- **Best For**: Complex applications, analytics
- **Pros**: Advanced features, ACID compliant
- **Used By**: Instagram, Spotify, Reddit

#### 3. **SQL Server**
- **Type**: Commercial (Microsoft)
- **Best For**: Enterprise applications, Windows ecosystem
- **Pros**: Excellent tooling, enterprise features
- **Used By**: Many Fortune 500 companies

#### 4. **Oracle Database**
- **Type**: Commercial, enterprise-grade
- **Best For**: Large enterprises, financial systems
- **Pros**: Very powerful, highly reliable
- **Used By**: Banks, insurance companies

#### 5. **SQLite**
- **Type**: File-based, embedded
- **Best For**: Mobile apps, small projects
- **Pros**: No server needed, zero configuration
- **Used By**: Android, iOS, Python

## 🚀 Setting Up Your Environment

### Option 1: SQLite (Easiest for Beginners)
No installation needed! SQLite is file-based.

**For Python:**
```bash
# Install SQLite (comes with Python)
# That's it! No additional setup needed
```

**Using SQLite:**
```bash
# Start SQLite
sqlite3 my_database.db

# Or from Python
import sqlite3
conn = sqlite3.connect('my_database.db')
```

### Option 2: Online SQL Playgrounds
Perfect for practicing without installation:

**Top Platforms:**
- **SQL Fiddle**: http://sqlfiddle.com
- **DB Fiddle**: https://www.db-fiddle.com
- **W3Schools TryIt**: https://www.w3schools.com/sql/trysql.asp
- **Mode Analytics**: https://mode.com/sql-tutorial/

### Option 3: Install Database

#### For MySQL:
```bash
# Windows
Download from: https://dev.mysql.com/downloads/

# macOS
brew install mysql

# Linux
sudo apt-get install mysql-server
```

#### For PostgreSQL:
```bash
# Windows
Download from: https://www.postgresql.org/download/

# macOS
brew install postgresql

# Linux
sudo apt-get install postgresql
```

## 📝 Writing Your First SQL Query

### Basic Query Structure

Every SQL query typically follows this pattern:
```sql
SELECT column1, column2
FROM table_name
WHERE condition;
```

### Your First Query

Let's imagine we have a table called `employees`:
```
+----+---------+-----------+-------+
| ID |  Name   | Department| Salary|
+----+---------+-----------+-------+
| 1  | Alice   | Sales     | 50000 |
| 2  | Bob     | IT        | 60000 |
| 3  | Charlie | Sales     | 55000 |
+----+---------+-----------+-------+
```

**Query 1: Get All Employees**
```sql
SELECT * FROM employees;
```

**What this does**: Returns all columns and rows from the employees table.

**Query 2: Get Specific Columns**
```sql
SELECT name, salary FROM employees;
```

**What this does**: Returns only name and salary columns for all employees.

**Query 3: Filter Data**
```sql
SELECT name, salary 
FROM employees 
WHERE department = 'Sales';
```

**What this does**: Returns only employees in the Sales department.

## 🎓 Core SQL Concepts

### 1. Data Types
SQL supports various data types:

**Common Types:**
- **INTEGER**: Whole numbers (1, 2, 3)
- **VARCHAR**: Text strings ('Hello', 'World')
- **DECIMAL**: Decimal numbers (10.5, 99.99)
- **DATE**: Dates ('2024-01-15')
- **BOOLEAN**: True/False values

### 2. Primary Key
A unique identifier for each row in a table.

**Example:**
```sql
CREATE TABLE students (
    id INTEGER PRIMARY KEY,
    name VARCHAR(100),
    age INTEGER
);
```

### 3. Constraints
Rules that enforce data integrity.

**Common Constraints:**
- **PRIMARY KEY**: Unique identifier
- **FOREIGN KEY**: References another table
- **NOT NULL**: Cannot be empty
- **UNIQUE**: Must be unique

## 🧪 Hands-On Practice

### Exercise 1: Create Sample Database

**Using SQLite:**
```sql
-- Create a database
CREATE TABLE employees (
    id INTEGER PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    department VARCHAR(50),
    salary DECIMAL(10,2),
    hire_date DATE
);

-- Insert sample data
INSERT INTO employees (name, department, salary, hire_date) VALUES
('Alice Johnson', 'Sales', 50000.00, '2023-01-15'),
('Bob Smith', 'IT', 60000.00, '2022-06-01'),
('Charlie Brown', 'Sales', 55000.00, '2023-03-20'),
('Diana Prince', 'HR', 52000.00, '2022-11-10'),
('Eve Wilson', 'IT', 65000.00, '2023-07-05');
```

### Exercise 2: Write Your First Queries

**Query A**: Get all employee names
```sql
SELECT name FROM employees;
```

**Query B**: Get employees in IT department
```sql
SELECT name, salary 
FROM employees 
WHERE department = 'IT';
```

**Query C**: Get employees earning more than $55,000
```sql
SELECT name, department, salary 
FROM employees 
WHERE salary > 55000;
```

### Exercise 3: Online Practice

Go to **https://sqlfiddle.com/** and:

1. Create the employees table
2. Insert the sample data
3. Run the queries above
4. Experiment with different queries

## 🎯 Key Takeaways

✅ **SQL** is the language for working with databases  
✅ **Tables** store data in rows and columns  
✅ **SELECT** retrieves data from tables  
✅ **WHERE** filters the results  
✅ **Multiple databases** available (MySQL, PostgreSQL, etc.)  
✅ **Practice** is essential for learning  

## 🔗 Practice Resources

### Beginner-Friendly Platforms
1. **SQLBolt**: https://sqlbolt.com
   - Interactive SQL tutorial
   - Progressive difficulty

2. **W3Schools SQL**: https://www.w3schools.com/sql/
   - Free tutorials
   - Try-it-yourself editor

3. **Mode Analytics**: https://mode.com/sql-tutorial/
   - Real-world examples
   - Professional tutorials

## 📚 Next Steps

Now that you understand the basics, let's dive deeper into database fundamentals!

---

**Key Takeaways:**
- SQL is used to manage data in databases
- Tables store data in rows and columns
- SELECT is used to retrieve data
- Practice is the key to mastery

**Next Tutorial:** [02-Database-Fundamentals.md](./02-Database-Fundamentals.md)

## 💡 Common Beginner Mistakes

### Mistake 1: Missing Semicolons
```sql
-- ❌ Wrong
SELECT * FROM employees

-- ✅ Correct
SELECT * FROM employees;
```

### Mistake 2: Wrong Case Sensitivity
```sql
-- MySQL: case insensitive for keywords, sensitive for identifiers
SELECT * FROM Employees;  -- If table is 'employees', this might work

-- PostgreSQL: case insensitive unless quoted
SELECT * FROM "Employees";  -- If created with quotes
```

### Mistake 3: Forgetting Quotes for Strings
```sql
-- ❌ Wrong
SELECT * FROM employees WHERE name = Alice

-- ✅ Correct
SELECT * FROM employees WHERE name = 'Alice'
```

## 🎓 Practice Quiz

**Question 1**: What does SQL stand for?  
**Answer**: Structured Query Language

**Question 2**: What is the purpose of a PRIMARY KEY?  
**Answer**: To uniquely identify each row in a table

**Question 3**: Which clause is used to filter data?  
**Answer**: WHERE clause

**Keep practicing and you'll master SQL in no time!** 🚀

