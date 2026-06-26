# SQL Theoretical Questions & Answers

## Table of Contents
1. [Database Fundamentals](#database-fundamentals)
2. [SQL Basics](#sql-basics)
3. [Joins](#joins)
4. [Subqueries & CTEs](#subqueries--ctes)
5. [Indexes & Optimization](#indexes--optimization)
6. [Transactions & ACID](#transactions--acid)
7. [Normalization](#normalization)
8. [Window Functions](#window-functions)
9. [Advanced Concepts](#advanced-concepts)

---

## Database Fundamentals

### Q1: What is a Relational Database?
**Answer:**
A relational database is a type of database that stores and provides access to data points that are related to one another. Data is organized into tables (relations) consisting of rows (tuples/records) and columns (attributes/fields). Tables can be linked to each other through relationships defined by primary and foreign keys.

**Key Characteristics:**
- Data stored in tables with predefined schemas
- Relationships between tables via keys
- Supports SQL for data manipulation
- Ensures data integrity through constraints
- Follows ACID properties

**Examples:** PostgreSQL, MySQL, SQL Server, Oracle, SQLite

---

### Q2: What is the difference between DBMS and RDBMS?
**Answer:**

| Feature | DBMS | RDBMS |
|---------|------|-------|
| Data Storage | Files | Tables |
| Relationship | No relationship between data | Data related via keys |
| Normalization | Not required | Required |
| Data Redundancy | High | Low |
| Security | Low | High |
| Users | Single user | Multiple users |
| Examples | File systems, XML | MySQL, PostgreSQL, Oracle |

**Key Difference:** RDBMS stores data in tabular form with relationships, while DBMS stores data as files without inherent relationships.

---

### Q3: Explain Primary Key, Foreign Key, and Unique Key
**Answer:**

**Primary Key:**
- Uniquely identifies each record in a table
- Cannot contain NULL values
- Only one primary key per table
- Creates a clustered index (in most DBs)
- Example: `employee_id` in an employees table

```sql
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100)
);
```

**Foreign Key:**
- Establishes a link between two tables
- References the primary key of another table
- Can contain NULL values (if optional)
- Multiple foreign keys allowed per table
- Enforces referential integrity

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    employee_id INT,
    FOREIGN KEY (employee_id) REFERENCES employees(employee_id)
);
```

**Unique Key:**
- Ensures all values in a column are different
- Can contain one NULL value (in most DBs)
- Multiple unique constraints per table allowed
- Creates a non-clustered index

```sql
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    email VARCHAR(100) UNIQUE,
    username VARCHAR(50) UNIQUE
);
```

---

### Q4: What are Constraints in SQL?
**Answer:**
Constraints are rules enforced on data columns to ensure data integrity and accuracy.

**Types of Constraints:**
1. **NOT NULL** - Ensures column cannot have NULL value
2. **UNIQUE** - Ensures all values are different
3. **PRIMARY KEY** - Combination of NOT NULL and UNIQUE
4. **FOREIGN KEY** - Ensures referential integrity between tables
5. **CHECK** - Ensures values meet a specific condition
6. **DEFAULT** - Sets default value if none specified
7. **INDEX** - Improves data retrieval speed

```sql
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100) NOT NULL,
    price DECIMAL(10,2) CHECK (price > 0),
    category VARCHAR(50) DEFAULT 'Uncategorized',
    sku VARCHAR(20) UNIQUE,
    supplier_id INT,
    FOREIGN KEY (supplier_id) REFERENCES suppliers(supplier_id)
);
```

---

### Q5: Difference between DELETE, TRUNCATE, and DROP
**Answer:**

| Aspect | DELETE | TRUNCATE | DROP |
|--------|--------|----------|------|
| Type | DML | DDL | DDL |
| Removes | Specific rows (with WHERE) | All rows | Entire table structure |
| WHERE Clause | Supported | Not supported | Not applicable |
| Rollback | Possible | Not possible | Not possible |
| Triggers | Fires triggers | Does not fire | N/A |
| Speed | Slower (row by row) | Faster (page deallocation) | Fastest |
| Identity Reset | No | Yes (resets auto-increment) | N/A |
| Space | Retains table space | Releases space | Releases all space |

```sql
-- DELETE: Remove specific rows
DELETE FROM employees WHERE department = 'Sales';

-- TRUNCATE: Remove all rows quickly
TRUNCATE TABLE temp_data;

-- DROP: Remove entire table
DROP TABLE obsolete_table;
```

---

### Q6: What is a View in SQL?
**Answer:**
A view is a virtual table based on the result of a SQL query. It doesn't store data physically (except materialized views) but provides a way to present data from one or more tables.

**Advantages:**
- Simplifies complex queries
- Provides security (restricts access to specific columns/rows)
- Ensures data consistency
- Logical data independence

```sql
-- Create a view
CREATE VIEW active_employees AS
SELECT employee_id, name, department, salary
FROM employees
WHERE status = 'Active';

-- Query the view
SELECT * FROM active_employees WHERE department = 'Engineering';
```

**Types of Views:**
1. **Simple View** - Based on single table, no functions/grouping
2. **Complex View** - Multiple tables, functions, grouping
3. **Materialized View** - Stores query results physically (faster reads, slower refresh)

---

### Q7: What is a Stored Procedure?
**Answer:**
A stored procedure is a precompiled set of SQL statements stored in the database that can be executed repeatedly. It accepts parameters and can return values.

**Benefits:**
- Reduces network traffic
- Improves performance (precompiled)
- Enhances security
- Promotes code reusability
- Encapsulates business logic

```sql
-- Creating a stored procedure
CREATE PROCEDURE GetEmployeeByDepartment
    @dept VARCHAR(50)
AS
BEGIN
    SELECT employee_id, name, salary
    FROM employees
    WHERE department = @dept
    ORDER BY salary DESC;
END;

-- Executing
EXEC GetEmployeeByDepartment 'Engineering';
```

---

### Q8: What is a Trigger?
**Answer:**
A trigger is a special type of stored procedure that automatically executes when a specific event occurs on a table (INSERT, UPDATE, DELETE).

**Types:**
1. **BEFORE Trigger** - Executes before the event
2. **AFTER Trigger** - Executes after the event
3. **INSTEAD OF Trigger** - Replaces the event (common in views)

```sql
-- Audit trigger example
CREATE TRIGGER trg_employee_audit
AFTER UPDATE ON employees
FOR EACH ROW
BEGIN
    INSERT INTO employee_audit (employee_id, old_salary, new_salary, changed_at)
    VALUES (OLD.employee_id, OLD.salary, NEW.salary, NOW());
END;
```

**Use Cases:**
- Audit trails
- Enforcing complex business rules
- Maintaining derived columns
- Data validation beyond constraints

---

### Q9: Difference between WHERE and HAVING
**Answer:**

| Aspect | WHERE | HAVING |
|--------|-------|--------|
| Purpose | Filters rows before grouping | Filters groups after aggregation |
| Can use aggregates? | No | Yes |
| Execution Order | Before GROUP BY | After GROUP BY |
| Can use aliases? | No | Yes (in some DBs) |

```sql
-- WHERE filters individual rows
SELECT department, AVG(salary)
FROM employees
WHERE hire_date > '2023-01-01'  -- Filter rows first
GROUP BY department;

-- HAVING filters grouped results
SELECT department, AVG(salary) as avg_salary
FROM employees
GROUP BY department
HAVING AVG(salary) > 50000;  -- Filter groups after aggregation

-- Combined usage
SELECT department, COUNT(*) as emp_count, AVG(salary) as avg_salary
FROM employees
WHERE status = 'Active'
GROUP BY department
HAVING COUNT(*) > 5 AND AVG(salary) > 60000;
```

---

### Q10: What are SQL Joins? Explain all types.
**Answer:**
Joins combine rows from two or more tables based on a related column.

**Types of Joins:**

**1. INNER JOIN** - Returns only matching rows from both tables
```sql
SELECT e.name, d.department_name
FROM employees e
INNER JOIN departments d ON e.dept_id = d.dept_id;
```

**2. LEFT JOIN** - Returns all rows from left table, matching from right
```sql
SELECT e.name, COALESCE(d.department_name, 'No Department')
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id;
```

**3. RIGHT JOIN** - Returns all rows from right table, matching from left
```sql
SELECT e.name, d.department_name
FROM employees e
RIGHT JOIN departments d ON e.dept_id = d.dept_id;
```

**4. FULL OUTER JOIN** - Returns all rows when there's a match in either table
```sql
SELECT e.name, d.department_name
FROM employees e
FULL OUTER JOIN departments d ON e.dept_id = d.dept_id;
```

**5. CROSS JOIN** - Cartesian product of both tables
```sql
SELECT e.name, p.project_name
FROM employees e
CROSS JOIN projects p;
```

**6. SELF JOIN** - Join a table with itself
```sql
SELECT e1.name as employee, e2.name as manager
FROM employees e1
JOIN employees e2 ON e1.manager_id = e2.employee_id;
```

---

### Q11: What is a CTE (Common Table Expression)?
**Answer:**
A CTE is a temporary named result set that you can reference within a SELECT, INSERT, UPDATE, or DELETE statement. Defined using the WITH clause.

**Benefits:**
- Improves readability of complex queries
- Enables recursive queries
- Can be referenced multiple times
- Better performance in some cases

```sql
-- Simple CTE
WITH high_earners AS (
    SELECT employee_id, name, salary, department
    FROM employees
    WHERE salary > 100000
)
SELECT department, COUNT(*) as high_earner_count
FROM high_earners
GROUP BY department;

-- Multiple CTEs
WITH dept_stats AS (
    SELECT department, AVG(salary) as avg_salary, COUNT(*) as emp_count
    FROM employees
    GROUP BY department
),
company_avg AS (
    SELECT AVG(salary) as overall_avg FROM employees
)
SELECT d.department, d.avg_salary, d.emp_count, c.overall_avg,
       d.avg_salary - c.overall_avg as diff_from_avg
FROM dept_stats d, company_avg c;

-- Recursive CTE (hierarchical data)
WITH RECURSIVE employee_hierarchy AS (
    -- Anchor: top-level employees
    SELECT employee_id, name, manager_id, 0 as level
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    -- Recursive: employees with managers
    SELECT e.employee_id, e.name, e.manager_id, eh.level + 1
    FROM employees e
    JOIN employee_hierarchy eh ON e.manager_id = eh.employee_id
)
SELECT * FROM employee_hierarchy;
```

---

### Q12: Explain Window Functions
**Answer:**
Window functions perform calculations across a set of table rows related to the current row, without collapsing rows like GROUP BY.

**Syntax:**
```sql
function_name(expression) OVER (
    [PARTITION BY partition_expression]
    [ORDER BY sort_expression]
    [frame_clause]
)
```

**Categories:**

**1. Ranking Functions:**
```sql
SELECT 
    name,
    department,
    salary,
    RANK() OVER (ORDER BY salary DESC) as rank,
    DENSE_RANK() OVER (ORDER BY salary DESC) as dense_rank,
    ROW_NUMBER() OVER (ORDER BY salary DESC) as row_num,
    NTILE(4) OVER (ORDER BY salary DESC) as quartile
FROM employees;
```

**2. Aggregate Window Functions:**
```sql
SELECT 
    name,
    department,
    salary,
    SUM(salary) OVER (PARTITION BY department) as dept_total,
    AVG(salary) OVER (PARTITION BY department) as dept_avg,
    salary - AVG(salary) OVER (PARTITION BY department) as diff_from_dept_avg
FROM employees;
```

**3. Navigation Functions:**
```sql
SELECT 
    name,
    salary,
    LAG(salary, 1) OVER (ORDER BY salary) as prev_salary,
    LEAD(salary, 1) OVER (ORDER BY salary) as next_salary,
    FIRST_VALUE(salary) OVER (ORDER BY salary) as lowest_salary,
    LAST_VALUE(salary) OVER (ORDER BY salary ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) as highest_salary
FROM employees;
```

**4. Frame Functions:**
```sql
SELECT 
    date,
    revenue,
    SUM(revenue) OVER (ORDER BY date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) as rolling_7day,
    AVG(revenue) OVER (ORDER BY date ROWS BETWEEN 29 PRECEDING AND CURRENT ROW) as rolling_30day_avg
FROM daily_sales;
```

---

### Q13: What are Indexes? Explain types.
**Answer:**
An index is a data structure that improves the speed of data retrieval operations on a database table at the cost of additional writes and storage space.

**Types of Indexes:**

**1. Clustered Index:**
- Determines physical order of data in table
- Only one per table
- Primary key automatically creates clustered index
- Faster for range queries

**2. Non-Clustered Index:**
- Separate structure from data
- Multiple allowed per table
- Contains pointers to actual data rows

**3. Unique Index:**
- Ensures no duplicate values
- Automatically created with UNIQUE constraint

**4. Composite Index:**
- Index on multiple columns
- Order of columns matters (most selective first)

**5. Full-Text Index:**
- For text search in large text columns
- Supports word/phrase searching

```sql
-- Create indexes
CREATE INDEX idx_employee_name ON employees(last_name, first_name);
CREATE UNIQUE INDEX idx_email ON users(email);
CREATE INDEX idx_salary ON employees(salary) INCLUDE (name, department);

-- Check if index is used
EXPLAIN SELECT * FROM employees WHERE last_name = 'Smith';
```

**When to Use Indexes:**
- Columns frequently in WHERE, JOIN, ORDER BY
- Columns with high cardinality (many unique values)
- Foreign key columns

**When NOT to Use:**
- Small tables
- Columns with low cardinality (few unique values)
- Frequently updated columns
- Columns rarely queried

---

### Q14: Explain ACID Properties
**Answer:**
ACID properties ensure reliable processing of database transactions.

**Atomicity:**
- All operations in a transaction complete successfully or none do
- "All or nothing" principle
- If any operation fails, entire transaction is rolled back

**Consistency:**
- Database remains in a consistent state before and after transaction
- All constraints, triggers, and rules are enforced
- Data integrity is maintained

**Isolation:**
- Concurrent transactions don't interfere with each other
- Each transaction sees a consistent snapshot
- Isolation levels: READ UNCOMMITTED, READ COMMITTED, REPEATABLE READ, SERIALIZABLE

**Durability:**
- Once committed, changes persist even in case of system failure
- Achieved through transaction logs and backups

```sql
BEGIN TRANSACTION;
    UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
    UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;
    -- If both succeed
    COMMIT;
    -- If any fails
    ROLLBACK;
```

---

### Q15: What is Normalization? Explain Normal Forms.
**Answer:**
Normalization is the process of organizing data to reduce redundancy and improve data integrity.

**1NF (First Normal Form):**
- Each cell contains single, atomic value
- No repeating groups
- Each row is unique (primary key)

**2NF (Second Normal Form):**
- Must be in 1NF
- No partial dependency (non-key attributes depend on entire primary key)
- Relevant for composite keys

**3NF (Third Normal Form):**
- Must be in 2NF
- No transitive dependency (non-key attributes depend only on primary key)
- No dependency between non-key attributes

**BCNF (Boyce-Codd Normal Form):**
- Stricter version of 3NF
- For every functional dependency X → Y, X must be a superkey

**4NF (Fourth Normal Form):**
- Must be in BCNF
- No multi-valued dependencies

**5NF (Fifth Normal Form):**
- Must be in 4NF
- No join dependencies (lossless decomposition)

**Example - Denormalized to 3NF:**
```
-- Denormalized (violates 1NF - repeating groups)
Orders: order_id, customer_name, customer_phone, product1, product2, product3

-- 1NF: Separate products into different rows
Order_Items: order_id, customer_name, customer_phone, product_id, quantity

-- 2NF: Remove partial dependency (customer info depends on order_id, not product)
Orders: order_id, customer_name, customer_phone
Order_Items: order_id, product_id, quantity

-- 3NF: Remove transitive dependency (customer_phone depends on customer_name, not order_id)
Customers: customer_id, customer_name, customer_phone
Orders: order_id, customer_id
Order_Items: order_id, product_id, quantity
Products: product_id, product_name, price
```

---

### Q16: Difference between UNION and UNION ALL
**Answer:**

| Feature | UNION | UNION ALL |
|---------|-------|-----------|
| Duplicates | Removes duplicates | Keeps all duplicates |
| Performance | Slower (sorts to remove dupes) | Faster |
| Result Size | Smaller | Larger |
| Use Case | When unique results needed | When all results needed |

```sql
-- UNION: Returns unique rows only
SELECT city FROM customers
UNION
SELECT city FROM suppliers;

-- UNION ALL: Returns all rows including duplicates
SELECT city FROM customers
UNION ALL
SELECT city FROM suppliers;
```

**Requirements:**
- Both queries must have same number of columns
- Corresponding columns must have compatible data types
- Column names from first query are used in result

---

### Q17: What is the difference between CHAR and VARCHAR?
**Answer:**

| Feature | CHAR | VARCHAR |
|---------|------|---------|
| Storage | Fixed length | Variable length |
| Trailing Spaces | Padded with spaces | Not padded |
| Storage Size | Always n bytes | Actual length + 1-2 bytes |
| Performance | Slightly faster | Slightly slower |
| Best For | Fixed-length data (codes, IDs) | Variable-length data (names, descriptions) |

```sql
CHAR(10) -- 'hello     ' (padded to 10 chars)
VARCHAR(10) -- 'hello' (5 chars + length byte)
```

---

### Q18: Explain the difference between IN and EXISTS
**Answer:**

**IN:** Compares a value against a list of values or subquery results
```sql
SELECT * FROM employees WHERE department_id IN (1, 2, 3);
SELECT * FROM employees WHERE department_id IN (SELECT dept_id FROM departments WHERE active = 1);
```

**EXISTS:** Checks if a subquery returns any rows (returns TRUE/FALSE)
```sql
SELECT * FROM employees e WHERE EXISTS (
    SELECT 1 FROM departments d WHERE d.dept_id = e.department_id AND d.active = 1
);
```

**Key Differences:**
- IN evaluates the subquery and compares values
- EXISTS stops at first match (often faster for large datasets)
- EXISTS handles NULL values better
- IN may have issues with NULL values in subquery
- EXISTS is generally preferred for correlated subqueries

**Performance:**
- IN: Better for small lists, non-correlated subqueries
- EXISTS: Better for correlated subqueries, large datasets
- NOT EXISTS is usually faster than NOT IN (handles NULLs better)

---

### Q19: What is a Correlated Subquery?
**Answer:**
A correlated subquery is a subquery that depends on the outer query for its values. It executes once for each row processed by the outer query.

```sql
-- Correlated subquery: finds employees earning above their department average
SELECT e.name, e.salary, e.department
FROM employees e
WHERE e.salary > (
    SELECT AVG(salary) 
    FROM employees 
    WHERE department = e.department  -- Correlated: references outer query
);
```

**vs Non-Correlated Subquery:**
```sql
-- Non-correlated: executes once, result reused
SELECT name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

**Performance Consideration:**
- Correlated subqueries can be slow on large datasets
- Often can be rewritten using JOINs or window functions for better performance

---

### Q20: Explain SQL Injection and Prevention
**Answer:**
SQL Injection is a code injection attack where malicious SQL statements are inserted into application queries through user input.

**Example of Vulnerability:**
```sql
-- Vulnerable code (DON'T DO THIS)
query = "SELECT * FROM users WHERE username = '" + username + "' AND password = '" + password + "'"
-- If username = "admin' --", the query becomes:
-- SELECT * FROM users WHERE username = 'admin' --' AND password = '...'
```

**Prevention Methods:**
1. **Parameterized Queries (Prepared Statements):**
```sql
-- Safe approach
SELECT * FROM users WHERE username = ? AND password = ?
```

2. **Input Validation:**
- Whitelist allowed characters
- Validate data types and lengths
- Reject suspicious patterns

3. **Least Privilege:**
- Application DB user should have minimal permissions
- No DROP, ALTER, or GRANT permissions for app user

4. **Stored Procedures:**
```sql
CREATE PROCEDURE AuthenticateUser
    @username VARCHAR(50),
    @password VARCHAR(100)
AS
BEGIN
    SELECT * FROM users WHERE username = @username AND password = HASHBYTES('SHA2_256', @password);
END;
```

5. **ORM Frameworks:**
- Use ORMs that automatically parameterize queries
- Examples: SQLAlchemy, Entity Framework, Hibernate

---

### Q21: What are the different types of SQL commands?
**Answer:**

**1. DDL (Data Definition Language):**
- CREATE, ALTER, DROP, TRUNCATE, RENAME
- Modifies database structure

**2. DML (Data Manipulation Language):**
- SELECT, INSERT, UPDATE, DELETE
- Manipulates data within tables

**3. DCL (Data Control Language):**
- GRANT, REVOKE
- Controls access to data

**4. TCL (Transaction Control Language):**
- COMMIT, ROLLBACK, SAVEPOINT, SET TRANSACTION
- Manages transactions

---

### Q22: Difference between Clustered and Non-Clustered Index
**Answer:**

| Feature | Clustered Index | Non-Clustered Index |
|---------|-----------------|---------------------|
| Data Storage | Data rows stored in index order | Separate structure with pointers |
| Number per Table | Only one | Multiple (up to 999 in SQL Server) |
| Leaf Nodes | Contain actual data | Contain index key + row locator |
| Performance | Faster for range queries | Faster for lookups on non-key columns |
| Size | No extra storage (data is the index) | Requires additional storage |
| Insert Speed | Slower (must maintain order) | Faster |
| Default | Created with PRIMARY KEY | Created with UNIQUE or CREATE INDEX |

---

### Q23: What is a Cursor in SQL?
**Answer:**
A cursor is a database object used to retrieve and manipulate data row by row, rather than the typical set-based operations.

```sql
DECLARE @emp_id INT, @emp_name VARCHAR(100);

DECLARE emp_cursor CURSOR FOR
SELECT employee_id, name FROM employees WHERE department = 'Sales';

OPEN emp_cursor;
FETCH NEXT FROM emp_cursor INTO @emp_id, @emp_name;

WHILE @@FETCH_STATUS = 0
BEGIN
    -- Process each row
    PRINT 'Employee: ' + @emp_name;
    FETCH NEXT FROM emp_cursor INTO @emp_id, @emp_name;
END;

CLOSE emp_cursor;
DEALLOCATE emp_cursor;
```

**Best Practice:** Avoid cursors when possible. Use set-based operations instead as they're much faster.

---

### Q24: Explain the MERGE Statement
**Answer:**
MERGE performs INSERT, UPDATE, or DELETE operations on a target table based on the results of a join with a source table.

```sql
MERGE INTO target_table AS target
USING source_table AS source
ON target.id = source.id
WHEN MATCHED THEN
    UPDATE SET target.name = source.name, target.value = source.value
WHEN NOT MATCHED BY TARGET THEN
    INSERT (id, name, value) VALUES (source.id, source.name, source.value)
WHEN NOT MATCHED BY SOURCE THEN
    DELETE;
```

**Use Cases:**
- Synchronizing tables (ETL processes)
- Upsert operations (update if exists, insert if not)
- Data warehouse loading

---

### Q25: What is a Deadlock?
**Answer:**
A deadlock occurs when two or more transactions permanently block each other by each holding a lock on a resource the other needs.

**Example:**
- Transaction A locks Table 1, then tries to lock Table 2
- Transaction B locks Table 2, then tries to lock Table 1
- Both wait forever

**Prevention:**
1. Consistent lock ordering
2. Shorter transactions
3. Using appropriate isolation levels
4. Deadlock detection and resolution by DBMS
5. Setting lock timeout

```sql
-- Detect deadlocks (SQL Server)
SELECT 
    request_session_id as spid,
    resource_type,
    resource_description,
    request_mode,
    request_status
FROM sys.dm_tran_locks
WHERE resource_database_id = DB_ID();
```

---

### Q26: Difference between ISNULL, COALESCE, and NULLIF
**Answer:**

```sql
-- ISNULL: Replaces NULL with specified value (SQL Server)
SELECT ISNULL(commission, 0) FROM sales;

-- COALESCE: Returns first non-NULL value (ANSI standard)
SELECT COALESCE(phone, mobile, email, 'No Contact') FROM customers;

-- NULLIF: Returns NULL if two expressions are equal
SELECT NULLIF(salary, 0) FROM employees; -- Returns NULL if salary is 0
```

| Function | Arguments | Returns NULL When | Standard |
|----------|-----------|-------------------|----------|
| ISNULL | 2 | First is NULL | SQL Server |
| COALESCE | 2+ | All are NULL | ANSI SQL |
| NULLIF | 2 | Both are equal | ANSI SQL |

---

### Q27: What is a Schema in SQL?
**Answer:**
A schema is a logical container for database objects (tables, views, indexes, etc.). It provides a namespace and helps organize database objects.

```sql
-- Create schema
CREATE SCHEMA sales;

-- Create table in schema
CREATE TABLE sales.orders (...);

-- Reference with schema
SELECT * FROM sales.orders;

-- Grant permissions on schema
GRANT SELECT ON SCHEMA::sales TO analyst_role;
```

**Benefits:**
- Logical organization
- Security isolation
- Multiple schemas per database
- Same object names in different schemas

---

### Q28: Explain the CASE Statement
**Answer:**
CASE provides conditional logic within SQL queries.

```sql
-- Simple CASE
SELECT 
    name,
    CASE department
        WHEN 'Sales' THEN 'Revenue'
        WHEN 'Marketing' THEN 'Growth'
        WHEN 'Engineering' THEN 'Product'
        ELSE 'Support'
    END as function
FROM employees;

-- Searched CASE (more flexible)
SELECT 
    name,
    salary,
    CASE 
        WHEN salary < 50000 THEN 'Low'
        WHEN salary BETWEEN 50000 AND 100000 THEN 'Medium'
        WHEN salary > 100000 THEN 'High'
        ELSE 'Unknown'
    END as salary_band,
    CASE 
        WHEN salary > (SELECT AVG(salary) FROM employees) THEN 'Above Average'
        ELSE 'Below Average'
    END as comparison
FROM employees;
```

---

### Q29: What are the different Isolation Levels?
**Answer:**

| Level | Dirty Read | Non-Repeatable Read | Phantom Read |
|-------|-----------|---------------------|--------------|
| READ UNCOMMITTED | Yes | Yes | Yes |
| READ COMMITTED | No | Yes | Yes |
| REPEATABLE READ | No | No | Yes |
| SERIALIZABLE | No | No | No |
| SNAPSHOT | No | No | No |

```sql
-- Set isolation level
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
BEGIN TRANSACTION;
-- ... queries ...
COMMIT;
```

**Dirty Read:** Reading uncommitted data from another transaction
**Non-Repeatable Read:** Same query returns different results within a transaction
**Phantom Read:** New rows appear/disappear between queries in a transaction

---

### Q30: What is a Partition in SQL?
**Answer:**
Partitioning divides a large table into smaller, more manageable pieces while maintaining a single logical table.

**Types:**

**1. Horizontal Partitioning (Table Partitioning):**
```sql
-- Range partitioning
CREATE TABLE sales (
    sale_id INT,
    sale_date DATE,
    amount DECIMAL(10,2)
) PARTITION BY RANGE (YEAR(sale_date)) (
    PARTITION p2022 VALUES LESS THAN (2023),
    PARTITION p2023 VALUES LESS THAN (2024),
    PARTITION p2024 VALUES LESS THAN (2025)
);
```

**2. Vertical Partitioning:**
- Splitting columns across tables
- Frequently accessed columns in one table, less frequent in another

**Benefits:**
- Improved query performance (partition elimination)
- Easier maintenance (archive old partitions)
- Parallel processing
- Better index management

---

## SQL Basics

### Q31: Write a query to find the second highest salary
**Answer:**

```sql
-- Method 1: Using subquery with MAX
SELECT MAX(salary) as second_highest
FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);

-- Method 2: Using LIMIT/OFFSET
SELECT DISTINCT salary
FROM employees
ORDER BY salary DESC
LIMIT 1 OFFSET 1;

-- Method 3: Using DENSE_RANK (handles ties correctly)
SELECT salary
FROM (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) as rnk
    FROM employees
) ranked
WHERE rnk = 2;

-- Method 4: Using TOP (SQL Server)
SELECT TOP 1 salary
FROM (
    SELECT DISTINCT TOP 2 salary
    FROM employees
    ORDER BY salary DESC
) sub
ORDER BY salary ASC;
```

---

### Q32: Find duplicate records in a table
**Answer:**

```sql
-- Find duplicates based on email
SELECT email, COUNT(*) as duplicate_count
FROM users
GROUP BY email
HAVING COUNT(*) > 1;

-- Find all duplicate rows (keeping one)
SELECT *
FROM users u1
WHERE EXISTS (
    SELECT 1 FROM users u2
    WHERE u2.email = u1.email AND u2.user_id > u1.user_id
);

-- Delete duplicates keeping the one with lowest ID
WITH duplicates AS (
    SELECT *,
           ROW_NUMBER() OVER (PARTITION BY email ORDER BY user_id) as rn
    FROM users
)
DELETE FROM duplicates WHERE rn > 1;
```

---

### Q33: Find employees who joined in the last 30 days
**Answer:**

```sql
-- Method 1: Using DATE functions
SELECT * FROM employees
WHERE hire_date >= DATEADD(day, -30, GETDATE()); -- SQL Server

SELECT * FROM employees
WHERE hire_date >= CURRENT_DATE - INTERVAL '30 days'; -- PostgreSQL/MySQL

-- Method 2: Using BETWEEN
SELECT * FROM employees
WHERE hire_date BETWEEN DATE_SUB(CURDATE(), INTERVAL 30 DAY) AND CURDATE();

-- Method 3: Using DATEDIFF
SELECT * FROM employees
WHERE DATEDIFF(day, hire_date, GETDATE()) <= 30;
```

---

### Q34: Get the Nth highest salary
**Answer:**

```sql
-- Using DENSE_RANK (most robust)
WITH ranked_salaries AS (
    SELECT DISTINCT salary,
           DENSE_RANK() OVER (ORDER BY salary DESC) as rnk
    FROM employees
)
SELECT salary FROM ranked_salaries WHERE rnk = 5; -- 5th highest

-- Using LIMIT with subquery (MySQL/PostgreSQL)
SELECT salary FROM (
    SELECT DISTINCT salary
    FROM employees
    ORDER BY salary DESC
    LIMIT 1 OFFSET 4  -- N-1
) sub;
```

---

### Q35: Find employees who earn more than their managers
**Answer:**

```sql
SELECT e.name as employee, e.salary as emp_salary,
       m.name as manager, m.salary as mgr_salary
FROM employees e
JOIN employees m ON e.manager_id = m.employee_id
WHERE e.salary > m.salary;
```

---

### Q36: Get department-wise employee count and average salary
**Answer:**

```sql
SELECT 
    d.department_name,
    COUNT(e.employee_id) as employee_count,
    AVG(e.salary) as average_salary,
    MIN(e.salary) as min_salary,
    MAX(e.salary) as max_salary,
    SUM(e.salary) as total_payroll
FROM departments d
LEFT JOIN employees e ON d.dept_id = e.department_id
GROUP BY d.department_name
ORDER BY average_salary DESC;
```

---

### Q37: Find the most recently hired employee in each department
**Answer:**

```sql
-- Method 1: Using window function
SELECT *
FROM (
    SELECT *,
           ROW_NUMBER() OVER (PARTITION BY department ORDER BY hire_date DESC) as rn
    FROM employees
) ranked
WHERE rn = 1;

-- Method 2: Using correlated subquery
SELECT e.*
FROM employees e
WHERE e.hire_date = (
    SELECT MAX(hire_date)
    FROM employees
    WHERE department = e.department
);
```

---

### Q38: Calculate running total of sales
**Answer:**

```sql
SELECT 
    order_date,
    daily_sales,
    SUM(daily_sales) OVER (ORDER BY order_date) as running_total,
    SUM(daily_sales) OVER (ORDER BY order_date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) as rolling_7day
FROM (
    SELECT order_date, SUM(amount) as daily_sales
    FROM orders
    GROUP BY order_date
) daily;
```

---

### Q39: Find the percentage contribution of each department to total salary
**Answer:**

```sql
SELECT 
    department,
    SUM(salary) as dept_total,
    SUM(salary) * 100.0 / SUM(SUM(salary)) OVER () as percentage_of_total
FROM employees
GROUP BY department;
```

---

### Q40: Get the top 3 employees by salary in each department
**Answer:**

```sql
WITH ranked AS (
    SELECT 
        name,
        department,
        salary,
        DENSE_RANK() OVER (PARTITION BY department ORDER BY salary DESC) as rnk
    FROM employees
)
SELECT * FROM ranked WHERE rnk <= 3;
```

---

## Additional Theory Questions

### Q41-50: Quick Reference

**Q41: What is a surrogate key?**
A synthetic primary key (usually auto-increment integer) with no business meaning, used instead of natural keys.

**Q42: What is a natural key?**
A key that has business meaning and exists naturally in the data (e.g., SSN, email).

**Q43: What is referential integrity?**
Ensures relationships between tables remain valid. Foreign keys must reference existing primary keys.

**Q44: What is a composite key?**
A primary key consisting of two or more columns.

**Q45: What is denormalization?**
Intentionally adding redundancy to improve read performance. Opposite of normalization.

**Q46: What is a covering index?**
An index that includes all columns needed for a query, so the database doesn't need to look up the actual table.

**Q47: What is query optimization?**
The process of choosing the most efficient way to execute a SQL query. The query optimizer evaluates different execution plans.

**Q48: What is an execution plan?**
A visual or text representation of how the database engine will execute a query.

**Q49: What is a temporary table?**
A table that exists only for the duration of a session or transaction.
```sql
CREATE TEMPORARY TABLE temp_results AS SELECT * FROM sales WHERE year = 2024;
```

**Q50: What is the difference between COUNT(*) and COUNT(column)?**
- COUNT(*) counts all rows including NULLs
- COUNT(column) counts only non-NULL values in that column
