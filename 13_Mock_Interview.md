# Mock Interview - Complete Practice Test

## Instructions: 90 minutes total

---

## PART 1: SQL (25 minutes)

### Q1: Employee Hierarchy (10 min)
Table: Employees(employee_id, name, manager_id, salary, department)
Write a query to find all employees who earn more than their managers.

### Q2: Running Total (10 min)
Table: Sales(sale_id, sale_date, amount)
Write a query to calculate a running total of sales by date.

### Q3: Top N Per Group (5 min)
Table: Students(student_id, class, score)
Find the top 3 students in each class.

---

## PART 2: Excel (20 minutes)

### Q1: Data Analysis (10 min)
You have: Date | Product | Region | Units | Price | Cost
Create: Revenue, Profit, Profit Margin columns
Build a pivot table showing Profit Margin by Product and Region

### Q2: Formula Challenge (10 min)
Create a formula that:
- Shows "High" if revenue > $10,000 AND margin > 20%
- Shows "Medium" if revenue > $5,000 OR margin > 15%
- Shows "Low" otherwise

---

## PART 3: Power BI (20 minutes)

### Q1: DAX (10 min)
Write measures for:
- Total Sales
- Sales Last Year
- Year-over-Year Growth %
- Sales YTD

### Q2: Modeling (10 min)
Describe how you would model:
- Sales fact table
- Product dimension
- Date dimension
- Customer dimension
What relationships would you create?

---

## PART 4: Case Study (25 minutes)

### Scenario: Retail Sales Decline
A retail chain has seen 20% sales decline over 3 months.

1. What data would you request? (5 min)
2. What analysis would you perform? (10 min)
3. How would you present findings? (5 min)
4. What recommendations might you make? (5 min)

---

## ANSWER KEY

### SQL Q1
```sql
SELECT e.name, e.salary, m.name as manager, m.salary as manager_salary
FROM Employees e
JOIN Employees m ON e.manager_id = m.employee_id
WHERE e.salary > m.salary;
```

### SQL Q2
```sql
SELECT sale_date, amount,
       SUM(amount) OVER (ORDER BY sale_date) as running_total
FROM Sales;
```

### SQL Q3
```sql
WITH ranked AS (
    SELECT *, ROW_NUMBER() OVER (PARTITION BY class ORDER BY score DESC) as rn
    FROM Students
)
SELECT * FROM ranked WHERE rn <= 3;
```

### Excel Q2
```excel
=IF(AND(Revenue>10000, Margin>0.2), "High",
   IF(OR(Revenue>5000, Margin>0.15), "Medium", "Low"))
```

### Power BI Q1
```dax
Total Sales = SUM(Sales[Amount])
Sales LY = CALCULATE([Total Sales], SAMEPERIODLASTYEAR('Date'[Date]))
YoY Growth % = DIVIDE([Total Sales] - [Sales LY], [Sales LY], 0)
Sales YTD = TOTALYTD([Total Sales], 'Date'[Date])
```
