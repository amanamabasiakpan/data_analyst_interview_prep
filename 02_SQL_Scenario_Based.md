# SQL Scenario-Based Questions & Answers

## Table of Contents
1. [E-Commerce Analytics](#e-commerce-analytics)
2. [Employee Management](#employee-management)
3. [Sales & Revenue](#sales--revenue)
4. [Customer Analytics](#customer-analytics)
5. [Inventory Management](#inventory-management)
6. [Time-Series Analysis](#time-series-analysis)
7. [Data Quality & Cleanup](#data-quality--cleanup)
8. [Advanced Scenarios](#advanced-scenarios)

---

## E-Commerce Analytics

### Scenario 1: Find customers who made purchases in consecutive months
**Tables:** `customers(customer_id, name)`, `orders(order_id, customer_id, order_date, amount)`

**Answer:**
```sql
WITH monthly_orders AS (
    SELECT 
        customer_id,
        DATE_TRUNC('month', order_date) as order_month,
        COUNT(*) as order_count
    FROM orders
    GROUP BY customer_id, DATE_TRUNC('month', order_date)
),
consecutive AS (
    SELECT 
        customer_id,
        order_month,
        LAG(order_month) OVER (PARTITION BY customer_id ORDER BY order_month) as prev_month
    FROM monthly_orders
)
SELECT DISTINCT c.customer_id, cu.name
FROM consecutive c
JOIN customers cu ON c.customer_id = cu.customer_id
WHERE c.order_month = DATE_ADD(c.prev_month, INTERVAL 1 MONTH);
```

---

### Scenario 2: Calculate customer lifetime value (CLV) and segment them
**Tables:** `customers`, `orders`

**Answer:**
```sql
WITH customer_metrics AS (
    SELECT 
        c.customer_id,
        c.name,
        COUNT(DISTINCT o.order_id) as total_orders,
        SUM(o.amount) as total_revenue,
        AVG(o.amount) as avg_order_value,
        MIN(o.order_date) as first_order,
        MAX(o.order_date) as last_order,
        DATEDIFF(MAX(o.order_date), MIN(o.order_date)) as customer_lifespan_days
    FROM customers c
    JOIN orders o ON c.customer_id = o.customer_id
    GROUP BY c.customer_id, c.name
),
segmented AS (
    SELECT *,
        CASE 
            WHEN total_revenue >= 10000 AND total_orders >= 10 THEN 'Champions'
            WHEN total_revenue >= 5000 THEN 'Loyal Customers'
            WHEN total_orders >= 5 THEN 'Potential Loyalists'
            WHEN customer_lifespan_days <= 30 AND total_orders >= 2 THEN 'New Customers'
            WHEN DATEDIFF(CURDATE(), last_order) > 180 THEN 'At Risk'
            ELSE 'Others'
        END as segment
    FROM customer_metrics
)
SELECT segment, COUNT(*) as customer_count, SUM(total_revenue) as segment_revenue
FROM segmented
GROUP BY segment
ORDER BY segment_revenue DESC;
```

---

### Scenario 3: Find products frequently bought together (Market Basket Analysis)
**Tables:** `orders(order_id)`, `order_items(order_id, product_id)`

**Answer:**
```sql
WITH product_pairs AS (
    SELECT 
        a.product_id as product_a,
        b.product_id as product_b,
        COUNT(DISTINCT a.order_id) as co_occurrence
    FROM order_items a
    JOIN order_items b ON a.order_id = b.order_id AND a.product_id < b.product_id
    GROUP BY a.product_id, b.product_id
),
total_orders AS (
    SELECT product_id, COUNT(DISTINCT order_id) as total_count
    FROM order_items
    GROUP BY product_id
)
SELECT 
    p1.product_name as product_a,
    p2.product_name as product_b,
    pp.co_occurrence,
    ROUND(pp.co_occurrence * 100.0 / t1.total_count, 2) as support_a_percent,
    ROUND(pp.co_occurrence * 100.0 / t2.total_count, 2) as support_b_percent
FROM product_pairs pp
JOIN products p1 ON pp.product_a = p1.product_id
JOIN products p2 ON pp.product_b = p2.product_id
JOIN total_orders t1 ON pp.product_a = t1.product_id
JOIN total_orders t2 ON pp.product_b = t2.product_id
WHERE pp.co_occurrence >= 10
ORDER BY pp.co_occurrence DESC
LIMIT 20;
```

---

### Scenario 4: Calculate month-over-month growth rate
**Tables:** `sales(date, revenue)`

**Answer:**
```sql
WITH monthly_revenue AS (
    SELECT 
        DATE_FORMAT(date, '%Y-%m') as month,
        SUM(revenue) as total_revenue
    FROM sales
    GROUP BY DATE_FORMAT(date, '%Y-%m')
),
growth AS (
    SELECT 
        month,
        total_revenue,
        LAG(total_revenue) OVER (ORDER BY month) as prev_month_revenue,
        ROUND(
            (total_revenue - LAG(total_revenue) OVER (ORDER BY month)) * 100.0 
            / LAG(total_revenue) OVER (ORDER BY month), 2
        ) as mom_growth_percent
    FROM monthly_revenue
)
SELECT * FROM growth ORDER BY month;
```

---

### Scenario 5: Identify abandoned shopping carts
**Tables:** `carts(cart_id, customer_id, created_at, status)`, `cart_items(cart_id, product_id, quantity)`

**Answer:**
```sql
SELECT 
    c.cart_id,
    c.customer_id,
    cu.name,
    c.created_at,
    TIMESTAMPDIFF(HOUR, c.created_at, NOW()) as hours_abandoned,
    COUNT(ci.product_id) as items_in_cart,
    SUM(ci.quantity * p.price) as cart_value
FROM carts c
JOIN customers cu ON c.customer_id = cu.customer_id
LEFT JOIN cart_items ci ON c.cart_id = ci.cart_id
LEFT JOIN products p ON ci.product_id = p.product_id
WHERE c.status = 'active'
  AND c.created_at < DATE_SUB(NOW(), INTERVAL 24 HOUR)
  AND NOT EXISTS (
      SELECT 1 FROM orders o WHERE o.cart_id = c.cart_id
  )
GROUP BY c.cart_id, c.customer_id, cu.name, c.created_at
HAVING cart_value > 50
ORDER BY cart_value DESC;
```

---

## Employee Management

### Scenario 6: Find employees with the longest tenure in each department
**Tables:** `employees(employee_id, name, department, hire_date, salary)`

**Answer:**
```sql
WITH tenure_calc AS (
    SELECT 
        employee_id,
        name,
        department,
        hire_date,
        DATEDIFF(CURDATE(), hire_date) as days_employed,
        ROW_NUMBER() OVER (PARTITION BY department ORDER BY hire_date ASC) as tenure_rank
    FROM employees
    WHERE status = 'Active'
)
SELECT department, name, hire_date, 
       FLOOR(days_employed / 365) as years,
       FLOOR((days_employed % 365) / 30) as months
FROM tenure_calc
WHERE tenure_rank = 1;
```

---

### Scenario 7: Calculate salary percentile for each employee
**Tables:** `employees(employee_id, name, department, salary)`

**Answer:**
```sql
SELECT 
    employee_id,
    name,
    department,
    salary,
    PERCENT_RANK() OVER (ORDER BY salary) as overall_percentile,
    PERCENT_RANK() OVER (PARTITION BY department ORDER BY salary) as dept_percentile,
    NTILE(4) OVER (ORDER BY salary) as salary_quartile,
    CASE 
        WHEN PERCENT_RANK() OVER (ORDER BY salary) >= 0.9 THEN 'Top 10%'
        WHEN PERCENT_RANK() OVER (ORDER BY salary) >= 0.75 THEN 'Top 25%'
        WHEN PERCENT_RANK() OVER (ORDER BY salary) >= 0.5 THEN 'Above Median'
        ELSE 'Below Median'
    END as salary_tier
FROM employees
WHERE status = 'Active';
```

---

### Scenario 8: Find employees who have never taken a leave
**Tables:** `employees(employee_id, name)`, `leaves(leave_id, employee_id, leave_date, type)`

**Answer:**
```sql
-- Method 1: Using NOT EXISTS
SELECT e.employee_id, e.name, e.department, e.hire_date
FROM employees e
WHERE NOT EXISTS (
    SELECT 1 FROM leaves l WHERE l.employee_id = e.employee_id
);

-- Method 2: Using LEFT JOIN
SELECT e.employee_id, e.name, e.department
FROM employees e
LEFT JOIN leaves l ON e.employee_id = l.employee_id
WHERE l.leave_id IS NULL;

-- Method 3: Using NOT IN
SELECT employee_id, name, department
FROM employees
WHERE employee_id NOT IN (
    SELECT DISTINCT employee_id FROM leaves WHERE employee_id IS NOT NULL
);
```

---

### Scenario 9: Find employees with salary higher than their department average but lower than company average
**Tables:** `employees(employee_id, name, department, salary)`

**Answer:**
```sql
WITH dept_avg AS (
    SELECT department, AVG(salary) as dept_avg_salary
    FROM employees
    GROUP BY department
),
company_avg AS (
    SELECT AVG(salary) as company_avg_salary FROM employees
)
SELECT 
    e.employee_id,
    e.name,
    e.department,
    e.salary,
    d.dept_avg_salary,
    c.company_avg_salary
FROM employees e
JOIN dept_avg d ON e.department = d.department
CROSS JOIN company_avg c
WHERE e.salary > d.dept_avg_salary
  AND e.salary < c.company_avg_salary;
```

---

### Scenario 10: Find employees who report to the same manager and work in different departments
**Tables:** `employees(employee_id, name, department, manager_id)`

**Answer:**
```sql
SELECT 
    m.name as manager_name,
    e1.name as employee_1,
    e1.department as dept_1,
    e2.name as employee_2,
    e2.department as dept_2
FROM employees e1
JOIN employees e2 ON e1.manager_id = e2.manager_id 
    AND e1.employee_id < e2.employee_id
    AND e1.department != e2.department
JOIN employees m ON e1.manager_id = m.employee_id
WHERE e1.manager_id IS NOT NULL;
```

---

## Sales & Revenue

### Scenario 11: Calculate year-over-year (YoY) growth by product category
**Tables:** `sales(order_id, product_id, category, sale_date, amount)`

**Answer:**
```sql
WITH yearly_sales AS (
    SELECT 
        category,
        YEAR(sale_date) as year,
        SUM(amount) as total_sales
    FROM sales
    GROUP BY category, YEAR(sale_date)
),
yoy AS (
    SELECT 
        category,
        year,
        total_sales,
        LAG(total_sales) OVER (PARTITION BY category ORDER BY year) as prev_year_sales,
        ROUND(
            (total_sales - LAG(total_sales) OVER (PARTITION BY category ORDER BY year)) * 100.0
            / NULLIF(LAG(total_sales) OVER (PARTITION BY category ORDER BY year), 0), 2
        ) as yoy_growth_percent
    FROM yearly_sales
)
SELECT * FROM yoy ORDER BY category, year;
```

---

### Scenario 12: Find the best-selling product each month
**Tables:** `sales(product_id, sale_date, quantity)`, `products(product_id, product_name)`

**Answer:**
```sql
WITH monthly_sales AS (
    SELECT 
        DATE_FORMAT(sale_date, '%Y-%m') as month,
        s.product_id,
        p.product_name,
        SUM(s.quantity) as total_quantity
    FROM sales s
    JOIN products p ON s.product_id = p.product_id
    GROUP BY DATE_FORMAT(sale_date, '%Y-%m'), s.product_id, p.product_name
),
ranked AS (
    SELECT *,
        RANK() OVER (PARTITION BY month ORDER BY total_quantity DESC) as rnk
    FROM monthly_sales
)
SELECT month, product_name, total_quantity
FROM ranked
WHERE rnk = 1
ORDER BY month;
```

---

### Scenario 13: Calculate cumulative revenue share (Pareto Analysis)
**Tables:** `customers(customer_id, name)`, `orders(customer_id, amount)`

**Answer:**
```sql
WITH customer_revenue AS (
    SELECT 
        c.customer_id,
        c.name,
        COALESCE(SUM(o.amount), 0) as total_revenue
    FROM customers c
    LEFT JOIN orders o ON c.customer_id = o.customer_id
    GROUP BY c.customer_id, c.name
),
ranked AS (
    SELECT *,
        SUM(total_revenue) OVER (ORDER BY total_revenue DESC) as cumulative_revenue,
        SUM(total_revenue) OVER () as total_company_revenue,
        ROUND(
            SUM(total_revenue) OVER (ORDER BY total_revenue DESC) * 100.0 
            / SUM(total_revenue) OVER (), 2
        ) as cumulative_percent,
        ROW_NUMBER() OVER (ORDER BY total_revenue DESC) as customer_rank
    FROM customer_revenue
    WHERE total_revenue > 0
)
SELECT 
    customer_rank,
    name,
    total_revenue,
    cumulative_percent,
    CASE 
        WHEN cumulative_percent <= 80 THEN 'Top 80% Revenue (Vital Few)'
        ELSE 'Bottom 20% Revenue (Trivial Many)'
    END as pareto_segment
FROM ranked;
```

---

### Scenario 14: Find sales representatives who exceeded their quarterly targets
**Tables:** `sales_reps(rep_id, name, quarterly_target)`, `sales(rep_id, sale_date, amount)`

**Answer:**
```sql
WITH quarterly_performance AS (
    SELECT 
        sr.rep_id,
        sr.name,
        sr.quarterly_target,
        CONCAT(YEAR(s.sale_date), '-Q', QUARTER(s.sale_date)) as quarter,
        SUM(s.amount) as quarterly_sales
    FROM sales_reps sr
    JOIN sales s ON sr.rep_id = s.rep_id
    GROUP BY sr.rep_id, sr.name, sr.quarterly_target, 
             YEAR(s.sale_date), QUARTER(s.sale_date)
)
SELECT 
    rep_id,
    name,
    quarter,
    quarterly_target,
    quarterly_sales,
    ROUND((quarterly_sales - quarterly_target) * 100.0 / quarterly_target, 2) as overachievement_percent
FROM quarterly_performance
WHERE quarterly_sales > quarterly_target
ORDER BY overachievement_percent DESC;
```

---

### Scenario 15: Calculate moving average of daily sales with 7-day and 30-day windows
**Tables:** `daily_sales(date, revenue)`

**Answer:**
```sql
SELECT 
    date,
    revenue,
    AVG(revenue) OVER (ORDER BY date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) as ma_7day,
    AVG(revenue) OVER (ORDER BY date ROWS BETWEEN 29 PRECEDING AND CURRENT ROW) as ma_30day,
    AVG(revenue) OVER (ORDER BY date ROWS BETWEEN 2 PRECEDING AND 2 FOLLOWING) as ma_centered_5day,
    -- Exponential moving average approximation
    revenue * 0.2 + LAG(revenue, 1) OVER (ORDER BY date) * 0.16 + 
    LAG(revenue, 2) OVER (ORDER BY date) * 0.128 as ema_approx
FROM daily_sales
ORDER BY date;
```

---

## Customer Analytics

### Scenario 16: Calculate customer retention rate by cohort
**Tables:** `orders(customer_id, order_date, amount)`

**Answer:**
```sql
WITH first_orders AS (
    SELECT 
        customer_id,
        MIN(DATE_FORMAT(order_date, '%Y-%m')) as cohort_month
    FROM orders
    GROUP BY customer_id
),
order_months AS (
    SELECT 
        fo.customer_id,
        fo.cohort_month,
        DATE_FORMAT(o.order_date, '%Y-%m') as order_month,
        PERIOD_DIFF(DATE_FORMAT(o.order_date, '%Y%m'), DATE_FORMAT(fo.cohort_month, '%Y%m')) as months_since_first
    FROM first_orders fo
    JOIN orders o ON fo.customer_id = o.customer_id
    GROUP BY fo.customer_id, fo.cohort_month, DATE_FORMAT(o.order_date, '%Y-%m')
),
cohort_analysis AS (
    SELECT 
        cohort_month,
        months_since_first,
        COUNT(DISTINCT customer_id) as active_customers,
        FIRST_VALUE(COUNT(DISTINCT customer_id)) OVER (
            PARTITION BY cohort_month ORDER BY months_since_first
        ) as cohort_size
    FROM order_months
    GROUP BY cohort_month, months_since_first
)
SELECT 
    cohort_month,
    months_since_first,
    active_customers,
    cohort_size,
    ROUND(active_customers * 100.0 / cohort_size, 2) as retention_rate
FROM cohort_analysis
ORDER BY cohort_month, months_since_first;
```

---

### Scenario 17: Identify customers at risk of churning (no purchase in 90 days)
**Tables:** `customers(customer_id, name, signup_date)`, `orders(customer_id, order_date, amount)`

**Answer:**
```sql
WITH customer_activity AS (
    SELECT 
        c.customer_id,
        c.name,
        c.signup_date,
        MAX(o.order_date) as last_order_date,
        COUNT(o.order_id) as total_orders,
        SUM(o.amount) as lifetime_value,
        DATEDIFF(CURDATE(), MAX(o.order_date)) as days_since_last_order
    FROM customers c
    LEFT JOIN orders o ON c.customer_id = o.customer_id
    GROUP BY c.customer_id, c.name, c.signup_date
)
SELECT 
    customer_id,
    name,
    last_order_date,
    days_since_last_order,
    total_orders,
    lifetime_value,
    CASE 
        WHEN days_since_last_order > 180 THEN 'Churned'
        WHEN days_since_last_order > 90 THEN 'At Risk'
        WHEN days_since_last_order > 60 THEN 'Warning'
        ELSE 'Active'
    END as churn_risk,
    CASE 
        WHEN lifetime_value > 5000 AND days_since_last_order > 90 THEN 'High Value - Urgent'
        WHEN lifetime_value > 1000 AND days_since_last_order > 90 THEN 'Medium Value - Follow Up'
        ELSE 'Standard'
    END as priority
FROM customer_activity
WHERE days_since_last_order > 60 OR days_since_last_order IS NULL
ORDER BY lifetime_value DESC, days_since_last_order DESC;
```

---

### Scenario 18: Find customers who made exactly 3 purchases in the last 6 months
**Tables:** `customers`, `orders`

**Answer:**
```sql
SELECT 
    c.customer_id,
    c.name,
    c.email,
    COUNT(o.order_id) as purchase_count,
    SUM(o.amount) as total_spent,
    MIN(o.order_date) as first_purchase,
    MAX(o.order_date) as last_purchase
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
WHERE o.order_date >= DATE_SUB(CURDATE(), INTERVAL 6 MONTH)
GROUP BY c.customer_id, c.name, c.email
HAVING COUNT(o.order_id) = 3
ORDER BY total_spent DESC;
```

---

### Scenario 19: Calculate average time between orders for each customer
**Tables:** `orders(order_id, customer_id, order_date)`

**Answer:**
```sql
WITH order_gaps AS (
    SELECT 
        customer_id,
        order_date,
        LAG(order_date) OVER (PARTITION BY customer_id ORDER BY order_date) as prev_order_date,
        DATEDIFF(order_date, LAG(order_date) OVER (PARTITION BY customer_id ORDER BY order_date)) as days_between
    FROM orders
)
SELECT 
    customer_id,
    COUNT(*) as total_orders,
    ROUND(AVG(days_between), 1) as avg_days_between_orders,
    MIN(days_between) as min_gap,
    MAX(days_between) as max_gap,
    ROUND(STDDEV(days_between), 1) as stddev_gap
FROM order_gaps
WHERE days_between IS NOT NULL
GROUP BY customer_id
HAVING COUNT(*) >= 2
ORDER BY avg_days_between_orders;
```

---

### Scenario 20: Find customers who bought from all product categories
**Tables:** `customers`, `orders`, `order_items`, `products(category)`

**Answer:**
```sql
WITH customer_categories AS (
    SELECT DISTINCT 
        o.customer_id,
        p.category
    FROM orders o
    JOIN order_items oi ON o.order_id = oi.order_id
    JOIN products p ON oi.product_id = p.product_id
),
category_counts AS (
    SELECT customer_id, COUNT(DISTINCT category) as categories_bought
    FROM customer_categories
    GROUP BY customer_id
),
total_categories AS (
    SELECT COUNT(DISTINCT category) as total FROM products
)
SELECT 
    c.customer_id,
    cu.name,
    cc.categories_bought,
    tc.total as total_categories_available
FROM category_counts c
JOIN customers cu ON c.customer_id = cu.customer_id
CROSS JOIN total_categories tc
WHERE cc.categories_bought = tc.total;
```

---

## Inventory Management

### Scenario 21: Find products that are below reorder level
**Tables:** `products(product_id, name, stock_quantity, reorder_level, supplier_id)`, `suppliers(supplier_id, name, lead_time_days)`

**Answer:**
```sql
SELECT 
    p.product_id,
    p.name as product_name,
    p.stock_quantity,
    p.reorder_level,
    p.reorder_level - p.stock_quantity as shortage,
    s.name as supplier_name,
    s.lead_time_days,
    CASE 
        WHEN p.stock_quantity = 0 THEN 'Critical - Out of Stock'
        WHEN p.stock_quantity < p.reorder_level * 0.5 THEN 'Urgent Reorder'
        WHEN p.stock_quantity < p.reorder_level THEN 'Below Reorder Level'
        ELSE 'Adequate'
    END as stock_status
FROM products p
JOIN suppliers s ON p.supplier_id = s.supplier_id
WHERE p.stock_quantity <= p.reorder_level
ORDER BY 
    CASE 
        WHEN p.stock_quantity = 0 THEN 1
        WHEN p.stock_quantity < p.reorder_level * 0.5 THEN 2
        ELSE 3
    END,
    shortage DESC;
```

---

### Scenario 22: Calculate inventory turnover ratio by category
**Tables:** `products(product_id, category, unit_cost)`, `sales(product_id, quantity, sale_date)`

**Answer:**
```sql
WITH category_sales AS (
    SELECT 
        p.category,
        SUM(s.quantity * p.unit_cost) as cogs
    FROM sales s
    JOIN products p ON s.product_id = p.product_id
    WHERE s.sale_date >= DATE_SUB(CURDATE(), INTERVAL 1 YEAR)
    GROUP BY p.category
),
category_inventory AS (
    SELECT 
        category,
        SUM(stock_quantity * unit_cost) as avg_inventory_value
    FROM products
    GROUP BY category
)
SELECT 
    cs.category,
    cs.cogs,
    ci.avg_inventory_value,
    ROUND(cs.cogs / NULLIF(ci.avg_inventory_value, 0), 2) as inventory_turnover_ratio,
    ROUND(365.0 / NULLIF(cs.cogs / ci.avg_inventory_value, 0), 1) as days_inventory_outstanding
FROM category_sales cs
JOIN category_inventory ci ON cs.category = ci.category
ORDER BY inventory_turnover_ratio DESC;
```

---

### Scenario 23: Find slow-moving inventory (no sales in 6 months)
**Tables:** `products`, `sales`

**Answer:**
```sql
WITH last_sale AS (
    SELECT 
        product_id,
        MAX(sale_date) as last_sale_date,
        COUNT(*) as total_sales_count,
        SUM(quantity) as total_units_sold
    FROM sales
    GROUP BY product_id
)
SELECT 
    p.product_id,
    p.name,
    p.category,
    p.stock_quantity,
    p.unit_cost,
    p.stock_quantity * p.unit_cost as inventory_value,
    COALESCE(ls.last_sale_date, 'Never') as last_sale_date,
    COALESCE(ls.total_sales_count, 0) as total_sales,
    COALESCE(ls.total_units_sold, 0) as units_sold,
    DATEDIFF(CURDATE(), ls.last_sale_date) as days_since_last_sale,
    CASE 
        WHEN ls.last_sale_date IS NULL THEN 'Dead Stock - Never Sold'
        WHEN DATEDIFF(CURDATE(), ls.last_sale_date) > 365 THEN 'Obsolete'
        WHEN DATEDIFF(CURDATE(), ls.last_sale_date) > 180 THEN 'Slow Moving'
        WHEN DATEDIFF(CURDATE(), ls.last_sale_date) > 90 THEN 'Moderate'
        ELSE 'Fast Moving'
    END as movement_status
FROM products p
LEFT JOIN last_sale ls ON p.product_id = ls.product_id
WHERE ls.last_sale_date < DATE_SUB(CURDATE(), INTERVAL 6 MONTH)
   OR ls.last_sale_date IS NULL
ORDER BY inventory_value DESC;
```

---

## Time-Series Analysis

### Scenario 24: Calculate week-over-week comparison for the last 12 weeks
**Tables:** `sales(date, amount)`

**Answer:**
```sql
WITH weekly_sales AS (
    SELECT 
        YEARWEEK(date) as year_week,
        MIN(date) as week_start,
        SUM(amount) as weekly_revenue
    FROM sales
    WHERE date >= DATE_SUB(CURDATE(), INTERVAL 12 WEEK)
    GROUP BY YEARWEEK(date)
),
weekly_comparison AS (
    SELECT 
        year_week,
        week_start,
        weekly_revenue,
        LAG(weekly_revenue) OVER (ORDER BY year_week) as prev_week_revenue,
        LAG(weekly_revenue, 52) OVER (ORDER BY year_week) as same_week_last_year,
        ROUND(
            (weekly_revenue - LAG(weekly_revenue) OVER (ORDER BY year_week)) * 100.0
            / NULLIF(LAG(weekly_revenue) OVER (ORDER BY year_week), 0), 2
        ) as wow_change_percent,
        ROUND(
            (weekly_revenue - LAG(weekly_revenue, 52) OVER (ORDER BY year_week)) * 100.0
            / NULLIF(LAG(weekly_revenue, 52) OVER (ORDER BY year_week), 0), 2
        ) as yoy_change_percent
    FROM weekly_sales
)
SELECT * FROM weekly_comparison
ORDER BY year_week DESC;
```

---

### Scenario 25: Find the longest streak of consecutive days with sales
**Tables:** `daily_sales(date, revenue)`

**Answer:**
```sql
WITH sales_days AS (
    SELECT DISTINCT date
    FROM daily_sales
    WHERE revenue > 0
),
streaks AS (
    SELECT 
        date,
        DATE_SUB(date, INTERVAL ROW_NUMBER() OVER (ORDER BY date) DAY) as streak_group
    FROM sales_days
),
streak_lengths AS (
    SELECT 
        streak_group,
        COUNT(*) as streak_length,
        MIN(date) as streak_start,
        MAX(date) as streak_end
    FROM streaks
    GROUP BY streak_group
)
SELECT 
    streak_start,
    streak_end,
    streak_length,
    DATEDIFF(streak_end, streak_start) + 1 as days_with_sales
FROM streak_lengths
ORDER BY streak_length DESC
LIMIT 10;
```

---

## Data Quality & Cleanup

### Scenario 26: Find and report data quality issues
**Tables:** `customers(customer_id, name, email, phone, address)`

**Answer:**
```sql
WITH quality_checks AS (
    SELECT 
        customer_id,
        name,
        email,
        phone,
        address,
        CASE WHEN name IS NULL OR TRIM(name) = '' THEN 1 ELSE 0 END as missing_name,
        CASE WHEN email IS NULL OR TRIM(email) = '' THEN 1 ELSE 0 END as missing_email,
        CASE WHEN email IS NOT NULL AND email NOT LIKE '%@%.%' THEN 1 ELSE 0 END as invalid_email,
        CASE WHEN phone IS NULL OR TRIM(phone) = '' THEN 1 ELSE 0 END as missing_phone,
        CASE WHEN phone IS NOT NULL AND LENGTH(REGEXP_REPLACE(phone, '[^0-9]', '')) < 10 THEN 1 ELSE 0 END as invalid_phone,
        CASE WHEN address IS NULL OR TRIM(address) = '' THEN 1 ELSE 0 END as missing_address
    FROM customers
)
SELECT 
    'Missing Names' as issue_type, COUNT(*) as count FROM quality_checks WHERE missing_name = 1
UNION ALL
SELECT 'Missing Emails', COUNT(*) FROM quality_checks WHERE missing_email = 1
UNION ALL
SELECT 'Invalid Emails', COUNT(*) FROM quality_checks WHERE invalid_email = 1
UNION ALL
SELECT 'Missing Phones', COUNT(*) FROM quality_checks WHERE missing_phone = 1
UNION ALL
SELECT 'Invalid Phones', COUNT(*) FROM quality_checks WHERE invalid_phone = 1
UNION ALL
SELECT 'Missing Addresses', COUNT(*) FROM quality_checks WHERE missing_address = 1
UNION ALL
SELECT 'Multiple Issues', COUNT(*) FROM quality_checks 
WHERE (missing_name + missing_email + invalid_email + missing_phone + invalid_phone + missing_address) > 1;
```

---

### Scenario 27: Standardize phone number formats
**Tables:** `customers(customer_id, phone)`

**Answer:**
```sql
-- Standardize to +1-XXX-XXX-XXXX format
SELECT 
    customer_id,
    phone as original_phone,
    CONCAT('+1-', 
        SUBSTRING(cleaned, 1, 3), '-',
        SUBSTRING(cleaned, 4, 3), '-',
        SUBSTRING(cleaned, 7, 4)
    ) as standardized_phone
FROM (
    SELECT 
        customer_id,
        phone,
        REGEXP_REPLACE(phone, '[^0-9]', '') as cleaned
    FROM customers
    WHERE phone IS NOT NULL
) sub
WHERE LENGTH(cleaned) = 10;
```

---

### Scenario 28: Find and merge duplicate customer records
**Tables:** `customers(customer_id, name, email, phone, created_at)`

**Answer:**
```sql
-- Find potential duplicates based on email or phone
WITH duplicates AS (
    SELECT 
        c1.customer_id as id1,
        c2.customer_id as id2,
        c1.name as name1,
        c2.name as name2,
        c1.email,
        c1.phone,
        c1.created_at as date1,
        c2.created_at as date2,
        CASE 
            WHEN c1.email = c2.email AND c1.phone = c2.phone THEN 'Exact Match'
            WHEN c1.email = c2.email THEN 'Email Match'
            WHEN c1.phone = c2.phone THEN 'Phone Match'
            ELSE 'Fuzzy Match'
        END as match_type
    FROM customers c1
    JOIN customers c2 ON (
        (c1.email = c2.email OR c1.phone = c2.phone)
        AND c1.customer_id < c2.customer_id
    )
)
SELECT * FROM duplicates ORDER BY match_type, email;
```

---

## Advanced Scenarios

### Scenario 29: Calculate median salary by department
**Tables:** `employees(employee_id, name, department, salary)`

**Answer:**
```sql
-- Method 1: Using PERCENTILE_CONT (PostgreSQL, SQL Server)
SELECT 
    department,
    PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY salary) as median_salary,
    AVG(salary) as mean_salary,
    PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY salary) as q1,
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY salary) as q3
FROM employees
GROUP BY department;

-- Method 2: Using window functions (works in MySQL 8+)
WITH ranked AS (
    SELECT 
        department,
        salary,
        ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary) as rn,
        COUNT(*) OVER (PARTITION BY department) as cnt
    FROM employees
)
SELECT 
    department,
    AVG(salary) as median_salary
FROM ranked
WHERE rn IN (FLOOR((cnt + 1) / 2.0), CEIL((cnt + 1) / 2.0))
GROUP BY department;
```

---

### Scenario 30: Find gaps in a sequence (missing invoice numbers)
**Tables:** `invoices(invoice_number)`

**Answer:**
```sql
WITH number_range AS (
    SELECT MIN(invoice_number) as min_num, MAX(invoice_number) as max_num
    FROM invoices
),
all_numbers AS (
    SELECT min_num + n as num
    FROM number_range
    CROSS JOIN (
        SELECT a.N + b.N * 10 + c.N * 100 as n
        FROM 
            (SELECT 0 as N UNION SELECT 1 UNION SELECT 2 UNION SELECT 3 UNION SELECT 4
             UNION SELECT 5 UNION SELECT 6 UNION SELECT 7 UNION SELECT 8 UNION SELECT 9) a,
            (SELECT 0 as N UNION SELECT 1 UNION SELECT 2 UNION SELECT 3 UNION SELECT 4
             UNION SELECT 5 UNION SELECT 6 UNION SELECT 7 UNION SELECT 8 UNION SELECT 9) b,
            (SELECT 0 as N UNION SELECT 1 UNION SELECT 2 UNION SELECT 3 UNION SELECT 4
             UNION SELECT 5 UNION SELECT 6 UNION SELECT 7 UNION SELECT 8 UNION SELECT 9) c
    ) numbers
    WHERE min_num + n <= (SELECT max_num FROM number_range)
)
SELECT num as missing_invoice_number
FROM all_numbers
WHERE num NOT IN (SELECT invoice_number FROM invoices)
ORDER BY num;
```

---

### Scenario 31: Build a recursive org chart with indentation
**Tables:** `employees(employee_id, name, manager_id, title)`

**Answer:**
```sql
WITH RECURSIVE org_chart AS (
    -- Anchor: CEO (no manager)
    SELECT 
        employee_id,
        name,
        title,
        manager_id,
        0 as level,
        CAST(name AS VARCHAR(500)) as hierarchy_path
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    -- Recursive: employees with managers
    SELECT 
        e.employee_id,
        e.name,
        e.title,
        e.manager_id,
        oc.level + 1,
        CONCAT(oc.hierarchy_path, ' > ', e.name)
    FROM employees e
    JOIN org_chart oc ON e.manager_id = oc.employee_id
)
SELECT 
    REPEAT('  ', level) || name as org_tree,
    title,
    level,
    hierarchy_path
FROM org_chart
ORDER BY hierarchy_path;
```

---

### Scenario 32: Calculate salary bands and count employees in each
**Tables:** `employees(salary)`

**Answer:**
```sql
WITH salary_stats AS (
    SELECT 
        MIN(salary) as min_sal,
        MAX(salary) as max_sal,
        (MAX(salary) - MIN(salary)) / 5 as band_width
    FROM employees
),
bands AS (
    SELECT 
        min_sal + (band_width * (n - 1)) as band_min,
        min_sal + (band_width * n) as band_max,
        CONCAT(
            ROUND(min_sal + (band_width * (n - 1)), 0), 
            ' - ', 
            ROUND(min_sal + (band_width * n), 0)
        ) as band_range
    FROM salary_stats
    CROSS JOIN (SELECT 1 as n UNION SELECT 2 UNION SELECT 3 UNION SELECT 4 UNION SELECT 5) nums
)
SELECT 
    b.band_range,
    COUNT(e.employee_id) as employee_count,
    ROUND(AVG(e.salary), 2) as avg_salary_in_band,
    MIN(e.salary) as min_in_band,
    MAX(e.salary) as max_in_band
FROM bands b
LEFT JOIN employees e ON e.salary >= b.band_min AND e.salary < b.band_max
GROUP BY b.band_range, b.band_min
ORDER BY b.band_min;
```

---

### Scenario 33: Find overlapping date ranges (double-booked rooms)
**Tables:** `bookings(room_id, start_date, end_date, guest_name)`

**Answer:**
```sql
SELECT 
    b1.room_id,
    b1.guest_name as guest_1,
    b1.start_date as start_1,
    b1.end_date as end_1,
    b2.guest_name as guest_2,
    b2.start_date as start_2,
    b2.end_date as end_2,
    GREATEST(b1.start_date, b2.start_date) as overlap_start,
    LEAST(b1.end_date, b2.end_date) as overlap_end,
    DATEDIFF(LEAST(b1.end_date, b2.end_date), GREATEST(b1.start_date, b2.start_date)) + 1 as overlap_days
FROM bookings b1
JOIN bookings b2 ON b1.room_id = b2.room_id 
    AND b1.booking_id < b2.booking_id
WHERE b1.start_date <= b2.end_date AND b1.end_date >= b2.start_date;
```

---

### Scenario 34: Calculate employee tenure in years, months, days
**Tables:** `employees(hire_date)`

**Answer:**
```sql
SELECT 
    employee_id,
    name,
    hire_date,
    TIMESTAMPDIFF(YEAR, hire_date, CURDATE()) as years,
    TIMESTAMPDIFF(MONTH, hire_date, CURDATE()) % 12 as months,
    TIMESTAMPDIFF(DAY, hire_date, CURDATE()) % 30 as days,
    CONCAT(
        TIMESTAMPDIFF(YEAR, hire_date, CURDATE()), ' years, ',
        TIMESTAMPDIFF(MONTH, hire_date, CURDATE()) % 12, ' months, ',
        TIMESTAMPDIFF(DAY, hire_date, CURDATE()) % 30, ' days'
    ) as tenure_formatted
FROM employees
WHERE status = 'Active'
ORDER BY hire_date;
```

---

### Scenario 35: Pivot sales data (rows to columns)
**Tables:** `sales(year, quarter, amount)`

**Answer:**
```sql
-- Static pivot
SELECT 
    year,
    SUM(CASE WHEN quarter = 1 THEN amount ELSE 0 END) as Q1,
    SUM(CASE WHEN quarter = 2 THEN amount ELSE 0 END) as Q2,
    SUM(CASE WHEN quarter = 3 THEN amount ELSE 0 END) as Q3,
    SUM(CASE WHEN quarter = 4 THEN amount ELSE 0 END) as Q4,
    SUM(amount) as total
FROM sales
GROUP BY year
ORDER BY year;

-- Dynamic pivot (SQL Server)
DECLARE @cols NVARCHAR(MAX), @query NVARCHAR(MAX);
SELECT @cols = STRING_AGG(QUOTENAME(quarter), ',') 
FROM (SELECT DISTINCT quarter FROM sales) t;

SET @query = '
SELECT year, ' + @cols + ', (Q1+Q2+Q3+Q4) as total
FROM (
    SELECT year, quarter, amount FROM sales
) src
PIVOT (
    SUM(amount) FOR quarter IN (' + @cols + ')
) piv
ORDER BY year';

EXEC sp_executesql @query;
```

---

### Scenario 36: Find top N per group with ties
**Tables:** `students(student_id, class, score)`

**Answer:**
```sql
-- Top 3 students per class, including ties
WITH ranked AS (
    SELECT 
        student_id,
        class,
        score,
        DENSE_RANK() OVER (PARTITION BY class ORDER BY score DESC) as rnk
    FROM students
)
SELECT * FROM ranked WHERE rnk <= 3 ORDER BY class, rnk, student_id;

-- Top 3 with exactly 3 per class (no ties)
WITH ranked AS (
    SELECT 
        student_id,
        class,
        score,
        ROW_NUMBER() OVER (PARTITION BY class ORDER BY score DESC, student_id) as rnk
    FROM students
)
SELECT * FROM ranked WHERE rnk <= 3 ORDER BY class, rnk;
```

---

### Scenario 37: Calculate running balance (bank account style)
**Tables:** `transactions(account_id, transaction_date, amount, type)`

**Answer:**
```sql
SELECT 
    account_id,
    transaction_date,
    type,
    amount,
    CASE type 
        WHEN 'credit' THEN amount 
        ELSE -amount 
    END as signed_amount,
    SUM(CASE type WHEN 'credit' THEN amount ELSE -amount END) 
        OVER (PARTITION BY account_id ORDER BY transaction_date, transaction_id) as running_balance
FROM transactions
ORDER BY account_id, transaction_date;
```

---

### Scenario 38: Find the mode (most frequent value) in a column
**Tables:** `orders(product_id)`

**Answer:**
```sql
WITH frequency AS (
    SELECT product_id, COUNT(*) as freq
    FROM orders
    GROUP BY product_id
),
max_freq AS (
    SELECT MAX(freq) as max_count FROM frequency
)
SELECT f.product_id, p.product_name, f.freq as mode_frequency
FROM frequency f
JOIN max_freq m ON f.freq = m.max_count
JOIN products p ON f.product_id = p.product_id;
```

---

### Scenario 39: Generate a date series and left join with sales
**Tables:** `sales(date, amount)`

**Answer:**
```sql
WITH RECURSIVE date_series AS (
    SELECT DATE('2024-01-01') as date
    UNION ALL
    SELECT DATE_ADD(date, INTERVAL 1 DAY)
    FROM date_series
    WHERE date < '2024-12-31'
)
SELECT 
    ds.date,
    DAYNAME(ds.date) as day_name,
    COALESCE(SUM(s.amount), 0) as daily_sales,
    COALESCE(COUNT(s.sale_id), 0) as transaction_count
FROM date_series ds
LEFT JOIN sales s ON ds.date = s.date
GROUP BY ds.date
ORDER BY ds.date;
```

---

### Scenario 40: Complex report - Sales performance dashboard query
**Tables:** `sales`, `products`, `categories`, `sales_reps`, `regions`

**Answer:**
```sql
WITH sales_metrics AS (
    SELECT 
        sr.rep_id,
        sr.name as rep_name,
        r.region_name,
        c.category_name,
        YEAR(s.sale_date) as year,
        MONTH(s.sale_date) as month,
        SUM(s.amount) as revenue,
        COUNT(DISTINCT s.order_id) as orders,
        SUM(s.quantity) as units_sold
    FROM sales s
    JOIN sales_reps sr ON s.rep_id = sr.rep_id
    JOIN regions r ON sr.region_id = r.region_id
    JOIN products p ON s.product_id = p.product_id
    JOIN categories c ON p.category_id = c.category_id
    WHERE s.sale_date >= DATE_SUB(CURDATE(), INTERVAL 1 YEAR)
    GROUP BY sr.rep_id, sr.name, r.region_name, c.category_name, 
             YEAR(s.sale_date), MONTH(s.sale_date)
),
with_growth AS (
    SELECT *,
        LAG(revenue) OVER (PARTITION BY rep_id, category_name ORDER BY year, month) as prev_month_revenue,
        AVG(revenue) OVER (
            PARTITION BY rep_id, category_name 
            ORDER BY year, month 
            ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
        ) as rolling_3m_avg
    FROM sales_metrics
)
SELECT 
    rep_name,
    region_name,
    category_name,
    CONCAT(year, '-', LPAD(month, 2, '0')) as period,
    revenue,
    orders,
    units_sold,
    ROUND(revenue / NULLIF(orders, 0), 2) as avg_order_value,
    ROUND((revenue - prev_month_revenue) * 100.0 / NULLIF(prev_month_revenue, 0), 2) as mom_growth,
    ROUND(rolling_3m_avg, 2) as trend_3m
FROM with_growth
ORDER BY rep_name, category_name, year, month;
```
