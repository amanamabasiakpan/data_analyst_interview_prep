# 19. Data Warehousing Concepts

## Theoretical Questions

### Q1: What is a data warehouse and how does it differ from a transactional database?
**A:** A data warehouse is a centralized repository for integrated data from multiple sources, optimized for analytical queries and reporting. Key differences from OLTP databases:
- **OLTP:** Row-oriented, normalized, frequent small transactions, current data only
- **Data Warehouse:** Column-oriented (often), denormalized/star schema, large read-heavy queries, historical data (years)
- **Data Warehouse supports:** Complex aggregations, trend analysis, and cross-functional reporting

### Q2: What are the two main data warehouse methodologies (Kimball vs. Inmon)?
**A:**
- **Ralph Kimball (Bottom-Up):** Start with dimensional models (star schemas) for specific business processes. Data marts are built first, then integrated into a warehouse. Focus: ease of use, fast delivery, user-facing design. Schema: star/snowflake.
- **Bill Inmon (Top-Down):** Build a centralized, normalized enterprise data warehouse (EDW) first, then create data marts from it. Focus: data integrity, single source of truth, comprehensive. Schema: 3NF normalized.
**Modern practice:** Often hybrid — Inmon's centralized warehouse with Kimball's dimensional modeling for marts.

### Q3: What is a star schema and why is it popular in data warehousing?
**A:** A dimensional modeling pattern with:
- **Fact table:** Central table containing measurable business events (sales, orders) with foreign keys to dimensions and numeric metrics (measures)
- **Dimension tables:** Surrounding tables with descriptive attributes (customer, product, time, location)
**Why popular:** Simple to understand, fast query performance (fewer joins), and optimized for BI tools. The "star" shape comes from fact in center with dimensions radiating out.

### Q4: What is a snowflake schema and when do you use it?
**A:** A variation of star schema where dimension tables are normalized into sub-dimensions. Example: Product dimension → Category sub-dimension → Department sub-dimension. **Use when:** Storage is a concern, dimensions are large and shared across multiple fact tables, or when dimensions have their own hierarchies. Trade-off: more joins, slightly slower queries than star schema.

### Q5: What is a fact table and what types exist?
**A:** A fact table stores quantitative business events. Types:
- **Transaction fact:** One row per event (e.g., each sale). Most granular.
- **Periodic snapshot:** One row per entity per time period (e.g., account balance at month-end). Good for state tracking.
- **Accumulating snapshot:** One row per business process instance, updated as process progresses (e.g., order status from placed → shipped → delivered). Good for workflow analysis.
- **Factless fact:** Captures relationships/events without measures (e.g., student attendance — just keys).

### Q6: What is a dimension table and what are the types?
**A:** A dimension table provides context for facts. Types:
- **Conformed dimension:** Shared across multiple fact tables (e.g., standard Date, Customer dimensions). Ensures consistency.
- **Degenerate dimension:** Dimension attribute stored in fact table (e.g., order_number — no separate dimension table needed)
- **Junk dimension:** Combine low-cardinality flags/indicators into one dimension (e.g., order_type + payment_status + shipping_method)
- **Role-playing dimension:** Same dimension used multiple ways (e.g., OrderDate vs. ShipDate both using Date dimension)
- **Slowly Changing Dimension (SCD):** Tracks changes over time (see Q12)

### Q7: What are the key components of a dimensional model?
**A:**
- **Facts:** Numeric measures (quantity, revenue, cost) — additive, semi-additive, or non-additive
- **Dimensions:** Descriptive context (who, what, where, when, why, how)
- **Grain:** The level of detail in the fact table (e.g., "one row per product per day per store")
- **Keys:** Surrogate keys (auto-generated), natural keys (source system), and foreign keys
- **Hierarchies:** Roll-up paths (Day → Month → Quarter → Year)

### Q8: What is grain in a data warehouse and why is it critical?
**A:** Grain defines the finest level of detail in a fact table. It answers: "What does one row represent?" Examples: one row per transaction, per day per product per store, or per web page view. **Critical because:** all calculations and aggregations must respect the grain. Changing grain after design is extremely difficult. Always define grain before building the fact table.

### Q9: What is a conformed dimension and why does it matter?
**A:** A dimension that has the same meaning and structure across all fact tables that use it. Example: a Customer dimension with consistent customer_id, name, and segment definitions used in Sales, Support, and Marketing fact tables. **Matters because:** enables cross-functional analysis ("show me revenue and support tickets for premium customers") and prevents conflicting definitions.

### Q10: What is a data mart and how does it relate to a data warehouse?
**A:** A data mart is a subset of a data warehouse focused on a specific business function (Sales, Marketing, Finance). Can be:
- **Dependent:** Sourced from the central warehouse (Kimball approach)
- **Independent:** Built directly from source systems (faster but risk of inconsistency)
- **Hybrid:** Mix of both
Benefits: tailored to user needs, faster queries, and easier security (users only see their mart).

### Q11: What is an OLAP cube and how does it work?
**A:** A multidimensional data structure for fast analytical querying. Pre-aggregates data across dimensions. Users "slice and dice" by selecting dimensions and measures. Operations:
- **Roll-up:** Aggregate up a hierarchy (daily → monthly)
- **Drill-down:** Navigate to finer detail (year → quarter → month)
- **Slice:** Filter one dimension (sales WHERE region = 'North')
- **Dice:** Filter multiple dimensions (sales WHERE region = 'North' AND product = 'Electronics')
- **Pivot:** Rotate the cube to view different dimension combinations

### Q12: What are Slowly Changing Dimensions (SCD) and what are the types?
**A:** Techniques for tracking changes in dimension attributes over time:
- **Type 0:** Fixed, never changes (original data)
- **Type 1:** Overwrite old value (no history). Simple but loses history.
- **Type 2:** Add new row with effective dates (full history). Most common. Adds start_date, end_date, and is_current flag.
- **Type 3:** Add new column for previous value (limited history). Rarely used.
- **Type 4:** Separate history table. Current value in main dimension, history in another table.
- **Type 6:** Hybrid (Type 1 + Type 2 + Type 3). Current flag + history + previous value column.

### Q13: What is a bridge table in dimensional modeling?
**A:** A table that resolves many-to-many relationships between a fact table and a dimension, or between dimensions. Example: A patient can have multiple diagnoses. Instead of putting multiple diagnosis_ids in the fact table (violates grain), use a bridge table: Fact → Bridge (patient_diagnosis_group) → Diagnosis dimension. Also used for: multi-valued dimensions and hierarchical roll-ups.

### Q14: What is a data warehouse bus architecture?
**A:** Kimball's concept of building the warehouse as a collection of interconnected data marts sharing conformed dimensions. Like a bus with standard stops (dimensions) that different routes (fact tables) can use. Benefits: incremental development, each mart delivers value independently, and integration happens through shared dimensions rather than a single monolithic design.

### Q15: What is the difference between additive, semi-additive, and non-additive facts?
**A:**
- **Additive:** Can be summed across all dimensions (sales_quantity, revenue). Most common and simplest.
- **Semi-additive:** Can be summed across some dimensions but not others (account_balance — can sum across customers but not across time; you want the latest balance, not the sum of daily balances).
- **Non-additive:** Cannot be summed across any dimension (ratios, percentages, distinct counts). Must be calculated from additive components at query time.

### Q16: What is a surrogate key and why is it essential in data warehouses?
**A:** A meaningless, auto-generated integer (or UUID) used as the primary key in dimension tables. Essential because:
- Source system keys can change, be reused, or be composite and complex
- Enables SCD Type 2 (same natural key, multiple surrogate keys over time)
- Improves join performance (integer keys are smaller and faster than string keys)
- Insulates the warehouse from source system changes

### Q17: What is data vault modeling and when is it used?
**A:** A modeling approach by Dan Linstedt designed for enterprise data warehouses. Components:
- **Hubs:** Core business entities (customer, product) with business keys
- **Links:** Relationships between hubs (customer ordered product)
- **Satellites:** Descriptive attributes and history for hubs and links
**Use when:** Multiple source systems with frequent changes, need for full audit history, agile development (can add sources without redesigning existing model), and regulatory requirements. More complex than dimensional modeling but more flexible.

### Q18: What is the difference between a data warehouse, data lake, and data lakehouse?
**A:**
- **Data Warehouse:** Structured, schema-on-write, SQL-optimized, ACID, expensive storage, fast queries. For: BI, reporting, structured analytics.
- **Data Lake:** Raw any-format data, schema-on-read, cheap storage, requires additional tools for querying. For: ML, data science, storing everything.
- **Data Lakehouse:** Combines both — cheap storage with warehouse features (ACID, schema enforcement, fast SQL). For: unified analytics and ML. Examples: Delta Lake, Iceberg, Hudi.

### Q19: What is ETL vs. ELT in the context of data warehousing?
**A:**
- **ETL:** Transform data in a separate staging server before loading to warehouse. Traditional, more control over data quality, but requires dedicated infrastructure.
- **ELT:** Load raw data into warehouse first, then transform using warehouse compute. Modern cloud-native approach. Faster setup, leverages warehouse power, but requires a powerful warehouse.
**Modern warehouses (Snowflake, BigQuery) favor ELT because their compute is elastic and cost-effective.**

### Q20: What is a date/time dimension and why is it pre-built?
**A:** A dimension table with one row per calendar date (and often time), pre-populated with attributes: day_of_week, month_name, quarter, fiscal_year, is_holiday, is_weekend, etc. Pre-built because:
- Calculating these at query time is expensive
- Enables consistent time-based analysis across all fact tables
- Supports fiscal calendars, holidays, and business-specific date logic
- Typically built once for 10-20 years of dates

### Q21: What is aggregate navigation and why is it important?
**A:** A technique where the BI tool or query engine automatically routes queries to pre-aggregated tables instead of the detailed fact table. Example: a user queries monthly sales, but the engine uses a monthly aggregate table instead of summing 1M daily rows. Important for: query performance, reducing warehouse compute costs, and enabling interactive dashboards on large datasets.

### Q22: What is a junk dimension?
**A:** A dimension that combines multiple low-cardinality flags and indicators into a single dimension to reduce the number of foreign keys in the fact table. Example: Instead of 5 separate boolean columns in the fact table (is_promotional, is_returned, is_online, is_gift, is_expedited), create a junk dimension with all combinations (32 rows). Cleans up the fact table and improves query performance.

### Q23: What is a mini-dimension and when do you use one?
**A:** A small dimension separated from a large dimension to improve performance. Example: A Customer dimension with 100 columns and 10M rows. If only 5 columns change frequently (segment, status, credit_score), create a "Customer Profile" mini-dimension with those 5 columns. The fact table joins to both. Reduces SCD Type 2 overhead on the main dimension.

### Q24: What is the role of a data warehouse in a modern data stack?
**A:** The central analytics layer: raw data from sources → ELT pipeline → data warehouse (modeled and transformed) → BI tools, ML platforms, and reverse ETL to operational systems. Modern stack: Fivetran/Airbyte (ingestion) → dbt (transformation) → Snowflake/BigQuery (warehouse) → Looker/Tableau (BI). The warehouse is the "single source of truth" for analytics.

### Q25: What is a degenerate dimension?
**A:** A dimension attribute that is stored directly in the fact table because it has no associated descriptive attributes and doesn't warrant a separate dimension table. Example: order_number, invoice_number, transaction_id. These are identifiers that users filter/group by but don't describe an entity. Saves a join and keeps the model simple.

### Q26: What is the difference between a star schema and a galaxy schema?
**A:**
- **Star Schema:** One fact table with surrounding dimensions. Simple, focused.
- **Galaxy Schema (Fact Constellation):** Multiple fact tables sharing dimensions. Example: Sales fact and Inventory fact both use Product, Date, and Store dimensions. More complex but reflects real business with multiple processes.

### Q27: What is a factless fact table and when do you use one?
**A:** A fact table with no numeric measures — only foreign keys to dimensions. Captures events or relationships. Examples:
- Student attendance (student_id, date_id, course_id, status='present')
- Employee coverage (employee_id, date_id, department_id — who worked where when)
- Promotion events (product_id, date_id, promotion_id — what was promoted when)
Useful for: counting occurrences, tracking coverage, and analyzing relationships.

### Q28: What is a data warehouse's "time variance"?
**A:** Every data warehouse data structure contains an element of time (explicitly or implicitly). Data represents a series of snapshots captured at regular intervals (daily, weekly, monthly). This time variance enables trend analysis, period-over-period comparisons, and historical reporting. The time dimension is usually the first dimension designed.

### Q29: What is a periodic snapshot fact table?
**A:** A fact table that captures the state of entities at regular intervals, regardless of whether events occurred. Example: Monthly account balance — one row per account per month, showing the balance at month-end. Unlike transaction facts (only when events happen), snapshots always have a row for every period. Good for: inventory levels, account balances, and headcount tracking.

### Q30: What is an accumulating snapshot fact table?
**A:** A fact table with one row per business process instance that gets updated as the process progresses through stages. Example: Order process — row created when order is placed, then updated when shipped, delivered, and paid. Columns: order_date, ship_date, delivery_date, payment_date, and days_between_ship_and_delivery. Good for: pipeline analysis, cycle time measurement, and workflow tracking.

## Scenario-Based Questions

### Q31: Design a star schema for a retail company's sales analysis.
**A:**
```sql
-- Fact table
fact_sales (
    sale_id BIGINT,           -- degenerate dimension
    date_key INT FK,          -- dim_date
    product_key INT FK,       -- dim_product
    customer_key INT FK,      -- dim_customer
    store_key INT FK,         -- dim_store
    promotion_key INT FK,     -- dim_promotion (optional, junk dimension candidate)
    quantity_sold INT,        -- additive
    revenue DECIMAL(12,2),    -- additive
    cost DECIMAL(12,2),       -- additive
    discount_amount DECIMAL(12,2) -- additive
);

-- Dimensions
dim_date (date_key PK, full_date, day_of_week, month_name, quarter, year, fiscal_year, is_holiday)
dim_product (product_key PK, product_id, product_name, category, subcategory, brand, unit_cost)
dim_customer (customer_key PK, customer_id, customer_name, segment, city, state, country, registration_date)
dim_store (store_key PK, store_id, store_name, region, city, store_type, square_footage)
dim_promotion (promotion_key PK, promotion_type, discount_percent, start_date, end_date)

-- Grain: One row per product per transaction per day
-- All measures are additive
```

### Q32: A customer dimension needs to track address changes (SCD Type 2). How do you implement it?
**A:**
```sql
dim_customer (
    customer_key INT PK,        -- surrogate key
    customer_id VARCHAR(50),    -- natural key (source system ID)
    customer_name VARCHAR(100),
    address VARCHAR(200),
    city VARCHAR(50),
    state VARCHAR(50),
    effective_date DATE,        -- SCD Type 2
    expiration_date DATE,       -- SCD Type 2
    is_current BOOLEAN          -- SCD Type 2
);

-- When customer moves:
-- 1. UPDATE existing row: SET expiration_date = yesterday, is_current = FALSE
-- 2. INSERT new row: new address, effective_date = today, expiration_date = '9999-12-31', is_current = TRUE
-- Query current customers: WHERE is_current = TRUE
-- Query history: All rows for customer_id
```

### Q33: A business wants to analyze both "sales by day" and "sales by month" quickly. How do you design for this?
**A:**
1. **Base fact:** fact_sales at daily grain (most granular)
2. **Aggregate table:** fact_sales_monthly (month_key, product_key, customer_key, store_key, total_quantity, total_revenue, total_cost)
3. **Aggregate navigation:** BI tool or materialized view routes monthly queries to the aggregate table
4. **ETL:** Aggregate table populated from daily fact during nightly load
5. **Alternative:** Use a columnar warehouse (Snowflake/BigQuery) with proper partitioning — monthly queries on daily grain are fast enough without explicit aggregates

### Q34: How do you handle a many-to-many relationship between orders and promotions (one order can have multiple promotions, one promotion can apply to multiple orders)?
**A:**
```sql
-- Bridge table approach
fact_orders (order_key PK, date_key, customer_key, product_key, ...)
bridge_order_promotion (order_key FK, promotion_key FK, promotion_count INT, weight_factor DECIMAL(3,2))
dim_promotion (promotion_key PK, promotion_name, type, discount_percent)

-- The bridge table sits between fact and dimension
-- weight_factor allows allocating order metrics across promotions (e.g., 0.5 each if 2 promotions)
-- Alternative: Put promotion_key in the fact table if usually only one promotion per order
-- If multiple promotions are common, bridge table is the correct dimensional modeling approach
```

### Q35: A company has both online and in-store sales. Should they be in one fact table or two?
**A:**
**One fact table (consolidated) if:**
- Same grain (one row per transaction line item)
- Same dimensions (customer, product, date, store/website)
- Same measures (quantity, revenue, cost)
- Business wants unified analysis (total sales across channels)

**Two fact tables (separate) if:**
- Different grains (online = per session, in-store = per transaction)
- Different dimensions (online has session_id, device; in-store has cashier_id, register)
- Different measures (online has shipping_cost, in-store has bag_fee)
- Different business owners who rarely need combined analysis

**Hybrid:** Two fact tables + a consolidated view/aggregate for cross-channel analysis.

### Q36: Design a data warehouse for a subscription SaaS business.
**A:**
```sql
-- Fact tables
fact_subscriptions (subscription_key, customer_key, plan_key, date_key, mrr, arr, subscriber_count)
fact_usage (usage_key, customer_key, feature_key, date_key, usage_count, usage_minutes)
fact_churn (churn_key, customer_key, date_key, churn_reason_key, days_since_signup, ltv_at_churn)

-- Dimensions
dim_customer (customer_key, customer_id, company_name, industry, segment, signup_date, country)
dim_plan (plan_key, plan_id, plan_name, tier, price, billing_cycle)
dim_date (date_key, ...)
dim_feature (feature_key, feature_name, feature_category, product_area)
dim_churn_reason (churn_reason_key, reason_category, reason_detail)

-- Key metrics: MRR, ARR, churn rate, expansion revenue, NRR (Net Revenue Retention)
-- SCD Type 2 on dim_customer for plan changes and segment changes
```

### Q37: How do you design a warehouse that supports both real-time and historical analytics?
**A:**
1. **Streaming layer:** Kafka/Kinesis → Real-time fact table (last 24 hours) → Real-time dashboard
2. **Batch layer:** Nightly ETL → Historical fact table (full history) → Historical reporting
3. **Lambda architecture:** Query layer combines real-time and batch results
4. **Kappa architecture:** Single streaming pipeline with reprocessing capability
5. **Modern approach:** Use a lakehouse (Delta Lake) with streaming inserts and batch backfill in one table. BigQuery streaming inserts + scheduled queries for aggregates.

### Q38: A business user wants to see "year-over-year growth by product category." What warehouse structures enable this?
**A:**
1. **Date dimension:** Pre-built with year, month, quarter, fiscal periods, and year-ago date mappings
2. **Fact table:** Daily grain with date_key and product_key
3. **Product dimension:** Category hierarchy (product → subcategory → category)
4. **Query:** Join fact to date (filter current year), join to product (group by category), self-join or window function for prior year comparison
5. **Pre-aggregate:** Monthly fact table with category-level aggregates for faster performance
6. **YoY calculation:** (Current Year - Prior Year) / Prior Year * 100

### Q39: How do you handle currency conversion in a multi-currency data warehouse?
**A:**
```sql
-- Fact table stores original currency
fact_sales (..., local_currency_code, local_amount, ...)

-- Exchange rate dimension (daily grain)
dim_exchange_rate (date_key, from_currency, to_currency, rate, rate_type)

-- Conversion at query time:
-- local_amount * rate = amount_in_usd (or reporting currency)

-- Alternative: Store both local and converted amounts in fact table
-- Pros: Faster queries, consistent reporting
-- Cons: Requires reprocessing if historical rates change
-- Best practice: Store local amount + rate reference, convert at query time for accuracy
```

### Q40: A company acquires another company with a different data model. How do you integrate them into one warehouse?
**A:**
1. **Inventory:** Document both source systems' schemas, data dictionaries, and business logic
2. **Mapping:** Create a unified conceptual model (what entities exist, what do they mean?)
3. **Conformed dimensions:** Build shared Customer, Product, Date dimensions that both companies' data maps into
4. **Staging:** Load both sources into separate staging areas
5. **Transformation:** Map each source to the unified model using ETL logic
6. **Data quality:** Profile both sources, resolve conflicts (e.g., different customer ID systems)
7. **Historical:** Decide whether to backfill the acquired company's history
8. **Governance:** Establish ownership and update processes for the unified model

### Q41: Design a data warehouse for a healthcare provider (patients, visits, diagnoses, treatments).
**A:**
```sql
-- Fact tables
fact_visits (visit_key, patient_key, provider_key, date_key, facility_key, 
             visit_type_key, length_of_stay, cost, is_readmission_30day)
             -- Grain: One row per visit

fact_procedures (procedure_key, visit_key, patient_key, procedure_code_key, 
                 date_key, provider_key, quantity, cost, outcome_key)
                 -- Grain: One row per procedure per visit

-- Dimensions
dim_patient (patient_key, patient_id, birth_date, gender, race, ethnicity, 
             insurance_type, first_visit_date, is_active)
             -- SCD Type 2 for address, insurance changes

dim_provider (provider_key, provider_id, name, specialty, department, 
              credentials, employment_status)

dim_date (date_key, ...)
dim_facility (facility_key, facility_name, type, region, bed_count)
dim_diagnosis (diagnosis_key, icd10_code, description, category, severity)
dim_procedure_code (procedure_key, cpt_code, description, category, cost_category)

-- Bridge table for many-to-many: patient can have multiple diagnoses per visit
bridge_visit_diagnosis (visit_key, diagnosis_key, diagnosis_order, is_primary)

-- Compliance: HIPAA requires audit trails, access controls, and data minimization
-- De-identification: Separate PHI into restricted-access tables or views
```
