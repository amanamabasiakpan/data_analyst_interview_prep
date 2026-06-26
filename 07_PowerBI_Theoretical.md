# Power BI Theoretical Questions & Answers

## Table of Contents
1. [Power BI Fundamentals](#power-bi-fundamentals)
2. [Data Modeling](#data-modeling)
3. [DAX](#dax)
4. [Visualizations](#visualizations)
5. [Power Query (M)](#power-query-m)
6. [Power BI Service](#power-bi-service)
7. [Performance & Optimization](#performance--optimization)
8. [Security & Governance](#security--governance)
9. [Advanced Concepts](#advanced-concepts)
10. [Integration & Deployment](#integration--deployment)

---

## Power BI Fundamentals

### Q1: What is Power BI and what are its components?
**Answer:**

**Power BI** is a business analytics service by Microsoft that provides interactive visualizations and BI capabilities.

**Components:**

**1. Power BI Desktop (Free):**
- Windows application for report development
- Data modeling, transformation, and visualization
- DAX formula editor
- Publish to Power BI Service

**2. Power BI Service (Cloud/SaaS):**
- Online platform for sharing and collaboration
- Dashboards, workspaces, apps
- Scheduled refresh
- Gateway for on-premises data

**3. Power BI Mobile:**
- iOS and Android apps
- Touch-optimized reports
- Alerts and notifications
- Offline access

**4. Power BI Report Server (On-Premises):**
- Self-hosted report server
- For organizations requiring on-premises deployment
- Requires Power BI Premium per user or Premium capacity

**5. Power BI Gateway:**
- Connects on-premises data to cloud
- Personal Gateway (for individual use)
- On-premises Data Gateway (enterprise, supports multiple users)

---

### Q2: What is the difference between a Report and a Dashboard in Power BI?
**Answer:**

| Feature | Report | Dashboard |
|---------|--------|-----------|
| Pages | Multiple pages | Single page |
| Data Sources | Can combine multiple | Tiles from various reports |
| Interactivity | Full (slicers, drill-through) | Limited (cross-highlight via Q&A) |
| Visualizations | All types | Tiles only (pinned visuals) |
| Filters | Page, report, visual level | Only Q&A |
| Creation | Built in Desktop/Service | Built in Service only |
| Sharing | Can be shared as app | Can be shared directly |
| Real-time | Can use streaming datasets | Better for real-time tiles |
| Alerts | No | Yes (on dashboard tiles) |

**Key Difference:**
- A **Report** is a multi-page, interactive view of data with full filtering and drill-through capabilities.
- A **Dashboard** is a single-pane collection of pinned tiles from various reports, optimized for monitoring KPIs.

---

### Q3: What are the different types of filters in Power BI?
**Answer:**

**1. Visual-Level Filter:**
- Applies to a single visual
- Most restrictive scope
- Example: Filter a bar chart to show top 10 products

**2. Page-Level Filter:**
- Applies to all visuals on a page
- Medium scope
- Example: Filter entire page to current year

**3. Report-Level Filter:**
- Applies to all pages in the report
- Broadest scope
- Example: Filter entire report to active customers only

**4. Drillthrough Filter:**
- Passes context from source page to target page
- Used for detailed analysis
- Example: Click on a product → drill to product detail page

**5. Cross-Filtering / Cross-Highlighting:**
- Interactive filtering by clicking on visuals
- Default behavior in Power BI
- Can be changed to "highlight" instead of "filter"

**6. Slicer:**
- User-facing filter control
- Types: List, Dropdown, Between, Before, After, Relative

**7. URL Parameter Filter (Power BI Embedded):**
- Pass filters via URL parameters
- Used for embedding scenarios

---

### Q4: What is the difference between Import, DirectQuery, and Live Connection?
**Answer:**

| Feature | Import | DirectQuery | Live Connection |
|---------|--------|-------------|-----------------|
| Data Storage | In-memory (VertiPaq) | In source only | In source only |
| Performance | Fastest (cached) | Depends on source | Depends on source |
| Data Size | Up to 10GB (Pro), 100GB (Premium) | Unlimited | Unlimited |
| Real-time | No (requires refresh) | Near real-time | Near real-time |
| Transformations | Full Power Query | Limited (query folding) | None |
| DAX | Full support | Some limitations | Limited (measures only) |
| Refresh | Scheduled/manual | Automatic per query | Automatic per query |
| Use Case | Historical analysis | Large datasets, real-time | SSAS/Analysis Services |

**Import Mode:**
- Data loaded into Power BI's in-memory engine (VertiPaq)
- Best performance for most scenarios
- Supports all transformations and DAX features
- Requires scheduled refresh

**DirectQuery:**
- Queries sent to source database in real-time
- No data stored in Power BI
- Limited to 1 million rows per query
- Some DAX functions not supported
- Best for: Large datasets, real-time needs, data sovereignty requirements

**Live Connection:**
- Connects to SSAS (SQL Server Analysis Services) or Azure Analysis Services
- Uses existing cube/model
- Cannot add new tables, only measures
- Best for: Enterprise scenarios with existing SSAS models

---

### Q5: What are Workspaces in Power BI?
**Answer:**

**Workspaces** are collaboration environments in Power BI Service for organizing content.

**Types:**

**1. My Workspace:**
- Personal workspace for individual use
- Content not shared by default
- Limited to 10GB storage (Pro)

**2. App Workspace (Shared Workspace):**
- For team collaboration
- Multiple members with different roles
- Content can be published as an App
- Supports deployment pipelines

**3. Premium Workspace:**
- Requires Premium capacity
- Larger storage (100TB)
- Paginated reports
- AI features
- No per-user license required for consumers

**Workspace Roles:**
- **Admin:** Full control, can delete workspace
- **Member:** Can publish, edit, share
- **Contributor:** Can edit content
- **Viewer:** Can only view (with Pro license or Premium)

---

### Q6: What is an App in Power BI?
**Answer:**

An **App** is a packaged collection of reports and dashboards designed for broad distribution.

**Benefits:**
- Clean, consumer-focused interface
- Easy distribution to large audiences
- Version control
- Permissions managed at app level
- Can be distributed via AppSource

**App vs Workspace:**
- **Workspace:** Development environment, collaboration
- **App:** Production environment, consumption

**Creating an App:**
1. Publish content to workspace
2. Create app from workspace
3. Add reports, dashboards, and navigation
4. Set permissions
5. Publish or update app

---

## Data Modeling

### Q7: What is a Data Model in Power BI?
**Answer:**

A **Data Model** is the structured representation of data in Power BI, consisting of:

**1. Tables:**
- Fact tables (measures, transactions)
- Dimension tables (attributes, descriptions)

**2. Relationships:**
- One-to-Many (1:*)
- Many-to-One (*:1)
- One-to-One (1:1)
- Many-to-Many (*:*)

**3. Hierarchies:**
- Natural hierarchies (Date: Year → Quarter → Month → Day)
- Custom hierarchies (Product: Category → Subcategory → Product)

**4. Calculations:**
- Measures (DAX)
- Calculated columns (DAX)
- Calculated tables (DAX)

**5. Properties:**
- Data types
- Formatting
- Sort by column
- Categories

---

### Q8: Explain Star Schema vs Snowflake Schema
**Answer:**

**Star Schema:**
```
        [Date]          [Product]
           |               |
           |               |
    [Customer] --- [Sales Fact] --- [Region]
           |               |
           |               |
        [Promotion]     [Shipper]
```
- Single fact table surrounded by dimension tables
- Dimensions directly connected to fact table
- Denormalized dimensions
- **Best for:** Power BI (simpler, faster)

**Snowflake Schema:**
```
        [Year] → [Quarter] → [Month] → [Date]
                                        |
    [Category] → [Subcategory] → [Product]
                                        |
    [Customer] --- [Sales Fact] --- [Region]
```
- Dimensions normalized into sub-dimensions
- Multiple levels of relationships
- **Best for:** Reducing redundancy, complex hierarchies

**Recommendation for Power BI:**
- Prefer **Star Schema** for simplicity and performance
- Use **Snowflake** only when necessary for data integrity
- Power BI's VertiPaq engine is optimized for star schemas

---

### Q9: What is a Relationship in Power BI? Explain cardinality and cross-filter direction.
**Answer:**

**Relationship** connects two tables based on matching columns.

**Cardinality:**

**1. One-to-Many (1:*):**
- Most common
- One row in dimension table matches many rows in fact table
- Example: One customer has many orders

**2. Many-to-One (*:1):**
- Reverse of one-to-many
- Many rows in fact table match one row in dimension

**3. One-to-One (1:1):**
- One row in each table matches exactly one row in the other
- Rare, used for splitting wide tables

**4. Many-to-Many (*:*):**
- Many rows in each table can match many in the other
- Requires bridge table or bi-directional filtering
- Example: Products and Tags

**Cross-Filter Direction:**

**1. Single (One-way):**
- Filters flow from the "one" side to the "many" side
- Default and recommended
- Example: Customer filters Orders, but Orders don't filter Customer

**2. Both (Bi-directional):**
- Filters flow both ways
- Can cause ambiguity and performance issues
- Use sparingly

**3. Many-to-Many with Bridge Table:**
- Bridge table in the middle
- Two one-to-many relationships
- Most reliable many-to-many approach

---

### Q10: What are Active and Inactive Relationships?
**Answer:**

**Active Relationship:**
- The primary relationship between two tables
- Used by default in visuals and DAX
- Only one active relationship allowed between two tables
- Shown as solid line in model view

**Inactive Relationship:**
- Alternative relationship between tables
- Not used by default
- Can be activated in DAX using USERELATIONSHIP()
- Shown as dashed line in model view

**Example - Multiple Date Columns:**
```dax
-- Active: OrderDate → Date table
-- Inactive: ShipDate → Date table

-- Use inactive relationship in measure
Total Sales by Ship Date =
CALCULATE(
    SUM(Sales[Amount]),
    USERELATIONSHIP(Sales[ShipDate], 'Date'[Date])
)
```

---

### Q11: What is a Calculated Column vs a Measure?
**Answer:**

| Feature | Calculated Column | Measure |
|---------|-------------------|---------|
| Evaluation | Row by row (row context) | Aggregate (filter context) |
| Storage | Stored in model (materialized) | Calculated on-the-fly |
| Use Case | Row-level attributes | Aggregations, KPIs |
| Context | Row context | Filter context |
| Example | Profit = Revenue - Cost | Total Profit = SUM(Sales[Profit]) |
| Performance | Impacts model size | Impacts query time |
| When to Use | Static attributes, lookup values | Dynamic calculations, ratios |

**Calculated Column:**
```dax
Profit = Sales[Revenue] - Sales[Cost]
-- Computed once when data is loaded/refreshed
-- Stored in the model
-- Can be used in rows, columns, filters, slicers
```

**Measure:**
```dax
Total Profit = SUM(Sales[Profit])
-- Computed on-the-fly based on filter context
-- Not stored (calculated when used)
-- Used in values area of visuals
-- Can reference other measures
```

**Rule of Thumb:**
- If you need the value in a row → Calculated Column
- If you need an aggregation → Measure
- When in doubt → Measure (more flexible, better performance)

---

### Q12: What are Hierarchies and how do you create them?
**Answer:**

**Hierarchy** is a structured drill-down path for data exploration.

**Types:**

**1. Date Hierarchy (Auto-generated):**
- Year → Quarter → Month → Day
- Created automatically when date column is marked as date

**2. Custom Hierarchies:**
- Product: Category → Subcategory → Product Name
- Geography: Country → State → City → Store
- Organization: Department → Team → Employee

**Creating a Hierarchy:**
1. In Model view or Data view
2. Right-click a column → "Create hierarchy"
3. Drag other columns into the hierarchy
4. Reorder levels as needed

**Using Hierarchies:**
- Drag hierarchy to visual axis
- Click + to expand/drill down
- Use "Expand all down one level" button
- Use "Drill mode" for focused exploration

**Best Practices:**
- Keep hierarchies intuitive (logical progression)
- Limit to 4-5 levels
- Use meaningful names
- Consider creating multiple hierarchies for different perspectives

---

## DAX

### Q13: What is DAX and what are its key concepts?
**Answer:**

**DAX (Data Analysis Expressions)** is the formula language used in Power BI, Power Pivot, and SSAS Tabular.

**Key Concepts:**

**1. Evaluation Context:**
- **Filter Context:** Set of filters applied to a calculation
- **Row Context:** Iterates row by row (in calculated columns and iterators)

**2. Functions:**
- Aggregation: SUM, AVERAGE, COUNT, MAX, MIN
- Filter: CALCULATE, FILTER, ALL, ALLEXCEPT
- Time Intelligence: TOTALYTD, SAMEPERIODLASTYEAR, DATESYTD
- Logical: IF, SWITCH, AND, OR
- Text: CONCATENATE, LEFT, RIGHT, SUBSTITUTE
- Math: ROUND, ABS, DIVIDE
- Iterator: SUMX, AVERAGEX, COUNTX, MAXX, MINX

**3. Measures vs Calculated Columns:**
- Measures: Dynamic, filter context, for values
- Calculated Columns: Static, row context, for attributes

**4. Variables:**
```dax
VAR TotalSales = SUM(Sales[Amount])
VAR TotalCost = SUM(Sales[Cost])
RETURN TotalSales - TotalCost
```

---

### Q14: Explain Filter Context and Row Context in DAX
**Answer:**

**Row Context:**
- Exists when iterating over rows
- Automatically created in calculated columns
- Created by iterator functions (SUMX, AVERAGEX, FILTER)
- Contains values of the current row

```dax
-- Row context in calculated column
Profit = Sales[Revenue] - Sales[Cost]
-- For each row, it uses that row's Revenue and Cost

-- Row context with iterator
Total Profit = SUMX(Sales, Sales[Revenue] - Sales[Cost])
-- SUMX iterates each row, calculates per row, then sums
```

**Filter Context:**
- Set of filters applied to a calculation
- Created by visuals, slicers, report filters
- Can be modified with CALCULATE
- Affects which rows are visible

```dax
-- Filter context from visual
-- If a bar chart shows "Sales by Category"
-- Each bar has a filter context: Category = "Electronics"

Total Sales = SUM(Sales[Amount])
-- In a bar chart by category, this automatically filters per category
```

**Transitioning Context:**
```dax
-- CALCULATE transitions row context to filter context
Average Sales per Customer =
AVERAGEX(
    Customers,
    CALCULATE(SUM(Sales[Amount]))
    -- CALCULATE converts the row context (current customer)
    -- into a filter context for the SUM
)
```

---

### Q15: What is CALCULATE and why is it so important?
**Answer:**

**CALCULATE** is the most powerful and important function in DAX. It modifies the filter context of an expression.

**Syntax:**
```dax
CALCULATE(expression, filter1, filter2, ...)
```

**What it does:**
1. Evaluates filters
2. Modifies the current filter context
3. Evaluates the expression in the modified context

**Examples:**
```dax
-- Override filter context
Sales 2024 = CALCULATE(SUM(Sales[Amount]), 'Date'[Year] = 2024)

-- Remove filters
Total Sales All Products = CALCULATE(SUM(Sales[Amount]), ALL(Products))

-- Add filters
Sales Electronics = CALCULATE(SUM(Sales[Amount]), Products[Category] = "Electronics")

-- Multiple filters
Sales Electronics 2024 = CALCULATE(
    SUM(Sales[Amount]),
    Products[Category] = "Electronics",
    'Date'[Year] = 2024
)

-- Use with ALL to get % of total
% of Total = 
DIVIDE(
    SUM(Sales[Amount]),
    CALCULATE(SUM(Sales[Amount]), ALL(Products))
)
```

**Key Behaviors:**
- Adds filters (AND logic with existing filters)
- Can override with ALL, ALLEXCEPT, ALLSELECTED
- Transitions row context to filter context
- Can use table expressions as filters

---

### Q16: Explain Time Intelligence functions in DAX
**Answer:**

**Time Intelligence** functions perform calculations over date periods.

**Prerequisites:**
- Date table marked as date table
- Continuous date range with no gaps
- One-to-many relationship with fact table

**Common Functions:**

**Year-to-Date:**
```dax
Sales YTD = TOTALYTD(SUM(Sales[Amount]), 'Date'[Date])
Sales QTD = TOTALQTD(SUM(Sales[Amount]), 'Date'[Date])
Sales MTD = TOTALMTD(SUM(Sales[Amount]), 'Date'[Date])
```

**Period Comparisons:**
```dax
Sales Same Period LY = CALCULATE(SUM(Sales[Amount]), SAMEPERIODLASTYEAR('Date'[Date]))
Sales PY = CALCULATE(SUM(Sales[Amount]), PARALLELPERIOD('Date'[Date], -1, YEAR))
Sales PM = CALCULATE(SUM(Sales[Amount]), DATEADD('Date'[Date], -1, MONTH))
Sales PQ = CALCULATE(SUM(Sales[Amount]), DATEADD('Date'[Date], -1, QUARTER))
```

**Running Totals:**
```dax
Sales Running Total =
CALCULATE(
    SUM(Sales[Amount]),
    FILTER(
        ALL('Date'),
        'Date'[Date] <= MAX('Date'[Date])
    )
)
```

**Moving Averages:**
```dax
Sales 30-Day MA =
AVERAGEX(
    DATESINPERIOD('Date'[Date], LASTDATE('Date'[Date]), -30, DAY),
    CALCULATE(SUM(Sales[Amount]))
)
```

**Custom Date Table:**
```dax
Date Table =
ADDCOLUMNS(
    CALENDAR(DATE(2020, 1, 1), DATE(2025, 12, 31)),
    "Year", YEAR([Date]),
    "Quarter", "Q" & QUARTER([Date]),
    "Month", FORMAT([Date], "MMMM"),
    "MonthNum", MONTH([Date]),
    "Week", WEEKNUM([Date]),
    "DayOfWeek", FORMAT([Date], "dddd"),
    "DayOfWeekNum", WEEKDAY([Date]),
    "IsWeekend", IF(WEEKDAY([Date]) IN {1, 7}, TRUE, FALSE),
    "FiscalYear", IF(MONTH([Date]) >= 7, YEAR([Date]) + 1, YEAR([Date]))
)
```

---

### Q17: What is the difference between ALL, ALLEXCEPT, ALLSELECTED, and REMOVEFILTERS?
**Answer:**

**ALL:**
- Removes all filters from the specified table or columns
- Returns all rows regardless of filters

```dax
Total Sales All Time = CALCULATE(SUM(Sales[Amount]), ALL('Date'))
Total Sales All Products = CALCULATE(SUM(Sales[Amount]), ALL(Products))
```

**ALLEXCEPT:**
- Removes all filters EXCEPT those on specified columns
- Keeps filters on the columns you specify

```dax
-- Remove all product filters except Category
Sales by Category Total = CALCULATE(SUM(Sales[Amount]), ALLEXCEPT(Products, Products[Category]))
```

**ALLSELECTED:**
- Removes filters within the visual but keeps external filters
- Respects filters from slicers and other visuals
- Most commonly used for % of total calculations

```dax
% of Selected Total =
DIVIDE(
    SUM(Sales[Amount]),
    CALCULATE(SUM(Sales[Amount]), ALLSELECTED(Products))
)
```

**REMOVEFILTERS:**
- Newer function (DAX 2019+)
- Same as ALL when used as a filter modifier in CALCULATE
- More readable and explicit

```dax
Total Sales = CALCULATE(SUM(Sales[Amount]), REMOVEFILTERS(Products))
-- Equivalent to: CALCULATE(SUM(Sales[Amount]), ALL(Products))
```

**Comparison:**

| Function | Removes Filters | Keeps Filters | Use Case |
|----------|----------------|---------------|----------|
| ALL | All from specified table/columns | None | % of grand total |
| ALLEXCEPT | All except specified | Specified columns | % within category |
| ALLSELECTED | Internal visual filters | External filters (slicers) | % of selected |
| REMOVEFILTERS | All from specified | None | Same as ALL, more readable |

---

### Q18: What are Iterator Functions (X-functions) in DAX?
**Answer:**

**Iterator (X) functions** iterate over a table, evaluate an expression for each row, and then aggregate the results.

**Syntax Pattern:**
```dax
FUNCTIONX(table, expression)
```

**Common Iterator Functions:**

```dax
-- SUMX: Sum of expression
Total Profit = SUMX(Sales, Sales[Revenue] - Sales[Cost])

-- AVERAGEX: Average of expression
Avg Profit per Order = AVERAGEX(Sales, Sales[Revenue] - Sales[Cost])

-- COUNTX: Count non-blank results
Count of Products Sold = COUNTX(Sales, Sales[ProductID])

-- COUNTAX: Count non-blank text
Count of Comments = COUNTAX(Feedback, Feedback[Comment])

-- MINX/MAXX: Min/Max of expression
Min Profit = MINX(Sales, Sales[Revenue] - Sales[Cost])
Max Profit = MAXX(Sales, Sales[Revenue] - Sales[Cost])

-- PRODUCTX: Product of expression
Compound Growth = PRODUCTX(Growth, 1 + Growth[Rate])

-- CONCATENATEX: Concatenate text
Product List = CONCATENATEX(Products, Products[Name], ", ")
```

**Iterator vs Simple Aggregation:**
```dax
-- Simple: Sum of stored column
Total Revenue = SUM(Sales[Revenue])

-- Iterator: Sum of calculated expression
Total Profit = SUMX(Sales, Sales[Revenue] - Sales[Cost])
-- Cannot do: SUM(Sales[Revenue] - Sales[Cost]) -- This is row-by-row then sum
```

**Performance Consideration:**
- Iterators can be slower on large tables
- Consider creating calculated columns for complex expressions if used frequently
- Use variables to optimize

---

### Q19: What is the EARLIER function in DAX?
**Answer:**

**EARLIER** refers to the previous row context in nested calculations.

**Use Case:**
When you have nested row contexts (e.g., FILTER inside a calculated column), EARLIER accesses the outer row context.

```dax
-- Rank customers by sales (without RANKX)
Customer Rank =
COUNTROWS(
    FILTER(
        ALL(Customers),
        Customers[TotalSales] >= EARLIER(Customers[TotalSales])
    )
)

-- Explanation:
-- FILTER creates a new row context iterating over ALL(Customers)
-- EARLIER refers to the current row in the outer context
-- So for each customer, count how many have >= sales
```

**Modern Alternative (preferred):**
```dax
-- Use RANKX instead
Customer Rank = RANKX(ALL(Customers), Customers[TotalSales])
```

**EARLIEST:**
- Refers to the outermost row context
- Rarely used

---

### Q20: Explain the TREATAS function
**Answer:**

**TREATAS** applies the result of a table expression as filters to unrelated columns.

**Use Case:**
When you need to filter one table based on values from another table that don't have a direct relationship.

```dax
-- Filter sales by products that were on promotion
Sales of Promoted Products =
CALCULATE(
    SUM(Sales[Amount]),
    TREATAS(VALUES(Promotions[ProductID]), Sales[ProductID])
)

-- TREATAS takes the ProductIDs from Promotions table
-- and applies them as filters on Sales[ProductID]
-- even if there's no direct relationship
```

**Comparison with RELATEDTABLE:**
```dax
-- RELATEDTABLE requires a relationship
Sales of Promoted = CALCULATE(SUM(Sales[Amount]), RELATEDTABLE(Promotions))

-- TREATAS works without relationship
Sales of Promoted = CALCULATE(SUM(Sales[Amount]), TREATAS(VALUES(Promotions[ProductID]), Sales[ProductID]))
```

---

## Visualizations

### Q21: What are the different types of visuals in Power BI?
**Answer:**

**Standard Visuals:**

**1. Bar/Column Charts:**
- Clustered, Stacked, 100% Stacked
- Horizontal (bar) and Vertical (column)

**2. Line Charts:**
- Basic line, Stacked area, Line and clustered column
- Forecasting capability

**3. Pie/Doughnut Charts:**
- Simple proportions
- Best for few categories (< 6)

**4. Cards:**
- Single value display
- Multi-row card

**5. Tables/Matrices:**
- Table: Flat grid
- Matrix: Pivot-style with hierarchies

**6. Maps:**
- Filled map (choropleth)
- Bubble map
- ArcGIS map
- Azure map

**7. Scatter/Bubble Charts:**
- X and Y axes with optional size
- Play axis for animation

**8. Waterfall/Funnel Charts:**
- Waterfall: Step changes
- Funnel: Process stages

**9. Gauges:**
- Radial gauge
- Linear gauge

**10. KPI:**
- Value, target, trend

**Custom Visuals:**
- From AppSource marketplace
- R/Python visuals
- Developer-created visuals

---

### Q22: What is a Slicer and what are its types?
**Answer:**

A **Slicer** is an interactive filter control that allows users to filter report data.

**Types:**

**1. List Slicer:**
- Vertical or horizontal list of values
- Single or multi-select
- Search capability

**2. Dropdown Slicer:**
- Compact, expandable list
- Good for many values

**3. Between Slicer:**
- For numeric/date ranges
- Slider control

**4. Before/After Slicer:**
- For dates
- Relative filtering

**5. Relative Date Slicer:**
- "Last 7 days", "This month", "Next quarter"
- Dynamic based on current date

**6. Hierarchy Slicer (Custom):**
- Drill-down slicer
- Multi-level filtering

**7. Tile Slicer:**
- Button-style slicer
- Good for few options

**8. Chiclet Slicer (Custom):**
- Image-based buttons
- Visual appeal

**Slicer Settings:**
- Single select vs Multi-select
- Select all option
- Search
- Show items with no data
- Responsive layout

---

### Q23: What are Bookmarks, Buttons, and Drillthrough in Power BI?
**Answer:**

**Bookmarks:**
- Save the current state of a report page
- Include: filters, slicers, visuals visibility, sort order
- Used for: storytelling, navigation, toggling views

```
Creating Bookmarks:
1. Set up the page state you want to save
2. View > Bookmarks pane
3. Add bookmark
4. Name and configure (Data, Display, Current page)
```

**Buttons:**
- Interactive elements for navigation and actions
- Types: Back, Bookmark, Drillthrough, Q&A, Web URL, Page navigation
- Can have custom images and text
- Action types: Bookmark, Drillthrough, Page navigation, Q&A, Web URL

**Drillthrough:**
- Navigate from summary page to detail page
- Pass filter context automatically
- Set up: Target page > Drillthrough fields > Source visual right-click > Drill through

```dax
-- Use Drillthrough filters in target page measures
Selected Product Sales = CALCULATE(SUM(Sales[Amount]))
-- Automatically filtered by the drillthrough context
```

---

### Q24: What is the Q&A Visual?
**Answer:**

**Q&A (Natural Language Query)** allows users to ask questions about their data in plain English.

**Features:**
- Type questions like "Show me sales by region for 2024"
- Auto-suggests questions
- Supports synonyms and row labels
- Can be pinned to dashboard

**Setup:**
1. Enable Q&A in report settings
2. Define synonyms for fields
3. Set row labels for tables
4. Configure suggested questions

**Best Practices:**
- Use clear, intuitive field names
- Define synonyms for business terms
- Hide unnecessary fields
- Test common questions
- Train users on phrasing

---

## Power Query (M)

### Q25: What is Power Query and how does it work in Power BI?
**Answer:**

**Power Query** is the data transformation engine in Power BI (also in Excel).

**Key Features:**
- Connect to 100+ data sources
- Clean, transform, and reshape data
- Reproducible steps (audit trail)
- Query folding (push processing to source)
- M Language for advanced transformations

**Workflow:**
1. **Connect:** Choose data source
2. **Transform:** Clean and shape data
3. **Combine:** Merge and append queries
4. **Load:** Import to model or DirectQuery

**Query Folding:**
- Power Query pushes transformations to the source database
- Improves performance
- View: Query > View Native Query
- Breaks with certain transformations (custom columns, pivot, etc.)

---

### Q26: What are the differences between Merge and Append Queries?
**Answer:**

**Merge Queries (SQL JOIN equivalent):**
- Combines columns from two tables
- Based on matching key columns
- Types: Inner, Left Outer, Right Outer, Full Outer, Left Anti, Right Anti, Inner

```m
-- M Language
Table.NestedJoin(Table1, {"Key"}, Table2, {"Key"}, "Joined", JoinKind.LeftOuter)
```

**Append Queries (SQL UNION ALL equivalent):**
- Stacks rows from two or more tables
- Tables should have same/similar columns

```m
-- M Language
Table.Combine({Table1, Table2})
```

**When to use:**
- **Merge:** When you need to add columns from another table (lookup)
- **Append:** When you need to combine data from similar sources (monthly files)

---

### Q27: What is Query Folding and why is it important?
**Answer:**

**Query Folding** is the process where Power Query pushes transformation logic back to the data source.

**Benefits:**
1. **Performance:** Source database is optimized for queries
2. **Efficiency:** Less data transferred over network
3. **Scalability:** Can handle larger datasets

**How to check if folding is happening:**
- Right-click a step > "View Native Query"
- If grayed out, folding is broken

**Operations that BREAK folding:**
- Adding index columns
- Pivot/Unpivot
- Merging with non-database sources
- Custom columns with complex logic
- Sorting (sometimes)

**Best Practices:**
- Do filtering early (before breaking operations)
- Use database-native functions when possible
- Minimize transformations after folding breaks
- Consider using SQL views for complex logic

---

## Power BI Service

### Q28: What is the Power BI Gateway and when do you need it?
**Answer:**

**Power BI Gateway** is software that connects on-premises data sources to Power BI Service.

**Types:**

**1. On-premises Data Gateway (Personal Mode):**
- For individual use
- One user per gateway
- Limited to 64-bit versions
- Good for: Personal reports, testing

**2. On-premises Data Gateway (Enterprise/Standard Mode):**
- Shared across organization
- Multiple users, multiple data sources
- Supports: Analysis Services, SQL Server, Oracle, SAP, etc.
- High availability with clustering
- Admin management

**When Needed:**
- Refreshing Import-mode datasets with on-premises data
- DirectQuery/Live Connection to on-premises sources
- Refreshing Excel workbooks with on-premises data

**Setup:**
1. Download gateway from Power BI Service
2. Install on server or dedicated machine
3. Configure data sources in Power BI Service
4. Map datasets to gateway data sources

---

### Q29: What is Row-Level Security (RLS) in Power BI?
**Answer:**

**Row-Level Security** restricts data access based on user identity, ensuring users only see data they're authorized to view.

**Types:**

**1. Static RLS:**
- Hardcoded filters in DAX
- Same for all users in a role

```dax
-- Role: Sales Managers
[Region] = "North America"

-- Role: Europe Users
[Region] = "Europe"
```

**2. Dynamic RLS:**
- Uses USERNAME() or USERPRINCIPALNAME()
- Different filters per user

```dax
-- Role: Dynamic
[Email] = USERNAME()
-- or
[Email] = USERPRINCIPALNAME()
```

**Setup Steps:**
1. Model view > Manage roles
2. Create role and define DAX filter
3. Test with "View as roles"
4. Assign users to roles in Power BI Service

**Best Practices:**
- Use USERPRINCIPALNAME() for email matching
- Create a user/role mapping table
- Test thoroughly with different users
- Document roles and assignments

---

### Q30: What is Incremental Refresh in Power BI?
**Answer:**

**Incremental Refresh** refreshes only new or changed data instead of the entire dataset, improving performance and reducing resource usage.

**Benefits:**
- Faster refresh times
- Lower memory usage
- Can handle larger datasets
- Reduced load on data sources

**Setup:**
1. Define parameters: RangeStart, RangeEnd (DateTime type)
2. Filter query using parameters
3. Enable incremental refresh in dataset settings
4. Configure:
   - Archive data starting: How far back to keep
   - Incrementally refresh data starting: How far back to refresh
   - Detect data changes (optional)
   - Only refresh complete days (optional)

**Requirements:**
- Power BI Premium or Pro with large dataset format
- Date column for partitioning
- Import mode only
- Parameters must be named RangeStart and RangeEnd

---

## Performance & Optimization

### Q31: How do you optimize Power BI report performance?
**Answer:**

**Data Model Optimization:**
1. **Use Star Schema:** Simpler relationships, better compression
2. **Remove unnecessary columns:** Reduces model size
3. **Use appropriate data types:** Smaller is better
4. **Create calculated columns sparingly:** Prefer measures
5. **Use integer keys:** Better than text for relationships
6. **Avoid bi-directional relationships:** Use CROSSFILTER or bridge tables
7. **Create aggregations:** For large fact tables

**DAX Optimization:**
1. **Use variables:** Reduces redundant calculations
2. **Avoid iterator functions on large tables:** SUMX, AVERAGEX
3. **Use DIVIDE instead of /:** Handles division by zero
4. **Minimize nested CALCULATE:** Each adds context transition overhead
5. **Use KEEPFILTERS when appropriate:** Preserves existing filters
6. **Avoid ALL on large tables:** Use ALL on specific columns

**Visual Optimization:**
1. **Limit visuals per page:** < 30 visuals
2. **Use filters:** Don't load unnecessary data
3. **Avoid high-cardinality slicers:** Use search or dropdown
4. **Use report-level filters:** Reduce data loaded
5. **Disable auto date/time:** Create explicit date table

**Query Optimization:**
1. **Enable query folding:** Do filtering in source
2. **Remove unnecessary steps:** Each step adds overhead
3. **Use native queries for complex logic:** SQL views
4. **Minimize data types conversions:** Do in source when possible

**Tools:**
- Performance Analyzer (View > Performance Analyzer)
- DAX Studio (external tool)
- VertiPaq Analyzer (external tool)
- Query Diagnostics

---

### Q32: What is the Performance Analyzer in Power BI?
**Answer:**

**Performance Analyzer** (View > Performance Analyzer) measures the time taken by each visual to render.

**Metrics:**
1. **DAX Query:** Time to run DAX query
2. **Visual Display:** Time to render visual
3. **Other:** Background processes
4. **Total:** Overall time

**Using Performance Analyzer:**
1. Start recording
2. Interact with report (filter, drill, etc.)
3. Stop recording
4. Analyze results
5. Copy query to DAX Studio for optimization

**Optimization based on results:**
- High DAX Query time → Optimize measures, reduce data
- High Visual Display time → Simplify visuals, reduce data points
- High Other time → Check model size, relationships

---

## Security & Governance

### Q33: What are the different ways to share content in Power BI?
**Answer:**

**1. Direct Sharing:**
- Share individual reports/dashboards
- Requires Pro license for both parties
- Limited control

**2. Workspace Sharing:**
- Add members to workspace
- Role-based access (Admin, Member, Contributor, Viewer)
- Good for collaboration

**3. Apps:**
- Package reports and dashboards
- Broad distribution
- Version control
- Can distribute to entire organization

**4. Embed:**
- **Publish to web:** Public, no authentication
- **Embed in SharePoint:** Internal, requires authentication
- **Embed in Teams:** Integrated with Microsoft Teams
- **Power BI Embedded:** For external applications (Azure)
- **Secure Embed:** Token-based authentication

**5. Export:**
- PDF, PowerPoint, Excel
- Static snapshots
- Can be scheduled

---

### Q34: What is Data Classification and Sensitivity Labels in Power BI?
**Answer:**

**Sensitivity Labels** (Microsoft Information Protection) classify and protect content.

**Capabilities:**
- Apply labels to datasets, reports, dashboards
- Enforce encryption and access policies
- Track and revoke access
- Watermarking

**Label Types:**
- Public
- General
- Confidential
- Highly Confidential

**Setup:**
1. Configure in Microsoft 365 Security Center
2. Enable in Power BI Admin portal
3. Apply labels to content
4. Enforce policies

---

## Advanced Concepts

### Q35: What is Composite Model in Power BI?
**Answer:**

**Composite Model** allows combining Import and DirectQuery tables in the same model.

**Benefits:**
- Import dimension tables (fast, small)
- DirectQuery fact tables (large, real-time)
- Best of both worlds

**Requirements:**
- Power BI Desktop (July 2018+)
- Power BI Service with compatible gateway

**Considerations:**
- Performance depends on DirectQuery sources
- Limited DAX functions for DirectQuery
- Relationships between Import and DirectQuery tables supported

---

### Q36: What is Aggregation in Power BI?
**Answer:**

**Aggregations** pre-calculate summary tables to improve query performance on large datasets.

**How it works:**
1. Create aggregation table (summarized data)
2. Define aggregation mappings
3. Power BI routes queries to aggregation table when possible
4. Falls back to detail table for unsupported queries

**Setup:**
1. Create aggregation table in source or Power Query
2. In model view, select table > Properties > Manage aggregations
3. Map aggregation columns to detail columns
4. Define summarization method

**Benefits:**
- Dramatically faster queries
- Reduced memory usage
- Can handle trillion-row datasets

---

### Q37: What is Field Parameters in Power BI?
**Answer:**

**Field Parameters** (formerly Dynamic M parameters) allow users to dynamically change the fields used in visuals.

**Use Cases:**
- Toggle between measures (Sales, Profit, Quantity)
- Toggle between dimensions (Product, Region, Category)
- Dynamic axis switching

**Creating:**
1. Modeling > New Parameter > Fields
2. Select fields to include
3. Use parameter in visuals

**Example:**
```dax
-- Create field parameter: Metric Selector
-- Include: Total Sales, Total Profit, Total Quantity

-- Use in visual:
-- Axis: Date
-- Values: Metric Selector
-- User can toggle between Sales, Profit, Quantity
```

---

## Integration & Deployment

### Q38: How do you implement CI/CD for Power BI?
**Answer:**

**CI/CD (Continuous Integration/Continuous Deployment)** for Power BI:

**1. Deployment Pipelines (Power BI Service):**
- Built-in CI/CD for Premium workspaces
- Three stages: Development, Test, Production
- Compare and deploy content between stages
- Dataset rules for environment-specific settings

**2. Azure DevOps / Git Integration:**
- Power BI Desktop files in Git repository
- Azure Pipelines for automated deployment
- REST APIs for programmatic operations

**3. Power BI REST APIs:**
- Automate dataset refresh
- Deploy reports
- Manage workspaces
- Update parameters

**4. ALM Toolkit (External):**
- Compare and deploy datasets
- Schema comparison
- BISM file comparison

**Best Practices:**
- Separate dev/test/prod workspaces
- Use parameters for environment-specific values
- Version control PBIX files (with limitations)
- Automate testing with REST APIs
- Document deployment process

---

### Q39: What is the difference between Power BI and Tableau?
**Answer:**

| Feature | Power BI | Tableau |
|---------|----------|---------|
| Cost | Lower (Pro $10/user/month) | Higher ($70/user/month) |
| Microsoft Integration | Excellent (Excel, Teams, SharePoint) | Good |
| ETL | Power Query (built-in) | Tableau Prep (separate) |
| DAX/Calculations | DAX (powerful, complex) | Table Calculations (simpler) |
| Custom Visuals | AppSource marketplace | Tableau Public/Exchange |
| Learning Curve | Moderate | Moderate |
| Enterprise Features | Strong with Premium | Strong with Server/Online |
| AI/ML | Built-in AI visuals, AutoML | Limited |
| On-Premises | Power BI Report Server | Tableau Server |
| Mobile | Excellent | Good |
| Community | Large, Microsoft-backed | Large, established |

**When to choose Power BI:**
- Microsoft ecosystem
- Cost-sensitive
- Need strong data modeling (DAX)
- Want integrated ETL

**When to choose Tableau:**
- Advanced visualizations
- Strong mapping/GIS needs
- Existing Tableau infrastructure
- Prefer simpler calculations

---

### Q40: What are AI Visuals in Power BI?
**Answer:**

**AI Visuals** use machine learning to provide insights:

**1. Q&A Visual:**
- Natural language queries
- Ask questions in plain English

**2. Key Influencers:**
- Identifies factors that influence a metric
- Example: "What influences customer churn?"

**3. Decomposition Tree:**
- Exploratory visual
- Drill down by dimensions
- AI-driven "High value" and "Low value" paths

**4. Smart Narratives:**
- Auto-generated text summaries
- Updates with filters

**5. Anomaly Detection:**
- Line charts with automatic anomaly highlighting
- Explains why anomaly occurred

**6. Automated ML (AutoML):**
- Build ML models in Power BI Service
- Requires Premium
- Classification, regression, forecasting

---

### Q41-50: Quick Reference

**Q41: What is a Dataflow?**
- Reusable ETL process in Power BI Service
- Creates entities (tables) that can be used by multiple datasets
- Uses Power Query Online

**Q42: What is the difference between a Measure and a Quick Measure?**
- Measure: Hand-written DAX
- Quick Measure: UI-generated DAX template

**Q43: What is the Analyze in Excel feature?**
- Connect Excel PivotTables to Power BI datasets
- Requires Analyze in Excel option enabled

**Q44: What is the Paginated Report?**
- Pixel-perfect, print-optimized reports
- Based on Report Definition Language (RDL)
- Requires Premium per user or Premium capacity

**Q45: What is the XMLA Endpoint?**
- Allows third-party tools to connect to Power BI datasets
- Read/write access to dataset metadata
- Requires Premium

**Q46: What is the Large Dataset Format?**
- Supports datasets > 10GB (up to 100GB with Premium)
- Enables incremental refresh
- Requires Premium

**Q47: What is the Hybrid Tables feature?**
- Combines import and DirectQuery in the same table
- Historical data imported, recent data DirectQuery
- Requires Premium

**Q48: What is Real-time Streaming in Power BI?**
- Push data to Power BI in real-time
- Types: Streaming dataset, Push dataset, PubNub
- For dashboards with real-time tiles

**Q49: What is the difference between a Personal Gateway and Enterprise Gateway?**
- Personal: Single user, simple setup
- Enterprise: Multiple users, multiple sources, admin management, high availability

**Q50: What is the Best Practice for Date Tables?**
- Create explicit date table (don't rely on auto date/time)
- Mark as date table
- Include fiscal calendar if needed
- Hide auto-generated date hierarchies
