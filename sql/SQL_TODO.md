# SQL Prep TODO (Basics -> Advanced, Interview-Focused for ~3 YOE)

Legend: `[ ]` todo, `[x]` done. Items marked **(P1)** = very high interview priority, **(P2)** = commonly asked, **(P3)** = nice to have.

## Daily Routine (30-60 min)
- [ ] 1 concept/term review (pick from the lists below, explain it out loud in your own words)
- [ ] 3-5 query problems (LeetCode SQL / HackerRank / StrataScratch / DataLemur)
- [ ] 1 interview question answered without notes
- [ ] Log the day in `sql/daily-log.md` (date, topic, problems solved, what was hard)
- [ ] Weekly: redo the 3 problems you struggled with most

Suggested tools: PostgreSQL or MySQL locally (Docker), DB Fiddle, SQLZoo, LeetCode (Database), DataLemur, Mode SQL tutorial.

---

## Phase 1: Basics
- [ ] What is a database, RDBMS vs NoSQL, tables/rows/columns, schema
- [ ] SQL sublanguages: DDL, DML, DQL, DCL, TCL **(P1)**
- [ ] Data types (INT, BIGINT, DECIMAL, VARCHAR vs CHAR vs TEXT, DATE, TIMESTAMP, BOOLEAN, JSON)
- [ ] SELECT, DISTINCT, aliases (AS)
- [ ] WHERE, comparison/logical operators (AND, OR, NOT), IN, BETWEEN, LIKE, IS NULL **(P1)**
- [ ] ORDER BY, LIMIT/OFFSET/TOP, FETCH FIRST
- [ ] Aggregate functions: COUNT, SUM, AVG, MIN, MAX
- [ ] COUNT(*) vs COUNT(col) vs COUNT(DISTINCT col) **(P1)**
- [ ] GROUP BY and HAVING; WHERE vs HAVING **(P1)**
- [ ] NULL handling: three-valued logic, COALESCE, NULLIF, IFNULL/ISNULL, NULL in aggregates and comparisons **(P1)**
- [ ] CASE WHEN expressions (incl. conditional aggregation / pivot with CASE) **(P1)**
- [ ] String functions (CONCAT, SUBSTRING, TRIM, UPPER/LOWER, REPLACE, LENGTH, POSITION)
- [ ] Date/time functions (NOW, DATE_TRUNC, DATEDIFF, EXTRACT, DATE_ADD, formatting)
- [ ] Numeric functions (ROUND, CEIL, FLOOR, MOD, ABS)
- [ ] Type casting (CAST, CONVERT, ::)
- [ ] Logical order of execution: FROM -> WHERE -> GROUP BY -> HAVING -> SELECT -> DISTINCT -> ORDER BY -> LIMIT **(P1)**

## Phase 2: Table Design and DDL/DML
- [ ] CREATE / ALTER / DROP / TRUNCATE; DELETE vs TRUNCATE vs DROP **(P1)**
- [ ] INSERT (single, multi-row, INSERT ... SELECT), UPDATE, DELETE
- [ ] UPDATE/DELETE with JOIN
- [ ] UPSERT (INSERT ON CONFLICT / ON DUPLICATE KEY / MERGE)
- [ ] Constraints: PRIMARY KEY, FOREIGN KEY, UNIQUE, NOT NULL, CHECK, DEFAULT **(P1)**
- [ ] Primary key vs unique key vs candidate key vs super key vs composite key **(P1)**
- [ ] Foreign key actions: ON DELETE CASCADE / SET NULL / RESTRICT
- [ ] Auto increment / SERIAL / IDENTITY / sequences / UUID as PK (tradeoffs)
- [ ] Relationships: 1:1, 1:N, M:N (junction tables)
- [ ] Surrogate vs natural keys

## Phase 3: Joins and Set Operations (Core Interview Area)
- [ ] INNER JOIN **(P1)**
- [ ] LEFT / RIGHT / FULL OUTER JOIN **(P1)**
- [ ] CROSS JOIN (cartesian product) and when it is useful
- [ ] SELF JOIN (employee-manager, find duplicates, compare rows) **(P1)**
- [ ] Join conditions: ON vs WHERE for outer joins (filter placement changes results) **(P1)**
- [ ] NATURAL JOIN, USING
- [ ] Anti-join patterns: `LEFT JOIN ... IS NULL` vs `NOT EXISTS` vs `NOT IN` (NOT IN + NULL trap) **(P1)**
- [ ] Semi-join: `EXISTS` vs `IN` **(P1)**
- [ ] Joining multiple tables, join order, many-to-many fan-out causing duplicated/inflated aggregates **(P1)**
- [ ] UNION vs UNION ALL, INTERSECT, EXCEPT/MINUS **(P1)**
- [ ] Non-equi joins (range joins, date between)

## Phase 4: Subqueries, CTEs, Views
- [ ] Scalar, row, table subqueries
- [ ] Correlated vs non-correlated subqueries **(P1)**
- [ ] Subquery in SELECT, FROM (derived table), WHERE, HAVING
- [ ] EXISTS / NOT EXISTS, ANY / ALL
- [ ] CTE (WITH) and readability; multiple CTEs **(P1)**
- [ ] Recursive CTE (hierarchies, org chart, number/date series, graph paths) **(P1)**
- [ ] CTE vs subquery vs temp table vs view (performance and materialization) **(P2)**
- [ ] Views, materialized views, updatable views **(P2)**
- [ ] Temp tables vs table variables

## Phase 5: Window Functions (Very Heavily Asked)
- [ ] OVER(), PARTITION BY, ORDER BY inside window **(P1)**
- [ ] ROW_NUMBER vs RANK vs DENSE_RANK vs NTILE **(P1)**
- [ ] LAG / LEAD **(P1)**
- [ ] FIRST_VALUE / LAST_VALUE / NTH_VALUE (frame gotcha for LAST_VALUE) **(P2)**
- [ ] Running totals / moving averages: `SUM() OVER (ORDER BY ... ROWS BETWEEN ...)` **(P1)**
- [ ] ROWS vs RANGE frames, default frame behavior **(P2)**
- [ ] PERCENT_RANK, CUME_DIST, percentiles (PERCENTILE_CONT / DISC), median **(P2)**
- [ ] Aggregate function vs window function difference **(P1)**
- [ ] Filtering on window results (must wrap in CTE/subquery; QUALIFY in some DBs)

## Phase 6: Indexing and Performance (Experience-Level Differentiator)
- [ ] What is an index, why it speeds reads and slows writes **(P1)**
- [ ] B-Tree vs Hash vs GIN/GiST vs Bitmap indexes **(P2)**
- [ ] Clustered vs non-clustered index **(P1)**
- [ ] Composite index and leftmost prefix rule, column order **(P1)**
- [ ] Covering index, index-only scan, included columns **(P1)**
- [ ] Selectivity / cardinality; when the optimizer ignores an index **(P1)**
- [ ] SARGability: why `WHERE YEAR(date)=2024` or `LIKE '%abc'` or functions on columns kill index use **(P1)**
- [ ] Reading EXPLAIN / EXPLAIN ANALYZE: seq scan vs index scan vs index seek, nested loop vs hash join vs merge join **(P1)**
- [ ] Table statistics, vacuum/analyze (Postgres) or update statistics
- [ ] Query optimization checklist: select only needed columns, avoid SELECT *, avoid N+1, push filters early, avoid correlated subquery where join works, pagination without big OFFSET (keyset pagination) **(P1)**
- [ ] Partial / filtered indexes, expression indexes, unique indexes
- [ ] Partitioning (range, list, hash) and sharding concepts **(P2)**
- [ ] Denormalization tradeoffs, caching, read replicas **(P2)**
- [ ] Slow query log, finding and fixing slow queries in a real system **(P1)**

## Phase 7: Transactions, Concurrency, ACID
- [ ] ACID properties explained with examples **(P1)**
- [ ] BEGIN / COMMIT / ROLLBACK / SAVEPOINT
- [ ] Isolation levels: Read Uncommitted, Read Committed, Repeatable Read, Serializable **(P1)**
- [ ] Anomalies: dirty read, non-repeatable read, phantom read, lost update, write skew **(P1)**
- [ ] Locking: shared vs exclusive, row vs table locks, `SELECT ... FOR UPDATE`, `SKIP LOCKED` **(P1)**
- [ ] Deadlocks: how they happen, detection, prevention **(P1)**
- [ ] Optimistic vs pessimistic locking (version column) **(P1)**
- [ ] MVCC basics **(P2)**
- [ ] Idempotent writes, handling duplicate requests, unique constraints as safety net
- [ ] Distributed transactions, 2PC, saga (conceptual) **(P3)**
- [ ] CAP theorem, BASE vs ACID **(P2)**

## Phase 8: Normalization and Data Modeling
- [ ] 1NF, 2NF, 3NF, BCNF with examples **(P1)**
- [ ] Functional dependencies, anomalies (insert/update/delete)
- [ ] When and why to denormalize **(P1)**
- [ ] ER diagrams, designing a schema from requirements (e.g., e-commerce, library, ride-sharing, URL shortener, booking system) **(P1)**
- [ ] OLTP vs OLAP; star vs snowflake schema; fact vs dimension tables **(P2)**
- [ ] Slowly changing dimensions (Type 1/2/3) **(P3)**
- [ ] Soft delete vs hard delete, audit/history tables, timestamps (created_at/updated_at)
- [ ] Storing money (DECIMAL not FLOAT), timezones (store UTC), enums vs lookup tables
- [ ] Multi-tenant schema design

## Phase 9: Advanced SQL Features
- [ ] Stored procedures vs functions vs triggers; pros/cons **(P2)**
- [ ] Triggers (BEFORE/AFTER, row vs statement level) and why overuse is risky
- [ ] Cursors and why to avoid (set-based thinking) **(P2)**
- [ ] Dynamic SQL and SQL injection; parameterized queries / prepared statements **(P1)**
- [ ] JSON / JSONB support, querying and indexing JSON **(P2)**
- [ ] Full-text search basics
- [ ] PIVOT / UNPIVOT (and CASE-based alternative)
- [ ] GROUPING SETS, ROLLUP, CUBE **(P2)**
- [ ] LATERAL / CROSS APPLY / OUTER APPLY **(P2)**
- [ ] Arrays, generate_series, string_agg / GROUP_CONCAT / LISTAGG
- [ ] Sequences, generated columns, computed columns
- [ ] User-defined types, domains, collations/encoding (UTF-8 pitfalls)
- [ ] Roles, GRANT/REVOKE, least privilege, row-level security **(P2)**
- [ ] Backup/restore, point-in-time recovery, replication (primary/replica), failover basics **(P2)**
- [ ] Migrations (Flyway/Liquibase/Alembic), zero-downtime schema changes (adding column, backfilling, index CONCURRENTLY) **(P1)**
- [ ] Connection pooling (HikariCP/PgBouncer), connection leaks **(P2)**
- [ ] ORM-related issues: N+1, lazy loading, generated SQL review (JPA/Hibernate if you use Java) **(P1)**

## Phase 10: Classic Query Patterns (Must Be Able to Write From Scratch)
- [ ] Nth highest salary (3+ ways: LIMIT/OFFSET, DENSE_RANK, subquery) **(P1)**
- [ ] Top N per group (e.g., top 3 earners per department) **(P1)**
- [ ] Find duplicates; delete duplicates but keep one **(P1)**
- [ ] Customers who never ordered / records missing in another table **(P1)**
- [ ] Employees earning more than their manager (self join) **(P1)**
- [ ] Department with highest average salary
- [ ] Consecutive days / consecutive numbers / streaks (gaps-and-islands) **(P1)**
- [ ] Running total, cumulative percentage, moving average **(P1)**
- [ ] Month-over-month / year-over-year growth using LAG **(P1)**
- [ ] Retention / cohort / DAU-MAU style queries **(P2)**
- [ ] Median calculation
- [ ] Pivot rows to columns and back **(P1)**
- [ ] Find missing IDs / gaps in a sequence; generate date series and fill zero-count dates **(P1)**
- [ ] Second/Nth transaction per user, first/last event per user
- [ ] Sessionization (group events by time gap)
- [ ] Hierarchy traversal with recursive CTE (all reports under a manager)
- [ ] Swap values / update with CASE, conditional update
- [ ] Compare two tables (rows in A not in B, differences)
- [ ] Percent of total per group
- [ ] Remove/handle NULLs, trimming dirty data, string splitting
- [ ] Pagination (OFFSET vs keyset)
- [ ] Join tables with date ranges (effective-dated records)

## Phase 11: Interview Questions Bank (Answer Out Loud)
Conceptual (**P1**):
- [ ] DELETE vs TRUNCATE vs DROP
- [ ] WHERE vs HAVING
- [ ] INNER vs LEFT vs FULL join; when LEFT JOIN becomes INNER by accident
- [ ] UNION vs UNION ALL (performance)
- [ ] IN vs EXISTS vs JOIN
- [ ] Primary key vs unique key; can a table have multiple NULLs in a UNIQUE column?
- [ ] Clustered vs non-clustered index
- [ ] Why is `SELECT *` bad in production?
- [ ] What is a deadlock and how do you resolve it?
- [ ] Explain isolation levels with a real example
- [ ] Explain normalization; when did you denormalize and why?
- [ ] ROW_NUMBER vs RANK vs DENSE_RANK
- [ ] CTE vs temp table vs subquery
- [ ] What is an execution plan? How did you debug a slow query?
- [ ] Why would the database not use an index?
- [ ] How do you prevent SQL injection?
- [ ] Stored procedure vs function vs view
- [ ] COUNT(*) vs COUNT(1) vs COUNT(col)
- [ ] NULL comparisons; why `= NULL` fails
- [ ] What happens in `NOT IN` when the subquery contains NULL?
- [ ] ACID with an example (bank transfer)
- [ ] SQL vs NoSQL: when to choose each
- [ ] How do you handle millions of rows (batching, indexing, partitioning, archiving)?
- [ ] How do you do zero-downtime migration of a large table?
- [ ] Optimistic vs pessimistic locking
- [ ] Soft delete tradeoffs

Scenario / design (**P1** at 3 YOE):
- [ ] Design schema for: e-commerce orders, banking ledger, chat app, booking/reservation (prevent double booking), URL shortener, notification system
- [ ] Prevent double-spend / overselling inventory under concurrency
- [ ] An API endpoint got slow after data grew 10x: walk through your debugging steps
- [ ] Generate a report over 100M rows efficiently
- [ ] Dedupe data in a pipeline; make an import idempotent
- [ ] Safely add a NOT NULL column to a huge production table
- [ ] Pagination for infinite scroll over large, changing data

## Phase 12: Dialect Differences (Know the one you use + basics of others)
- [ ] PostgreSQL vs MySQL vs SQL Server vs Oracle: LIMIT/TOP/ROWNUM, string concat, date functions, upsert syntax, auto-increment
- [ ] MySQL storage engines (InnoDB vs MyISAM)
- [ ] Postgres specifics: JSONB, arrays, CTE materialization, MVCC/vacuum, `RETURNING`
- [ ] Case sensitivity, collation, default isolation level per DB

## Phase 13: Bonus (Data Engineering / Backend Overlap)
- [ ] NoSQL basics: document, key-value, column-family, graph; when to use which
- [ ] Redis caching patterns with DB (cache-aside, write-through, invalidation)
- [ ] Message queue + DB (outbox pattern)
- [ ] Data warehouse basics (BigQuery/Snowflake/Redshift), columnar storage
- [ ] ETL/ELT, CDC (change data capture)
- [ ] Time-series data basics

---

## Practice Tracker
| Week | Focus | Problems Solved | Weak Spots |
|------|-------|-----------------|------------|
| 1 | Phase 1 + 2 | | |
| 2 | Phase 3 (joins) | | |
| 3 | Phase 4 (subqueries, CTE) | | |
| 4 | Phase 5 (window functions) | | |
| 5 | Phase 6 (indexing, EXPLAIN) | | |
| 6 | Phase 7 + 8 (transactions, modeling) | | |
| 7 | Phase 9 + 10 (advanced + patterns) | | |
| 8 | Phase 11 (mock interviews, full revision) | | |

## Suggested Order If Time Is Short (Interview in 1-2 weeks)
1. Joins + NULL traps + GROUP BY/HAVING
2. Window functions + classic patterns (Nth salary, top N per group, duplicates, running totals, gaps-and-islands)
3. Indexing, EXPLAIN, SARGability, query tuning
4. Transactions, isolation levels, locking, deadlocks
5. Normalization + schema design scenarios
6. Rapid-fire conceptual question bank (Phase 11)
