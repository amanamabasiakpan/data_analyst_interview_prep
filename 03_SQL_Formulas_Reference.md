# SQL Formulas, Quick Reference & Cheat Sheet

## Table of Contents
1. [Date/Time Formulas](#datetime-formulas)
2. [String Manipulation](#string-manipulation)
3. [Mathematical Operations](#mathematical-operations)
4. [Aggregation Patterns](#aggregation-patterns)
5. [Conditional Logic](#conditional-logic)
6. [Data Type Conversions](#data-type-conversions)
7. [Window Function Patterns](#window-function-patterns)
8. [Performance Tips](#performance-tips)
9. [Common Errors & Fixes](#common-errors--fixes)
10. [SQL Dialect Differences](#sql-dialect-differences)

---

## Date/Time Formulas

### Get Current Date/Time
```sql
-- MySQL
SELECT CURDATE(), CURTIME(), NOW(), UTC_TIMESTAMP();

-- PostgreSQL
SELECT CURRENT_DATE, CURRENT_TIME, NOW(), CURRENT_TIMESTAMP;

-- SQL Server
SELECT GETDATE(), CAST(GETDATE() AS DATE), CAST(GETDATE() AS TIME);

-- SQLite
SELECT DATE('now'), TIME('now'), DATETIME('now');
```

### Date Arithmetic
```sql
-- MySQL
DATE_ADD(date, INTERVAL 7 DAY)      -- Add 7 days
DATE_SUB(date, INTERVAL 1 MONTH)     -- Subtract 1 month
DATEDIFF(end_date, start_date)       -- Days between
TIMESTAMPDIFF(MONTH, start, end)     -- Months between
DATE_FORMAT(date, '%Y-%m-%d')        -- Format date
STR_TO_DATE(string, '%Y-%m-%d')      -- Parse date

-- PostgreSQL
date + INTERVAL '7 days'             -- Add 7 days
date - INTERVAL '1 month'             -- Subtract 1 month
AGE(end_date, start_date)             -- Interval between
EXTRACT(YEAR FROM date)               -- Extract year
TO_CHAR(date, 'YYYY-MM-DD')           -- Format date
TO_DATE(string, 'YYYY-MM-DD')         -- Parse date

-- SQL Server
DATEADD(day, 7, date)                 -- Add 7 days
DATEADD(month, -1, date)              -- Subtract 1 month
DATEDIFF(day, start, end)             -- Days between
FORMAT(date, 'yyyy-MM-dd')            -- Format date
CONVERT(DATE, string, 120)            -- Parse date
EOMONTH(date)                         -- End of month
```

### Date Truncation / Period Grouping
```sql
-- MySQL
DATE_FORMAT(date, '%Y-%m')            -- Month level
YEAR(date), QUARTER(date), MONTH(date), WEEK(date), DAY(date)
YEARWEEK(date)                        -- Year-week

-- PostgreSQL
DATE_TRUNC('month', date)             -- Truncate to month
DATE_TRUNC('week', date)              -- Truncate to week
EXTRACT(YEAR FROM date)               -- Extract year

-- SQL Server
DATEPART(year, date)                  -- Extract year
DATETRUNC(month, date)                -- Truncate to month (SQL Server 2022+)
```

### Period Comparisons
```sql
-- Last 30 days
WHERE date >= DATE_SUB(CURDATE(), INTERVAL 30 DAY)

-- Current month
WHERE YEAR(date) = YEAR(CURDATE()) AND MONTH(date) = MONTH(CURDATE())

-- Previous month
WHERE date >= DATE_SUB(DATE_SUB(CURDATE(), INTERVAL DAY(CURDATE())-1 DAY), INTERVAL 1 MONTH)
  AND date < DATE_SUB(CURDATE(), INTERVAL DAY(CURDATE())-1 DAY)

-- Year-to-date
WHERE date >= DATE_FORMAT(CURDATE(), '%Y-01-01')

-- Same period last year
WHERE date BETWEEN DATE_SUB(CURDATE(), INTERVAL 1 YEAR) AND DATE_SUB(CURDATE(), INTERVAL 1 YEAR)
```

---

## String Manipulation

### Concatenation
```sql
-- MySQL
CONCAT(first_name, ' ', last_name)
CONCAT_WS(' - ', col1, col2, col3)    -- With separator

-- PostgreSQL
first_name || ' ' || last_name
CONCAT(first_name, ' ', last_name)

-- SQL Server
first_name + ' ' + last_name
CONCAT(first_name, ' ', last_name)
```

### Substring & Position
```sql
-- MySQL
SUBSTRING(string, start, length)
SUBSTRING_INDEX(string, delimiter, count)  -- Get part before/after delimiter
LEFT(string, n), RIGHT(string, n)
LOCATE(substring, string)                   -- Find position
LENGTH(string), CHAR_LENGTH(string)

-- PostgreSQL
SUBSTRING(string FROM start FOR length)
SPLIT_PART(string, delimiter, field_number)
LEFT(string, n), RIGHT(string, n)
POSITION(substring IN string)
LENGTH(string)

-- SQL Server
SUBSTRING(string, start, length)
LEFT(string, n), RIGHT(string, n)
CHARINDEX(substring, string)
LEN(string), DATALENGTH(string)
```

### Pattern Matching & Replacement
```sql
-- MySQL
string LIKE '%pattern%'
string REGEXP '^[A-Z]'                    -- Regex match
REGEXP_REPLACE(string, pattern, replacement)
REPLACE(string, old, new)
TRIM(string), LTRIM(string), RTRIM(string)
UPPER(string), LOWER(string)

-- PostgreSQL
string LIKE '%pattern%'
string ~ '^[A-Z].*'                       -- Regex match
REGEXP_REPLACE(string, pattern, replacement)
REPLACE(string, old, new)
TRIM(BOTH ' ' FROM string)
INITCAP(string)                           -- Title Case

-- SQL Server
string LIKE '%pattern%'
string LIKE '%[0-9]%'                     -- Pattern with character class
REPLACE(string, old, new)
LTRIM(RTRIM(string))
UPPER(string), LOWER(string)
```

---

## Mathematical Operations

### Basic Math
```sql
ROUND(number, decimals)           -- Round to N decimals
CEIL(number), FLOOR(number)        -- Round up/down
ABS(number)                        -- Absolute value
POWER(base, exponent)              -- Exponentiation
SQRT(number)                       -- Square root
MOD(dividend, divisor)             -- Remainder
SIGN(number)                       -- -1, 0, or 1

-- Random numbers
RAND()                             -- MySQL: 0 to 1
RANDOM()                           -- PostgreSQL
RAND()                             -- SQL Server
```

### Statistical Functions
```sql
AVG(column)                        -- Average
SUM(column)                        -- Sum
COUNT(*), COUNT(column)            -- Count rows/non-null
COUNT(DISTINCT column)             -- Unique count
MIN(column), MAX(column)           -- Min/Max
STDDEV(column)                     -- Standard deviation
VARIANCE(column)                   -- Variance

-- Running calculations
SUM(amount) OVER (ORDER BY date)   -- Running total
AVG(amount) OVER (ORDER BY date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW)  -- Moving avg
```

### Percentage Calculations
```sql
-- Percentage of total
amount * 100.0 / SUM(amount) OVER ()

-- Percentage change
(new - old) * 100.0 / NULLIF(old, 0)

-- Percentile
PERCENT_RANK() OVER (ORDER BY salary)
CUME_DIST() OVER (ORDER BY salary)

-- Percentile value
PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY salary)  -- Median
PERCENTILE_CONT(0.9) WITHIN GROUP (ORDER BY salary)  -- 90th percentile
```

---

## Aggregation Patterns

### Grouping with Multiple Levels
```sql
-- GROUP BY with ROLLUP (subtotals)
SELECT 
    COALESCE(region, 'TOTAL') as region,
    COALESCE(product, 'ALL') as product,
    SUM(sales) as total_sales
FROM sales_data
GROUP BY ROLLUP(region, product);

-- GROUP BY with CUBE (all combinations)
SELECT region, product, SUM(sales)
FROM sales_data
GROUP BY CUBE(region, product);

-- GROUPING SETS
SELECT region, product, SUM(sales)
FROM sales_data
GROUP BY GROUPING SETS (
    (region, product),
    (region),
    (product),
    ()
);
```

### Filtering Aggregated Results
```sql
-- WHERE vs HAVING
SELECT department, AVG(salary)
FROM employees
WHERE status = 'Active'          -- Filter rows BEFORE grouping
GROUP BY department
HAVING AVG(salary) > 50000;      -- Filter groups AFTER aggregation

-- Using QUALIFY (some DBs)
SELECT department, AVG(salary) as avg_sal
FROM employees
GROUP BY department
QUALIFY avg_sal > 50000;         -- Like HAVING for window functions
```

---

## Conditional Logic

### CASE Statements
```sql
-- Simple CASE
CASE grade
    WHEN 'A' THEN 4.0
    WHEN 'B' THEN 3.0
    WHEN 'C' THEN 2.0
    ELSE 0.0
END as gpa

-- Searched CASE
CASE 
    WHEN salary >= 100000 THEN 'High'
    WHEN salary >= 50000 THEN 'Medium'
    WHEN salary > 0 THEN 'Low'
    ELSE 'Unknown'
END as salary_band

-- Nested CASE
CASE 
    WHEN age >= 18 THEN 
        CASE 
            WHEN age >= 65 THEN 'Senior'
            ELSE 'Adult'
        END
    ELSE 'Minor'
END
```

### NULL Handling
```sql
COALESCE(col1, col2, col3, 0)     -- First non-NULL
IFNULL(column, 0)                  -- MySQL: if NULL then 0
ISNULL(column, 0)                  -- SQL Server
NULLIF(col1, col2)                 -- NULL if equal, else col1

-- Safe division
COALESCE(numerator / NULLIF(denominator, 0), 0)
```

---

## Data Type Conversions

### Casting
```sql
-- MySQL
CAST(column AS DECIMAL(10,2))
CAST(column AS DATE)
CAST(column AS CHAR)
CONVERT(column, DECIMAL(10,2))

-- PostgreSQL
CAST(column AS NUMERIC(10,2))
column::NUMERIC(10,2)              -- Shorthand
column::TEXT
column::DATE

-- SQL Server
CAST(column AS DECIMAL(10,2))
CONVERT(DECIMAL(10,2), column)
TRY_CAST(column AS INT)            -- Returns NULL on error
```

### Formatting Numbers
```sql
-- MySQL
FORMAT(number, 2)                  -- 1234.56 -> '1,234.56'
ROUND(number, 2)

-- PostgreSQL
TO_CHAR(number, 'FM999,999,999.00')  -- Format with commas
TO_CHAR(number, 'FM999999999.00')    -- Without commas

-- SQL Server
FORMAT(number, 'N2')                 -- 1234.56 -> '1,234.56'
FORMAT(number, 'C', 'en-US')         -- Currency
```

---

## Window Function Patterns

### Ranking
```sql
ROW_NUMBER() OVER (ORDER BY salary DESC)           -- Unique rank, no ties
RANK() OVER (ORDER BY salary DESC)                 -- Ties get same rank, skips next
DENSE_RANK() OVER (ORDER BY salary DESC)            -- Ties get same rank, no skip
NTILE(4) OVER (ORDER BY salary DESC)               -- Quartiles
PERCENT_RANK() OVER (ORDER BY salary DESC)          -- 0 to 1
CUME_DIST() OVER (ORDER BY salary DESC)             -- Cumulative distribution
```

### Running Calculations
```sql
-- Running total
SUM(sales) OVER (ORDER BY date)

-- Running total by group
SUM(sales) OVER (PARTITION BY region ORDER BY date)

-- Moving average (7-day)
AVG(sales) OVER (ORDER BY date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW)

-- Moving sum (current + previous 2)
SUM(sales) OVER (ORDER BY date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW)

-- Centered moving average
AVG(sales) OVER (ORDER BY date ROWS BETWEEN 2 PRECEDING AND 2 FOLLOWING)
```

### Navigation Functions
```sql
LAG(column, offset, default) OVER (ORDER BY date)     -- Previous row
LEAD(column, offset, default) OVER (ORDER BY date)     -- Next row
FIRST_VALUE(column) OVER (ORDER BY date)              -- First in window
LAST_VALUE(column) OVER (ORDER BY date ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING)  -- Last
NTH_VALUE(column, n) OVER (ORDER BY date)             -- Nth value
```

### Frame Specifications
```sql
ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW       -- From start to current
ROWS BETWEEN 3 PRECEDING AND CURRENT ROW               -- Last 4 rows
ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING       -- Current to end
RANGE BETWEEN INTERVAL '7' DAY PRECEDING AND CURRENT ROW  -- Date range
```

---

## Performance Tips

### Indexing Strategy
```sql
-- Create composite index (order matters!)
CREATE INDEX idx_name ON table(col1, col2, col3);
-- Most selective column first
-- Columns used in WHERE/JOIN/ORDER BY

-- Covering index
CREATE INDEX idx_covering ON table(col1, col2) INCLUDE (col3, col4);

-- Partial index
CREATE INDEX idx_active ON table(status) WHERE status = 'Active';
```

### Query Optimization
```sql
-- Avoid SELECT *
SELECT specific_columns FROM table;

-- Use EXISTS instead of IN for correlated subqueries
WHERE EXISTS (SELECT 1 FROM ...)

-- Use JOIN instead of subqueries when possible
-- Use appropriate JOIN type (INNER vs LEFT)

-- Filter early with WHERE before JOIN
-- Use LIMIT/TOP for preview queries

-- Avoid functions on indexed columns in WHERE
-- BAD: WHERE YEAR(date_col) = 2024
-- GOOD: WHERE date_col >= '2024-01-01' AND date_col < '2025-01-01'

-- Use UNION ALL instead of UNION when duplicates aren't a concern
```

### EXPLAIN Plans
```sql
-- MySQL
EXPLAIN SELECT * FROM table WHERE ...;
EXPLAIN ANALYZE SELECT * FROM table WHERE ...;

-- PostgreSQL
EXPLAIN (ANALYZE, BUFFERS, FORMAT JSON) SELECT ...;

-- SQL Server
SET SHOWPLAN_ALL ON;
SELECT * FROM table WHERE ...;
SET SHOWPLAN_ALL OFF;
```

---

## Common Errors & Fixes

### Division by Zero
```sql
-- BAD
SELECT col1 / col2 FROM table;

-- GOOD
SELECT col1 / NULLIF(col2, 0) FROM table;
SELECT CASE WHEN col2 = 0 THEN 0 ELSE col1 / col2 END FROM table;
```

### NULL in Aggregations
```sql
-- COUNT(*) counts NULLs, COUNT(column) does not
-- SUM/AVG ignore NULLs
-- Use COALESCE for safety
SELECT COALESCE(SUM(amount), 0) FROM orders;
```

### String Concatenation with NULL
```sql
-- CONCAT handles NULLs gracefully
SELECT CONCAT(first_name, ' ', last_name);  -- NULL safe

-- || operator may return NULL if any part is NULL
SELECT COALESCE(first_name, '') || ' ' || COALESCE(last_name, '');
```

### Date Comparison Issues
```sql
-- Compare dates, not strings
WHERE date_col >= '2024-01-01'  -- Good
WHERE date_col >= '2024-1-1'    -- Risky (format dependent)

-- For timestamp columns, use date truncation
WHERE DATE(date_col) = '2024-01-01'  -- May prevent index use
WHERE date_col >= '2024-01-01 00:00:00' AND date_col < '2024-01-02 00:00:00'  -- Better
```

---

## SQL Dialect Differences

### LIMIT vs TOP vs FETCH
```sql
-- MySQL / PostgreSQL / SQLite
SELECT * FROM table LIMIT 10;
SELECT * FROM table LIMIT 10 OFFSET 20;

-- SQL Server
SELECT TOP 10 * FROM table;
SELECT TOP 10 * FROM table ORDER BY id;
SELECT * FROM table ORDER BY id OFFSET 20 ROWS FETCH NEXT 10 ROWS ONLY;

-- Oracle
SELECT * FROM table WHERE ROWNUM <= 10;
SELECT * FROM table FETCH FIRST 10 ROWS ONLY;
```

### String Concatenation
```sql
-- MySQL / PostgreSQL
SELECT CONCAT(col1, ' ', col2);

-- PostgreSQL also
SELECT col1 || ' ' || col2;

-- SQL Server
SELECT col1 + ' ' + col2;
SELECT CONCAT(col1, ' ', col2);
```

### Current Date/Time
```sql
-- MySQL: NOW(), CURDATE(), CURTIME()
-- PostgreSQL: NOW(), CURRENT_DATE, CURRENT_TIME
-- SQL Server: GETDATE(), SYSDATETIME()
-- Oracle: SYSDATE, CURRENT_TIMESTAMP
-- SQLite: DATE('now'), DATETIME('now')
```

### Auto-Increment
```sql
-- MySQL
CREATE TABLE t (id INT AUTO_INCREMENT PRIMARY KEY, ...);

-- PostgreSQL
CREATE TABLE t (id SERIAL PRIMARY KEY, ...);
-- or
CREATE TABLE t (id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY, ...);

-- SQL Server
CREATE TABLE t (id INT IDENTITY(1,1) PRIMARY KEY, ...);

-- SQLite
CREATE TABLE t (id INTEGER PRIMARY KEY AUTOINCREMENT, ...);
```

---

## Quick Reference: Most Common Queries

### Top N per Group
```sql
WITH ranked AS (
    SELECT *, ROW_NUMBER() OVER (PARTITION BY group_col ORDER BY sort_col DESC) as rn
    FROM table
)
SELECT * FROM ranked WHERE rn <= N;
```

### Running Total
```sql
SELECT *, SUM(amount) OVER (ORDER BY date) as running_total FROM table;
```

### Percentage of Total
```sql
SELECT *, amount * 100.0 / SUM(amount) OVER () as pct_of_total FROM table;
```

### Date Difference in Days
```sql
-- MySQL: DATEDIFF(end, start)
-- PostgreSQL: end - start
-- SQL Server: DATEDIFF(day, start, end)
```

### First/Last Value per Group
```sql
SELECT DISTINCT
    group_col,
    FIRST_VALUE(value_col) OVER (PARTITION BY group_col ORDER BY date) as first_val,
    LAST_VALUE(value_col) OVER (PARTITION BY group_col ORDER BY date ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) as last_val
FROM table;
```

### Gap Analysis (Missing Sequence)
```sql
SELECT t1.id + 1 as gap_start, t2.id - 1 as gap_end
FROM table t1
JOIN table t2 ON t1.id = t2.id - 1
WHERE t2.id - t1.id > 1;
```

### Median Calculation
```sql
-- PostgreSQL / SQL Server
SELECT PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY value) FROM table;

-- MySQL 8+
WITH ranked AS (
    SELECT value, ROW_NUMBER() OVER (ORDER BY value) as rn, COUNT(*) OVER () as cnt
    FROM table
)
SELECT AVG(value) as median FROM ranked
WHERE rn IN (FLOOR((cnt + 1) / 2), CEIL((cnt + 1) / 2));
```

### Cohort Analysis
```sql
WITH first_activity AS (
    SELECT user_id, MIN(activity_date) as cohort_date
    FROM activity
    GROUP BY user_id
),
activity_periods AS (
    SELECT 
        f.user_id,
        f.cohort_date,
        a.activity_date,
        DATEDIFF(a.activity_date, f.cohort_date) / 30 as period_months
    FROM first_activity f
    JOIN activity a ON f.user_id = a.user_id
)
SELECT 
    cohort_date,
    period_months,
    COUNT(DISTINCT user_id) as active_users,
    COUNT(DISTINCT user_id) * 100.0 / FIRST_VALUE(COUNT(DISTINCT user_id)) OVER (PARTITION BY cohort_date ORDER BY period_months) as retention_pct
FROM activity_periods
GROUP BY cohort_date, period_months;
```
