# 16. ETL & ELT Concepts

## Theoretical Questions

### Q1: What is ETL and how does it work?
**A:** ETL = Extract, Transform, Load. A data integration process where data is:
1. **Extracted** from source systems (databases, APIs, files, SaaS tools)
2. **Transformed** — cleaned, validated, aggregated, joined, and formatted in a staging area
3. **Loaded** into the target system (data warehouse, data mart)
Traditional ETL transforms data *before* loading, which requires a separate processing server.

### Q2: What is ELT and how does it differ from ETL?
**A:** ELT = Extract, Load, Transform. Data is extracted, loaded raw into the target system (usually a cloud data warehouse), and transformed using the warehouse's compute power (SQL). Key difference: transformation happens *after* loading, leveraging modern warehouse scalability. ELT is faster to set up, more flexible, and better for large volumes, but requires a powerful target system.

### Q3: When would you choose ETL over ELT?
**A:** Choose ETL when:
- Target system has limited compute (traditional RDBMS)
- Strict data privacy requirements (PII must be masked before loading)
- Complex transformations that are easier in Python/Spark than SQL
- Need for data quality gates before loading
- Legacy systems that can't handle raw data volumes

### Q4: What are the main stages of a data pipeline?
**A:**
1. **Ingestion:** Collecting data from sources (batch or streaming)
2. **Staging:** Raw storage before transformation
3. **Transformation:** Cleaning, joining, aggregating, business logic
4. **Validation:** Data quality checks, schema validation
5. **Loading:** Writing to target (warehouse, lake, mart)
6. **Orchestration:** Scheduling, dependencies, error handling
7. **Monitoring:** Logging, alerting, performance tracking

### Q5: What is the difference between batch and streaming data processing?
**A:**
- **Batch:** Data collected over a period, processed together at scheduled intervals (hourly, daily). Simpler, more cost-effective, suitable for historical analysis and reporting.
- **Streaming:** Data processed continuously as it arrives, near real-time. More complex, higher infrastructure cost, suitable for fraud detection, live dashboards, IoT, and event-driven systems.

### Q6: What is a data pipeline and what makes it robust?
**A:** A data pipeline is an automated sequence of processes that moves data from source to destination. Robust pipelines have: idempotency (re-running doesn't duplicate data), error handling with retries, data quality checks, schema evolution handling, monitoring/alerting, backfill capability, and clear lineage documentation.

### Q7: What is data ingestion and what are common patterns?
**A:** Data ingestion is the process of importing data from sources. Patterns:
- **Full load:** Copy entire dataset every run (simple, but slow for large data)
- **Incremental load:** Only new/changed records (requires change detection)
- **Change Data Capture (CDC):** Capture inserts, updates, deletes in real-time from database logs
- **API polling:** Regularly query APIs for new data
- **Event streaming:** Push-based via message queues (Kafka, Kinesis)

### Q8: What is Change Data Capture (CDC)?
**A:** CDC captures and delivers changes (inserts, updates, deletes) from source databases in real-time. Methods:
- **Log-based:** Read database transaction logs (WAL, binlog) — low overhead, accurate
- **Trigger-based:** Database triggers write changes to a table — higher overhead, simpler
- **Timestamp-based:** Query for records modified since last run — simplest, misses hard deletes
- **Diff-based:** Compare full snapshots — high overhead, only for small tables

### Q9: What is a data warehouse vs. a data lake vs. a data lakehouse?
**A:**
- **Data Warehouse:** Structured data, schema-on-write, optimized for SQL analytics. Examples: Snowflake, BigQuery, Redshift.
- **Data Lake:** Raw data in any format (structured, semi-structured, unstructured), schema-on-read, cheap storage. Examples: S3, Azure Data Lake, HDFS.
- **Data Lakehouse:** Combines lake storage with warehouse features — ACID transactions, schema enforcement, SQL performance on raw data. Examples: Databricks Delta Lake, Apache Iceberg.

### Q10: What is schema evolution and how do you handle it?
**A:** Schema evolution is when source data structures change over time (new columns, type changes, renamed fields). Handling:
- Use schema registries (Confluent Schema Registry for Avro)
- Design pipelines to handle new columns gracefully (ignore or add with defaults)
- Version your schemas
- Implement breaking change detection in CI/CD
- Use flexible formats (Parquet, JSON) that handle schema changes better than CSV

### Q11: What is data partitioning and why is it important in ETL?
**A:** Partitioning divides data into segments (usually by date, region, or category) for efficient querying and loading. Benefits: query performance (prune irrelevant partitions), parallel processing, easier data management (drop old partitions), and cost optimization (only scan relevant data). Common in: Hive, BigQuery, Snowflake, Delta Lake.

### Q12: What is idempotency in data pipelines and why does it matter?
**A:** Idempotency means running the same job multiple times produces the same result. Critical for: recovery from failures (re-run without duplicates), backfilling historical data, and testing. Implementation: MERGE/UPSERT operations, overwrite partitions instead of append, or use transaction IDs to deduplicate.

### Q13: What are slowly changing dimensions (SCD) and what are the types?
**A:** SCD tracks how dimensional data changes over time in a warehouse.
- **Type 0:** Fixed, never changes (e.g., original hire date)
- **Type 1:** Overwrite old value (no history)
- **Type 2:** Add new row with effective dates (full history, most common)
- **Type 3:** Add new column for previous value (limited history)
- **Type 4:** Separate history table
- **Type 6:** Hybrid (Type 1 + Type 2 + Type 3)

### Q14: What is a data mart and how does it relate to a data warehouse?
**A:** A data mart is a subset of a data warehouse focused on a specific business line (sales, marketing, finance). It's typically smaller, more focused, and optimized for a particular user group. Can be dependent (sourced from warehouse), independent (sourced directly), or hybrid.

### Q15: What is data virtualization?
**A:** Data virtualization creates a unified data layer without physically moving data. Queries are federated across sources in real-time. Pros: no ETL latency, access to latest data, reduced storage. Cons: performance depends on source systems, complex optimization, single point of failure. Tools: Denodo, Starburst, Dremio.

### Q16: What is the medallion architecture (bronze, silver, gold)?
**A:** A data lakehouse layering pattern:
- **Bronze (Raw):** Ingested data as-is, schema-on-read, full history
- **Silver (Cleaned):** Validated, deduplicated, joined, and lightly transformed. Business logic applied.
- **Gold (Curated):** Aggregated, business-ready data for analytics and ML. Optimized for query performance.
Benefits: data quality improves at each layer, easy to reprocess from bronze if silver logic changes, clear separation of concerns.

### Q17: What is data lineage in the context of ETL pipelines?
**A:** Tracking the flow of data from source to destination, including all transformations. In ETL: source table → extraction job → transformation logic → target table → downstream dashboard. Lineage answers: "If this source changes, what breaks?" and "Where did this dashboard metric come from?" Tools: OpenLineage, DataHub, Collibra.

### Q18: What is a dead letter queue in data pipelines?
**A:** A queue that stores messages that couldn't be processed successfully after retries. Used in streaming pipelines to prevent one bad message from blocking the entire pipeline. Messages in the DLQ are analyzed, fixed, and either reprocessed or discarded. Common in: Kafka, AWS SQS, Azure Service Bus.

### Q19: What is data reconciliation and why is it important?
**A:** Reconciliation compares source and target data to ensure ETL accuracy. Methods: row counts, sum of key numeric columns, checksums, and sample record comparison. Should be done after every load and automated. Catches: data loss, transformation errors, and source system changes.

### Q20: What is the difference between a data pipeline and a workflow orchestration?
**A:**
- **Data Pipeline:** The actual movement and transformation of data (the "what").
- **Workflow Orchestration:** The scheduling, dependency management, and execution control (the "when and how"). Orchestrators (Airflow, Prefect, Dagster) manage pipelines: run this job, then these two in parallel, then this one, with retries and alerts.

### Q21: What is backfilling in ETL?
**A:** Re-processing historical data, usually because: the transformation logic changed, a bug was fixed, or a new data source was added. Requires: idempotent jobs, ability to process specific date ranges, sufficient compute resources, and coordination to avoid impacting current loads.

### Q22: What is a data pipeline's SLA and how do you ensure it?
**A:** Service Level Agreement — the commitment for when data must be available (e.g., "sales data ready by 6 AM daily"). Ensured by: monitoring job duration, setting up alerts for delays, optimizing slow queries, scaling resources during peak, having fallback/retry logic, and communicating with stakeholders when SLAs are missed.

### Q23: What is data quality testing in ETL?
**A:** Automated checks embedded in pipelines to catch issues before they reach consumers. Types:
- **Schema tests:** Column exists, correct type, not null where required
- **Custom tests:** Business rules (revenue > 0, dates in valid range)
- **Referential integrity:** Foreign key relationships valid
- **Anomaly detection:** Unusual row counts, value distributions
Tools: dbt tests, Great Expectations, Soda, Deequ.

### Q24: What is the difference between push and pull ingestion?
**A:**
- **Push:** Source system sends data when available (webhooks, event streams, message queues). Lower latency, but requires source cooperation.
- **Pull:** ETL system queries source at intervals (API polling, database queries). More control, but higher latency and load on source systems.

### Q25: What is a staging area in ETL?
**A:** An intermediate storage layer between extraction and transformation. Holds raw, unmodified data from sources. Benefits: decouples extraction from transformation (can reprocess without re-extracting), provides audit trail, enables data recovery, and allows multiple transformations from the same source data. Usually purged after successful load.

### Q26: What is data compaction and why is it needed?
**A:** In streaming or frequent small-batch writes, many small files accumulate (the "small file problem"). Compaction merges small files into larger ones for better query performance. Important in: Delta Lake, Hudi, Hive, and any system with frequent appends. Trade-off: compaction consumes compute, so schedule during off-peak hours.

### Q27: What is a semantic layer in data architecture?
**A:** A business abstraction layer between raw data and end users. Defines business metrics ("active user = logged in within 30 days"), dimensions, and calculations in one place. Benefits: consistent definitions across tools, reduced duplication, and self-service for non-technical users. Examples: dbt metrics, Looker ML, Cube.dev, MetricFlow.

### Q28: What is the difference between a materialized view and a regular view?
**A:**
- **Regular View:** Stored query definition, executed on every query. No storage overhead, but slower for complex queries.
- **Materialized View:** Query result pre-computed and stored. Faster reads, but requires refresh (on schedule or on change) and uses storage. Best for: complex aggregations queried frequently with infrequent source changes.

### Q29: What is data freshness and how do you measure it?
**A:** How current the data is compared to source. Measured as: max(source timestamp) - current time, or the lag between source update and target availability. Critical for: operational dashboards, real-time analytics, and SLA compliance. Monitored via: pipeline logs, metadata timestamps, and freshness checks in dbt/Great Expectations.

### Q30: What is a zero-ETL approach?
**A:** Modern cloud-native pattern where data moves directly from source to analytics without traditional ETL jobs. Examples: Aurora zero-ETL to Redshift, DynamoDB to OpenSearch, BigQuery federated queries. Benefits: near real-time, reduced maintenance, less code. Limitations: less control over transformations, vendor lock-in, and limited to supported source-target pairs.

## Scenario-Based Questions

### Q31: A daily ETL job has been failing for 3 days but no one noticed. How do you prevent this?
**A:**
1. **Immediate:** Fix the failure and backfill the 3 days of missing data
2. **Alerting:** Add failure alerts (email, Slack, PagerDuty) with escalation
3. **SLA monitoring:** Alert if data isn't fresh by expected time, not just on failure
4. **Data quality checks:** Add row count and sum reconciliation that would catch silent failures
5. **Dashboard:** Create a pipeline health dashboard visible to the team
6. **On-call rotation:** Ensure someone is responsible for responding to alerts
7. **Runbook:** Document common failure modes and resolution steps

### Q32: Source system added a new column without notice. Your ETL broke. How do you handle it?
**A:**
1. **Fix immediate:** Update pipeline to handle the new column (ignore or incorporate)
2. **Schema registry:** Implement schema detection and versioning for this source
3. **Monitoring:** Add schema drift alerts that notify when source schemas change
4. **Communication:** Establish a contact point with the source team for change notifications
5. **Resilience:** Design pipelines to be forward-compatible (ignore unknown columns by default)
6. **Contract:** Propose a formal data contract with the source team

### Q33: You need to migrate from on-premise ETL to cloud ELT. What's your migration strategy?
**A:**
1. **Assess:** Catalog all pipelines, dependencies, and data sources
2. **Pilot:** Migrate 2-3 non-critical pipelines first to learn patterns
3. **Infrastructure:** Set up cloud warehouse, IAM, networking, and security
4. **Parallel run:** Run old and new systems side-by-side for 2-4 weeks
5. **Reconcile:** Compare outputs daily to ensure parity
6. **Cutover:** Switch consumers to new system, keep old as fallback
7. **Decommission:** Retire old system after 30 days of stable operation
8. **Document:** Update runbooks and train the team

### Q34: A pipeline processes 10M rows daily but is now taking 8 hours. How do you optimize?
**A:**
1. **Profile:** Identify the slowest step (extraction, transformation, or loading)
2. **Incremental:** Switch from full to incremental load if possible
3. **Parallelize:** Process partitions in parallel instead of sequentially
4. **Optimize SQL:** Add indexes, reduce joins, push filters down
5. **Scale up:** Increase warehouse compute during the job window
6. **Caching:** Cache lookup tables that don't change often
7. **File format:** Use columnar formats (Parquet) instead of row-based (CSV)
8. **Partitioning:** Partition target tables by date for faster writes

### Q35: You need to combine data from 5 different SaaS tools into one warehouse. What's your approach?
**A:**
1. **Inventory:** Document APIs, rate limits, authentication, and data structures for each tool
2. **ELT tool:** Use Fivetran, Stitch, or Airbyte for managed connectors
3. **Custom if needed:** Build Python scripts for tools without standard connectors
4. **Bronze layer:** Load raw data from each source into separate schemas
5. **Silver layer:** Standardize naming, handle time zones, deduplicate
6. **Gold layer:** Join across sources to create unified business entities
7. **Schedule:** Align extraction frequency with business needs and API limits
8. **Monitor:** Track API quota usage and failure rates

### Q36: A business user needs real-time data but your pipeline runs daily. What do you do?
**A:**
1. **Clarify need:** What does "real-time" mean? 5 minutes? 1 hour? What decision depends on it?
2. **Assess cost:** Streaming infrastructure is 5-10x more expensive than batch
3. **Hybrid approach:** Keep daily batch for historical, add streaming for the last 24 hours
4. **CDC:** Implement Change Data Capture from the source database
5. **Caching:** If the data changes infrequently, a cache with TTL may suffice
6. **Near-real-time:** Increase batch frequency to every 15-30 minutes as a compromise
7. **Prototype:** Build a small streaming proof-of-concept before full investment

### Q37: Your ETL creates duplicate records in the target table. How do you fix and prevent this?
**A:**
1. **Identify cause:** Is it re-running without idempotency? Or source has duplicates?
2. **Fix data:** Deduplicate target table using MERGE or DELETE with window functions
3. **Idempotency:** Implement UPSERT/MERGE instead of INSERT
4. **Source dedup:** Add deduplication logic in transformation if source has duplicates
5. **Transaction IDs:** Use source system transaction IDs as natural keys
6. **Constraints:** Add unique constraints on target tables where possible
7. **Testing:** Add data quality tests for duplicate detection

### Q38: A source system goes down during extraction. How should your pipeline handle it?
**A:**
1. **Retry logic:** Exponential backoff with 3-5 retries
2. **Circuit breaker:** After repeated failures, stop trying and alert
3. **Graceful degradation:** If this source is non-critical, continue with other sources
4. **Checkpointing:** Resume from last successful extraction point, not restart
5. **Alerting:** Notify on-call engineer after retries exhausted
6. **SLA communication:** Inform stakeholders if the outage affects their data freshness
7. **Backfill:** Once source recovers, automatically catch up missed data

### Q39: You need to join a 100M row table with a 1B row table. What's your strategy?
**A:**
1. **Filter early:** Apply WHERE clauses before the join to reduce data
2. **Partition alignment:** Ensure both tables are partitioned on the join key
3. **Broadcast join:** If one table is small enough, broadcast it to all nodes
4. **Sort-merge join:** Ensure both sides are sorted on join key
5. **Bucketed tables:** Pre-bucket both tables on join key for map-side join
6. **Sampling:** If exact join isn't needed, sample both tables first
7. **Distributed engine:** Use Spark, Presto, or BigQuery that handle large joins natively
8. **Incremental:** If possible, join only new/changed records

### Q40: A new regulation requires you to delete user data within 30 days of request. How do you implement this in your pipeline?
**A:**
1. **Identify all locations:** Warehouse tables, data marts, backups, logs, and downstream systems
2. **Golden record:** Maintain a user ID mapping table to track all instances
3. **Deletion pipeline:** Create a job that propagates deletion requests through all layers
4. **Soft delete first:** Mark for deletion, then hard delete after validation
5. **Audit log:** Log all deletions for compliance evidence
6. **Backups:** Ensure deletion from backups or mark for exclusion from restore
7. **Testing:** Regularly test deletion end-to-end with test accounts
8. **SLA monitoring:** Track time from request to complete deletion

### Q41: You're designing a pipeline that needs to handle both structured and unstructured data. What's your architecture?
**A:**
1. **Data Lake:** Store raw structured (Parquet/CSV) and unstructured (JSON, images, text) in object storage (S3, ADLS)
2. **Bronze layer:** Raw ingestion of all data types
3. **Structured path:** ELT into warehouse for SQL analytics
4. **Unstructured path:** Use Spark/Databricks for processing, extract structured features (NLP, image recognition), then load results to warehouse
5. **Metadata catalog:** Unified catalog (DataHub, Glue) tracking all assets
6. **Query layer:** Use lakehouse (Delta Lake, Iceberg) for unified SQL access across structured and semi-structured
7. **Governance:** Consistent access controls and lineage across all data types
