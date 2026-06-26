# 18. Database Design

## Theoretical Questions

### Q1: What is database design and why does it matter?
**A:** Database design is the process of defining how data is stored, organized, and accessed. It matters because good design ensures data integrity, query performance, scalability, and maintainability. Poor design leads to data redundancy, anomalies, slow queries, and costly refactoring. A well-designed database is the foundation of reliable data systems.

### Q2: What are the steps in the database design process?
**A:**
1. **Requirements Analysis:** Understand business needs and data requirements
2. **Conceptual Design:** Create ER diagrams (entities, relationships, attributes) — independent of DBMS
3. **Logical Design:** Convert ER model to relational schema (tables, keys, normalization)
4. **Physical Design:** Define indexes, partitioning, storage, and hardware considerations
5. **Implementation:** Create DDL, load data, and build applications
6. **Testing & Optimization:** Validate design, tune performance, and iterate

### Q3: What is an Entity-Relationship (ER) diagram?
**A:** A visual representation of database structure showing:
- **Entities:** Objects (tables) like Customer, Order, Product
- **Attributes:** Properties of entities (columns)
- **Relationships:** How entities connect (1:1, 1:N, M:N)
- **Keys:** Primary keys (unique identifiers) and foreign keys (relationships)
Types: Chen notation (boxes, diamonds) and Crow's Foot notation (lines with symbols).

### Q4: What are the types of relationships in ER diagrams?
**A:**
- **One-to-One (1:1):** One record in A relates to one in B. Example: Employee ↔ EmployeeDetails.
- **One-to-Many (1:N):** One record in A relates to many in B. Example: Customer → Orders.
- **Many-to-Many (M:N):** Many records in A relate to many in B. Example: Students ↔ Courses. Implemented with a junction table.

### Q5: What is normalization and what are the normal forms?
**A:** Normalization is organizing data to reduce redundancy and improve integrity. Normal forms:
- **1NF:** Atomic values (no repeating groups), each cell has single value
- **2NF:** 1NF + no partial dependency (non-key attributes depend on full PK)
- **3NF:** 2NF + no transitive dependency (non-key attributes depend only on PK)
- **BCNF:** 3NF + every determinant is a candidate key (stricter 3NF)
- **4NF:** BCNF + no multi-valued dependencies
- **5NF:** 4NF + no join dependencies (perfect decomposition)
Most production databases target 3NF or BCNF.

### Q6: What is denormalization and when should you use it?
**A:** Denormalization intentionally adds redundancy to improve read performance. Use when:
- Read-heavy workloads with frequent joins
- Real-time analytics requiring fast aggregations
- Data warehousing and reporting databases
- When the cost of joins exceeds the cost of maintaining redundant data
Trade-off: faster reads, but slower writes, more storage, and risk of inconsistency (requires careful update logic).

### Q7: What is a primary key and what makes a good one?
**A:** A primary key uniquely identifies each row in a table. A good PK is:
- **Unique:** No two rows share the same value
- **Non-null:** Every row must have one
- **Stable:** Doesn't change over time
- **Minimal:** As few columns as possible
- **Meaningless:** Surrogate keys (auto-increment, UUID) are often better than natural keys (SSN, email) because natural keys can change and may not be unique.

### Q8: What is a foreign key and what is referential integrity?
**A:** A foreign key is a column that references the primary key of another table, establishing a relationship. Referential integrity ensures:
- You can't insert a child row with a non-existent parent (prevent orphaned records)
- Updating/deleting a parent can cascade, restrict, or set null on children (ON DELETE/UPDATE actions)
Enforced by the database via FOREIGN KEY constraints.

### Q9: What are the common data types and when do you use each?
**A:**
- **INT/BIGINT:** Whole numbers (IDs, counts, quantities). Use BIGINT for IDs to avoid overflow.
- **DECIMAL/NUMERIC:** Exact precision for money (never use FLOAT for currency)
- **VARCHAR:** Variable-length strings (names, descriptions). Specify max length.
- **TEXT:** Long strings (comments, articles). Often stored separately for performance.
- **DATE/TIME/TIMESTAMP:** Temporal data. Use TIMESTAMP WITH TIME ZONE when global.
- **BOOLEAN:** True/false flags.
- **JSON/JSONB:** Semi-structured data (configs, API responses). JSONB is indexed and faster in PostgreSQL.
- **ENUM:** Fixed set of values (statuses, types). Use sparingly — hard to modify.
- **UUID:** Universally unique identifiers. Good for distributed systems.

### Q10: What is an index and how does it improve performance?
**A:** An index is a data structure (typically B-Tree) that allows the database to find rows without scanning the entire table. Like a book's index — instead of reading every page, jump to the right section. Improves SELECT and WHERE performance. Costs: slower INSERT/UPDATE/DELETE (index must be maintained) and additional storage. Types: B-Tree (default), Hash, GiST, GIN, and Full-Text.

### Q11: What is the difference between a clustered and non-clustered index?
**A:**
- **Clustered Index:** Determines the physical order of data on disk. Only one per table (the data IS the index). In SQL Server, the primary key is clustered by default. In PostgreSQL, tables are heap-organized (no clustered index natively, but CLUSTER command exists).
- **Non-Clustered Index:** Separate structure with pointers to actual data. Multiple allowed per table. Like an index in a book with page numbers.

### Q12: What is a composite index and when should you use one?
**A:** An index on multiple columns. Use when queries frequently filter on multiple columns together. Order matters: put the most selective (most filtering power) column first. Example: INDEX on (state, city) is good for queries filtering by state AND city, but not for queries filtering only by city.

### Q13: What is database cardinality and why does it matter?
**A:** Cardinality refers to the uniqueness of data values in a column:
- **High cardinality:** Many unique values (email, SSN) — good for indexing
- **Low cardinality:** Few unique values (gender, status, boolean) — poor for B-Tree indexing, consider bitmap indexes or partitioning
Matters for index selection, query optimization, and storage efficiency.

### Q14: What is a covering index?
**A:** An index that includes all columns needed for a query, so the database never needs to access the actual table ("index-only scan"). Example: if you query SELECT name, salary WHERE department = 'Sales', a covering index on (department, name, salary) satisfies the query entirely from the index. Dramatically improves performance for read-heavy workloads.

### Q15: What is database partitioning and what are the strategies?
**A:** Splitting large tables into smaller, more manageable pieces. Strategies:
- **Range partitioning:** By value range (date, ID). Most common.
- **List partitioning:** By discrete values (region, status).
- **Hash partitioning:** By hash of a key. Even distribution, good for load balancing.
- **Composite:** Combine strategies (range + hash).
Benefits: faster queries (prune irrelevant partitions), easier maintenance, and parallel processing.

### Q16: What is a view and what are its use cases?
**A:** A virtual table based on a stored query. Use cases:
- Simplify complex queries for end users
- Restrict access to specific columns/rows (security layer)
- Present data in a different format without duplicating it
- Encapsulate business logic
- Materialized views: pre-computed for performance (but require refresh)

### Q17: What is a stored procedure and when should you use one?
**A:** A precompiled SQL program stored in the database. Use when:
- Complex business logic that should be centralized and reusable
- Need for transaction control within the database
- Performance-critical operations (avoids network round trips)
- Security: users can execute without direct table access
Caution: can create vendor lock-in and make testing harder. Modern preference is often application-layer logic.

### Q18: What is ACID and why is it important?
**A:** Properties of reliable database transactions:
- **Atomicity:** All operations succeed or all fail (no partial completion)
- **Consistency:** Database remains in a valid state before and after
- **Isolation:** Concurrent transactions don't interfere with each other
- **Durability:** Committed data survives system crashes
Important for: financial transactions, inventory management, and any system where data integrity is critical.

### Q19: What is the CAP theorem and how does it apply to database design?
**A:** In distributed systems, you can only guarantee two of three:
- **Consistency:** All nodes see the same data at the same time
- **Availability:** Every request gets a response
- **Partition Tolerance:** System works despite network failures
Since partition tolerance is mandatory in distributed systems, you choose CP (Consistency + Partition tolerance) or AP (Availability + Partition tolerance). Traditional RDBMS = CP. NoSQL (Cassandra) = AP.

### Q20: What is a surrogate key vs. a natural key?
**A:**
- **Natural Key:** Derived from real-world data (email, SSN, phone number). Pros: meaningful, no extra column. Cons: can change, may not be unique, privacy concerns.
- **Surrogate Key:** Artificial identifier (auto-increment ID, UUID). Pros: stable, always unique, small, no privacy issues. Cons: meaningless, requires extra column.
Best practice: Use surrogate keys for primary keys, with unique constraints on natural keys when needed.

### Q21: What is database sharding and when do you need it?
**A:** Horizontal partitioning where data is split across multiple database servers (shards). Each shard holds a subset of data (e.g., customers A-M on Shard 1, N-Z on Shard 2). Needed when: single server can't handle data volume or query load. Challenges: cross-shard queries, rebalancing when adding shards, and complex application logic. Common in: large-scale web apps (Uber, Airbnb).

### Q22: What is the difference between OLTP and OLAP databases?
**A:**
- **OLTP (Online Transaction Processing):** Optimized for fast, frequent transactions (INSERT, UPDATE, DELETE). Row-oriented, normalized, many small queries. Examples: PostgreSQL, MySQL, SQL Server.
- **OLAP (Online Analytical Processing):** Optimized for complex read-heavy queries and aggregations. Column-oriented, denormalized, few large queries. Examples: Snowflake, BigQuery, ClickHouse.

### Q23: What is a database constraint and what types exist?
**A:** Rules enforced by the database to maintain data integrity:
- **PRIMARY KEY:** Unique, non-null identifier
- **FOREIGN KEY:** Enforces referential integrity between tables
- **UNIQUE:** No duplicate values in a column
- **NOT NULL:** Column must have a value
- **CHECK:** Custom condition (e.g., salary > 0, age >= 18)
- **DEFAULT:** Auto-populate if no value provided

### Q24: What is database replication and what are the types?
**A:** Copying data from one database to another for availability or scaling.
- **Master-Slave (Primary-Replica):** Writes to master, reads from replicas. Good for read scaling.
- **Master-Master:** Writes to both. Complex, risk of conflicts.
- **Synchronous:** Master waits for replica confirmation. Consistent but slower.
- **Asynchronous:** Master doesn't wait. Faster but potential data loss on failover.

### Q25: What is a database transaction and how do you manage it?
**A:** A logical unit of work that must complete entirely or not at all. Managed with:
- **BEGIN TRANSACTION:** Start
- **COMMIT:** Save changes permanently
- **ROLLBACK:** Undo changes
- **SAVEPOINT:** Partial rollback point within a transaction
Best practice: keep transactions as short as possible to reduce locking.

### Q26: What is database locking and what are the types?
**A:** Locking prevents concurrent transactions from corrupting data.
- **Shared Lock (S):** For reading. Multiple transactions can hold shared locks.
- **Exclusive Lock (X):** For writing. Only one transaction can hold it.
- **Row-level:** Granular, minimal contention. Preferred.
- **Table-level:** Coarse, can cause bottlenecks.
- **Deadlock:** Two transactions waiting for each other's locks. Database detects and kills one.

### Q27: What is a database trigger and when should you use it?
**A:** A procedure that automatically executes on INSERT, UPDATE, or DELETE. Use for:
- Audit logging (who changed what and when)
- Enforcing complex business rules
- Cascading updates that foreign keys can't handle
- Maintaining derived columns or summary tables
Caution: can create hidden complexity, slow down operations, and make debugging hard. Use sparingly.

### Q28: What is database schema evolution and how do you handle it?
**A:** Managing changes to database structure over time. Strategies:
- **Version control:** Use migration tools (Flyway, Liquibase, Alembic)
- **Backward compatibility:** New schema supports old and new code during deployment
- **Blue-green deployment:** Deploy new schema, switch traffic, rollback if issues
- **Expand-contract:** Add new column (expand), migrate data, remove old column (contract)
- **Never:** Drop columns immediately — always deprecate first

### Q29: What is the difference between a database and a schema in PostgreSQL/SQL Server?
**A:** Confusing terminology! In PostgreSQL and SQL Server:
- **Database:** A complete isolated instance with its own users, tables, and settings. Heavyweight.
- **Schema:** A namespace within a database that groups tables (like folders). Lightweight. One database can have multiple schemas (public, analytics, staging).
In MySQL, "database" and "schema" are synonyms.

### Q30: What is database connection pooling and why is it important?
**A:** Reusing database connections instead of creating new ones for each query. Important because: creating connections is expensive (TCP handshake + authentication), and databases have connection limits. Pooling maintains a set of open connections that applications borrow and return. Tools: PgBouncer, HikariCP, SQLAlchemy pool. Prevents connection exhaustion under load.

## Scenario-Based Questions

### Q31: Design a database for an e-commerce platform (customers, orders, products, payments).
**A:**
```sql
-- Core tables
customers (customer_id PK, email UNIQUE, name, created_at)
products (product_id PK, name, price, category_id FK, stock_quantity)
categories (category_id PK, name)
orders (order_id PK, customer_id FK, order_date, status, total_amount)
order_items (order_item_id PK, order_id FK, product_id FK, quantity, unit_price)
payments (payment_id PK, order_id FK, amount, method, status, paid_at)

-- Design decisions:
-- - Surrogate keys (UUID or BIGINT) for all tables
-- - order_items captures historical price (unit_price) since product price changes
-- - payments separate from orders for partial payments and refunds
-- - Indexes: orders(customer_id, order_date), order_items(order_id), products(category_id)
-- - Constraints: CHECK (stock_quantity >= 0), CHECK (unit_price > 0)
```

### Q32: A table with 100M rows is slow to query. How do you optimize it?
**A:**
1. **Analyze:** EXPLAIN ANALYZE to find the bottleneck (full table scan? missing index?)
2. **Indexing:** Add indexes on WHERE, JOIN, and ORDER BY columns. Consider composite indexes.
3. **Partitioning:** Partition by date or range if queries are time-bounded
4. **Query rewrite:** Avoid SELECT *, push filters down, use covering indexes
5. **Archiving:** Move old data to a history table or cold storage
6. **Denormalization:** If read-heavy, pre-join frequently accessed data
7. **Hardware:** More RAM (cache), faster SSD, or scale up (read replicas)
8. **Vacuum/Analyze:** In PostgreSQL, run maintenance to update statistics

### Q33: You need to design a database that supports both transactional operations and analytics. What's your approach?
**A:**
1. **Separate databases:** OLTP database for transactions, OLAP warehouse for analytics
2. **ETL pipeline:** CDC or batch sync from OLTP to OLAP (nightly or near real-time)
3. **OLTP design:** Normalized (3NF), row-oriented, indexes for fast lookups
4. **OLAP design:** Denormalized, column-oriented, partitioned by date, materialized views
5. **Alternative:** If budget/team is small, use PostgreSQL with read replicas for light analytics, or a lakehouse (Delta Lake) for unified approach

### Q34: How do you handle a "soft delete" requirement in database design?
**A:**
```sql
-- Add to every table:
deleted_at TIMESTAMP NULL DEFAULT NULL,
-- Instead of DELETE, UPDATE deleted_at = NOW()
-- Queries filter: WHERE deleted_at IS NULL
-- Benefits: recoverable, audit trail, referential integrity preserved
-- Indexes: partial index WHERE deleted_at IS NULL for performance
-- Consider: unique constraints need to account for soft deletes
-- UNIQUE(email, deleted_at) won't work well; use partial unique indexes or UUIDs
```

### Q35: A business requirement says "every customer must have exactly one active subscription." How do you enforce this at the database level?
**A:**
```sql
-- subscriptions table
subscription_id PK, customer_id FK, plan_id FK, 
start_date, end_date, status ENUM('active', 'cancelled', 'expired')

-- Partial unique index (PostgreSQL):
CREATE UNIQUE INDEX idx_one_active_sub ON subscriptions(customer_id) 
WHERE status = 'active';

-- Or use a trigger to prevent multiple active subscriptions
-- Application layer also validates, but database is the final enforcer
```

### Q36: You need to store hierarchical data (org chart, file system). What are your options?
**A:**
1. **Adjacency List:** Each row has a parent_id. Simple, but recursive queries are hard in MySQL (easier in PostgreSQL with RECURSIVE CTE).
2. **Nested Set Model:** Each node has left/right values. Fast reads, terrible updates.
3. **Path Enumeration:** Store path as string ("/1/2/5/"). Easy to query ancestors, harder to enforce integrity.
4. **Closure Table:** Separate table storing all ancestor-descendant pairs. Fast for both traversal and finding all descendants.
**Recommendation:** Adjacency list with recursive CTEs for most cases (PostgreSQL, SQL Server). Closure table for very deep hierarchies with frequent reads.

### Q37: Design a database for a multi-tenant SaaS application. What are the approaches?
**A:**
1. **Shared Database, Shared Schema:** tenant_id column in every table. Simple, but risk of data leakage if queries forget the filter. Best for: small tenants, high density.
2. **Shared Database, Separate Schema:** One schema per tenant. Better isolation, but harder to scale (schema limits). Best for: medium tenants, need some isolation.
3. **Separate Database:** One database per tenant. Maximum isolation, easiest compliance, but most expensive. Best for: enterprise tenants, strict compliance.
**Considerations:** Backup/restore per tenant, connection pooling, schema migrations across all tenants, and query performance.

### Q38: How do you design a database to handle time-series data (IoT sensors, metrics)?
**A:**
```sql
-- Time-series optimized table
sensor_readings (
    sensor_id INT,
    timestamp TIMESTAMP,
    value DECIMAL(10,2),
    metadata JSONB,
    PRIMARY KEY (sensor_id, timestamp)
) PARTITION BY RANGE (timestamp);

-- Design choices:
-- - Partition by time (daily or monthly) for easy archival
-- - Use hypertables (TimescaleDB) or specialized DB (InfluxDB) for heavy workloads
-- - Compress old partitions
-- - TTL: Auto-drop partitions older than retention period
-- - Aggregate to daily/hourly summaries for historical queries
-- - Index: (sensor_id, timestamp) for time-range queries per sensor
```

### Q39: You need to support full-text search in a relational database. What's your approach?
**A:**
- **PostgreSQL:** Built-in full-text search with tsvector/tsquery, GIN indexes. Good for moderate scale.
- **MySQL:** FULLTEXT indexes on InnoDB (MySQL 5.6+). Limited features.
- **SQL Server:** Full-Text Search component.
- **External:** Elasticsearch, OpenSearch, or Meilisearch for advanced features (fuzzy search, relevance scoring, faceting). Sync via CDC or application dual-write.
**Recommendation:** Start with PostgreSQL's built-in search. Move to Elasticsearch when you need: fuzzy matching, complex relevance tuning, or high query volume.

### Q40: A database migration needs to run while the application is live. How do you do it without downtime?
**A:**
1. **Backward-compatible changes first:** Add new columns/tables (old code still works)
2. **Dual-write:** Application writes to both old and new structures
3. **Backfill:** Migrate existing data in background batches
4. **Verify:** Compare old and new data for consistency
5. **Switch reads:** Point application to new structure
6. **Clean up:** Remove old columns/tables after monitoring period
7. **Rollback plan:** Keep old structure until confident

### Q41: How do you design a database for a system that needs to track all changes to critical tables (audit trail)?
**A:**
```sql
-- Option 1: Audit table per critical table
CREATE TABLE customers_audit (
    audit_id BIGSERIAL PRIMARY KEY,
    customer_id INT NOT NULL,
    operation CHAR(1) NOT NULL, -- I, U, D
    changed_at TIMESTAMP DEFAULT NOW(),
    changed_by VARCHAR(100),
    old_data JSONB,
    new_data JSONB
);

-- Option 2: Generic audit log
CREATE TABLE audit_log (
    audit_id BIGSERIAL PRIMARY KEY,
    table_name VARCHAR(100),
    record_id VARCHAR(100),
    operation CHAR(1),
    changed_at TIMESTAMP DEFAULT NOW(),
    changed_by VARCHAR(100),
    diff JSONB -- {"old": {...}, "new": {...}}
);

-- Implementation: Database triggers or application middleware
-- Considerations: Performance impact, storage growth, purging old audits
-- Alternative: Use temporal tables (SQL Server) or audit extensions (pgaudit for PostgreSQL)
```
