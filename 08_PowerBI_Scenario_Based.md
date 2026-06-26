# Power BI Scenario-Based Questions & Answers

## Table of Contents
1. [Sales & Revenue Analysis](#sales--revenue-analysis)
2. [Customer Analytics](#customer-analytics)
3. [Financial Reporting](#financial-reporting)
4. [Operational Dashboards](#operational-dashboards)
5. [Advanced DAX Scenarios](#advanced-dax-scenarios)
6. [Data Modeling Scenarios](#data-modeling-scenarios)

---

## Sales & Revenue Analysis

### Scenario 1: Create a Year-over-Year Sales Comparison Report
**Requirements:** Compare current year sales to previous year with variance, growth %, and trend visualization.

**Answer:**
```dax
-- Measures
Total Sales = SUM(Sales[Amount])

Sales PY = CALCULATE([Total Sales], SAMEPERIODLASTYEAR('Date'[Date]))

Sales YoY Growth = [Total Sales] - [Sales PY]

Sales YoY Growth % = 
DIVIDE([Sales YoY Growth], [Sales PY], 0)

Sales YTD = TOTALYTD([Total Sales], 'Date'[Date])

Sales YTD PY = TOTALYTD([Sales PY], 'Date'[Date])

-- Formatting
-- Total Sales: Currency, 0 decimals
-- Sales YoY Growth %: Percentage, 1 decimal
-- Conditional formatting: Green if > 0, Red if < 0

-- Visuals
-- 1. KPI Cards: Total Sales, YoY Growth %, YTD Sales
-- 2. Line Chart: Monthly Sales (Current vs PY)
-- 3. Matrix: Sales by Category with YoY columns
-- 4. Bar Chart: Top 10 Products by Growth
-- 5. Slicers: Year, Quarter, Region
```

---

### Scenario 2: Build a Sales Funnel Dashboard
**Requirements:** Track leads through pipeline stages with conversion rates and identify bottlenecks.

**Answer:**
```dax
-- Measures
Total Leads = DISTINCTCOUNT(Leads[LeadID])

Leads by Stage = 
CALCULATE([Total Leads], Leads[Stage] = SELECTEDVALUE(Stages[StageName]))

Stage Count = DISTINCTCOUNT(Leads[Stage])

-- Conversion Rate (from previous stage)
Conversion Rate = 
VAR CurrentStageCount = [Total Leads]
VAR PreviousStageCount = 
    CALCULATE(
        [Total Leads],
        Leads[Stage] = 
            SWITCH(
                SELECTEDVALUE(Stages[StageName]),
                "Qualified", "Lead",
                "Proposal", "Qualified",
                "Negotiation", "Proposal",
                "Closed Won", "Negotiation",
                BLANK()
            )
    )
RETURN
    DIVIDE(CurrentStageCount, PreviousStageCount, 0)

-- Win Rate
Win Rate = 
DIVIDE(
    CALCULATE([Total Leads], Leads[Stage] = "Closed Won"),
    CALCULATE([Total Leads], Leads[Stage] IN {"Closed Won", "Closed Lost"}),
    0
)

-- Average Deal Size
Avg Deal Size = 
AVERAGEX(
    FILTER(Leads, Leads[Stage] = "Closed Won"),
    Leads[DealValue]
)

-- Pipeline Value
Pipeline Value = 
SUMX(
    FILTER(Leads, Leads[Stage] <> "Closed Won" AND Leads[Stage] <> "Closed Lost"),
    Leads[DealValue] * Leads[Probability]
)

-- Visuals
-- 1. Funnel Chart: Leads by Stage
-- 2. KPI Cards: Total Leads, Win Rate, Avg Deal Size, Pipeline Value
-- 3. Waterfall Chart: Stage-to-stage conversion
-- 4. Line Chart: Leads trend over time by stage
-- 5. Table: Top opportunities with probability weighting
```

---

### Scenario 3: Create a Product Performance Matrix
**Requirements:** Analyze products by revenue, profit margin, growth, and market share with ABC classification.

**Answer:**
```dax
-- Measures
Revenue = SUM(Sales[Amount])
Cost = SUM(Sales[Cost])
Profit = [Revenue] - [Cost]
Profit Margin % = DIVIDE([Profit], [Revenue], 0)

Revenue PY = CALCULATE([Revenue], SAMEPERIODLASTYEAR('Date'[Date]))
Revenue Growth % = DIVIDE([Revenue] - [Revenue PY], [Revenue PY], 0)

-- Market Share
Market Share % = 
DIVIDE(
    [Revenue],
    CALCULATE([Revenue], ALL(Products))
)

-- ABC Classification (cumulative revenue share)
Cumulative Revenue % =
VAR CurrentRevenue = [Revenue]
VAR TotalRevenue = CALCULATE([Revenue], ALL(Products))
VAR CumulativeRevenue =
    CALCULATE(
        [Revenue],
        FILTER(
            ALL(Products),
            [Revenue] >= CurrentRevenue
        )
    )
RETURN
    DIVIDE(CumulativeRevenue, TotalRevenue, 0)

ABC Class =
SWITCH(
    TRUE(),
    [Cumulative Revenue %] <= 0.80, "A",
    [Cumulative Revenue %] <= 0.95, "B",
    "C"
)

-- Visuals
-- 1. Scatter Chart: Revenue vs Profit Margin (bubble = growth %)
-- 2. Matrix: Product | Revenue | Margin | Growth | Share | ABC
-- 3. Treemap: Revenue by Category, sized by profit
-- 4. Bar Chart: Top 20 products by revenue
-- 5. Slicers: Category, ABC Class, Time period
```

---

## Customer Analytics

### Scenario 4: Build a Customer Segmentation Dashboard (RFM Analysis)
**Requirements:** Segment customers by Recency, Frequency, and Monetary value.

**Answer:**
```dax
-- Calculated Columns (in Customer table)
Recency = DATEDIFF([LastPurchaseDate], TODAY(), DAY)

Frequency = 
CALCULATE(
    DISTINCTCOUNT(Sales[OrderID]),
    RELATEDTABLE(Sales)
)

Monetary = 
CALCULATE(
    SUM(Sales[Amount]),
    RELATEDTABLE(Sales)
)

-- RFM Scores (1-5 scale)
R_Score = 
SWITCH(
    TRUE(),
    [Recency] <= 30, 5,
    [Recency] <= 60, 4,
    [Recency] <= 90, 3,
    [Recency] <= 180, 2,
    1
)

F_Score = 
SWITCH(
    TRUE(),
    [Frequency] >= 20, 5,
    [Frequency] >= 15, 4,
    [Frequency] >= 10, 3,
    [Frequency] >= 5, 2,
    1
)

M_Score = 
SWITCH(
    TRUE(),
    [Monetary] >= 10000, 5,
    [Monetary] >= 5000, 4,
    [Monetary] >= 2500, 3,
    [Monetary] >= 1000, 2,
    1
)

-- RFM Combined Score
RFM_Score = [R_Score] * 100 + [F_Score] * 10 + [M_Score]

-- Segment
Customer Segment = 
SWITCH(
    TRUE(),
    [R_Score] >= 4 && [F_Score] >= 4 && [M_Score] >= 4, "Champions",
    [R_Score] >= 3 && [F_Score] >= 3 && [M_Score] >= 3, "Loyal Customers",
    [R_Score] >= 4 && [F_Score] <= 2, "New Customers",
    [R_Score] >= 3 && [F_Score] >= 3 && [M_Score] <= 2, "Potential Loyalists",
    [R_Score] <= 2 && [F_Score] >= 3, "At Risk",
    [R_Score] <= 2 && [F_Score] <= 2 && [M_Score] >= 3, "Cannot Lose Them",
    [R_Score] <= 2 && [F_Score] <= 2 && [M_Score] <= 2, "Lost",
    "Others"
)

-- Measures
Customer Count = DISTINCTCOUNT(Customers[CustomerID])
Avg Customer Value = AVERAGEX(Customers, [Monetary])

-- Visuals
-- 1. Pie Chart: Customer distribution by segment
-- 2. Matrix: Segment | Count | Avg Revenue | Avg Recency | Avg Frequency
-- 3. Scatter Chart: Frequency vs Monetary (color = Recency)
-- 4. Funnel: Customer journey through segments
-- 5. Slicers: Segment, R Score, F Score, M Score
```

---

### Scenario 5: Create a Customer Cohort Retention Analysis
**Requirements:** Track customer retention by signup cohort over time.

**Answer:**
```dax
-- Calculated Column in Customers
Cohort Month = FORMAT([SignupDate], "YYYY-MM")

-- Calculated Column in Sales
Months Since Signup = 
DATEDIFF(
    RELATED(Customers[SignupDate]),
    Sales[OrderDate],
    MONTH
)

-- Measures
Cohort Size = 
CALCULATE(
    DISTINCTCOUNT(Customers[CustomerID]),
    ALL('Date'),
    Customers[Cohort Month] = SELECTEDVALUE(Customers[Cohort Month])
)

Active Customers = DISTINCTCOUNT(Sales[CustomerID])

Retention Rate = 
DIVIDE(
    [Active Customers],
    [Cohort Size],
    0
)

-- Visuals
-- 1. Matrix: Rows = Cohort Month, Columns = Months Since Signup, Values = Retention %
-- 2. Conditional formatting: Color scale (white to green)
-- 3. Line Chart: Retention curves by cohort
-- 4. Heat Map: Cohort retention matrix
```

---

## Financial Reporting

### Scenario 6: Build a P&L (Profit & Loss) Statement
**Requirements:** Create a dynamic P&L with actuals, budget, variance, and drill-down capabilities.

**Answer:**
```dax
-- Measures
Actual = SUM(GL[Amount])
Budget = SUM(Budget[Amount])

Variance = [Actual] - [Budget]
Variance % = DIVIDE([Variance], [Budget], 0)

-- P&L Line Items (using SWITCH)
P&L Amount = 
SWITCH(
    SELECTEDVALUE(PL[LineItem]),
    "Revenue", [Actual],
    "COGS", -[Actual],
    "Gross Profit", [Actual] - CALCULATE([Actual], GL[AccountType] = "COGS"),
    "Operating Expenses", -CALCULATE([Actual], GL[AccountType] = "OpEx"),
    "EBITDA", CALCULATE([Actual], GL[AccountType] = "Revenue") - CALCULATE([Actual], GL[AccountType] = "COGS") - CALCULATE([Actual], GL[AccountType] = "OpEx"),
    BLANK()
)

-- Running Total for Waterfall
Running Total = 
CALCULATE(
    [P&L Amount],
    FILTER(
        ALL(PL[Order]),
        PL[Order] <= MAX(PL[Order])
    )
)

-- Visuals
-- 1. Matrix: Line Item | Actual | Budget | Variance | Variance %
-- 2. Waterfall Chart: P&L breakdown
-- 3. KPI Cards: Revenue, Gross Profit, EBITDA, Net Income
-- 4. Line Chart: Monthly trend (Actual vs Budget)
-- 5. Drill-through: From summary to account detail
```

---

### Scenario 7: Create a Balance Sheet Dashboard
**Requirements:** Display assets, liabilities, and equity with period comparisons and ratios.

**Answer:**
```dax
-- Measures
Current Assets = CALCULATE([Actual], GL[AccountCategory] = "Current Assets")
NonCurrent Assets = CALCULATE([Actual], GL[AccountCategory] = "Non-Current Assets")
Total Assets = [Current Assets] + [NonCurrent Assets]

Current Liabilities = CALCULATE([Actual], GL[AccountCategory] = "Current Liabilities")
NonCurrent Liabilities = CALCULATE([Actual], GL[AccountCategory] = "Non-Current Liabilities")
Total Liabilities = [Current Liabilities] + [NonCurrent Liabilities]

Equity = CALCULATE([Actual], GL[AccountCategory] = "Equity")
Total Liab & Equity = [Total Liabilities] + [Equity]

-- Ratios
Current Ratio = DIVIDE([Current Assets], [Current Liabilities], 0)
Debt to Equity = DIVIDE([Total Liabilities], [Equity], 0)
Working Capital = [Current Assets] - [Current Liabilities]

-- Period Comparison
Assets PY = CALCULATE([Total Assets], SAMEPERIODLASTYEAR('Date'[Date]))
Assets Change % = DIVIDE([Total Assets] - [Assets PY], [Assets PY], 0)

-- Visuals
-- 1. Cards: Total Assets, Total Liabilities, Equity, Current Ratio
-- 2. Bar Chart: Assets vs Liabilities comparison
-- 3. Pie Chart: Asset composition
-- 4. Table: Detailed account balances with PY comparison
-- 5. Slicers: Date, Entity (if multi-entity)
```

---

## Operational Dashboards

### Scenario 8: Build an Inventory Management Dashboard
**Requirements:** Track stock levels, reorder alerts, turnover, and slow-moving items.

**Answer:**
```dax
-- Measures
Current Stock = SUM(Inventory[Quantity])
Stock Value = SUMX(Inventory, Inventory[Quantity] * Inventory[UnitCost])

Reorder Point = SUM(Products[ReorderPoint])
Safety Stock = SUM(Products[SafetyStock])

-- Status
Stock Status = 
SWITCH(
    TRUE(),
    [Current Stock] = 0, "Out of Stock",
    [Current Stock] <= [Safety Stock], "Critical",
    [Current Stock] <= [Reorder Point], "Reorder",
    "OK"
)

-- Turnover
COGS = CALCULATE([Actual], GL[AccountType] = "COGS")
Avg Inventory = AVERAGEX(ALL('Date'), [Stock Value])
Inventory Turnover = DIVIDE([COGS], [Avg Inventory], 0)
Days Inventory Outstanding = DIVIDE(365, [Inventory Turnover], 0)

-- Slow Moving (no sales in 90 days)
Days Since Last Sale = 
DATEDIFF(
    MAX(Sales[OrderDate]),
    TODAY(),
    DAY
)

-- Visuals
-- 1. Cards: Total SKUs, Out of Stock Count, Inventory Value, Turnover
-- 2. Table: Product | Stock | Reorder Pt | Status | Value | Days Since Sale
-- 3. Conditional formatting: Red for Critical, Yellow for Reorder
-- 4. Bar Chart: Inventory value by category
-- 5. Gauge: Overall stock health score
```

---

### Scenario 9: Create an Employee Performance Scorecard
**Requirements:** Track individual and team KPIs with targets, ratings, and trends.

**Answer:**
```dax
-- Measures
Sales Achievement = DIVIDE([Actual Sales], [Target Sales], 0)
Quality Score = AVERAGE(Performance[QualityRating])
Attendance Rate = DIVIDE([Days Present], [Working Days], 0)
Customer Satisfaction = AVERAGE(Feedback[Rating])

-- Weighted Performance Score
Performance Score = 
([Sales Achievement] * 0.30) +
([Quality Score] / 5 * 0.25) +
([Attendance Rate] * 0.15) +
([Customer Satisfaction] / 5 * 0.20) +
([Training Completion] * 0.10)

-- Rating
Performance Rating = 
SWITCH(
    TRUE(),
    [Performance Score] >= 0.90, "Excellent",
    [Performance Score] >= 0.80, "Good",
    [Performance Score] >= 0.70, "Satisfactory",
    [Performance Score] >= 0.60, "Needs Improvement",
    "Unsatisfactory"
)

-- Rank
Performance Rank = RANKX(ALL(Employees), [Performance Score], , DESC)

-- Visuals
-- 1. Scorecard Table: Employee | KPIs | Score | Rating | Rank
-- 2. Radar Chart: Individual vs Team average
-- 3. Bullet Chart: Actual vs Target for each KPI
-- 4. Trend Line: Performance over time
-- 5. Slicers: Department, Manager, Period
```

---

## Advanced DAX Scenarios

### Scenario 10: Implement a Dynamic Top N + Others
**Requirements:** Show top N customers by sales and group remaining as "Others".

**Answer:**
```dax
-- Parameter: TopN (What-if parameter, values 5, 10, 20, 50)

-- Measure to determine if customer is in Top N
Is Top N = 
VAR TopNValue = SELECTEDVALUE('TopN'[TopN], 10)
VAR CurrentCustomerSales = [Total Sales]
VAR RankCustomer = RANKX(ALL(Customers), [Total Sales], , DESC)
RETURN
    IF(RankCustomer <= TopNValue, 1, 0)

-- Modified Customer Name for display
Customer Name Display = 
IF([Is Top N] = 1, SELECTEDVALUE(Customers[Name]), "Others")

-- Sales with Others grouping
Sales with Others = 
VAR TopNValue = SELECTEDVALUE('TopN'[TopN], 10)
VAR CurrentRank = RANKX(ALL(Customers), [Total Sales], , DESC)
RETURN
    IF(
        HASONEVALUE(Customers[CustomerID]),
        IF(CurrentRank <= TopNValue, [Total Sales], BLANK()),
        -- For "Others" row
        CALCULATE(
            [Total Sales],
            FILTER(
                ALL(Customers),
                RANKX(ALL(Customers), [Total Sales], , DESC) > TopNValue
            )
        )
    )

-- Alternative: Using a calculated table for Top N + Others
Top N Customers = 
VAR TopNValue = 10
RETURN
    UNION(
        TOPN(TopNValue, ALL(Customers[Name]), [Total Sales], DESC),
        ROW("Name", "Others")
    )

-- Visuals
-- 1. Bar Chart: Customer Name Display vs Sales with Others
-- 2. Slicer: Top N parameter
-- 3. Table: Detailed breakdown with Others row
```

---

### Scenario 11: Create a Moving Average with Variable Period
**Requirements:** Allow users to select the moving average period (7, 14, 30, 90 days).

**Answer:**
```dax
-- Parameter: MA Period (What-if parameter: 7, 14, 30, 90)

Moving Average = 
VAR Period = SELECTEDVALUE('MA Period'[MA Period], 30)
VAR EndDate = MAX('Date'[Date])
VAR StartDate = EndDate - Period + 1
RETURN
    CALCULATE(
        AVERAGEX(VALUES('Date'[Date]), [Total Sales]),
        DATESBETWEEN('Date'[Date], StartDate, EndDate)
    )

-- Alternative using DATESINPERIOD
Moving Average V2 = 
VAR Period = SELECTEDVALUE('MA Period'[MA Period], 30)
RETURN
    AVERAGEX(
        DATESINPERIOD('Date'[Date], LASTDATE('Date'[Date]), -Period, DAY),
        CALCULATE([Total Sales])
    )

-- Visuals
-- 1. Line Chart: Daily Sales + Moving Average line
-- 2. Slicer: MA Period
-- 3. Area Chart: Showing volatility vs smoothed trend
```

---

### Scenario 12: Implement a Custom Calendar (Fiscal Year)
**Requirements:** Support fiscal year starting in April instead of January.

**Answer:**
```dax
-- Calculated Table: Date Table with Fiscal Calendar
Date Table =
ADDCOLUMNS(
    CALENDAR(DATE(2020, 1, 1), DATE(2025, 12, 31)),
    "Year", YEAR([Date]),
    "Quarter", "Q" & QUARTER([Date]),
    "Month", FORMAT([Date], "MMMM"),
    "MonthNum", MONTH([Date]),
    "Week", WEEKNUM([Date]),
    "DayOfWeek", FORMAT([Date], "dddd"),

    -- Fiscal Calendar (April start)
    "FiscalYear", IF(MONTH([Date]) >= 4, YEAR([Date]) + 1, YEAR([Date])),
    "FiscalQuarter", 
        SWITCH(
            TRUE(),
            MONTH([Date]) IN {4,5,6}, "FQ1",
            MONTH([Date]) IN {7,8,9}, "FQ2",
            MONTH([Date]) IN {10,11,12}, "FQ3",
            "FQ4"
        ),
    "FiscalMonthNum", 
        SWITCH(
            MONTH([Date]),
            4, 1, 5, 2, 6, 3, 7, 4, 8, 5, 9, 6,
            10, 7, 11, 8, 12, 9, 1, 10, 2, 11, 3, 12
        ),
    "FiscalMonth", 
        FORMAT(DATE(2020, [FiscalMonthNum], 1), "MMMM"),
    "IsCurrentFY", 
        IF(
            IF(MONTH(TODAY()) >= 4, YEAR(TODAY()) + 1, YEAR(TODAY())) = [FiscalYear],
            TRUE,
            FALSE
        )
)

-- Fiscal YTD Measure
Fiscal YTD Sales = 
TOTALYTD(
    [Total Sales],
    'Date Table'[Date],
    "3/31"  -- Fiscal year end
)

-- Visuals
-- 1. Slicers: Fiscal Year, Fiscal Quarter
-- 2. Line Chart: Monthly sales with fiscal year grouping
-- 3. Matrix: FY vs PY comparison
```

---

### Scenario 13: Create a Variance Analysis with Multiple Scenarios
**Requirements:** Compare Actual vs Budget vs Forecast with selectable scenario.

**Answer:**
```dax
-- Field Parameter for Scenario Selection
Scenario Selector = {
    ("Actual", NAMEOF([Actual]), 0),
    ("Budget", NAMEOF([Budget]), 1),
    ("Forecast", NAMEOF([Forecast]), 2)
}

-- Selected Value Measure
Selected Scenario Value = SELECTEDVALUE('Scenario Selector'[Scenario Selector])

-- Dynamic measure based on selection
Scenario Value = 
SWITCH(
    [Selected Scenario Value],
    "Actual", [Actual],
    "Budget", [Budget],
    "Forecast", [Forecast],
    [Actual]
)

-- Variance to Actual
Variance to Actual = 
SWITCH(
    [Selected Scenario Value],
    "Budget", [Actual] - [Budget],
    "Forecast", [Actual] - [Forecast],
    BLANK()
)

-- Visuals
-- 1. Line Chart: Scenario Value over time (toggle between Actual/Budget/Forecast)
-- 2. Waterfall Chart: Variance breakdown
-- 3. Matrix: Department | Actual | Budget | Forecast | Variance
-- 4. Slicers: Scenario, Department, Period
```

---

### Scenario 14: Implement a Custom Time Intelligence (Week-over-Week)
**Requirements:** Calculate week-over-week changes when fiscal calendar doesn't align with standard weeks.

**Answer:**
```dax
-- Custom Week Table
Week Table = 
ADDCOLUMNS(
    SUMMARIZE(
        Sales,
        Sales[Year],
        Sales[WeekNum]
    ),
    "WeekKey", [Year] * 100 + [WeekNum],
    "WeekLabel", [Year] & "-W" & FORMAT([WeekNum], "00")
)

-- Current Week Sales
Current Week Sales = [Total Sales]

-- Previous Week Sales
Previous Week Sales = 
VAR CurrentWeekKey = SELECTEDVALUE('Week Table'[WeekKey])
VAR PrevWeekKey = CurrentWeekKey - 1
RETURN
    CALCULATE(
        [Total Sales],
        'Week Table'[WeekKey] = PrevWeekKey,
        REMOVEFILTERS('Week Table')
    )

-- Week-over-Week Change
WoW Change = [Current Week Sales] - [Previous Week Sales]
WoW Change % = DIVIDE([WoW Change], [Previous Week Sales], 0)

-- Rolling 4-Week Average
Rolling 4W Avg = 
AVERAGEX(
    DATESINPERIOD('Week Table'[WeekKey], MAX('Week Table'[WeekKey]), -4, WEEK),
    [Total Sales]
)

-- Visuals
-- 1. Line Chart: Weekly sales with WoW % on secondary axis
-- 2. Bar Chart: WoW change by week
-- 3. Table: Week | Sales | Prev Week | Change | Change %
```

---

## Data Modeling Scenarios

### Scenario 15: Handle Many-to-Many Relationships
**Requirements:** Analyze sales by multiple tags/categories per product.

**Answer:**
```dax
-- Tables: Products, Tags, ProductTags (bridge), Sales

-- Relationship: Products 1:* ProductTags *:1 Tags
-- Relationship: Products 1:* Sales

-- Measure: Sales by Tag
Sales by Tag = 
CALCULATE(
    [Total Sales],
    CROSSFILTER(Products[ProductID], ProductTags[ProductID], Both)
)

-- Alternative using TREATAS
Sales by Tag V2 = 
CALCULATE(
    [Total Sales],
    TREATAS(VALUES(Tags[TagID]), ProductTags[TagID])
)

-- Visuals
-- 1. Slicer: Tags (multi-select)
-- 2. Table: Tag | Sales | % of Total
-- 3. Note: Products may appear in multiple tag groups
```

---

### Scenario 16: Implement Role-Playing Dimensions (Multiple Date Columns)
**Requirements:** Analyze sales by Order Date, Ship Date, and Delivery Date.

**Answer:**
```dax
-- One Date Table, multiple relationships (one active, others inactive)
-- Active: Sales[OrderDate] -> Date[Date]
-- Inactive: Sales[ShipDate] -> Date[Date]
-- Inactive: Sales[DeliveryDate] -> Date[Date]

-- Sales by Order Date (default, uses active relationship)
Sales by Order Date = [Total Sales]

-- Sales by Ship Date
Sales by Ship Date = 
CALCULATE(
    [Total Sales],
    USERELATIONSHIP(Sales[ShipDate], 'Date'[Date])
)

-- Sales by Delivery Date
Sales by Delivery Date = 
CALCULATE(
    [Total Sales],
    USERELATIONSHIP(Sales[DeliveryDate], 'Date'[Date])
)

-- Days to Ship
Avg Days to Ship = 
AVERAGEX(
    Sales,
    CALCULATE(
        VALUES('Date'[Date]),
        USERELATIONSHIP(Sales[ShipDate], 'Date'[Date])
    ) -
    CALCULATE(
        VALUES('Date'[Date]),
        USERELATIONSHIP(Sales[OrderDate], 'Date'[Date])
    )
)

-- Visuals
-- 1. Line Chart: Sales by Order Date vs Ship Date vs Delivery Date
-- 2. Slicer: Date (applies to selected measure)
-- 3. Table: Order | Order Date | Ship Date | Delivery Date | Days to Ship
```

---

### Scenario 17: Create a Parent-Child Hierarchy (Organization Chart)
**Requirements:** Display organizational hierarchy with roll-up calculations.

**Answer:**
```dax
-- Table: Employees (EmployeeID, Name, ManagerID, Department, Salary)

-- Path function for hierarchy
Path = PATH(Employees[EmployeeID], Employees[ManagerID])

-- Path items for each level
Level1 = PATHITEM(Employees[Path], 1, INTEGER)
Level2 = PATHITEM(Employees[Path], 2, INTEGER)
Level3 = PATHITEM(Employees[Path], 3, INTEGER)
Level4 = PATHITEM(Employees[Path], 4, INTEGER)

-- Look up names for each level
Level1 Name = LOOKUPVALUE(Employees[Name], Employees[EmployeeID], Employees[Level1])
Level2 Name = LOOKUPVALUE(Employees[Name], Employees[EmployeeID], Employees[Level2])

-- Create hierarchy in model view
-- Hierarchy: Level1 Name -> Level2 Name -> Level3 Name -> Name

-- Roll-up salary (sum of all subordinates)
Team Salary = 
VAR CurrentEmployee = SELECTEDVALUE(Employees[EmployeeID])
RETURN
    SUMX(
        FILTER(
            ALL(Employees),
            PATHCONTAINS(Employees[Path], CurrentEmployee)
        ),
        Employees[Salary]
    )

-- Visuals
-- 1. Matrix: Hierarchy with Salary, Team Salary, Headcount
-- 2. Decomposition Tree: Drill from CEO down
-- 3. Cards: Selected manager's team metrics
```

---

### Scenario 18: Build a Real-Time Operational Dashboard
**Requirements:** Display real-time KPIs from streaming data sources.

**Answer:**
```dax
-- Streaming Dataset (created in Power BI Service)
-- Fields: Timestamp, MetricName, MetricValue, MachineID

-- Real-time measures
Current Temperature = 
CALCULATE(
    MAX(Streaming[MetricValue]),
    Streaming[MetricName] = "Temperature"
)

Avg Temperature (Last Hour) = 
CALCULATE(
    AVERAGE(Streaming[MetricValue]),
    Streaming[MetricName] = "Temperature",
    Streaming[Timestamp] >= NOW() - TIME(1, 0, 0)
)

-- Alerts
Temperature Alert = 
IF([Current Temperature] > 80, "CRITICAL",
   IF([Current Temperature] > 70, "WARNING", "NORMAL"))

-- Visuals
-- 1. Card: Current Temperature with conditional formatting
-- 2. Line Chart: Temperature over last hour (auto-refresh)
-- 3. Gauge: Current vs Target
-- 4. Table: All machines with status
-- 5. Dashboard tiles with data alerts

-- Setup:
-- 1. Create Streaming Dataset in Service
-- 2. Use REST API or Azure Stream Analytics to push data
-- 3. Create report from streaming dataset
-- 4. Pin tiles to dashboard
-- 5. Set up data alerts
```

---

### Scenario 19: Create a What-If Analysis Dashboard
**Requirements:** Allow users to adjust assumptions and see impact on forecasts.

**Answer:**
```dax
-- What-if Parameters
Revenue Growth % = GENERATESERIES(-0.2, 0.5, 0.01)
Cost Reduction % = GENERATESERIES(0, 0.3, 0.01)
Price Increase % = GENERATESERIES(0, 0.2, 0.01)

-- Base Measures
Base Revenue = SUM(Forecast[Revenue])
Base Cost = SUM(Forecast[Cost])

-- Adjusted Measures
Adjusted Revenue = [Base Revenue] * (1 + SELECTEDVALUE('Revenue Growth %'[Revenue Growth %], 0))
Adjusted Cost = [Base Cost] * (1 - SELECTEDVALUE('Cost Reduction %'[Cost Reduction %], 0))
Adjusted Price = AVERAGE(Products[Price]) * (1 + SELECTEDVALUE('Price Increase %'[Price Increase %], 0))

-- Impact
Projected Profit = [Adjusted Revenue] - [Adjusted Cost]
Profit Impact = [Projected Profit] - ([Base Revenue] - [Base Cost])

-- Break-even Analysis
Break-even Volume = DIVIDE([Adjusted Cost], [Adjusted Price], 0)

-- Visuals
-- 1. Slicers: Revenue Growth %, Cost Reduction %, Price Increase %
-- 2. Cards: Base Profit, Projected Profit, Impact
-- 3. Waterfall Chart: Impact breakdown by assumption
-- 4. Scatter Chart: Sensitivity analysis (two variables)
-- 5. Table: Scenario comparison (Base vs Adjusted)
```

---

### Scenario 20: Implement Advanced Row-Level Security
**Requirements:** Show users only data for their region, but managers see their region + subordinates' regions.

**Answer:**
```dax
-- User Access Table
-- UserEmail | Region | AccessLevel (User/Manager/Admin)

-- RLS Role: Dynamic Security
[Region] IN 
    SELECTCOLUMNS(
        FILTER(
            UserAccess,
            UserAccess[UserEmail] = USERPRINCIPALNAME()
        ),
        "Region", UserAccess[Region]
    )

-- For managers who need to see subordinate regions
-- Add a RegionHierarchy table
-- Region | ParentRegion

-- Enhanced RLS
VAR UserRegions = 
    SELECTCOLUMNS(
        FILTER(UserAccess, UserAccess[UserEmail] = USERPRINCIPALNAME()),
        "Region", UserAccess[Region]
    )

VAR SubordinateRegions =
    SELECTCOLUMNS(
        FILTER(
            RegionHierarchy,
            RegionHierarchy[ParentRegion] IN UserRegions
        ),
        "Region", RegionHierarchy[Region]
    )

VAR AllAccessibleRegions = UNION(UserRegions, SubordinateRegions)

RETURN
    [Region] IN AllAccessibleRegions

-- Testing
-- View as roles: Test with different user emails
-- Verify each user sees correct data
```
