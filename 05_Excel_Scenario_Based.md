# Excel Scenario-Based Questions & Answers

## Table of Contents
1. [Financial Analysis](#financial-analysis)
2. [Sales & Marketing](#sales--marketing)
3. [Operations & Inventory](#operations--inventory)
4. [HR & Payroll](#hr--payroll)
5. [Data Cleaning & Transformation](#data-cleaning--transformation)
6. [Dashboard & Reporting](#dashboard--reporting)
7. [Advanced Scenarios](#advanced-scenarios)

---

## Financial Analysis

### Scenario 1: Calculate loan amortization schedule
**Requirements:** Create a full amortization table showing monthly payment, principal, interest, and remaining balance.

**Answer:**
```excel
-- Inputs
Loan Amount:     $300,000  (Cell B1)
Annual Rate:     5%        (Cell B2)
Years:           30        (Cell B3)

-- Monthly Payment (PMT)
=PMT(B2/12, B3*12, -B1)
-- Result: $1,610.46

-- Amortization Table (starting row 5)
Month | Payment | Interest | Principal | Balance
------|---------|----------|-----------|--------
0     |         |          |           | =B1
1     | =$B$4   | =E5*$B$2/12 | =B6-C6 | =E5-D6
2     | =$B$4   | =E6*$B$2/12 | =B7-C7 | =E6-D7
-- Drag down for 360 rows

-- Summary
Total Interest: =SUM(C6:C365)
Total Paid:     =SUM(B6:B365)
```

**Key Formulas:**
- `PMT(rate, nper, pv)` - Monthly payment
- `IPMT(rate, per, nper, pv)` - Interest portion for specific period
- `PPMT(rate, per, nper, pv)` - Principal portion for specific period
- `CUMIPMT(rate, nper, pv, start_period, end_period, type)` - Cumulative interest

---

### Scenario 2: Build a financial model with sensitivity analysis
**Requirements:** Create a 3-statement model (Income Statement, Balance Sheet, Cash Flow) with sensitivity to revenue growth and margin assumptions.

**Answer:**
```excel
-- Assumptions Sheet
Revenue Growth:    10%  (Cell B2)
Gross Margin:      60%  (Cell B3)
OpEx as % Rev:   30%  (Cell B4)
Tax Rate:        25%  (Cell B5)

-- Income Statement
Revenue:          =Prior_Year_Revenue * (1 + Assumptions!$B$2)
COGS:             =Revenue * (1 - Assumptions!$B$3)
Gross Profit:     =Revenue - COGS
Operating Exp:    =Revenue * Assumptions!$B$4
EBIT:             =Gross_Profit - Operating_Exp
Tax:              =MAX(EBIT * Assumptions!$B$5, 0)
Net Income:       =EBIT - Tax

-- Sensitivity Table (Data Table)
        | 5% Growth | 10% Growth | 15% Growth
55% Margin |  =TABLE()  |  =TABLE()  |  =TABLE()
60% Margin |  =TABLE()  |  =TABLE()  |  =TABLE()
65% Margin |  =TABLE()  |  =TABLE()  |  =TABLE()

-- Setup: Row Input = Growth%, Column Input = Margin%
```

---

### Scenario 3: Calculate NPV, IRR, and Payback Period
**Requirements:** Evaluate an investment project with initial cost and future cash flows.

**Answer:**
```excel
-- Cash Flows
Year 0:  -$500,000  (Initial investment)
Year 1:   $100,000
Year 2:   $150,000
Year 3:   $200,000
Year 4:   $250,000
Year 5:   $300,000

-- NPV (Net Present Value)
=NPV(10%, B2:B6) + B1
-- Note: NPV assumes first cash flow at end of period 1
-- Add initial investment separately

-- IRR (Internal Rate of Return)
=IRR(B1:B6)
-- Result: 17.2%

-- MIRR (Modified IRR, with different reinvestment rate)
=MIRR(B1:B6, 8%, 12%)

-- Payback Period (cumulative cash flow)
Year 0: =B1
Year 1: =C1 + B2
Year 2: =C2 + B3
-- Find first year where cumulative > 0

-- Exact Payback Period
=MATCH(TRUE, cumulative_range > 0, 0) - 1 + 
 ABS(INDEX(cumulative_range, MATCH(TRUE, cumulative_range > 0, 0) - 1)) / 
 INDEX(cash_flows, MATCH(TRUE, cumulative_range > 0, 0))
```

---

### Scenario 4: Create a budget vs actual variance analysis
**Requirements:** Compare budgeted amounts to actuals with dollar and percentage variances, highlighting significant deviations.

**Answer:**
```excel
-- Data Structure
Category | Budget | Actual | Variance $ | Variance % | Status
---------|--------|--------|------------|------------|-------
Revenue  | 100000 | 95000  | =D2-C2     | =E2/C2     | =IF(ABS(F2)>10%, "Review", "OK")
COGS     | 40000  | 42000  | =D3-C3     | =E3/C3     | =IF(ABS(F3)>10%, "Review", "OK")

-- Conditional Formatting
Variance % Column:
- Green if within ±5%
- Yellow if ±5-10%
- Red if > ±10%

-- Formula for status with color coding
=IF(ABS(F2)<=0.05, "✓ On Track", 
   IF(ABS(F2)<=0.10, "⚠ Watch", "✗ Review"))

-- Summary Dashboard
Total Budget: =SUM(C2:C10)
Total Actual: =SUM(D2:D10)
Total Variance: =SUM(E2:E10)
Variance %: =E11/C11

-- Sparkline for trend
=SPARKLINE(actual_range, {"charttype","line";"color","blue"})
```

---

## Sales & Marketing

### Scenario 5: Build a sales commission calculator
**Requirements:** Calculate tiered commissions based on sales targets with accelerators.

**Answer:**
```excel
-- Commission Tiers
Tier 1: 0-50K     → 5%
Tier 2: 50K-100K  → 7%
Tier 3: 100K-200K → 10%
Tier 4: 200K+     → 15%

-- Formula (Progressive/Tiered)
=MIN(Sales, 50000) * 0.05 +
 MAX(0, MIN(Sales, 100000) - 50000) * 0.07 +
 MAX(0, MIN(Sales, 200000) - 100000) * 0.10 +
 MAX(0, Sales - 200000) * 0.15

-- Alternative using SUMPRODUCT
=SUMPRODUCT(
  (Sales > {0,50000,100000,200000}) * 
  (Sales - {0,50000,100000,200000}) * 
  {0.05, 0.02, 0.03, 0.05}
)

-- With Quota Achievement Bonus
Quota: $150,000
Achievement %: =Sales / Quota

Bonus Multiplier:
=IF(Achievement >= 1.5, 1.5,
   IF(Achievement >= 1.2, 1.3,
   IF(Achievement >= 1.0, 1.0, 0.8)))

Total Commission: =Base_Commission * Bonus_Multiplier
```

---

### Scenario 6: Create a sales funnel analysis
**Requirements:** Track leads through stages (Lead → Qualified → Proposal → Negotiation → Closed Won/Lost) with conversion rates.

**Answer:**
```excel
-- Funnel Data
Stage          | Count | Value     | Conversion Rate
---------------|-------|-----------|----------------
Leads          | 1000  | $5,000,000| 100%
Qualified      | 400   | $2,800,000| =C3/C2
Proposal       | 200   | $1,800,000| =C4/C3
Negotiation    | 100   | $1,000,000| =C5/C4
Closed Won     | 60    | $650,000  | =C6/C5
Closed Lost    | 40    | $350,000  | =C7/C5

-- Overall Conversion
Lead to Win: =Closed_Won / Leads
Win Rate: =Closed_Won / (Closed_Won + Closed_Lost)
Avg Deal Size: =Closed_Won_Value / Closed_Won_Count

-- Funnel Chart (Inverted Pyramid)
-- Use stacked bar chart with invisible bottom series

-- Pipeline Value by Stage
=SUMPRODUCT((Stage_Range="Negotiation") * Value_Range)

-- Weighted Pipeline
=SUMPRODUCT((Stage_Range="Proposal") * Value_Range * 0.5) +
 SUMPRODUCT((Stage_Range="Negotiation") * Value_Range * 0.75)
```

---

### Scenario 7: Calculate customer cohort retention
**Requirements:** Track monthly retention rates for customers who signed up in each cohort month.

**Answer:**
```excel
-- Raw Data: customer_id, signup_date, activity_date

-- Step 1: Determine cohort month
=C2&"-"&TEXT(D2,"mm")  -- signup year-month

-- Step 2: Calculate months since signup
=DATEDIF(signup_date, activity_date, "M")

-- Step 3: Create PivotTable
Rows: Cohort Month
Columns: Months Since Signup
Values: COUNTD(Customer_ID) or DISTINCTCOUNT

-- Step 4: Calculate Retention %
-- Divide each cell by first column (Month 0)
=Cohort_Cell / $B3

-- Format as percentage

-- Heat Map with Conditional Formatting
-- Color scale: White (0%) to Green (100%)
```

**Alternative using formulas:**
```excel
-- Cohort Size (Month 0)
=COUNTIFS(Signup_Range, Cohort_Date, Activity_Range, ">="&Cohort_Date, Activity_Range, "<"&EDATE(Cohort_Date,1))

-- Month N Retention
=COUNTIFS(Signup_Range, Cohort_Date, 
          Activity_Range, ">="&EDATE(Cohort_Date,N), 
          Activity_Range, "<"&EDATE(Cohort_Date,N+1)) / Cohort_Size
```

---

### Scenario 8: Build a marketing ROI calculator
**Requirements:** Calculate ROI for multiple marketing channels with attribution.

**Answer:**
```excel
-- Channel Performance
Channel    | Spend    | Leads | Customers | Revenue | CAC     | LTV   | ROI
-----------|----------|-------|-----------|---------|---------|-------|-----
Google Ads | $50,000  | 500   | 50        | $200,000| =B2/D2  | =E2/D2| =(E2-B2)/B2
Facebook   | $30,000  | 300   | 30        | $90,000 | =B3/D3  | =E3/D3| =(E3-B3)/B3
Email      | $5,000   | 200   | 20        | $60,000 | =B4/D4  | =E4/D4| =(E4-B4)/B4

-- Additional Metrics
CPA (Cost per Acquisition): =Spend / Customers
CPL (Cost per Lead): =Spend / Leads
Conversion Rate: =Customers / Leads
ROAS (Return on Ad Spend): =Revenue / Spend

-- Blended Metrics
Total Spend: =SUM(B2:B10)
Total Revenue: =SUM(E2:E10)
Blended ROI: =(Total_Revenue - Total_Spend) / Total_Spend
Blended CAC: =Total_Spend / SUM(D2:D10)

-- Channel Mix Optimization
-- Use Solver to maximize revenue given budget constraint
-- Objective: Maximize Total Revenue
-- Variable: Spend per channel
-- Constraint: Total Spend <= Budget
```

---

## Operations & Inventory

### Scenario 9: Create an inventory reorder system
**Requirements:** Flag items that need reordering based on current stock, reorder point, and lead time.

**Answer:**
```excel
-- Inventory Data
Product | Stock | Reorder Pt | Lead Time | Supplier | Unit Cost | Status
--------|-------|------------|-----------|----------|-----------|-------
A100    | 50    | 100        | 14        | Supplier1| $25       | =IF(C2<=D2, "REORDER", "OK")

-- Advanced Status with Priority
=IF(B2=0, "🚨 CRITICAL - Out of Stock",
   IF(B2<=D2*0.5, "🔴 URGENT - Below 50% of Reorder",
   IF(B2<=D2, "🟡 REORDER - At Reorder Point",
   "🟢 OK")))

-- Economic Order Quantity (EOQ)
Annual Demand: =SUMIFS(Sales_Qty, Product_Range, Product)
Order Cost: $50
Holding Cost %: 20%
EOQ: =SQRT(2 * Annual_Demand * Order_Cost / (Unit_Cost * Holding_Cost_Pct))

-- Reorder Date
=IF(Status="REORDER", TODAY() + Lead_Time, "")

-- Total Inventory Value
=SUMPRODUCT(Stock_Range, Unit_Cost_Range)

-- ABC Analysis (by value)
Product Value: =Stock * Unit_Cost
Cumulative %: Running total / Total value
A: Top 80% value
B: Next 15% value
C: Bottom 5% value
```

---

### Scenario 10: Calculate employee shift scheduling
**Requirements:** Create a weekly schedule ensuring coverage requirements are met.

**Answer:**
```excel
-- Schedule Grid
Employee | Mon | Tue | Wed | Thu | Fri | Sat | Sun | Total Hrs
---------|-----|-----|-----|-----|-----|-----|-----|----------
John     | 8   | 8   | 0   | 8   | 8   | 0   | 0   | =SUM(B2:H2)
Jane     | 0   | 8   | 8   | 8   | 0   | 8   | 0   | =SUM(B3:H3)

-- Daily Coverage
Mon Coverage: =SUM(B2:B10)
Required: 40
Shortage: =MAX(0, Required - Coverage)

-- Validation Rules
-- Data Validation: Allow whole numbers 0-12
-- Conditional Formatting: Red if > 8 hours/day
-- Check: Total hours per employee <= 40

-- Overtime Calculation
Regular Hours: =MIN(Total_Hours, 40)
Overtime Hours: =MAX(0, Total_Hours - 40)
Overtime Pay: =Regular_Hours * Rate + Overtime_Hours * Rate * 1.5

-- Cost Optimization
Total Labor Cost: =SUMPRODUCT(Hours_Range, Rate_Range)
-- Use Solver to minimize cost while meeting coverage
```

---

## HR & Payroll

### Scenario 11: Build a payroll calculator with tax brackets
**Requirements:** Calculate net pay with progressive tax brackets, deductions, and benefits.

**Answer:**
```excel
-- Inputs
Gross Salary: $75,000
401k Contribution %: 6%
Health Insurance: $200/month
State: CA

-- Deductions
401k Amount: =Gross_Salary * 401k_Pct
Taxable Income: =Gross_Salary - 401k_Amount - Standard_Deduction

-- Federal Tax (Progressive)
Bracket 1: $0 - $11,000 @ 10%
Bracket 2: $11,001 - $44,725 @ 12%
Bracket 3: $44,726 - $95,375 @ 22%
Bracket 4: $95,376 - $182,100 @ 24%

Federal Tax:
=MIN(Taxable, 11000) * 0.10 +
 MAX(0, MIN(Taxable, 44725) - 11000) * 0.12 +
 MAX(0, MIN(Taxable, 95375) - 44725) * 0.22 +
 MAX(0, MIN(Taxable, 182100) - 95375) * 0.24

-- FICA
Social Security: =MIN(Gross_Salary, 168600) * 0.062
Medicare: =Gross_Salary * 0.0145
Additional Medicare: =MAX(0, Gross_Salary - 200000) * 0.009

-- State Tax (CA example, simplified)
State Tax: =Taxable * 0.093  -- Simplified rate

-- Net Pay
Total Deductions: =Federal_Tax + SS + Medicare + State_Tax + Health_Insurance * 12
Net Annual: =Gross_Salary - Total_Deductions
Net Monthly: =Net_Annual / 12
Net Biweekly: =Net_Annual / 26
```

---

### Scenario 12: Create an employee performance scorecard
**Requirements:** Score employees across multiple KPIs with weighted averages.

**Answer:**
```excel
-- KPI Weights
KPI           | Weight | Weight %
--------------|--------|---------
Sales Target  | 30     | =B2/SUM($B$2:$B$6)
Quality Score | 25     | =B3/SUM($B$2:$B$6)
Attendance    | 15     | =B4/SUM($B$2:$B$6)
Customer Sat  | 20     | =B5/SUM($B$2:$B$6)
Teamwork      | 10     | =B6/SUM($B$2:$B$6)

-- Employee Scores (normalized 0-100)
Employee | Sales | Quality | Attendance | CSAT | Teamwork | Weighted Score
---------|-------|---------|------------|------|----------|---------------
John     | 85    | 90      | 95         | 88   | 92       | =SUMPRODUCT(C2:G2, $Weight_Pcts)

-- Rating
=IF(Weighted>=90, "Excellent",
   IF(Weighted>=80, "Good",
   IF(Weighted>=70, "Satisfactory",
   IF(Weighted>=60, "Needs Improvement", "Unsatisfactory"))))

-- Rank
=RANK(Weighted_Score, Weighted_Range, 0)

-- Percentile
=PERCENTRANK.INC(Weighted_Range, Weighted_Score)

-- Bell Curve Visualization
-- Calculate mean and std dev, then create histogram
```

---

## Data Cleaning & Transformation

### Scenario 13: Clean and standardize a messy dataset
**Requirements:** Address missing values, duplicates, inconsistent formats, and standardize data.

**Answer:**
```excel
-- Original messy data issues:
-- 1. Extra spaces in names
-- 2. Inconsistent date formats
-- 3. Mixed case in categories
-- 4. Missing values
-- 5. Duplicates
-- 6. Inconsistent phone formats

-- Cleaning Steps (using formulas or Power Query)

-- 1. Trim spaces
=CLEAN(TRIM(A2))

-- 2. Standardize dates
=IF(ISNUMBER(A2), A2, DATEVALUE(A2))

-- 3. Standardize text case
=PROPER(A2)  -- Title Case
=UPPER(A2)   -- All caps for codes
=LOWER(A2)   -- All lowercase for emails

-- 4. Fill missing values
=IF(ISBLANK(A2), "Unknown", A2)
=IF(ISBLANK(A2), AVERAGE(A:A), A2)  -- For numbers

-- 5. Standardize phone numbers
=TEXT(VALUE(SUBSTITUTE(SUBSTITUTE(SUBSTITUTE(A2,"-",""),"(",""),")","")),"000-000-0000")

-- 6. Remove duplicates
Data > Remove Duplicates > Select columns

-- 7. Validate emails
=IF(ISNUMBER(SEARCH("@",A2)*SEARCH(".",A2)), "Valid", "Invalid")

-- 8. Standardize currency
=VALUE(SUBSTITUTE(SUBSTITUTE(A2,"$",""),",",""))

-- Power Query Alternative (Recommended)
-- Load data > Transform > 
--   Trim > Clean > Change Type > Replace Values > 
--   Fill Down > Remove Duplicates > Close & Load
```

---

### Scenario 14: Unpivot and reshape data for analysis
**Requirements:** Convert wide-format monthly sales data to long-format for PivotTable analysis.

**Answer:**
```excel
-- Wide Format (Current)
Product | Jan | Feb | Mar | Apr | ...
--------|-----|-----|-----|-----|----
A       | 100 | 120 | 110 | 130 | ...
B       | 200 | 180 | 220 | 210 | ...

-- Goal: Long Format
Product | Month | Sales
--------|-------|-------
A       | Jan   | 100
A       | Feb   | 120
...

-- Using Power Query
1. Select data range
2. Data > From Table/Range
3. Select Product column
4. Transform > Unpivot Other Columns
5. Rename "Attribute" to "Month", "Value" to "Sales"
6. Close & Load

-- Using Formulas (without Power Query)
-- For row 2, column N (month)
Product: =INDEX($A$2:$A$10, INT((ROW()-2)/12)+1)
Month: =INDEX($B$1:$M$1, MOD(ROW()-2, 12)+1)
Sales: =INDEX($B$2:$M$10, INT((ROW()-2)/12)+1, MOD(ROW()-2, 12)+1)

-- Using Dynamic Arrays (Excel 365)
=TOCOL(A2:A10&"|"&B1:M1&"|"&B2:M10)
-- Then split by delimiter
```

---

## Dashboard & Reporting

### Scenario 15: Build an executive KPI dashboard
**Requirements:** Create a one-page dashboard with key metrics, trends, and alerts.

**Answer:**
```excel
-- Layout: Single page, no scrolling

-- Section 1: KPI Cards (Top Row)
Revenue:        =SUM(Sales[Revenue])
YoY Growth:     =(Current_Revenue - Prior_Revenue) / Prior_Revenue
Customers:      =COUNTD(Sales[Customer_ID])
Avg Order:      =AVERAGE(Sales[Amount])

-- Format with conditional formatting
-- Green if positive, red if negative
-- Use large font, icons

-- Section 2: Trend Charts (Middle)
-- Line chart: Monthly revenue trend (12 months)
-- Bar chart: Revenue by category
-- Combo chart: Revenue vs Target

-- Section 3: Detailed Tables (Bottom)
-- Top 10 products table
-- Regional performance table

-- Interactivity
-- Slicers: Date range, Region, Category
-- All charts connected to same slicers

-- Dynamic Titles
="Revenue Dashboard - "&TEXT(TODAY(),"mmmm yyyy")

-- Alert System
=IF(Revenue_Growth < -0.1, "⚠ Revenue declining", "✓ On track")
```

---

### Scenario 16: Create an automated monthly report
**Requirements:** Generate a monthly sales report that updates automatically when new data is pasted.

**Answer:**
```excel
-- Data Sheet: Paste new data each month
-- Named range for data: =Table1[#All] or dynamic range

-- Summary Sheet
Month: =TEXT(MAX(Sales[Date]),"mmmm yyyy")
Total Sales: =SUMIFS(Sales[Amount], Sales[Date], ">="&EOMONTH(TODAY(),-1)+1, Sales[Date], "<="&EOMONTH(TODAY(),0))

-- Top Products
=LARGE(Sales[Amount], ROW(A1))
=XLOOKUP(LARGE(Sales[Amount], ROW(A1)), Sales[Amount], Sales[Product])

-- Monthly Comparison
Current Month: =SUMIFS(Sales[Amount], Sales[Date], ">="&EOMONTH(TODAY(),-1)+1, Sales[Date], "<="&EOMONTH(TODAY(),0))
Previous Month: =SUMIFS(Sales[Amount], Sales[Date], ">="&EOMONTH(TODAY(),-2)+1, Sales[Date], "<="&EOMONTH(TODAY(),-1))
YoY: =(Current - Previous) / Previous

-- Automated with VBA (optional)
Sub GenerateMonthlyReport()
    ' Refresh all data connections
    ActiveWorkbook.RefreshAll

    ' Update date references
    Range("Report_Date").Value = Date

    ' Save as PDF
    ActiveSheet.ExportAsFixedFormat Type:=xlTypePDF, _
        Filename:="Monthly_Report_" & Format(Date, "yyyy_mm") & ".pdf"
End Sub
```

---

## Advanced Scenarios

### Scenario 17: Build a Monte Carlo simulation
**Requirements:** Simulate project outcomes with uncertainty using random variables.

**Answer:**
```excel
-- Inputs with uncertainty
Revenue: =NORM.INV(RAND(), 100000, 15000)  -- Mean 100K, SD 15K
Cost: =NORM.INV(RAND(), 60000, 8000)       -- Mean 60K, SD 8K

-- Outcome
Profit: =Revenue - Cost

-- Run 1000 iterations
-- Use Data Table with blank row input to force recalculation
-- Column of 1s (1000 rows)
-- Data Table: Row input = blank cell, Column input = blank cell
-- Reference profit cell

-- Analyze results
Mean Profit: =AVERAGE(Simulation_Results)
Std Dev: =STDEV.S(Simulation_Results)
Probability of Loss: =COUNTIF(Simulation_Results, "<0") / COUNT(Simulation_Results)
95% Confidence: =PERCENTILE.INC(Simulation_Results, {0.025, 0.975})

-- Histogram
-- Use FREQUENCY function or Analysis ToolPak
```

---

### Scenario 18: Create a dynamic Gantt chart
**Requirements:** Visualize project timeline with tasks, dependencies, and progress.

**Answer:**
```excel
-- Project Data
Task | Start | Duration | End | Progress | Dependencies
-----|-------|----------|-----|----------|-------------
A    | 1     | 5        | =C2+D2-1 | 100%   | -
B    | =E2+1 | 3        | =C3+D3-1 | 50%    | A
C    | =E2+1 | 4        | =C4+D4-1 | 0%     | A

-- Gantt Chart (Conditional Formatting)
-- For each task row, apply conditional formatting
-- Formula: =AND(cell_column >= Start, cell_column <= End)
-- Format: Fill color for task bar
-- Additional rule for progress: =AND(cell_column >= Start, cell_column <= Start + Duration * Progress)
-- Format: Darker fill for completed portion

-- Timeline header
Week 1 | Week 2 | Week 3 | ...
=IF(AND(column >= $C2, column <= $E2), "█", "")

-- Critical Path (manual or using formulas)
-- Identify tasks with no float/slack
```

---

### Scenario 19: Build a what-if scenario model
**Requirements:** Allow users to toggle between Best Case, Base Case, and Worst Case scenarios.

**Answer:**
```excel
-- Scenario Table
Assumption    | Best Case | Base Case | Worst Case | Selected
--------------|-----------|-----------|------------|----------
Revenue Growth| 20%       | 10%       | -5%        | =CHOOSE(Scenario_Num, B2, C2, D2)
Gross Margin  | 65%       | 60%       | 50%        | =CHOOSE(Scenario_Num, B3, C3, D3)
OpEx Growth   | 5%        | 10%       | 15%        | =CHOOSE(Scenario_Num, B4, C4, D4)

-- Scenario Selector (Data Validation dropdown)
Cell: Scenario_Name
List: Best Case, Base Case, Worst Case

-- Scenario Number
=MATCH(Scenario_Name, {"Best Case","Base Case","Worst Case"}, 0)

-- Financial Model uses Selected values
Revenue: =Prior_Revenue * (1 + Selected_Revenue_Growth)
Gross Profit: =Revenue * Selected_Gross_Margin

-- Scenario Summary
=CHOOSE(Scenario_Num, "Optimistic outlook", "Expected outcome", "Conservative estimate")

-- Scenario Comparison Table
Metric     | Best Case | Base Case | Worst Case
-----------|-----------|-----------|------------
Revenue    | =Calc(Best) | =Calc(Base) | =Calc(Worst)
EBITDA     | ...       | ...       | ...
```

---

### Scenario 20: Create a dynamic stock portfolio tracker
**Requirements:** Track multiple stocks with real-time prices, performance, and allocation.

**Answer:**
```excel
-- Portfolio Data
Symbol | Shares | Purchase Price | Current Price | Value | Gain/Loss | Weight
-------|--------|----------------|---------------|-------|-----------|-------
AAPL   | 100    | $150           | =STOCKHISTORY(A2, TODAY(), TODAY(), 0, 1, 1) | =B2*D2 | =E2-(B2*C2) | =E2/Total_Value
MSFT   | 50     | $300           | ...           | ...   | ...       | ...

-- Alternative current price (if STOCKHISTORY not available)
-- Use WEBSERVICE or manual update

-- Portfolio Metrics
Total Value: =SUM(E2:E10)
Total Cost: =SUMPRODUCT(B2:B10, C2:C10)
Total Gain: =Total_Value - Total_Cost
Return %: =Total_Gain / Total_Cost

-- Allocation Pie Chart
-- Based on Weight column

-- Performance vs Benchmark
Benchmark Return: =STOCKHISTORY("SPY", start, end, 0, 1, 1)
Portfolio Return: =(Current_Value - Start_Value) / Start_Value
Alpha: =Portfolio_Return - Benchmark_Return

-- Rebalancing
Target Weight: 20% per stock
Target Value: =Total_Value * Target_Weight
Difference: =Target_Value - Current_Value
Action: =IF(Difference > 1000, "Buy " & TEXT(Difference, "$#,##0"), 
            IF(Difference < -1000, "Sell " & TEXT(ABS(Difference), "$#,##0"), "Hold"))
```
