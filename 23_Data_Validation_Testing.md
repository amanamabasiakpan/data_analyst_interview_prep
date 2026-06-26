# 23. Data Validation & Testing

## Theoretical Questions

### Q1: What is data validation and why is it critical?
**A:** Data validation is the process of ensuring data meets quality, format, and business rule requirements before it's used for analysis or decision-making. Critical because: bad data leads to bad decisions ("garbage in, garbage out"), data issues are cheaper to catch early than after a report goes to the CEO, and data quality directly impacts trust in analytics and ML models.

### Q2: What are the dimensions of data quality?
**A:**
- **Accuracy:** Does the data reflect reality? (e.g., customer age = 150 is inaccurate)
- **Completeness:** Are all required fields populated? (e.g., 30% of emails are null)
- **Consistency:** Is data uniform across systems? (e.g., "USA" vs "United States" vs "US")
- **Timeliness:** Is data up-to-date? (e.g., yesterday's sales data available today)
- **Validity:** Does data conform to defined formats/rules? (e.g., email format, date ranges)
- **Uniqueness:** Are there duplicate records? (e.g., same customer in the database twice)

### Q3: What is the difference between data validation and data verification?
**A:**
- **Validation:** Checks if data meets specified requirements (format, range, business rules). "Is this data acceptable?"
- **Verification:** Checks if data accurately represents the real-world entity it describes. "Is this data true?"
Example: A birth date of "2025-01-01" passes validation (valid date format) but fails verification (future date for a person born).

### Q4: What are schema tests and what do they validate?
**A:** Tests that verify the structure of data. Common schema tests:
- **Not null:** Column must not contain nulls
- **Unique:** All values in a column must be distinct
- **Referential integrity:** Foreign keys must reference valid primary keys
- **Data type:** Values must match expected type (integer, string, date)
- **Accepted values:** Column values must be from a defined set (e.g., status ∈ {active, inactive, pending})
- **Relationships:** Cardinality between tables (1:1, 1:N)

### Q5: What is Great Expectations and how does it work?
**A:** Great Expectations (GX) is an open-source Python framework for data validation. You define "expectations" (declarative tests) about your data, and GX validates them. Example: `expect_column_values_to_be_between(column="age", min_value=0, max_value=120)`. Features: auto-generates documentation, creates data quality reports, integrates with pipelines, and stores validation history. Expectations are version-controlled and reusable.

### Q6: What is dbt (data build tool) testing and what types does it support?
**A:** dbt is a data transformation tool with built-in testing capabilities:
- **Built-in tests:** `not_null`, `unique`, `accepted_values`, `relationships`
- **Custom tests:** SQL queries that return failing rows
- **Singular tests:** One-off SQL tests in the `tests/` directory
- **Generic tests:** Reusable test definitions applied to multiple models
- **Sources tests:** Validate raw data before transformation
Tests run as part of `dbt test` and can be integrated into CI/CD pipelines.

### Q7: What is the difference between unit tests and integration tests for data pipelines?
**A:**
- **Unit Tests:** Test individual components in isolation (e.g., a single SQL transformation, a Python function). Fast, deterministic, easy to debug.
- **Integration Tests:** Test how components work together (e.g., full ETL pipeline from extraction to load). Slower, require test data, catch interface issues.
**Data teams need both:** Unit tests for transformation logic, integration tests for pipeline end-to-end validation.

### Q8: What is a data diff and when do you use it?
**A:** Comparing two datasets to identify differences. Use cases:
- **Migration validation:** "Does the new warehouse have the same data as the old one?"
- **Pipeline testing:** "Did my transformation change the expected rows?"
- **Regression testing:** "Did today's data load produce the same results as yesterday?"
Tools: `data-diff` (open-source), custom SQL with EXCEPT/UNION, or Python with pandas.

### Q9: What is anomaly detection in data quality?
**A:** Automated identification of unusual patterns that may indicate data issues. Methods:
- **Statistical:** Z-score, IQR for outliers
- **Time-series:** Detect sudden drops/spikes in row counts or metric values
- **Distribution:** Compare current data distribution to historical baseline (KS test)
- **ML-based:** Isolation Forest, autoencoders for complex patterns
Use when: rules-based tests can't catch all issues, and you need to monitor large-scale data continuously.

### Q10: What is a data contract and how does it enforce quality?
**A:** A formal agreement between data producers and consumers specifying: schema, data types, nullability, value ranges, update frequency, and SLAs. Enforced by:
- **Schema registries** (Confluent, AWS Glue) that reject non-compliant data
- **CI/CD gates** that block deployment if contract is violated
- **Automated testing** that validates incoming data against the contract
- **Monitoring** that alerts when contract violations occur

### Q11: What is Soda Core and how does it compare to Great Expectations?
**A:** Soda Core is an open-source data quality testing framework. Compared to GX:
- **Simpler syntax:** YAML-based checks vs. Python expectations
- **Scan-based:** Define checks in YAML, run `soda scan` to validate
- **Self-service:** Business users can write checks without Python knowledge
- **Less mature ecosystem** than GX but growing rapidly
**Choose Soda for:** simpler setups, business user involvement. **Choose GX for:** complex expectations, deep Python integration, and comprehensive documentation.

### Q12: What is data profiling and how does it support validation?
**A:** Data profiling analyzes datasets to understand their structure, content, and quality. Outputs: column statistics (min, max, mean, null %), data types, value distributions, pattern frequencies, and relationship cardinalities. Supports validation by: identifying columns that need tests, establishing baselines for anomaly detection, and documenting data for stakeholders. Tools: pandas-profiling (ydata-profiling), Apache Griffin, Talend Data Quality.

### Q13: What is a data quality score and how do you calculate it?
**A:** A composite metric representing overall data quality. Calculation approaches:
- **Simple:** Percentage of tests passing (80% of 20 tests passed = 80% score)
- **Weighted:** Critical tests weighted higher (revenue accuracy = 30%, completeness = 20%, etc.)
- **Dimensional:** Average of dimension scores (accuracy, completeness, timeliness, etc.)
- **SLA-based:** Percentage of time data meets SLA (e.g., 99.5% freshness)
Display on a dashboard for stakeholders and track trends over time.

### Q14: What is regression testing for data pipelines?
**A:** Verifying that changes to a pipeline don't break existing functionality. Methods:
- **Snapshot testing:** Compare output of new code vs. known-good output
- **Row count validation:** Ensure row counts match expectations after changes
- **Sum validation:** Key metrics (revenue, counts) should match before/after
- **Schema validation:** Ensure output schema hasn't changed unexpectedly
- **Integration tests:** Run full pipeline with test data
Run automatically in CI/CD before deploying to production.

### Q15: What is a data quality SLA and what should it include?
**A:** Service Level Agreement defining data quality commitments. Should include:
- **Freshness:** Data available within X hours of source update
- **Completeness:** Y% of required fields populated
- **Accuracy:** Z% match with source of truth (reconciliation)
- **Schema stability:** No breaking changes without 30-day notice
- **Uptime:** Pipeline available X% of the time
- **Response time:** Issues resolved within Y hours
- **Escalation:** Who to contact when SLAs are breached

### Q16: What is test-driven development (TDD) for data pipelines?
**A:** Writing tests before writing the transformation code. Process:
1. Write a test that defines expected output for given input
2. Run the test — it should fail (no code yet)
3. Write the minimum code to pass the test
4. Refactor and improve
For data: define expected row counts, column values, and business logic outputs before building the pipeline. Ensures the pipeline does what you intended.

### Q17: What is a data quality monitoring dashboard?
**A:** A centralized view showing the health of all data assets. Components:
- **Pass/fail status** of all data tests (green/red indicators)
- **Trend charts** of data quality scores over time
- **Alert history** — when did issues occur and how were they resolved?
- **SLA compliance** — are we meeting freshness and accuracy commitments?
- **Issue backlog** — open data quality issues with owners and priorities
Audience: data team, data stewards, and business stakeholders who depend on data.

### Q18: What is the difference between proactive and reactive data quality?
**A:**
- **Proactive:** Preventing bad data from entering the system. Schema validation, input constraints, data contracts, and source system validation.
- **Reactive:** Detecting and fixing bad data after it's in the system. Data testing, anomaly detection, and data cleansing jobs.
**Best practice:** Invest 70% in proactive (cheaper) and 30% in reactive (necessary safety net).

### Q19: What is data lineage and how does it help with testing?
**A:** Tracking the flow of data from source to destination. Helps testing by:
- **Impact analysis:** "If I change this source column, which tests will break?"
- **Root cause analysis:** "This dashboard is wrong — trace back through 5 transformations to find the bug"
- **Test coverage:** "Which tables have no tests? Which have the most?"
- **Regression scope:** "Only these 3 models are affected by my change — I only need to test those"

### Q20: What is a data quality issue triage process?
**A:** A systematic approach to handling data quality failures:
1. **Detect:** Alert fires (test failure, anomaly detected)
2. **Assess:** Severity (P1 = blocking business decisions, P2 = incorrect but not critical, P3 = minor)
3. **Contain:** Stop downstream consumers if data is severely wrong
4. **Investigate:** Root cause analysis — source issue, transformation bug, or schema change?
5. **Fix:** Correct the data or the pipeline
6. **Validate:** Re-run tests to confirm fix
7. **Communicate:** Notify stakeholders of issue and resolution
8. **Prevent:** Add tests or monitoring to catch similar issues

### Q21: What is Monte Carlo Data and what problem does it solve?
**A:** Monte Carlo is a data observability platform (SaaS) that automatically monitors data quality across your stack. Problems it solves:
- **Silent failures:** Pipelines that run but produce wrong data
- **Schema drift:** Source schema changes that break downstream models
- **Anomaly detection:** Unusual patterns in data volume, freshness, or distribution
- **Lineage:** Visual map of data dependencies
- **Incident management:** Alerting, triage, and resolution tracking
**For teams:** that lack resources to build custom monitoring but need enterprise-grade observability.

### Q22: What is Apache Griffin and when do you use it?
**A:** Apache Griffin is an open-source data quality solution for big data. Features: batch and streaming data quality measurement, define quality rules in DSL, and generate quality reports. Use when: you're in a big data ecosystem (Hadoop, Spark), need to measure data quality at scale, and want an open-source alternative to commercial tools.

### Q23: What is a data freshness test and how do you implement it?
**A:** A test that verifies data is up-to-date. Implementation:
```sql
-- dbt freshness test (built-in)
sources:
  - name: raw_sales
    freshness:
      warn_after: {count: 12, period: hour}
      error_after: {count: 24, period: hour}
    loaded_at_field: updated_at

-- Custom SQL test
SELECT COUNT(*) AS stale_rows
FROM orders
WHERE created_at < CURRENT_DATE - INTERVAL '1 day';
-- If > 0, data is stale
```

### Q24: What is referential integrity and how do you test it?
**A:** Ensuring relationships between tables are valid (foreign keys reference existing primary keys). Testing:
```sql
-- dbt relationship test
models:
  - name: orders
    columns:
      - name: customer_id
        tests:
          - relationships:
              to: ref('customers')
              field: customer_id

-- Manual SQL check
SELECT COUNT(*) AS orphaned_orders
FROM orders o
LEFT JOIN customers c ON o.customer_id = c.customer_id
WHERE c.customer_id IS NULL AND o.customer_id IS NOT NULL;
```

### Q25: What is a data quality gate in CI/CD?
**A:** An automated checkpoint that prevents code deployment if data quality checks fail. Implementation:
1. Developer opens PR with dbt model changes
2. CI runs `dbt test` on a staging environment
3. If tests pass → allow merge
4. If tests fail → block merge, notify developer
5. Post-merge: run tests in production to catch environment-specific issues
Prevents bad data transformations from reaching production.

### Q26: What is data reconciliation and how is it different from data validation?
**A:**
- **Data Validation:** Checks if data meets defined rules (internal quality)
- **Data Reconciliation:** Compares data between two systems to ensure consistency (cross-system quality)
Example: Validation checks if revenue > 0. Reconciliation checks if warehouse revenue matches source system revenue.

### Q27: What is a data quality incident and how do you document it?
**A:** A documented event where data quality failed. Documentation should include:
- **Detection:** When and how was it discovered?
- **Impact:** Which reports, dashboards, or decisions were affected?
- **Root cause:** Technical and process causes
- **Resolution:** How was it fixed?
- **Timeline:** Detection → Investigation → Fix → Validation
- **Prevention:** What tests or processes were added to prevent recurrence?
- **Lessons learned:** What would we do differently?
Store in a shared knowledge base (Confluence, Notion, or incident management tool).

### Q28: What is the "shift-left" approach to data quality?
**A:** Moving data quality checks earlier in the data lifecycle (closer to the source). Instead of catching issues in the warehouse, validate at:
- **Source systems:** Input validation, constraints, and triggers
- **Ingestion:** Schema validation and basic checks on data entry
- **Staging:** Comprehensive testing before transformation
- **Production:** Monitoring and anomaly detection
**Benefit:** Cheaper to fix issues earlier; source fixes prevent propagation to downstream systems.

### Q29: What is a data quality framework and what components should it have?
**A:** A comprehensive system for managing data quality. Components:
1. **Standards:** Definitions of data quality dimensions and acceptable thresholds
2. **Testing:** Automated tests at multiple pipeline stages
3. **Monitoring:** Continuous observation of data health metrics
4. **Alerting:** Notification system for quality failures
5. **Triage:** Process for assessing and prioritizing issues
6. **Remediation:** Procedures for fixing data and root causes
7. **Governance:** Ownership, accountability, and escalation paths
8. **Reporting:** Dashboards and metrics for stakeholders

### Q30: What is data observability and how does it differ from traditional data quality?
**A:** Data observability is the ability to understand the health of data systems by observing their outputs, without relying on predefined rules. Compared to traditional quality:
- **Traditional:** Rule-based ("revenue must be > 0"). Catches known issues.
- **Observability:** Pattern-based ("revenue dropped 50% from yesterday"). Catches unknown issues.
**Five pillars:** Freshness, Volume, Schema, Distribution, Lineage. Tools: Monte Carlo, Bigeye, Metaplane, Anomalo.

## Scenario-Based Questions

### Q31: A critical revenue report shows $0 for yesterday. Your data tests didn't catch it. What do you do?
**A:**
1. **Immediate:** Verify source data — is the source system down or did it send empty data?
2. **Contain:** Add a warning banner to the dashboard: "Data for [date] is incomplete — investigation in progress"
3. **Investigate:** Check pipeline logs, row counts, and data freshness timestamps
4. **Fix:** If source issue, coordinate with source team. If pipeline bug, fix and backfill
5. **Add tests:**
   - Freshness test: `orders` table must have data within 4 hours
   - Volume anomaly test: yesterday's row count within 20% of 7-day average
   - Null check: `SUM(revenue)` must not be NULL
6. **Communicate:** Notify stakeholders of issue, root cause, and prevention measures
7. **Document:** Add to incident log for pattern analysis

### Q32: You're building a new ETL pipeline. What tests should you write before deploying to production?
**A:**
1. **Schema tests:** All required columns exist, correct types, not null where appropriate
2. **Referential integrity:** Foreign keys reference valid records
3. **Business rule tests:** Revenue = quantity * price, dates in valid range, status values valid
4. **Row count validation:** Output row count within expected range (not 0, not 10x normal)
5. **Reconciliation:** Sum of key metrics matches source system
6. **Duplicate check:** No unexpected duplicates in primary key
7. **Freshness:** Data loaded within SLA timeframe
8. **Integration test:** Run full pipeline end-to-end with realistic test data

### Q33: A stakeholder says, "I don't trust this data." How do you rebuild trust?
**A:**
1. **Listen:** What specifically don't they trust? A specific metric? A time period? The whole dataset?
2. **Validate:** Run your data tests in front of them. Show pass/fail results.
3. **Trace:** Walk them through the lineage — source → extraction → transformation → report
4. **Reconcile:** Compare your numbers to a source they trust (e.g., finance system)
5. **Acknowledge:** If there's a known limitation, be transparent about it
6. **Improve:** Add tests for the specific concern they raised
7. **Monitor:** Set up alerts for the metric they care about
8. **Follow-up:** Check in after 2 weeks — is trust improving?

### Q34: Your dbt tests are taking 30 minutes to run, slowing down development. How do you optimize?
**A:**
1. **Parallelize:** Run tests in parallel using `dbt test --threads 8`
2. **Selective testing:** Only test changed models: `dbt test --select state:modified+`
3. **Separate environments:** Run lightweight tests in CI, comprehensive tests nightly
4. **Optimize SQL:** Add indexes on tested columns, partition large tables
5. **Reduce test scope:** Not every column needs a test — focus on critical columns
6. **Materialized tests:** For expensive tests, materialize results and test the materialized table
7. **CI strategy:** Run tests on a subset of data (last 7 days) in CI, full data in production

### Q35: A data source sends a schema change (new column, renamed column) that breaks your pipeline. How do you prevent this?
**A:**
1. **Schema tests:** Add `dbt source` tests that verify expected columns exist
2. **Schema registry:** Use Confluent Schema Registry or AWS Glue to enforce schemas
3. **Monitoring:** dbt source freshness + schema change detection
4. **Contract:** Establish a data contract with the source team requiring 30-day notice for breaking changes
5. **CI gate:** Block PR merge if source schema tests fail
6. **Resilience:** Design pipelines to handle new columns gracefully (ignore unknown columns)
7. **Alerting:** Notify immediately when source schema changes

### Q36: You need to validate that a machine learning model's training data hasn't degraded. What tests do you run?
**A:**
1. **Schema validation:** Training data has expected columns and types
2. **Distribution tests:** Feature distributions match baseline (KS test, PSI — Population Stability Index)
3. **Missing value checks:** No significant increase in nulls
4. **Target variable stability:** Class distribution hasn't shifted dramatically
5. **Correlation checks:** Feature correlations haven't changed unexpectedly
6. **Outlier detection:** No extreme values that could skew training
7. **Data drift:** Compare current data to data at last model training
Tools: Evidently AI, Great Expectations, custom Python tests.

### Q37: A business rule says "customer lifetime value must be positive." How do you test this at multiple pipeline stages?
**A:**
```sql
-- Stage 1: Source data validation
tests:
  - name: source_orders_positive_amount
    sql: |
      SELECT * FROM source_orders WHERE order_amount <= 0

-- Stage 2: After transformation
models:
  - name: fct_customer_ltv
    columns:
      - name: lifetime_value
        tests:
          - dbt_utils.accepted_range:
              min_value: 0

-- Stage 3: Final report validation (Great Expectations)
validator.expect_column_values_to_be_between(
    column="lifetime_value",
    min_value=0
)

-- Stage 4: Anomaly detection
-- Alert if % of customers with negative LTV exceeds 0.1%
```

### Q38: Your team has 50 tables but only 10 have data tests. How do you prioritize which to test next?
**A:**
1. **Business criticality:** Which tables power the most important dashboards and decisions?
2. **Downstream impact:** Which tables feed the most downstream models? (Use lineage)
3. **Historical issues:** Which tables have had quality problems before?
4. **Regulatory requirements:** Which tables contain data subject to compliance?
5. **Data freshness:** Which tables have the most time-sensitive data?
6. **Stakeholder complaints:** Which tables do users question most often?
7. **Quick wins:** Which tables are simplest to test? (Build momentum)

### Q39: You need to set up data quality monitoring for a startup with limited resources. What's your minimum viable approach?
**A:**
1. **dbt tests (free):** Add not_null, unique, accepted_values, and relationships tests to critical models
2. **Freshness checks:** Built into dbt sources — 5 minutes to configure
3. **Row count monitoring:** Simple SQL query run daily: `SELECT COUNT(*) FROM table WHERE date = yesterday`
4. **GitHub Actions (free):** Run dbt tests on every PR
5. **Slack alerts:** Free tier of Slack + dbt Cloud or custom webhook
6. **Dashboard:** Google Sheets or Notion tracking test pass rates weekly
7. **Expand later:** Add Great Expectations or Soda when you have more resources

### Q40: A data test passes but the business user says the data is still wrong. What's happening?
**A:**
1. **Test scope:** Your test checks schema/not_null but not business logic. The data exists but is logically wrong.
2. **Test logic bug:** The test itself has a flaw (e.g., checking `COUNT(*) > 0` when the issue is incorrect values)
3. **Edge case:** The test doesn't cover the specific scenario the user encountered
4. **Stale test:** The test was written for old business rules that have changed
5. **Source issue:** The data passes your test but the source data itself is wrong
6. **Fix:** Add a business rule test that captures the user's concern, and investigate the root cause

### Q41: You're tasked with building a data quality program from scratch. What's your 90-day plan?
**A:**
**Days 1-30: Assessment**
- Inventory all data assets and their business criticality
- Interview stakeholders about data pain points
- Profile top 10 critical datasets
- Document current data issues and their frequency

**Days 31-60: Foundation**
- Choose a testing framework (dbt tests as baseline)
- Add tests to top 5 most critical tables
- Set up automated testing in CI/CD
- Create a data quality dashboard
- Define data quality SLAs with stakeholders

**Days 61-90: Scale & Monitor**
- Expand tests to top 20 tables
- Implement source data freshness monitoring
- Create incident response process
- Train team on writing and maintaining tests
- First retrospective: what's working, what's not?
- Plan next quarter: anomaly detection, data contracts, or observability platform
