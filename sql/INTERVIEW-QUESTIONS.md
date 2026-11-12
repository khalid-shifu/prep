# SQL and Database Interview Questions (Senior Backend)

54 questions with answers for a Java, Spring Boot, JPA/Hibernate and MySQL developer. Examples use the practice tables from [TABLES.md](TABLES.md) (departments, employees, customers, orders) and MySQL syntax. Where MySQL, PostgreSQL and Oracle differ, the answer says so. Oracle PL/SQL has its own file.

Each question has: a short answer, an explanation, SQL or a diagram, and likely follow-up questions with brief answers. The level (Basic, Intermediate, Advanced) is shown on every question.

## Index

### Jump by level

- **Basic:** [Q1](#q1-basic-what-are-the-types-of-joins-and-when-do-you-use-each), [Q2](#q2-basic-what-is-an-anti-join-and-a-semi-join), [Q3](#q3-basic-what-is-the-difference-between-where-and-having-and-what-does-group-by-do), [Q4](#q4-basic-what-is-the-logical-execution-order-of-a-sql-query), [Q5](#q5-basic-how-does-null-behave-in-sql), [Q6](#q6-basic-what-is-the-difference-between-union-and-union-all), [Q7](#q7-basic-what-is-the-difference-between-delete-truncate-and-drop), [Q8](#q8-basic-what-are-views-and-materialized-views), [Q9](#q9-basic-what-is-the-difference-between-a-stored-procedure-and-a-function), [Q10](#q10-basic-what-is-a-trigger-and-when-should-you-avoid-it), [Q19](#q19-basic-what-is-an-index-and-why-does-it-make-queries-faster), [Q32](#q32-basic-what-is-a-transaction-and-what-does-acid-mean), [Q41](#q41-basic-how-do-backups-and-high-availability-work-brief), [Q50](#q50-basic-sql-vs-nosql-what-is-the-difference-and-when-do-you-choose-each), [Q51](#q51-basic-what-are-primary-foreign-and-unique-keys-and-what-constraints-exist)
- **Intermediate:** [Q11](#q11-intermediate-when-do-you-use-a-subquery-a-cte-or-a-join), [Q12](#q12-intermediate-how-do-row_number-rank-and-dense_rank-differ), [Q13](#q13-intermediate-how-do-lag-and-lead-work), [Q14](#q14-intermediate-how-do-you-calculate-a-running-total-and-what-is-a-window-frame), [Q15](#q15-intermediate-classic-queries-second-highest-salary-duplicates-employees-with-no-department), [Q16](#q16-intermediate-how-do-you-get-the-top-n-rows-per-group), [Q20](#q20-intermediate-how-does-a-b-tree-index-work), [Q21](#q21-intermediate-what-is-the-difference-between-a-clustered-and-a-non-clustered-index), [Q22](#q22-intermediate-what-is-a-composite-index-and-the-leftmost-prefix-rule), [Q23](#q23-intermediate-what-is-a-covering-index), [Q24](#q24-intermediate-when-is-an-index-not-used), [Q25](#q25-intermediate-what-are-selectivity-and-cardinality-and-what-is-the-cost-of-too-many-indexes), [Q26](#q26-intermediate-how-do-you-read-an-explain-plan), [Q27](#q27-intermediate-how-do-you-paginate-offset-vs-keyset-seek-pagination), [Q28](#q28-intermediate-what-is-the-n1-problem-in-jpahibernate-and-how-do-you-fix-it), [Q29](#q29-intermediate-why-use-connection-pooling-and-how-do-you-size-a-pool), [Q33](#q33-intermediate-what-are-isolation-levels-and-the-read-anomalies), [Q34](#q34-intermediate-what-types-of-locks-exist-row-table-shared-exclusive), [Q35](#q35-intermediate-optimistic-vs-pessimistic-locking-what-is-the-difference-and-how-is-each-done-in-jpa), [Q36](#q36-intermediate-what-are-the-common-transactional-pitfalls-in-spring-boot), [Q42](#q42-intermediate-what-is-database-replication-primary-replica), [Q43](#q43-intermediate-what-is-replication-lag-and-how-do-you-deal-with-it), [Q44](#q44-intermediate-what-is-the-difference-between-partitioning-and-sharding), [Q45](#q45-intermediate-how-do-you-use-redis-as-a-cache-in-front-of-a-database), [Q46](#q46-intermediate-what-is-the-cap-theorem-and-eventual-consistency), [Q52](#q52-intermediate-what-is-normalization-explain-1nf-2nf-3nf-and-bcnf), [Q53](#q53-intermediate-what-is-denormalization-and-when-do-you-use-it)
- **Advanced:** [Q17](#q17-advanced-how-do-you-find-gaps-and-consecutive-groups-islands-in-a-sequence), [Q18](#q18-advanced-how-do-you-query-hierarchical-data-employee-to-manager-chain), [Q30](#q30-advanced-a-query-is-slow-in-production-how-do-you-diagnose-and-optimize-it), [Q31](#q31-advanced-how-do-you-avoid-filesort-and-temporary-tables-in-group-by-and-order-by), [Q37](#q37-advanced-what-is-a-deadlock-and-how-do-you-handle-it), [Q38](#q38-advanced-how-does-mvcc-work-simple-explanation), [Q39](#q39-advanced-what-are-gap-locks-and-next-key-locks-and-how-do-phantoms-relate-to-them-in-innodb), [Q40](#q40-advanced-how-do-you-keep-consistency-across-several-databases-or-services-2pc-saga-outbox), [Q47](#q47-advanced-how-do-you-choose-a-shard-key-and-what-is-a-hot-shard), [Q48](#q48-advanced-how-do-consistent-hashing-and-resharding-work), [Q49](#q49-advanced-what-are-the-problems-with-cross-shard-queries-joins-and-transactions), [Q54](#q54-advanced-design-questions-how-do-you-store-money-handle-soft-deletes-and-choose-ids)

### By topic

**1. SQL fundamentals and joins**

- [Q1. [Basic] What are the types of joins and when do you use each?](#q1-basic-what-are-the-types-of-joins-and-when-do-you-use-each)
- [Q2. [Basic] What is an anti-join and a semi-join?](#q2-basic-what-is-an-anti-join-and-a-semi-join)
- [Q3. [Basic] What is the difference between WHERE and HAVING, and what does GROUP BY do?](#q3-basic-what-is-the-difference-between-where-and-having-and-what-does-group-by-do)
- [Q4. [Basic] What is the logical execution order of a SQL query?](#q4-basic-what-is-the-logical-execution-order-of-a-sql-query)
- [Q5. [Basic] How does NULL behave in SQL?](#q5-basic-how-does-null-behave-in-sql)
- [Q6. [Basic] What is the difference between UNION and UNION ALL?](#q6-basic-what-is-the-difference-between-union-and-union-all)
- [Q7. [Basic] What is the difference between DELETE, TRUNCATE and DROP?](#q7-basic-what-is-the-difference-between-delete-truncate-and-drop)
- [Q8. [Basic] What are views and materialized views?](#q8-basic-what-are-views-and-materialized-views)
- [Q9. [Basic] What is the difference between a stored procedure and a function?](#q9-basic-what-is-the-difference-between-a-stored-procedure-and-a-function)
- [Q10. [Basic] What is a trigger and when should you avoid it?](#q10-basic-what-is-a-trigger-and-when-should-you-avoid-it)
- [Q11. [Intermediate] When do you use a subquery, a CTE or a join?](#q11-intermediate-when-do-you-use-a-subquery-a-cte-or-a-join)
- [Q12. [Intermediate] How do ROW_NUMBER, RANK and DENSE_RANK differ?](#q12-intermediate-how-do-row_number-rank-and-dense_rank-differ)
- [Q13. [Intermediate] How do LAG and LEAD work?](#q13-intermediate-how-do-lag-and-lead-work)
- [Q14. [Intermediate] How do you calculate a running total, and what is a window frame?](#q14-intermediate-how-do-you-calculate-a-running-total-and-what-is-a-window-frame)
- [Q15. [Intermediate] Classic queries: second highest salary, duplicates, employees with no department](#q15-intermediate-classic-queries-second-highest-salary-duplicates-employees-with-no-department)
- [Q16. [Intermediate] How do you get the top N rows per group?](#q16-intermediate-how-do-you-get-the-top-n-rows-per-group)
- [Q17. [Advanced] How do you find gaps and consecutive groups (islands) in a sequence?](#q17-advanced-how-do-you-find-gaps-and-consecutive-groups-islands-in-a-sequence)
- [Q18. [Advanced] How do you query hierarchical data (employee to manager chain)?](#q18-advanced-how-do-you-query-hierarchical-data-employee-to-manager-chain)

**2. Indexing and query performance**

- [Q19. [Basic] What is an index and why does it make queries faster?](#q19-basic-what-is-an-index-and-why-does-it-make-queries-faster)
- [Q20. [Intermediate] How does a B-tree index work?](#q20-intermediate-how-does-a-b-tree-index-work)
- [Q21. [Intermediate] What is the difference between a clustered and a non-clustered index?](#q21-intermediate-what-is-the-difference-between-a-clustered-and-a-non-clustered-index)
- [Q22. [Intermediate] What is a composite index and the leftmost prefix rule?](#q22-intermediate-what-is-a-composite-index-and-the-leftmost-prefix-rule)
- [Q23. [Intermediate] What is a covering index?](#q23-intermediate-what-is-a-covering-index)
- [Q24. [Intermediate] When is an index NOT used?](#q24-intermediate-when-is-an-index-not-used)
- [Q25. [Intermediate] What are selectivity and cardinality, and what is the cost of too many indexes?](#q25-intermediate-what-are-selectivity-and-cardinality-and-what-is-the-cost-of-too-many-indexes)
- [Q26. [Intermediate] How do you read an EXPLAIN plan?](#q26-intermediate-how-do-you-read-an-explain-plan)
- [Q27. [Intermediate] How do you paginate: OFFSET vs keyset (seek) pagination?](#q27-intermediate-how-do-you-paginate-offset-vs-keyset-seek-pagination)
- [Q28. [Intermediate] What is the N+1 problem in JPA/Hibernate and how do you fix it?](#q28-intermediate-what-is-the-n1-problem-in-jpahibernate-and-how-do-you-fix-it)
- [Q29. [Intermediate] Why use connection pooling and how do you size a pool?](#q29-intermediate-why-use-connection-pooling-and-how-do-you-size-a-pool)
- [Q30. [Advanced] A query is slow in production. How do you diagnose and optimize it?](#q30-advanced-a-query-is-slow-in-production-how-do-you-diagnose-and-optimize-it)
- [Q31. [Advanced] How do you avoid filesort and temporary tables in GROUP BY and ORDER BY?](#q31-advanced-how-do-you-avoid-filesort-and-temporary-tables-in-group-by-and-order-by)

**3. Transactions and locking**

- [Q32. [Basic] What is a transaction and what does ACID mean?](#q32-basic-what-is-a-transaction-and-what-does-acid-mean)
- [Q33. [Intermediate] What are isolation levels and the read anomalies?](#q33-intermediate-what-are-isolation-levels-and-the-read-anomalies)
- [Q34. [Intermediate] What types of locks exist (row, table, shared, exclusive)?](#q34-intermediate-what-types-of-locks-exist-row-table-shared-exclusive)
- [Q35. [Intermediate] Optimistic vs pessimistic locking: what is the difference and how is each done in JPA?](#q35-intermediate-optimistic-vs-pessimistic-locking-what-is-the-difference-and-how-is-each-done-in-jpa)
- [Q36. [Intermediate] What are the common `@Transactional` pitfalls in Spring Boot?](#q36-intermediate-what-are-the-common-transactional-pitfalls-in-spring-boot)
- [Q37. [Advanced] What is a deadlock and how do you handle it?](#q37-advanced-what-is-a-deadlock-and-how-do-you-handle-it)
- [Q38. [Advanced] How does MVCC work (simple explanation)?](#q38-advanced-how-does-mvcc-work-simple-explanation)
- [Q39. [Advanced] What are gap locks and next-key locks, and how do phantoms relate to them in InnoDB?](#q39-advanced-what-are-gap-locks-and-next-key-locks-and-how-do-phantoms-relate-to-them-in-innodb)
- [Q40. [Advanced] How do you keep consistency across several databases or services (2PC, saga, outbox)?](#q40-advanced-how-do-you-keep-consistency-across-several-databases-or-services-2pc-saga-outbox)

**4. Scaling: replication, partitioning, sharding, caching**

- [Q41. [Basic] How do backups and high availability work (brief)?](#q41-basic-how-do-backups-and-high-availability-work-brief)
- [Q42. [Intermediate] What is database replication (primary-replica)?](#q42-intermediate-what-is-database-replication-primary-replica)
- [Q43. [Intermediate] What is replication lag and how do you deal with it?](#q43-intermediate-what-is-replication-lag-and-how-do-you-deal-with-it)
- [Q44. [Intermediate] What is the difference between partitioning and sharding?](#q44-intermediate-what-is-the-difference-between-partitioning-and-sharding)
- [Q45. [Intermediate] How do you use Redis as a cache in front of a database?](#q45-intermediate-how-do-you-use-redis-as-a-cache-in-front-of-a-database)
- [Q46. [Intermediate] What is the CAP theorem and eventual consistency?](#q46-intermediate-what-is-the-cap-theorem-and-eventual-consistency)
- [Q47. [Advanced] How do you choose a shard key, and what is a hot shard?](#q47-advanced-how-do-you-choose-a-shard-key-and-what-is-a-hot-shard)
- [Q48. [Advanced] How do consistent hashing and resharding work?](#q48-advanced-how-do-consistent-hashing-and-resharding-work)
- [Q49. [Advanced] What are the problems with cross-shard queries, joins and transactions?](#q49-advanced-what-are-the-problems-with-cross-shard-queries-joins-and-transactions)

**5. Design: SQL vs NoSQL, keys, normalization**

- [Q50. [Basic] SQL vs NoSQL: what is the difference and when do you choose each?](#q50-basic-sql-vs-nosql-what-is-the-difference-and-when-do-you-choose-each)
- [Q51. [Basic] What are primary, foreign and unique keys, and what constraints exist?](#q51-basic-what-are-primary-foreign-and-unique-keys-and-what-constraints-exist)
- [Q52. [Intermediate] What is normalization? Explain 1NF, 2NF, 3NF and BCNF.](#q52-intermediate-what-is-normalization-explain-1nf-2nf-3nf-and-bcnf)
- [Q53. [Intermediate] What is denormalization and when do you use it?](#q53-intermediate-what-is-denormalization-and-when-do-you-use-it)
- [Q54. [Advanced] Design questions: how do you store money, handle soft deletes, and choose IDs?](#q54-advanced-design-questions-how-do-you-store-money-handle-soft-deletes-and-choose-ids)

---

## 1. SQL fundamentals and joins

### Q1. [Basic] What are the types of joins and when do you use each?

**Level: Basic**

**Short answer:** A join combines rows from two tables using a condition. INNER keeps only matching rows. LEFT and RIGHT keep all rows from one side and fill NULL when there is no match. FULL keeps all rows from both sides. CROSS makes every pair. A self join joins a table to itself.

**Explanation**

| Join | What it returns | Use it when |
|------|-----------------|-------------|
| INNER JOIN | Only rows that match in both tables | You need data that exists on both sides |
| LEFT JOIN | All rows from the left table, plus matches from the right. NULL where no match | You want everything from the left, even if it has no match |
| RIGHT JOIN | Mirror of LEFT JOIN | Rare. Swap the table order and use LEFT instead |
| FULL OUTER JOIN | All rows from both tables. NULL on the side with no match | You want unmatched rows on both sides. MySQL does not support it (use LEFT UNION RIGHT) |
| CROSS JOIN | Every row of A paired with every row of B (rows A x rows B) | Generating combinations, calendars, test data |
| SELF JOIN | A table joined to itself with two aliases | Hierarchies such as employee and manager |

```text
employees (E)                 departments (D)
 Heidi  dept NULL              Finance (4)  <- nobody works there
 Alice  dept 1 ...             Engineering (1) ...

INNER      : only pairs that match            (Heidi and Finance missing)
LEFT  E->D : all employees, Heidi gets NULL dept
RIGHT E->D : all departments, Finance gets NULL employee
FULL       : both Heidi and Finance appear
```

```sql
-- INNER: employees that have a department
SELECT e.name,
       d.dept_name
FROM employees e
INNER JOIN departments d
    ON d.dept_id = e.dept_id;
-- 7 rows. Heidi is missing because her dept_id is NULL.

-- LEFT: all employees, department may be NULL
SELECT e.name,
       d.dept_name
FROM employees e
LEFT JOIN departments d
    ON d.dept_id = e.dept_id;
-- 8 rows. Heidi | NULL

-- SELF JOIN: employee and manager name
SELECT e.name AS employee,
       m.name AS manager
FROM employees e
LEFT JOIN employees m
    ON m.emp_id = e.manager_id;
-- Alice | NULL, Bob | Alice, Eve | Dave, Grace | Bob ...

-- FULL OUTER JOIN emulated in MySQL
SELECT e.name,
       d.dept_name
FROM employees e
LEFT JOIN departments d
    ON d.dept_id = e.dept_id
UNION
SELECT e.name,
       d.dept_name
FROM employees e
RIGHT JOIN departments d
    ON d.dept_id = e.dept_id;
```

**Follow-up questions**

- *What is the difference between ON and WHERE in a LEFT JOIN?* ON filters rows before the outer join, so unmatched left rows are still kept. A WHERE on a right-table column runs after the join and removes the NULL rows, which turns the LEFT JOIN into an INNER JOIN. Put right-table filters in ON.
- *What happens if the join column has duplicates?* Each matching pair produces a row, so rows multiply. Joining 2 rows to 3 matching rows gives 6. This is a common cause of wrong SUM results.
- *How many rows does a CROSS JOIN of 1,000 and 1,000 rows return?* 1,000,000. Avoid it by accident (a missing join condition does this).
- *Which join algorithms does the database use?* Nested loop (good with an index on the inner table), hash join (good for large unsorted inputs; PostgreSQL, Oracle, and MySQL 8.0.18+), and merge join (both sides sorted; PostgreSQL and Oracle).

### Q2. [Basic] What is an anti-join and a semi-join?

**Level: Basic**

**Short answer:** An anti-join returns rows from A that have NO match in B (for example customers with no orders). A semi-join returns rows from A that have at least one match in B, without duplicating A rows.

**Explanation**

There are three ways to write an anti-join. LEFT JOIN with IS NULL and NOT EXISTS are safe. NOT IN breaks when the subquery returns a NULL.

```sql
-- Anti-join 1: LEFT JOIN ... IS NULL
SELECT c.customer_id,
       c.name
FROM customers c
LEFT JOIN orders o
    ON o.customer_id = c.customer_id
WHERE o.order_id IS NULL;
-- 4 Umbrella
-- 5 Hooli

-- Anti-join 2: NOT EXISTS (usually best)
SELECT c.customer_id,
       c.name
FROM customers c
WHERE NOT EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.customer_id = c.customer_id
);

-- Semi-join: customers that have at least one order (no duplicates)
SELECT c.customer_id,
       c.name
FROM customers c
WHERE EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.customer_id = c.customer_id
);
-- 1 Acme Corp, 2 Globex, 3 Initech
```

**Follow-up questions**

- *Why is NOT IN dangerous?* `x NOT IN (1, 2, NULL)` evaluates to FALSE (for 1 and 2) or UNKNOWN (for every other value), never TRUE, so it returns nothing. If the subquery column can be NULL, use NOT EXISTS.
- *EXISTS vs IN?* EXISTS stops at the first match and handles NULL safely. Modern optimizers often treat them the same, but EXISTS is the safer habit.
- *Why not use INNER JOIN for a semi-join?* If a customer has 3 orders, the join returns the customer 3 times. You would need DISTINCT.

### Q3. [Basic] What is the difference between WHERE and HAVING, and what does GROUP BY do?

**Level: Basic**

**Short answer:** WHERE filters individual rows before grouping. HAVING filters groups after aggregation. GROUP BY puts rows with the same value into one group so aggregate functions (COUNT, SUM, AVG, MIN, MAX) work per group.

**Explanation**

Every column in SELECT must be either in GROUP BY or inside an aggregate function. MySQL with `ONLY_FULL_GROUP_BY` (default since 5.7) enforces this. PostgreSQL and Oracle always enforce it.

```sql
SELECT dept_id,
       COUNT(*)    AS headcount,
       AVG(salary) AS avg_salary
FROM employees
WHERE hire_date >= '2020-01-01'   -- row filter, runs first
GROUP BY dept_id
HAVING AVG(salary) > 50000        -- group filter, runs after GROUP BY
ORDER BY avg_salary DESC;
```

Result on the practice data (Alice, hired 2019, is removed by WHERE before grouping):

| dept_id | headcount | avg_salary |
|---------|-----------|------------|
| 1 | 3 | 68333.33 |
| 2 | 2 | 52500.00 |

HR (45000) and the NULL group (Heidi, 48000) are removed by HAVING.

**Follow-up questions**

- *Can you use a column alias in HAVING?* MySQL allows it. PostgreSQL and Oracle do not. Repeat the expression to be portable.
- *Can WHERE contain an aggregate?* No. The aggregate does not exist yet when WHERE runs.
- *Is it faster to filter in WHERE or HAVING?* WHERE. It removes rows before grouping, so less data is grouped. Put non-aggregate conditions in WHERE.
- *How does GROUP BY treat NULL?* All NULLs go into one group.

### Q4. [Basic] What is the logical execution order of a SQL query?

**Level: Basic**

**Short answer:** FROM and JOIN, then WHERE, GROUP BY, HAVING, SELECT (including window functions), DISTINCT, ORDER BY, LIMIT. It is different from the order you write the clauses.

**Explanation**

```text
Written order           Logical execution order
-------------           -----------------------
SELECT                  1. FROM / JOIN    build the row set
FROM                    2. WHERE          filter rows
WHERE                   3. GROUP BY       make groups
GROUP BY                4. HAVING         filter groups
HAVING                  5. SELECT         compute columns, window functions, aliases
ORDER BY                6. DISTINCT       remove duplicates
LIMIT                   7. ORDER BY       sort
                        8. LIMIT / OFFSET take the first rows
```

This explains common errors:

- You cannot use a SELECT alias in WHERE, because WHERE runs before SELECT. (MySQL allows aliases in GROUP BY and HAVING as an extension.)
- You cannot filter on a window function in WHERE. Wrap the query in a subquery or CTE and filter outside.
- ORDER BY can use aliases because it runs after SELECT.

This is the logical order. The optimizer may physically run steps in a different order as long as the result is the same.

**Follow-up questions**

- *Why does `WHERE rn = 1` fail when `rn` is ROW_NUMBER() in the same SELECT?* Window functions are computed in SELECT, after WHERE. Use a CTE: compute `rn` inside, filter outside.
- *Does LIMIT without ORDER BY return a stable result?* No. Row order is not guaranteed without ORDER BY.

### Q5. [Basic] How does NULL behave in SQL?

**Level: Basic**

**Short answer:** NULL means "unknown or missing". Any comparison with NULL (`=`, `<>`, `<`) gives UNKNOWN, not TRUE. Use `IS NULL` and `IS NOT NULL`. Aggregates ignore NULL, except `COUNT(*)`.

**Explanation**

SQL uses three-valued logic: TRUE, FALSE, UNKNOWN. WHERE keeps only TRUE rows.

```sql
SELECT name
FROM customers
WHERE city <> 'Dhaka';
-- Globex, Hooli. Umbrella (city NULL) is NOT returned.

SELECT name
FROM customers
WHERE city <> 'Dhaka'
   OR city IS NULL;
-- Globex, Umbrella, Hooli

SELECT COUNT(*)    AS all_rows,      -- 5
       COUNT(city) AS non_null_city  -- 4
FROM customers;

SELECT name,
       COALESCE(city, 'Unknown') AS city   -- first non-NULL value
FROM customers;
```

| Function | What it does |
|----------|--------------|
| COALESCE(a, b, ...) | Returns the first non-NULL argument. Standard SQL |
| IFNULL(a, b) | MySQL, two arguments |
| NVL(a, b) | Oracle, two arguments |
| NULLIF(a, b) | Returns NULL if a = b, else a. Handy to avoid divide by zero |

**Follow-up questions**

- *Is NULL = NULL true?* No, it is UNKNOWN. Use `IS NOT DISTINCT FROM` (PostgreSQL) or `<=>` (MySQL) for NULL-safe equality.
- *Does a UNIQUE constraint allow several NULLs?* MySQL and PostgreSQL: yes. Oracle: yes for a single column, but rows with all-NULL composite keys are not compared. SQL Server allows only one.
- *How do NULLs sort?* MySQL treats NULL as the smallest value (first in ASC). PostgreSQL and Oracle treat it as the largest (last in ASC). Use `NULLS FIRST` or `NULLS LAST` in PostgreSQL and Oracle.
- *Oracle and empty strings?* Oracle treats `''` as NULL. MySQL and PostgreSQL do not.

### Q6. [Basic] What is the difference between UNION and UNION ALL?

**Level: Basic**

**Short answer:** Both stack the results of two queries. UNION removes duplicate rows (extra sort or hash step, slower). UNION ALL keeps everything and is faster. Use UNION ALL unless you need de-duplication.

**Explanation**

Both queries need the same number of columns with compatible types.

```sql
SELECT city
FROM customers
WHERE city IS NOT NULL
UNION
SELECT 'Dhaka';
-- Dhaka, Chittagong, Sylhet  (3 rows, duplicates removed)

SELECT city
FROM customers
WHERE city IS NOT NULL
UNION ALL
SELECT 'Dhaka';
-- Dhaka, Chittagong, Dhaka, Sylhet, Dhaka  (5 rows)
```

**Follow-up questions**

- *What are INTERSECT and EXCEPT?* INTERSECT returns rows in both queries. EXCEPT (MINUS in Oracle) returns rows in the first but not the second. MySQL supports them from 8.0.31.
- *Where does ORDER BY go?* Once, at the very end, and it applies to the whole combined result.

### Q7. [Basic] What is the difference between DELETE, TRUNCATE and DROP?

**Level: Basic**

**Short answer:** DELETE removes chosen rows, can use WHERE, fires triggers and can be rolled back. TRUNCATE removes all rows quickly and resets auto-increment. DROP removes the whole table (structure and data).

| | DELETE | TRUNCATE | DROP |
|---|--------|----------|------|
| Type | DML (data change) | DDL (structure change) | DDL |
| WHERE allowed | Yes | No | No |
| Removes | Chosen rows | All rows | Table and everything in it |
| Speed | Slow on big tables (row by row, writes undo and log) | Fast (deallocates storage) | Fast |
| Rollback | Yes | MySQL and Oracle: no (implicit commit). PostgreSQL: yes, it is transactional | MySQL and Oracle: no. PostgreSQL: yes |
| Row triggers fire | Yes | No | No |
| Resets auto-increment | No | Yes | Table is gone |
| Foreign keys | Respected | Blocked if another table references it (MySQL) | Blocked if referenced |

```sql
DELETE FROM orders
WHERE status = 'CANCELLED';

TRUNCATE TABLE orders;

DROP TABLE orders;
```

**Follow-up questions**

- *How do you delete 50 million rows without locking the table for hours?* Delete in batches (`DELETE ... LIMIT 10000` in a loop with commits), or partition by date and drop old partitions, or copy the rows to keep into a new table and swap.
- *Does DELETE free disk space?* Not always. InnoDB keeps the space for reuse. `OPTIMIZE TABLE` rebuilds it.
- *What is soft delete?* A `deleted_at` or `is_deleted` column instead of real deletion. It keeps history, but every query must filter it and unique constraints get harder.

### Q8. [Basic] What are views and materialized views?

**Level: Basic**

**Short answer:** A view is a saved SELECT that behaves like a table. It stores no data and runs each time. A materialized view stores the query result on disk and must be refreshed, so reads are fast but data can be stale.

**Explanation**

```sql
CREATE VIEW v_dept_stats AS
SELECT d.dept_name,
       COUNT(e.emp_id) AS headcount,
       AVG(e.salary)   AS avg_salary
FROM departments d
LEFT JOIN employees e
    ON e.dept_id = d.dept_id
GROUP BY d.dept_name;

SELECT *
FROM v_dept_stats
WHERE headcount > 1;

-- PostgreSQL / Oracle only
CREATE MATERIALIZED VIEW mv_dept_stats AS
SELECT d.dept_name,
       COUNT(e.emp_id) AS headcount
FROM departments d
LEFT JOIN employees e
    ON e.dept_id = d.dept_id
GROUP BY d.dept_name;

REFRESH MATERIALIZED VIEW mv_dept_stats;   -- PostgreSQL syntax
```

| | View | Materialized view |
|---|------|-------------------|
| Stores data | No | Yes |
| Freshness | Always current | As of last refresh |
| Read speed | Same as the underlying query | Fast (can be indexed) |
| MySQL | Supported | Not built in. Emulate with a summary table refreshed by a scheduled job |

Why use a view: hide complexity, give limited column access for security, keep a stable interface when tables change.

**Follow-up questions**

- *Can you INSERT or UPDATE through a view?* Only simple views on one table without aggregates, DISTINCT or GROUP BY are updatable.
- *Does a view make a query faster?* No. MySQL and PostgreSQL merge the view into the outer query. It is for convenience, not speed.
- *How does Oracle refresh materialized views?* COMPLETE or FAST (incremental, needs materialized view logs), on demand or on commit.

### Q9. [Basic] What is the difference between a stored procedure and a function?

**Level: Basic**

**Short answer:** A stored procedure is saved SQL logic run with CALL. It can change data, control transactions and return several outputs. A function must return a value and is usually used inside a SELECT. This is a brief topic. Oracle PL/SQL has its own file.

| | Stored procedure | Function |
|---|------------------|----------|
| Called with | `CALL proc(...)` | Inside SQL: `SELECT fn(col)` |
| Return value | Optional. Uses OUT parameters | Required, one value (a table in some databases) |
| Data changes (DML) | Yes | Often restricted |
| Transaction control | Yes (COMMIT/ROLLBACK) | Usually no |

```sql
DELIMITER //
CREATE PROCEDURE raise_salary(IN p_dept INT, IN p_pct DECIMAL(5,2))
BEGIN
    UPDATE employees
    SET salary = salary * (1 + p_pct / 100)
    WHERE dept_id = p_dept;
END //
DELIMITER ;

CALL raise_salary(1, 10);
```

**Follow-up questions**

- *Why do many teams avoid stored procedures in Spring Boot apps?* Logic is split between code and database, harder to test, version and review, and it ties you to one vendor. They are still useful for heavy set-based batch work near the data.
- *Benefits?* Fewer network round trips, cached plans, permission control.

### Q10. [Basic] What is a trigger and when should you avoid it?

**Level: Basic**

**Short answer:** A trigger is code the database runs automatically before or after INSERT, UPDATE or DELETE on a table. Use it sparingly (audit logs, simple integrity rules). It hides logic and slows bulk writes.

```sql
CREATE TRIGGER trg_salary_audit
AFTER UPDATE ON employees
FOR EACH ROW
INSERT INTO salary_audit (emp_id, old_salary, new_salary, changed_at)
VALUES (OLD.emp_id, OLD.salary, NEW.salary, NOW());
```

- BEFORE triggers can change the incoming row (`NEW`). AFTER triggers react to a change that already happened.
- `OLD` is the row before the change. `NEW` is the row after.

**Follow-up questions**

- *Downsides?* Invisible side effects, hard to debug, slow bulk loads, cascading triggers, and the logic is not in your Java code.
- *Alternatives?* Application-level auditing (Hibernate Envers, JPA `@EntityListeners`), or change data capture (reading the binlog with a tool such as Debezium).

### Q11. [Intermediate] When do you use a subquery, a CTE or a join?

**Level: Intermediate**

**Short answer:** Use a join when you need columns from both tables. Use a subquery for a simple filter or one value. Use a CTE (WITH clause) to name a step and make a multi-step query readable, or for recursion. Performance is usually the same, because the optimizer rewrites them.

**Explanation**

```sql
-- Join: need columns from both
SELECT e.name,
       d.dept_name
FROM employees e
JOIN departments d
    ON d.dept_id = e.dept_id;

-- Scalar subquery: employees paid above the average
SELECT name,
       salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);

-- Correlated subquery: paid above their own department average
SELECT e.name,
       e.salary
FROM employees e
WHERE e.salary > (
    SELECT AVG(x.salary)
    FROM employees x
    WHERE x.dept_id = e.dept_id
);

-- CTE: name the intermediate step
WITH dept_avg AS (
    SELECT dept_id,
           AVG(salary) AS avg_sal
    FROM employees
    GROUP BY dept_id
)
SELECT e.name,
       e.salary,
       d.avg_sal
FROM employees e
JOIN dept_avg d
    ON d.dept_id = e.dept_id
WHERE e.salary > d.avg_sal;
```

A correlated subquery references the outer query, so conceptually it runs once per outer row. A join to a grouped derived table (the CTE above) usually scales better.

**Follow-up questions**

- *Is a CTE materialized?* PostgreSQL 12+ inlines it unless it is referenced twice or marked `MATERIALIZED`. MySQL 8 merges it like a derived table when possible. Oracle may materialize it. Do not assume a CTE is a performance boundary.
- *What is a recursive CTE?* A CTE that refers to itself, used for trees (see the hierarchy question below). MySQL 8.0+ supports it.
- *Subquery in FROM vs CTE?* Same thing functionally. A CTE can be referenced more than once.

### Q12. [Intermediate] How do ROW_NUMBER, RANK and DENSE_RANK differ?

**Level: Intermediate**

**Short answer:** All three number rows inside a window ordered by a column. ROW_NUMBER gives unique numbers even for ties. RANK gives ties the same number and skips the next numbers. DENSE_RANK gives ties the same number and does not skip.

**Explanation**

A window function computes a value for each row using a related set of rows (the "window"). Unlike GROUP BY, it keeps every row. Syntax: `function() OVER (PARTITION BY ... ORDER BY ...)`.

- `PARTITION BY` splits rows into independent groups (optional).
- `ORDER BY` sets the order inside each group.

```sql
SELECT name,
       salary,
       ROW_NUMBER() OVER (ORDER BY salary DESC) AS row_num,
       RANK()       OVER (ORDER BY salary DESC) AS rnk,
       DENSE_RANK() OVER (ORDER BY salary DESC) AS dense_rnk
FROM employees;
```

| name | salary | row_num | rnk | dense_rnk |
|------|--------|---------|-----|-----------|
| Alice | 90000 | 1 | 1 | 1 |
| Bob | 70000 | 2 | 2 | 2 |
| Grace | 70000 | 3 | 2 | 2 |
| Carol | 65000 | 4 | 4 | 3 |
| Eve | 55000 | 5 | 5 | 4 |
| Dave | 50000 | 6 | 6 | 5 |
| Heidi | 48000 | 7 | 7 | 6 |
| Frank | 45000 | 8 | 8 | 7 |

Bob and Grace tie. With ROW_NUMBER the order between them is arbitrary, so add a tiebreaker (`ORDER BY salary DESC, emp_id`) when you need a stable result.

**Follow-up questions**

- *Which one for "second highest salary"?* DENSE_RANK, so ties do not hide the second value.
- *Which one for de-duplication?* ROW_NUMBER partitioned by the duplicate key, then keep `rn = 1`.
- *Do window functions work in MySQL?* From 8.0. Not in 5.7.
- *What is NTILE?* It splits ordered rows into N buckets (quartiles, percentiles).

### Q13. [Intermediate] How do LAG and LEAD work?

**Level: Intermediate**

**Short answer:** LAG reads a value from a previous row and LEAD reads from a following row, in the window order, without a self join. Use them for differences between consecutive rows.

```sql
SELECT order_id,
       order_date,
       amount,
       LAG(amount)  OVER (ORDER BY order_date) AS prev_amount,
       LEAD(amount) OVER (ORDER BY order_date) AS next_amount,
       amount - LAG(amount, 1, 0) OVER (ORDER BY order_date) AS change_from_prev
FROM orders;
```

| order_id | order_date | amount | prev_amount | next_amount |
|----------|------------|--------|-------------|-------------|
| 101 | 2025-01-05 | 1200.00 | NULL | 300.50 |
| 102 | 2025-01-20 | 300.50 | 1200.00 | 800.00 |
| 103 | 2025-02-02 | 800.00 | 300.50 | 50.00 |
| 104 | 2025-02-14 | 50.00 | 800.00 | 999.99 |

`LAG(col, offset, default)`: offset is how many rows back (default 1). The default value replaces NULL on the first row.

**Follow-up questions**

- *How do you do it per customer?* Add `PARTITION BY customer_id` inside OVER.
- *Typical use cases?* Month-over-month growth, time since the previous event, detecting a status change, finding gaps in sequences.

### Q14. [Intermediate] How do you calculate a running total, and what is a window frame?

**Level: Intermediate**

**Short answer:** Use `SUM(col) OVER (ORDER BY ...)`. The window frame decides which rows around the current row are included. With ORDER BY, the default frame runs from the first row up to the current row.

```sql
SELECT order_id,
       order_date,
       amount,
       SUM(amount) OVER (
           ORDER BY order_date, order_id
           ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
       ) AS running_total
FROM orders;
```

| order_id | amount | running_total |
|----------|--------|---------------|
| 101 | 1200.00 | 1200.00 |
| 102 | 300.50 | 1500.50 |
| 103 | 800.00 | 2300.50 |
| 104 | 50.00 | 2350.50 |
| 105 | 999.99 | 3350.49 |
| 106 | 450.00 | 3800.49 |
| 107 | 150.00 | 3950.49 |

Moving average of the last 3 rows: `AVG(amount) OVER (ORDER BY order_date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW)`.

**Follow-up questions**

- *ROWS vs RANGE?* ROWS counts physical rows. RANGE includes all rows with the same ORDER BY value (peers). The default frame is RANGE, so rows with the same date get the same running total. Use ROWS and a unique tiebreaker for a strict row-by-row total.
- *Running total per customer?* Add `PARTITION BY customer_id`.
- *How was this done before window functions?* A self join or a correlated subquery. It is O(n squared) and slow.

### Q15. [Intermediate] Classic queries: second highest salary, duplicates, employees with no department

**Level: Intermediate**

**Short answer:** Know one clean pattern for each. Second highest: DENSE_RANK or a subquery. Duplicates: GROUP BY with HAVING COUNT(*) > 1. No department: IS NULL filter or anti-join.

```sql
-- Second highest DISTINCT salary (window function)
SELECT DISTINCT salary
FROM (
    SELECT salary,
           DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employees
) t
WHERE rnk = 2;
-- 70000

-- Second highest without window functions
SELECT MAX(salary)
FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);
-- 70000. Returns NULL (not an error) if there is no second value

-- Nth highest with LIMIT/OFFSET (MySQL, PostgreSQL)
SELECT DISTINCT salary
FROM employees
ORDER BY salary DESC
LIMIT 1 OFFSET 1;

-- Find duplicates (salaries shared by more than one employee)
SELECT salary,
       COUNT(*) AS cnt
FROM employees
GROUP BY salary
HAVING COUNT(*) > 1;
-- 70000 | 2

-- Delete duplicates but keep the lowest id (MySQL).
-- Illustration only: here salary plays the role of the "duplicate key".
DELETE e1
FROM employees e1
JOIN employees e2
    ON e1.salary = e2.salary
   AND e1.emp_id > e2.emp_id;

-- Employees with no department
SELECT name
FROM employees
WHERE dept_id IS NULL;
-- Heidi

-- Departments with no employees
SELECT d.dept_name
FROM departments d
LEFT JOIN employees e
    ON e.dept_id = d.dept_id
WHERE e.emp_id IS NULL;
-- Finance
```

**Follow-up questions**

- *Why is `LIMIT 1 OFFSET 1` risky?* Without DISTINCT, ties return the same salary. It also returns no row if the table has too few values.
- *How do you prevent duplicates for good?* A UNIQUE constraint or unique index on the natural key.
- *Oracle syntax for top rows?* `FETCH FIRST n ROWS ONLY` (12c+) or `ROWNUM`. There is no LIMIT.

### Q16. [Intermediate] How do you get the top N rows per group?

**Level: Intermediate**

**Short answer:** Rank rows inside each group with a window function using PARTITION BY, then keep ranks up to N in an outer query.

```sql
-- Top 2 salaries in each department
SELECT dept_id,
       name,
       salary
FROM (
    SELECT dept_id,
           name,
           salary,
           DENSE_RANK() OVER (
               PARTITION BY dept_id
               ORDER BY salary DESC
           ) AS rnk
    FROM employees
) t
WHERE rnk <= 2;
```

| dept_id | name | salary |
|---------|------|--------|
| 1 | Alice | 90000 |
| 1 | Bob | 70000 |
| 1 | Grace | 70000 |
| 2 | Eve | 55000 |
| 2 | Dave | 50000 |
| 3 | Frank | 45000 |
| NULL | Heidi | 48000 |

Engineering returns 3 rows because Bob and Grace tie for rank 2. Use ROW_NUMBER if you want exactly 2 rows per group.

**Follow-up questions**

- *Without window functions (MySQL 5.7)?* A correlated subquery counting rows in the same group with a higher value: `WHERE (SELECT COUNT(*) FROM employees x WHERE x.dept_id = e.dept_id AND x.salary > e.salary) < 2`.
- *Performance on a big table?* An index on `(dept_id, salary)` lets the database read each group already sorted.
- *Latest order per customer?* Same pattern: `ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date DESC)` and keep `rn = 1`.

### Q17. [Advanced] How do you find gaps and consecutive groups (islands) in a sequence?

**Level: Advanced**

**Short answer:** For gaps, compare each value to the next with LEAD and report where the difference is more than 1. For islands (consecutive runs), subtract ROW_NUMBER from the value. Rows in one run get the same result.

```sql
-- Assume tickets.ticket_no holds: 1, 2, 3, 7, 8, 10
-- Gaps: start and end of each missing range
SELECT ticket_no + 1 AS gap_start,
       next_no - 1   AS gap_end
FROM (
    SELECT ticket_no,
           LEAD(ticket_no) OVER (ORDER BY ticket_no) AS next_no
    FROM tickets
) t
WHERE next_no - ticket_no > 1;
-- 4..6 and 9..9

-- Islands: consecutive runs
SELECT MIN(ticket_no) AS run_start,
       MAX(ticket_no) AS run_end
FROM (
    SELECT ticket_no,
           ticket_no - ROW_NUMBER() OVER (ORDER BY ticket_no) AS grp
    FROM tickets
) t
GROUP BY grp;
-- 1..3, 7..8, 10..10
```

Why it works: for 1, 2, 3 the row numbers are 1, 2, 3, so `value - rn` is 0 for all. After the gap, 7 has row number 4, so 7 - 4 = 3, and 8 - 5 = 3. Each run has its own constant.

**Follow-up questions**

- *Use case?* Login streaks, missing invoice numbers, uptime periods.
- *Duplicates in the sequence?* Use DISTINCT or DENSE_RANK first.
- *Dates instead of numbers?* Subtract the row number in days from the date (`order_date - INTERVAL rn DAY` in MySQL). Consecutive days give the same result.

### Q18. [Advanced] How do you query hierarchical data (employee to manager chain)?

**Level: Advanced**

**Short answer:** Use a recursive CTE. It has an anchor query (the starting rows) and a recursive query that joins the previous result to the table until nothing new is found.

```sql
-- All people under Alice, with their depth
WITH RECURSIVE org AS (
    SELECT emp_id,
           name,
           manager_id,
           0 AS depth
    FROM employees
    WHERE name = 'Alice'              -- anchor
    UNION ALL
    SELECT e.emp_id,
           e.name,
           e.manager_id,
           o.depth + 1
    FROM employees e
    JOIN org o
        ON e.manager_id = o.emp_id    -- recursive step
)
SELECT name,
       depth
FROM org
ORDER BY depth, name;
-- Alice 0 | Bob, Carol, Dave, Frank 1 | Eve, Grace, Heidi 2
```

Oracle also has `CONNECT BY PRIOR` for the same purpose. Oracle does not use the word RECURSIVE.

**Follow-up questions**

- *How do you stop infinite loops on cyclic data?* Track visited ids in a path column, or limit depth. MySQL has `cte_max_recursion_depth` (default 1000).
- *Other ways to store trees?* Adjacency list (the `manager_id` above), materialized path (`/1/2/7/`), nested sets, closure table. Adjacency is simplest. Closure tables make subtree queries fast.


---

## 2. Indexing and query performance

### Q19. [Basic] What is an index and why does it make queries faster?

**Level: Basic**

**Short answer:** An index is a separate sorted data structure that stores column values with pointers to the table rows. The database searches the small sorted structure instead of reading every row (a full table scan). It speeds up reads and slows down writes.

**Explanation**

Without an index, `WHERE email = 'a@x.com'` reads every row: cost grows with table size (O(n)). With an index, the database finds the value in a few steps (O(log n)). For 10 million rows that is about 3 to 4 page reads instead of millions of row checks.

```sql
CREATE INDEX idx_employees_dept
    ON employees (dept_id);

CREATE UNIQUE INDEX uq_customers_name
    ON customers (name);

SHOW INDEX FROM employees;      -- MySQL
-- Primary key and UNIQUE constraints create an index automatically.
-- In MySQL InnoDB, a FOREIGN KEY also creates an index on the column if none exists.
-- PostgreSQL does NOT index foreign key columns automatically. Add them yourself.
```

```text
Full table scan                      Index lookup
---------------                      ------------
read row 1, 2, 3, ... n              root -> branch -> leaf -> row
n row reads                          about 3-4 page reads for millions of rows
```

**Follow-up questions**

- *What is the downside of an index?* Extra disk space, and every INSERT, UPDATE and DELETE must also update every index on the table.
- *Should you index every column?* No. See the question on the cost of too many indexes.
- *Which columns should be indexed?* Columns used in WHERE, JOIN ON, ORDER BY and GROUP BY of frequent queries, and foreign key columns.

### Q20. [Intermediate] How does a B-tree index work?

**Level: Intermediate**

**Short answer:** A B-tree (really B+tree in InnoDB and PostgreSQL) is a balanced tree of sorted pages. The search starts at the root, follows pointers down through branch pages and reaches a leaf page that holds the key. Leaves are linked in order, so range scans and ORDER BY are fast. Depth stays small (usually 3 to 4 levels) even for hundreds of millions of rows.

**Explanation**

A page is a fixed-size block of disk storage (16 KB in InnoDB, 8 KB in PostgreSQL). Each page holds many keys, so the tree is wide and shallow.

```text
                      [ root page:   50 | 100 ]
                     /          |            \
          keys < 50        50 .. 99         >= 100
        [ 10 | 25 | 40 ]  [ 60 | 75 | 90 ]  [ 120 | 150 ]    <- branch pages
         /    |    |   \
   [leaf]-> [leaf]-> [leaf]-> [leaf]-> ...                   <- leaf pages, linked in order
   10,12,..  25,30,..  ...
   (key + pointer to row, or the row itself in a clustered index)

Lookup key = 75 : root (go middle) -> branch (60|75|90) -> leaf -> found. 3 page reads.
Range  60..90   : find 60 in the leaf, then walk the linked leaves to 90.
```

What a B-tree index is good at:

| Operation | Helped? | Why |
|-----------|---------|-----|
| `=` equality | Yes | Direct descent |
| `<`, `>`, `BETWEEN` | Yes | Walk the sorted leaves |
| `ORDER BY` on the indexed column | Yes | Already sorted, no sort step |
| `LIKE 'abc%'` | Yes | Prefix is a range |
| `LIKE '%abc'` | No | Cannot descend with an unknown start |
| MIN / MAX | Yes | Read the first or last leaf |

Inserts go into the right leaf. If a page is full, it splits into two, which costs time and random I/O.

**Follow-up questions**

- *B-tree vs hash index?* A hash index only supports equality and has no ordering. MySQL InnoDB uses B-trees (with an internal adaptive hash index). MySQL MEMORY engine and PostgreSQL support hash indexes.
- *Why not a binary tree?* A binary tree is deep (about 24 levels for 10 million rows). Each level may be a disk read. A B-tree packs hundreds of keys in one page, so it is only 3 or 4 levels.
- *What other index types exist?* PostgreSQL: GIN (arrays, JSONB, full text), GiST, BRIN (huge ordered tables). MySQL: FULLTEXT, SPATIAL. Oracle: bitmap indexes (low cardinality, read-heavy warehouses).
- *Why can random UUID primary keys hurt?* Inserts land in random leaf pages, cause page splits and cache misses, and secondary indexes in InnoDB get bigger. Sequential ids or time-ordered ids (UUIDv7, ULID) are better.

### Q21. [Intermediate] What is the difference between a clustered and a non-clustered index?

**Level: Intermediate**

**Short answer:** A clustered index stores the actual table rows in the leaf pages, sorted by the key, so there can be only one per table. A non-clustered (secondary) index is a separate structure whose leaves point to the row. In MySQL InnoDB the primary key is always the clustered index.

**Explanation**

```text
InnoDB clustered index (primary key = emp_id)
  leaf pages hold the FULL ROW:
  [1 Alice 90000 ...] [2 Bob 70000 ...] [3 Carol ...]

InnoDB secondary index on (salary)
  leaf = (salary, primary key value)
  [45000 -> 6] [48000 -> 8] [50000 -> 4] ...

Query: SELECT * FROM employees WHERE salary = 50000
  1. search secondary index  -> finds primary key 4
  2. search clustered index by 4 -> gets the full row     ("bookmark lookup" / double lookup)
```

| | Clustered | Non-clustered (secondary) |
|---|-----------|---------------------------|
| Leaf contains | The whole row | Indexed columns + row locator |
| How many per table | 1 | Many |
| Range scans on the key | Very fast, rows are physically together | Needs a lookup per row |
| Row locator | Not needed | InnoDB: the primary key value. PostgreSQL and Oracle: physical row address (ctid / ROWID) |

Database differences:

- MySQL InnoDB: table is the clustered index on the primary key. If there is no primary key, it picks a unique NOT NULL index or makes a hidden row id.
- PostgreSQL: tables are heaps. All indexes are secondary. The `CLUSTER` command orders the table once, but does not keep it ordered.
- Oracle: ordinary tables are heaps. An Index-Organized Table (IOT) is the clustered form.
- SQL Server: has clustered indexes like InnoDB.

**Follow-up questions**

- *Why keep the InnoDB primary key short?* Every secondary index stores a copy of the primary key. A 36-character UUID string makes every index larger.
- *Why is a covering index useful here?* It avoids the second lookup into the clustered index. See the covering index question.
- *What happens to a secondary index when the row is updated?* If an indexed column changes, the index entry is removed and re-inserted. If only other columns change, it is untouched (InnoDB).

### Q22. [Intermediate] What is a composite index and the leftmost prefix rule?

**Level: Intermediate**

**Short answer:** A composite (multi-column) index sorts by the first column, then the second within equal first values, and so on. The database can use it only for queries that filter on a leftmost prefix of its columns: the first column, or the first two, and so on.

**Explanation**

```sql
CREATE INDEX idx_emp_dept_salary_hire
    ON employees (dept_id, salary, hire_date);
```

The index entries are ordered like this:

```text
(dept_id, salary, hire_date)
 NULL, 48000, 2023-08-01       (Heidi, NULL sorts first in MySQL)
 1, 65000, 2021-01-10
 1, 70000, 2020-06-15
 1, 70000, 2023-04-18
 1, 90000, 2019-03-01
 2, 50000, 2020-09-20
 2, 55000, 2022-02-11
 3, 45000, 2021-07-05
```

| Query filter | Uses the index? | Why |
|--------------|-----------------|-----|
| `dept_id = 1` | Yes | Leftmost column |
| `dept_id = 1 AND salary > 60000` | Yes, both columns | Prefix, range on the second |
| `dept_id = 1 AND salary = 70000 AND hire_date > '2021-01-01'` | Yes, all three | Full prefix |
| `salary = 70000` | No (or full index scan) | Skips the first column. Salaries are scattered across dept groups |
| `dept_id = 1 AND hire_date > '2021-01-01'` | Partly | Uses dept_id only. hire_date is filtered afterwards |
| `dept_id > 1 AND salary = 70000` | Partly | After a range on dept_id, the later columns cannot narrow the search |

Rules for column order:

1. Equality columns first, then the range column last.
2. Among equality columns, put the most selective one first (but queries you actually run matter more).
3. A column used for ORDER BY can follow the equality columns, so the index gives sorted output without a sort step. Example: `WHERE dept_id = 1 ORDER BY salary` uses `(dept_id, salary)`.

**Follow-up questions**

- *Is `(a, b)` redundant if you already have `(a)`?* Yes. The `(a, b)` index can serve every query that `(a)` serves. Drop `(a)`.
- *Does the order of columns in the WHERE clause matter?* No. The optimizer reorders conditions. The order of columns in the index definition matters.
- *Does PostgreSQL have the same rule?* It can use a non-leading column but only by scanning the whole index, which is rarely good. Oracle has "index skip scan" for a low-cardinality leading column. MySQL 8.0 has a limited skip scan.

### Q23. [Intermediate] What is a covering index?

**Level: Intermediate**

**Short answer:** A covering index contains all the columns a query needs, so the database answers from the index alone and never reads the table rows. It is shown as `Using index` in MySQL EXPLAIN and as an Index Only Scan in PostgreSQL.

**Explanation**

```sql
-- Query
SELECT dept_id,
       salary
FROM employees
WHERE dept_id = 1;

-- Index that covers it: both columns are inside the index
CREATE INDEX idx_emp_dept_salary
    ON employees (dept_id, salary);
```

- Without the covering index: find rows in the index, then jump to the table for `salary`. One extra lookup per row.
- With it: everything comes from the index leaf pages. No table access.
- In InnoDB, secondary indexes already contain the primary key, so `SELECT emp_id, dept_id WHERE dept_id = 1` is covered by an index on `(dept_id)`.
- PostgreSQL and SQL Server also have `INCLUDE (col)` to add non-searchable payload columns: `CREATE INDEX ... ON employees (dept_id) INCLUDE (salary)`. MySQL has no INCLUDE.

**Follow-up questions**

- *Downside?* Wider indexes use more space and slow writes. Cover only your hottest queries.
- *Why does `SELECT *` kill covering indexes?* It asks for all columns, which are not all in the index.
- *PostgreSQL index-only scan caveat?* It needs the visibility map to be current (VACUUM), otherwise it still visits the table.

### Q24. [Intermediate] When is an index NOT used?

**Level: Intermediate**

**Short answer:** When the query cannot search the index in order: a function or calculation on the indexed column, a leading wildcard, a type mismatch, a missing leftmost column, an OR across unindexed columns, or when the optimizer judges a table scan cheaper (low selectivity, small table).

**Explanation**

| Case | Bad query | Fix |
|------|-----------|-----|
| Function on column | `WHERE YEAR(order_date) = 2025` | `WHERE order_date >= '2025-01-01' AND order_date < '2026-01-01'` |
| Arithmetic on column | `WHERE salary * 12 > 600000` | `WHERE salary > 50000` |
| Leading wildcard | `WHERE name LIKE '%rice'` | Use prefix `LIKE 'Ali%'`, or a FULLTEXT / trigram index |
| Implicit type conversion | `WHERE phone = 8801700000` when `phone` is VARCHAR | Compare with a string: `'8801700000'` |
| Skipping leftmost column | Index `(dept_id, salary)`, query `WHERE salary = 70000` | Add an index that starts with salary |
| `OR` on different columns | `WHERE dept_id = 1 OR name = 'Bob'` | Index both columns (index merge) or use UNION |
| Not-equal / NOT IN | `WHERE status <> 'PAID'` | Often scans, since most rows match |
| Low selectivity | `WHERE status = 'PAID'` when 90% of rows are PAID | Optimizer chooses a full scan on purpose |
| Collation / charset mismatch in a join | Joining utf8 to utf8mb4 columns | Make the column definitions identical |
| Stale statistics | Optimizer guesses wrong | `ANALYZE TABLE` (MySQL), `ANALYZE` (PostgreSQL) |
| Very small table | 50 rows | A scan is cheaper than an index. This is correct behavior |

Functional indexes can fix the function case: MySQL 8.0.13+ `CREATE INDEX i ON orders ((YEAR(order_date)))`, PostgreSQL and Oracle `CREATE INDEX i ON orders (EXTRACT(YEAR FROM order_date))`.

**Follow-up questions**

- *A query has an index but still runs a full scan. What do you check?* EXPLAIN, then the rows above: function on the column, type mismatch, leftmost rule, low selectivity, stale statistics.
- *Can you force an index?* MySQL: `FORCE INDEX (name)` or `USE INDEX`. Oracle: hints. PostgreSQL has no hints by default. Forcing is a last resort, since data changes.
- *Is a full scan always bad?* No. For a large fraction of the table, a sequential scan is faster than millions of random index lookups.

### Q25. [Intermediate] What are selectivity and cardinality, and what is the cost of too many indexes?

**Level: Intermediate**

**Short answer:** Cardinality is the number of distinct values in a column. Selectivity is distinct values divided by total rows. High selectivity (close to 1) means an index narrows the search a lot. Every extra index speeds some reads but slows every write and uses space and memory.

**Explanation**

| Column | Distinct values | Selectivity | Good index alone? |
|--------|-----------------|-------------|-------------------|
| `email` | 1,000,000 of 1,000,000 | 1.0 | Excellent |
| `customer_id` in orders | 50,000 of 1,000,000 | 0.05 | Good |
| `status` (3 values) | 3 of 1,000,000 | 0.000003 | Poor alone. Often ignored by the optimizer |
| `gender` | 2 of 1,000,000 | Very low | Poor alone |

Low-selectivity columns are still useful as a later column in a composite index, or when the value is rare (an index on `status` helps `WHERE status = 'REFUNDED'` if only 0.1% of rows are refunded).

The cost of too many indexes:

```text
One INSERT into a table with 6 indexes
  -> 1 write to the table (clustered index)
  -> 5 writes to secondary indexes
  -> each can cause a page split
  -> all of it is also written to the redo log / WAL and replicated
```

- Slower INSERT, UPDATE (of indexed columns) and DELETE.
- More disk and buffer pool memory. Hot indexes push useful data out of cache.
- Optimizer has more plans to evaluate and can pick a worse one.
- Longer replication and backup time.

How to manage it:

```sql
-- MySQL: find unused indexes (sys schema, 5.7+/8.0)
SELECT *
FROM sys.schema_unused_indexes;

-- Find redundant (prefix duplicate) indexes
SELECT *
FROM sys.schema_redundant_indexes;
```

PostgreSQL: `pg_stat_user_indexes` shows `idx_scan` per index. Zero scans means it is never used.

**Follow-up questions**

- *How do you decide to add an index?* Look at slow query log and EXPLAIN. Add one for a frequent, slow query. Measure before and after.
- *How does MySQL estimate cardinality?* From sampled statistics (`SHOW INDEX` column Cardinality). It can be inaccurate. `ANALYZE TABLE` refreshes it.
- *Write-heavy table (logs, events)?* Keep few indexes, maybe only the primary key and one time index. Index heavy reads on a replica or a separate reporting store.

### Q26. [Intermediate] How do you read an EXPLAIN plan?

**Level: Intermediate**

**Short answer:** EXPLAIN shows how the database will run the query: which tables are read in what order, which index is chosen, how many rows it expects, and extra operations like sorting. Look for full scans, large row estimates, filesort and temporary tables. `EXPLAIN ANALYZE` actually runs the query and shows real timings.

**Explanation**

```sql
EXPLAIN
SELECT e.name,
       d.dept_name
FROM employees e
JOIN departments d
    ON d.dept_id = e.dept_id
WHERE e.salary > 60000
ORDER BY e.name;
```

Key columns in MySQL EXPLAIN:

| Column | Meaning | What to look for |
|--------|---------|------------------|
| `type` | How rows are accessed, best to worst: `system`, `const`, `eq_ref`, `ref`, `range`, `index`, `ALL` | `ALL` = full table scan. `index` = full index scan. Aim for `ref`, `range` or better |
| `possible_keys` | Indexes that could be used | Empty means no usable index |
| `key` | The index actually chosen | NULL means none |
| `key_len` | Bytes of the index used | Shows how many composite columns are used |
| `rows` | Estimated rows to examine | Large numbers on a join are a red flag |
| `filtered` | Percent of rows kept after the condition | Low means lots of wasted reads |
| `Extra` | Extra info | See below |

Common `Extra` values:

| Extra | Meaning |
|-------|---------|
| `Using index` | Covering index. Good |
| `Using where` | Rows are filtered after reading |
| `Using filesort` | A separate sort step, no index gives the order. Can be slow on large sets |
| `Using temporary` | A temporary table is used (GROUP BY, DISTINCT, UNION). Can be slow |
| `Using index condition` | Index condition pushdown. Good |

PostgreSQL and Oracle differences:

- PostgreSQL: `EXPLAIN (ANALYZE, BUFFERS)` shows a node tree. Look for `Seq Scan` (full scan), `Index Scan`, `Index Only Scan`, `Bitmap Heap Scan`, `Nested Loop`, `Hash Join`, and compare estimated vs actual rows.
- Oracle: `EXPLAIN PLAN FOR ...` then `SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);`. Look for `TABLE ACCESS FULL`, `INDEX RANGE SCAN`, `INDEX UNIQUE SCAN`.
- MySQL 8.0.18+: `EXPLAIN ANALYZE` gives actual time and row counts.

**Follow-up questions**

- *The plan looks fine but the query is slow. Why?* Estimated rows may be wrong (stale statistics), the data may not be in cache, lock waits, or the result set is huge. Use EXPLAIN ANALYZE to compare estimated to actual rows.
- *Does EXPLAIN run the query?* Plain EXPLAIN does not. `EXPLAIN ANALYZE` does, so be careful with INSERT, UPDATE and DELETE.
- *What does a big gap between estimated and actual rows mean?* Bad statistics or correlated columns. Run `ANALYZE`, and consider extended statistics or histograms.

### Q27. [Intermediate] How do you paginate: OFFSET vs keyset (seek) pagination?

**Level: Intermediate**

**Short answer:** OFFSET pagination skips N rows each time, so deep pages get slower because the database still reads and throws away all skipped rows. Keyset pagination remembers the last seen key and asks for rows after it using an index, so every page costs the same.

**Explanation**

```sql
-- OFFSET: page 5000 with 20 rows per page
SELECT order_id,
       order_date,
       amount
FROM orders
ORDER BY order_id
LIMIT 20 OFFSET 99980;
-- Reads 100,000 index entries, returns 20.

-- Keyset: remember the last order_id from the previous page (say 99980)
SELECT order_id,
       order_date,
       amount
FROM orders
WHERE order_id > 99980
ORDER BY order_id
LIMIT 20;
-- Jumps straight to 99980 in the index, reads 20 rows.
```

For a sort on a non-unique column, add a unique tiebreaker and use a row comparison:

```sql
SELECT order_id,
       order_date,
       amount
FROM orders
WHERE (order_date, order_id) > ('2025-03-01', 105)
ORDER BY order_date, order_id
LIMIT 20;
-- Needs an index on (order_date, order_id)
```

| | OFFSET | Keyset |
|---|--------|--------|
| Cost at page N | Grows with N | Constant |
| Jump to page 500 | Easy | Not possible directly |
| Stable with concurrent inserts | No (rows can repeat or be skipped) | Yes |
| Good for | Small data, admin screens | Infinite scroll, APIs, big tables |

Spring Data: `Pageable` / `Page<T>` use OFFSET and run an extra COUNT query. `Slice<T>` skips the count. Spring Data 3.1+ has `ScrollPosition` for keyset scrolling.

**Follow-up questions**

- *Why is the COUNT query for total pages also slow?* `COUNT(*)` on a big table scans an index. Cache the count, estimate it, or drop total pages and show "next".
- *Oracle syntax?* `OFFSET 99980 ROWS FETCH NEXT 20 ROWS ONLY` (12c+).
- *Can the client jump to an arbitrary page with keyset?* Not directly. That is its main trade-off.

### Q28. [Intermediate] What is the N+1 problem in JPA/Hibernate and how do you fix it?

**Level: Intermediate**

**Short answer:** N+1 is when loading a list runs 1 query for the list and then N extra queries, one per row, to load a related entity or collection lazily. Fix it by fetching the relation in the same query (JOIN FETCH, EntityGraph) or in batches.

**Explanation**

```java
// 1 query: SELECT * FROM departments
List<Department> depts = departmentRepository.findAll();

for (Department d : depts) {
    // each call triggers a lazy load:
    // SELECT * FROM employees WHERE dept_id = ?
    System.out.println(d.getEmployees().size());
}
// Total: 1 + N queries (N = number of departments)
```

```text
Query 1 : SELECT * FROM departments                      -> 4 rows
Query 2 : SELECT * FROM employees WHERE dept_id = 1
Query 3 : SELECT * FROM employees WHERE dept_id = 2
Query 4 : SELECT * FROM employees WHERE dept_id = 3
Query 5 : SELECT * FROM employees WHERE dept_id = 4
```

Fixes:

```java
// 1. JOIN FETCH in JPQL (one query with a join)
@Query("SELECT DISTINCT d FROM Department d JOIN FETCH d.employees")
List<Department> findAllWithEmployees();

// 2. EntityGraph
@EntityGraph(attributePaths = "employees")
List<Department> findAll();

// 3. Batch fetching: loads collections for many parents in one IN query
@BatchSize(size = 50)
@OneToMany(mappedBy = "department")
private List<Employee> employees;
// or globally: spring.jpa.properties.hibernate.default_batch_fetch_size=50
```

| Fix | Queries | Notes |
|-----|---------|-------|
| JOIN FETCH / EntityGraph | 1 | Row multiplication with collections. Cannot fetch two List collections at once (MultipleBagFetchException) |
| `@BatchSize` | 1 + N/size | Simple and safe with pagination |
| DTO projection | 1 | Select only the columns you need. Often the best for read screens |
| `@ManyToOne` default is EAGER | | Causes N+1 with JPQL queries. Set `fetch = LAZY` on all associations |

**Follow-up questions**

- *How do you detect it?* Turn on SQL logging (`spring.jpa.show-sql`, or better, a datasource proxy), Hibernate statistics, or tests that assert the query count.
- *Why not just make everything EAGER?* EAGER loads data you may not need on every query and can still cause N+1 with JPQL. Keep LAZY and fetch explicitly per use case.
- *Why does JOIN FETCH with pagination warn?* Hibernate cannot apply LIMIT to a joined collection, so it loads everything and paginates in memory (warning HHH000104). Page the parent ids first, or use `@BatchSize`.
- *What is LazyInitializationException?* Accessing a lazy relation after the session or transaction closed. Fetch what you need inside the transaction.

### Q29. [Intermediate] Why use connection pooling and how do you size a pool?

**Level: Intermediate**

**Short answer:** Opening a database connection is expensive (network handshake, authentication, memory on the server). A pool keeps a set of open connections and lends them out. Size it small: a few dozen connections serve thousands of requests. HikariCP is the Spring Boot default.

**Explanation**

```text
Request threads (200)          Pool (10 connections)          Database
 T1 --\                         [conn1] [conn2] ...           handles 10 active
 T2 ---+-- borrow / return -->  [conn10]                      queries at a time
 ...  /   (wait if empty)
```

- Each database connection uses server memory (MySQL: a thread, buffers) and CPU for context switching. More connections than the database can run in parallel does not add throughput. It adds contention.
- A common starting formula (from the HikariCP and PostgreSQL guidance): `connections = (CPU cores x 2) + number of disks`. Often 10 to 20 is enough for one app instance. Measure.
- Total connections = pool size x number of app instances. It must stay below the database `max_connections` (MySQL default 151).

```properties
spring.datasource.hikari.maximum-pool-size=10
spring.datasource.hikari.minimum-idle=10
spring.datasource.hikari.connection-timeout=30000
spring.datasource.hikari.max-lifetime=1800000
```

**Follow-up questions**

- *What happens when the pool is exhausted?* New requests wait up to `connection-timeout`, then fail with "Connection is not available". Typical causes: slow queries, long transactions, connections held during external HTTP calls, connection leaks.
- *Why is a bigger pool often slower?* Too many concurrent queries compete for CPU, I/O and locks.
- *What is `open-in-view` in Spring Boot?* It keeps the connection tied to the whole web request (default true). It can starve the pool. Many teams set `spring.jpa.open-in-view=false`.
- *Many microservices, too many connections?* Use a server-side proxy such as ProxySQL, or PgBouncer for PostgreSQL.

### Q30. [Advanced] A query is slow in production. How do you diagnose and optimize it?

**Level: Advanced**

**Short answer:** Find the slow query (slow query log), read its EXPLAIN plan, then fix in this order: query shape, indexes, schema, and only then hardware or caching. Measure before and after.

**Explanation**

Step by step:

1. Find it. MySQL: slow query log (`long_query_time`), `performance_schema`, `sys.statement_analysis`. PostgreSQL: `pg_stat_statements`. Oracle: AWR report.
2. Run `EXPLAIN` (or `EXPLAIN ANALYZE`) on it with realistic data. Check `type`, `key`, `rows`, `Extra`.
3. Check the query:
   - Select only needed columns. Avoid `SELECT *`.
   - Filter early. Put conditions in WHERE, not HAVING.
   - No functions on indexed columns (see "when is an index not used").
   - Replace `NOT IN` and correlated subqueries with joins or EXISTS where useful.
   - Avoid `OFFSET` on deep pages. Use keyset.
   - Avoid `DISTINCT` and `UNION` when `UNION ALL` or a better join works.
   - Avoid ORs across different columns.
4. Check indexes: add a composite index matching WHERE equality columns then range then ORDER BY. Consider a covering index.
5. Check statistics: `ANALYZE TABLE`.
6. Check the schema: wrong data types (joining INT to VARCHAR), huge rows, TEXT columns fetched needlessly.
7. Reduce the work: pre-aggregate into a summary table or materialized view, cache the result, move reports to a replica.
8. Check the application: N+1 queries, many tiny queries in a loop (batch them), long transactions, missing batch inserts (`rewriteBatchedStatements=true` for MySQL JDBC).
9. Last: partition the table, shard, or scale hardware.

Before and after example:

```sql
-- Slow: function on column, SELECT *, no usable index
SELECT *
FROM orders
WHERE DATE(order_date) = '2025-03-09'
  AND status = 'PAID';

-- Fast: range on the column, only needed columns
-- plus a composite index on (status, order_date)
CREATE INDEX idx_orders_status_date
    ON orders (status, order_date);

SELECT order_id,
       customer_id,
       amount
FROM orders
WHERE status = 'PAID'
  AND order_date >= '2025-03-09'
  AND order_date <  '2025-03-10';
```

**Follow-up questions**

- *The same query is fast alone but slow under load. Why?* Lock waits, buffer pool misses, connection pool exhaustion, or a plan change. Check lock waits and cache hit ratio.
- *The query got slow after a data growth, with no code change?* Statistics changed, the optimizer switched plan, or an index no longer fits in memory.
- *How do you add an index to a 500M-row table without downtime?* MySQL 8 InnoDB online DDL (`ALGORITHM=INPLACE, LOCK=NONE`), or tools like gh-ost and pt-online-schema-change. PostgreSQL: `CREATE INDEX CONCURRENTLY`.

### Q31. [Advanced] How do you avoid filesort and temporary tables in GROUP BY and ORDER BY?

**Level: Advanced**

**Short answer:** Give the database an index that already holds the rows in the needed order. Then ORDER BY and GROUP BY read the index in order and skip the sort or the temporary table.

**Explanation**

```sql
EXPLAIN
SELECT dept_id,
       COUNT(*)
FROM employees
GROUP BY dept_id;
-- No index on dept_id: Extra = Using temporary

CREATE INDEX idx_emp_dept
    ON employees (dept_id);
-- Now the rows are read in dept_id order. No temporary table.

EXPLAIN
SELECT name,
       salary
FROM employees
WHERE dept_id = 1
ORDER BY salary DESC;
-- Index (dept_id) only: Extra = Using filesort

CREATE INDEX idx_emp_dept_salary
    ON employees (dept_id, salary);
-- Equality on dept_id, then rows are already sorted by salary: no filesort
```

Rules:

- The index columns must be: equality columns first, then the ORDER BY columns, in the same order and direction.
- Mixed directions (`ORDER BY a ASC, b DESC`) need a matching index. MySQL 8.0 supports descending index parts. Older versions cannot.
- A filesort is not always a disk sort. It is just a sort step. It is fine for small result sets, but costly for millions of rows. When memory (`sort_buffer_size`) is too small, it spills to disk.
- `ORDER BY ... LIMIT 10` on an indexed order is very cheap, because the database stops after 10 rows.

**Follow-up questions**

- *Why does `ORDER BY RAND()` hurt?* It generates a random value per row and sorts all of them. Pick random ids another way.
- *Does GROUP BY imply sorting?* In MySQL 8.0 no (older versions sorted implicitly). Add ORDER BY if you need order.


---

## 3. Transactions and locking

### Q32. [Basic] What is a transaction and what does ACID mean?

**Level: Basic**

**Short answer:** A transaction is a group of SQL statements that succeed or fail together. ACID are its four guarantees: Atomicity (all or nothing), Consistency (rules stay true), Isolation (concurrent transactions do not see each other's half-done work), Durability (committed data survives a crash).

**Explanation**

```sql
START TRANSACTION;

UPDATE accounts SET balance = balance - 500 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 500 WHERE account_id = 2;

COMMIT;      -- make both changes permanent
-- ROLLBACK; -- or undo both
```

| Letter | Meaning | How the database does it |
|--------|---------|---------------------------|
| Atomicity | All statements apply or none do | Undo log: old values are kept so a rollback can restore them |
| Consistency | Data moves from one valid state to another (constraints, foreign keys, unique keys hold) | Constraints and triggers check each change. Your business rules also matter |
| Isolation | Parallel transactions behave as if they ran in some order | Locks and MVCC (see the isolation and MVCC questions) |
| Durability | After COMMIT returns, the data is not lost if the server crashes | Write-ahead log (redo log in InnoDB, WAL in PostgreSQL) is flushed to disk before commit completes |

```text
Crash recovery:   committed but not yet in data files -> replay from redo log (durable)
                  not committed                       -> undo using undo log (atomic)
```

MySQL: InnoDB supports transactions. The old MyISAM engine does not. `autocommit` is ON by default, so each statement is its own transaction unless you start one.

**Follow-up questions**

- *What does `innodb_flush_log_at_trx_commit` do?* Value 1 (default) flushes the log to disk at every commit (full durability). 2 or 0 are faster but can lose about one second of commits on a crash.
- *What is a savepoint?* A marker inside a transaction you can roll back to without undoing the whole transaction.
- *Do NoSQL databases support ACID?* Some do (MongoDB multi-document transactions, DynamoDB transactions) but many default to weaker guarantees.

### Q33. [Intermediate] What are isolation levels and the read anomalies?

**Level: Intermediate**

**Short answer:** Isolation levels decide how much concurrent transactions can see of each other. From weakest to strongest: READ UNCOMMITTED, READ COMMITTED, REPEATABLE READ, SERIALIZABLE. Stronger levels prevent more anomalies (dirty read, non-repeatable read, phantom read) but allow less concurrency.

**Explanation**

The three anomalies:

- **Dirty read:** you read data another transaction has changed but not committed. If it rolls back, you read something that never existed.
- **Non-repeatable read:** you read the same row twice in one transaction and get different values, because another transaction committed an UPDATE in between.
- **Phantom read:** you run the same range query twice and get different sets of rows, because another transaction committed an INSERT or DELETE in between.

```text
DIRTY READ (T2 reads uncommitted data)
 T1: UPDATE acct SET bal = 0 WHERE id = 1;
 T2:                                         SELECT bal FROM acct WHERE id = 1;  -> 0   (dirty)
 T1: ROLLBACK;                               (real balance is still 1000)

NON-REPEATABLE READ (same row, different value)
 T1: SELECT bal WHERE id = 1;  -> 1000
 T2:                           UPDATE bal = 0 WHERE id = 1; COMMIT;
 T1: SELECT bal WHERE id = 1;  -> 0          (changed within T1)

PHANTOM READ (same query, different set of rows)
 T1: SELECT COUNT(*) WHERE dept_id = 1;  -> 4
 T2:                                      INSERT employee in dept 1; COMMIT;
 T1: SELECT COUNT(*) WHERE dept_id = 1;  -> 5   (a new "phantom" row)
```

What each level prevents (SQL standard):

| Level | Dirty read | Non-repeatable read | Phantom read |
|-------|-----------|---------------------|--------------|
| READ UNCOMMITTED | Possible | Possible | Possible |
| READ COMMITTED | Prevented | Possible | Possible |
| REPEATABLE READ | Prevented | Prevented | Possible (by the standard) |
| SERIALIZABLE | Prevented | Prevented | Prevented |

Actual defaults and behavior:

| Database | Default level | Notes |
|----------|---------------|-------|
| MySQL InnoDB | REPEATABLE READ | Also prevents most phantoms for plain SELECTs (snapshot) and with next-key locks for locking reads |
| PostgreSQL | READ COMMITTED | REPEATABLE READ is snapshot isolation (no phantoms). READ UNCOMMITTED behaves as READ COMMITTED. SERIALIZABLE uses SSI and may abort with a serialization failure |
| Oracle | READ COMMITTED | Only READ COMMITTED, SERIALIZABLE and READ ONLY. No dirty reads ever |

```sql
SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;   -- MySQL
SELECT @@transaction_isolation;
```

Spring: `@Transactional(isolation = Isolation.READ_COMMITTED)`.

**Follow-up questions**

- *Which level would you choose?* READ COMMITTED is a good default for most OLTP apps (less locking, fewer deadlocks). Use REPEATABLE READ or SERIALIZABLE for critical consistent reads, such as financial reports, or use explicit locks on specific rows instead.
- *What is a lost update?* Two transactions read a value, both compute a new one, and the second write overwrites the first. Fix with `UPDATE ... SET qty = qty - 1`, optimistic locking, or `SELECT ... FOR UPDATE`.
- *What is write skew?* Two transactions each read overlapping data, make decisions, and write different rows so that together they break a rule (two doctors both go off call). Snapshot isolation allows it. SERIALIZABLE prevents it.
- *Why is READ UNCOMMITTED almost never used?* Dirty reads give wrong data and the speed gain is small with MVCC.

### Q34. [Intermediate] What types of locks exist (row, table, shared, exclusive)?

**Level: Intermediate**

**Short answer:** A lock stops other transactions from conflicting access. Shared (S) locks allow many readers but block writers. Exclusive (X) locks allow one writer and block everyone else. Locks can cover a row, a page, or a whole table. InnoDB uses row-level locks to allow high concurrency.

**Explanation**

| Lock | What it does |
|------|--------------|
| Shared (S) lock | Taken for reading when you ask for it (`SELECT ... FOR SHARE`). Many transactions can hold S on the same row. Blocks X |
| Exclusive (X) lock | Taken automatically by UPDATE, DELETE, INSERT, and by `SELECT ... FOR UPDATE`. Only one holder. Blocks both S and X |
| Row lock | Locks specific rows. Others can still change other rows. InnoDB default |
| Table lock | Locks the entire table. MyISAM uses it. Also taken by DDL (`ALTER TABLE`) through metadata locks |
| Intention locks (IS, IX) | InnoDB table-level markers saying "I hold row locks inside". They let the table-level check be quick |
| Gap / next-key lock | InnoDB locks a range between index entries to block inserts there (see the gap lock question) |

Compatibility (can the second request get the lock while the first is held?):

| Held \ Requested | S | X |
|------------------|---|---|
| S | Yes | No |
| X | No | No |

```sql
-- Session 1
START TRANSACTION;
SELECT *
FROM employees
WHERE emp_id = 2
FOR UPDATE;               -- X lock on row 2

-- Session 2
UPDATE employees
SET salary = 75000
WHERE emp_id = 2;         -- waits until session 1 commits or rolls back
-- (error 1205 "Lock wait timeout exceeded" after innodb_lock_wait_timeout, default 50s)

UPDATE employees
SET salary = 80000
WHERE emp_id = 3;         -- different row: runs immediately
```

Important: InnoDB locks the index records it scans. If `WHERE` has no usable index, InnoDB can lock many rows or effectively the whole table. Always make locking statements use an index.

**Follow-up questions**

- *Do plain SELECT statements take locks?* In InnoDB (and PostgreSQL, Oracle) no. They read a snapshot using MVCC. Only locking reads (`FOR UPDATE`, `FOR SHARE`) lock.
- *What is lock escalation?* SQL Server can convert many row locks into one table lock. InnoDB and Oracle do not escalate.
- *How do you see current locks?* MySQL: `performance_schema.data_locks` and `data_lock_waits` (8.0), `SHOW ENGINE INNODB STATUS`. PostgreSQL: `pg_locks` and `pg_stat_activity`.
- *What is `SKIP LOCKED`?* `SELECT ... FOR UPDATE SKIP LOCKED` ignores rows locked by others. It is used for job queues where many workers each grab different rows (MySQL 8.0+, PostgreSQL, Oracle).

### Q35. [Intermediate] Optimistic vs pessimistic locking: what is the difference and how is each done in JPA?

**Level: Intermediate**

**Short answer:** Pessimistic locking locks the row when you read it, so nobody else can change it until you finish. Optimistic locking does not lock. It lets everyone read, and checks a version number at update time to detect that someone else changed the row first.

**Explanation**

```text
OPTIMISTIC (version column)
 T1: read row (version 5)            T2: read row (version 5)
 T1: UPDATE ... SET ..., version = 6 WHERE id = 1 AND version = 5   -> 1 row updated, OK
 T2: UPDATE ... SET ..., version = 6 WHERE id = 1 AND version = 5   -> 0 rows updated -> conflict!
     JPA throws OptimisticLockException; the caller retries or reports an error.

PESSIMISTIC (database lock)
 T1: SELECT ... WHERE id = 1 FOR UPDATE      -> lock held
 T2: SELECT ... WHERE id = 1 FOR UPDATE      -> waits until T1 commits
```

| | Optimistic | Pessimistic |
|---|-----------|-------------|
| Locks held | None while thinking | Row lock from read to commit |
| Conflict handling | Detect at write, retry | Prevented by waiting |
| Best when | Conflicts are rare, read-heavy, long user think-time | Conflicts are frequent, short critical sections (stock counters, seat booking) |
| Cost | Retries on conflict | Waiting, deadlock risk, lower throughput |

```java
// Optimistic: add a version field
@Entity
public class Product {
    @Id private Long id;
    private int stock;

    @Version
    private long version;     // Hibernate adds "AND version = ?" to every UPDATE
}

// Pessimistic: Spring Data
public interface ProductRepository extends JpaRepository<Product, Long> {
    @Lock(LockModeType.PESSIMISTIC_WRITE)       // SELECT ... FOR UPDATE
    @Query("SELECT p FROM Product p WHERE p.id = :id")
    Optional<Product> findByIdForUpdate(@Param("id") Long id);
}
```

An alternative that needs neither: an atomic update.

```sql
UPDATE products
SET stock = stock - 1
WHERE id = 10
  AND stock > 0;
-- Check affected rows: 1 = success, 0 = out of stock. No race condition.
```

**Follow-up questions**

- *How do you handle OptimisticLockException?* Retry a few times (for example with Spring Retry `@Retryable`), or return a conflict (HTTP 409) so the user can reload.
- *Does `@Version` work across several app servers?* Yes. The check happens in the database row, not in memory.
- *What is `PESSIMISTIC_READ` vs `PESSIMISTIC_WRITE`?* READ takes a shared lock (`FOR SHARE`), WRITE takes an exclusive lock (`FOR UPDATE`).
- *Which one for a ticket or seat booking system with heavy contention?* Pessimistic lock or an atomic conditional UPDATE. Optimistic would cause many retries.

### Q36. [Intermediate] What are the common `@Transactional` pitfalls in Spring Boot?

**Level: Intermediate**

**Short answer:** `@Transactional` works through a proxy, so it only applies when the call goes through the Spring bean from outside. Self-invocation, private methods, checked exceptions and long transactions are the usual causes of bugs.

**Explanation**

| Pitfall | What happens | Fix |
|---------|--------------|-----|
| Self-invocation: `this.save()` inside the same class | The proxy is bypassed, no transaction starts | Move the method to another bean, or inject self |
| `@Transactional` on a private or final method | Ignored by the proxy | Use public methods of Spring beans |
| Checked exception thrown | Spring rolls back only for RuntimeException and Error by default | `@Transactional(rollbackFor = Exception.class)` |
| Catching the exception inside the method | Spring never sees it, so it commits | Rethrow, or mark rollback only |
| Remote HTTP call inside a transaction | Connection and locks are held while waiting on the network | Do the call outside the transaction |
| Whole request is one big transaction | Long locks, pool exhaustion | Keep transactions short, scoped to the DB work |
| `REQUIRES_NEW` misunderstanding | Starts a separate transaction. Inner commit stays even if the outer rolls back. Can deadlock with the outer one | Use only where independence is needed (audit log) |
| `readOnly = true` | A hint: Hibernate skips dirty checking and the driver or router can use a replica. It does not forbid writes in all databases | Use it for query methods |

Propagation in short: `REQUIRED` (default) joins an existing transaction or starts one. `REQUIRES_NEW` always starts a new one. `SUPPORTS`, `MANDATORY`, `NOT_SUPPORTED`, `NEVER` and `NESTED` (savepoints) are rarer.

**Follow-up questions**

- *Why does a rolled-back inner method mark the outer transaction rollback-only?* With `REQUIRED` both share one transaction. When the inner method throws a RuntimeException through the proxy, the shared transaction is marked rollback-only, and the outer commit then throws `UnexpectedRollbackException`.
- *Where should `@Transactional` go?* On the service layer methods, not on controllers or repositories.

### Q37. [Advanced] What is a deadlock and how do you handle it?

**Level: Advanced**

**Short answer:** A deadlock is when two transactions each hold a lock the other needs, so both wait forever. The database detects the cycle and kills one transaction (the victim) with an error. Prevent deadlocks by locking rows in a consistent order and keeping transactions short, and retry when one happens.

**Explanation**

```text
T1: UPDATE accounts SET ... WHERE id = 1;    -- holds lock on row 1
T2: UPDATE accounts SET ... WHERE id = 2;    -- holds lock on row 2
T1: UPDATE accounts SET ... WHERE id = 2;    -- waits for T2
T2: UPDATE accounts SET ... WHERE id = 1;    -- waits for T1  -> cycle -> DEADLOCK

   T1 --waits for--> T2
    ^                 |
    +---waits for-----+
```

- InnoDB detects the cycle and rolls back the transaction with the fewest changes. The app sees `ERROR 1213 (40001): Deadlock found when trying to get lock; try restarting transaction`.
- PostgreSQL checks after `deadlock_timeout` (1s default) and aborts one with SQLSTATE `40P01`. Oracle raises `ORA-00060`.
- A lock wait timeout is different: one transaction just waits too long (`innodb_lock_wait_timeout`, error 1205). There is no cycle.

How to prevent:

1. Access rows in the same order everywhere (always lower id first).
2. Keep transactions short. Do no network calls or user waits inside them.
3. Use indexes on filtered columns, so fewer rows are locked.
4. Use lower isolation (READ COMMITTED avoids many gap locks).
5. Take all locks you need early (`SELECT ... FOR UPDATE` on both rows in sorted order).
6. Retry on deadlock errors.

```java
// Always transfer by locking the lower id first
long first  = Math.min(fromId, toId);
long second = Math.max(fromId, toId);
accountRepository.lockById(first);    // FOR UPDATE
accountRepository.lockById(second);
```

```sql
SHOW ENGINE INNODB STATUS;   -- section "LATEST DETECTED DEADLOCK" shows both statements
```

**Follow-up questions**

- *Do deadlocks mean a bug?* Occasional deadlocks in a busy system are normal. The app must retry. Frequent ones point to inconsistent lock order or missing indexes.
- *How do you log all deadlocks in MySQL?* `SET GLOBAL innodb_print_all_deadlocks = ON;`.
- *Can a single statement deadlock itself?* Not itself, but two concurrent statements touching rows and index entries in different orders can (a bulk UPDATE versus another bulk UPDATE).

### Q38. [Advanced] How does MVCC work (simple explanation)?

**Level: Advanced**

**Short answer:** MVCC (Multi-Version Concurrency Control) keeps several versions of a row. A reader sees the version that was committed when its snapshot was taken, so readers do not block writers and writers do not block readers.

**Explanation**

```text
Row id=1 balance history (each version stamped with the transaction id that wrote it)

  v1: bal=1000  (trx 10, committed)
  v2: bal=700   (trx 20, committed)
  v3: bal=0     (trx 30, NOT yet committed)

Reader trx 25 (snapshot taken after trx 20 committed, before trx 30):
  sees v2 = 700.  It ignores v3, because trx 30 was not committed at snapshot time.
```

- InnoDB: the newest row version lives in the table. Older versions are rebuilt from the undo log. A "read view" tells each transaction which versions are visible. A background purge thread removes undo records that no one needs.
- PostgreSQL: each update writes a new row version (tuple) into the table with `xmin` and `xmax` transaction ids. Dead versions are cleaned by VACUUM.
- Oracle: uses undo segments, like InnoDB, to build a read-consistent view.
- The difference between isolation levels is when the snapshot is taken:
  - READ COMMITTED: a new snapshot for every statement.
  - REPEATABLE READ: one snapshot for the whole transaction (taken at the first read).
- Writes still use locks. Two transactions updating the same row conflict. MVCC only removes read-write blocking.

**Follow-up questions**

- *What is the downside?* Old versions pile up. A very long transaction prevents cleanup: InnoDB history list grows, PostgreSQL tables bloat. Keep transactions short, and watch for idle-in-transaction sessions.
- *Why does a plain SELECT not see a row that another session updated and committed, in REPEATABLE READ?* It reads from the snapshot taken at the first read of the transaction.
- *What is PostgreSQL VACUUM?* It removes dead row versions and frees space for reuse. Autovacuum runs it in the background.

### Q39. [Advanced] What are gap locks and next-key locks, and how do phantoms relate to them in InnoDB?

**Level: Advanced**

**Short answer:** InnoDB at REPEATABLE READ locks not only the matching index records but also the gaps between them (next-key lock = record lock + gap lock). This stops other transactions from inserting into the range, which prevents phantom rows for locking reads. Gap locks can also cause surprising waits and deadlocks.

**Explanation**

```text
Index on salary:  ... 50000   55000        70000 ...
                                   |  gap  |
Transaction T1:  SELECT * FROM employees WHERE salary BETWEEN 55000 AND 70000 FOR UPDATE;

InnoDB locks: the records 55000 and 70000 and the gaps around them.
T2: INSERT employee with salary 60000  -> BLOCKED until T1 ends (prevents a phantom)
```

- Plain consistent SELECTs use the MVCC snapshot and take no locks, so they do not see phantoms either.
- Locking reads (`FOR UPDATE`), UPDATE and DELETE read the latest committed data and use next-key locks.
- Real InnoDB REPEATABLE READ can still show a phantom: if T1 runs a plain SELECT, T2 inserts a row and commits, and then T1 runs an UPDATE that matches that row, the UPDATE changes it and later SELECTs in T1 see it. Plain SELECTs alone do not show phantoms.
- Gap locking is disabled at READ COMMITTED for searches and index scans (it is still used for foreign key and duplicate-key checks), which is one reason many teams choose it to reduce deadlocks. The cost is that phantoms are possible again.
- A search on a non-unique index or a range takes gap locks. A search on a unique index with an equal match takes only a record lock.

**Follow-up questions**

- *A `SELECT ... FOR UPDATE` on a row that does not exist: what is locked?* A gap lock on the spot where it would be, so two transactions can both "check, then insert" and block or deadlock each other.
- *How do you check-then-insert safely?* A UNIQUE constraint plus `INSERT ... ON DUPLICATE KEY UPDATE` (MySQL), `ON CONFLICT DO NOTHING` (PostgreSQL), or `MERGE` (Oracle). Let the constraint decide.

### Q40. [Advanced] How do you keep consistency across several databases or services (2PC, saga, outbox)?

**Level: Advanced**

**Short answer:** A local ACID transaction only covers one database. Across services you either use two-phase commit (strong but slow and fragile), or sagas with compensating actions (eventual consistency), usually with the transactional outbox pattern to publish events reliably.

**Explanation**

- **Two-phase commit (2PC, XA):** a coordinator asks all participants to "prepare" (vote yes and hold locks). If all say yes, it tells everyone to commit. Locks are held across the network, and if the coordinator crashes, participants stay blocked. It is rarely used between microservices.
- **Saga:** split the work into local transactions, each with an undo step (compensation). If step 3 fails, run the undo steps of steps 2 and 1.

```text
Order saga
 1. Order service   : create order (PENDING)
 2. Payment service : charge card              fail -> cancel order (compensate 1)
 3. Stock service   : reserve items            fail -> refund payment (compensate 2), cancel order
 4. Order service   : mark order CONFIRMED
```

- **Transactional outbox:** the dual-write problem is that you update the database and then publish a message to Kafka, and a crash between the two loses the event. Solution: in the same local transaction, write the business row and an `outbox` row. A separate relay (or CDC tool like Debezium) reads the outbox and publishes. Delivery is at least once, so consumers must be idempotent (handling the same message twice is safe, e.g. by checking an event id).

**Follow-up questions**

- *Choreography vs orchestration sagas?* Choreography: services react to each other's events, no central controller. Orchestration: one orchestrator tells each service what to do. Orchestration is easier to follow and debug.
- *What does idempotent mean here?* Repeating the same request produces the same result, for example storing a unique request id and ignoring duplicates.
- *Does Spring `@Transactional` help across two data sources?* Not by itself. It manages one transaction manager. Chained transaction managers are best effort, not atomic.


---

## 4. Scaling: replication, partitioning, sharding, caching

### Q41. [Basic] How do backups and high availability work (brief)?

**Level: Basic**

**Short answer:** Backups protect against data loss (mistakes, corruption). High availability (HA) protects against downtime by keeping a standby that takes over. Replication is not a backup, because a bad DELETE replicates too.

**Explanation**

| Backup type | What it is | Tools |
|-------------|------------|-------|
| Logical | SQL text dump of schema and data. Portable, slow for big data | `mysqldump`, `mysqlpump`, `pg_dump` |
| Physical | Copy of the data files. Fast restore | Percona XtraBackup, MySQL Enterprise Backup, `pg_basebackup`, Oracle RMAN |
| Incremental / differential | Only changes since the last backup | XtraBackup incremental |
| Point-in-time recovery (PITR) | Restore a full backup, then replay the binlog (or WAL) up to just before a mistake | Binlog or WAL archiving |
| Snapshots | Storage or cloud volume snapshot | RDS and Aurora automated snapshots |

Key terms:

- **RPO (Recovery Point Objective):** how much data you can afford to lose (time). Backup plus PITR gives a small RPO.
- **RTO (Recovery Time Objective):** how long you can afford to be down while restoring.

```text
Mon 02:00 full backup  --> binlog continuously archived
Wed 14:32 someone runs DELETE without WHERE
Restore Mon full backup, replay binlog up to Wed 14:31:59  -> data back, only the mistake removed
```

High availability building blocks:

- A primary with one or more replicas in other availability zones.
- Automatic failover (managed service, Patroni, orchestrator, InnoDB Cluster).
- A virtual IP, DNS name or proxy so the app reconnects without config changes.
- Regular restore tests. A backup you have never restored is not proven.

**Follow-up questions**

- *Why is replication not a backup?* It copies mistakes and corruption within seconds. A delayed replica or PITR is needed to undo them.
- *Backup without locking?* InnoDB hot backups with XtraBackup, or `mysqldump --single-transaction` using a consistent snapshot.
- *How do you reduce failover downtime?* Health checks, short DNS TTL or a proxy, retry logic and a connection pool that discards broken connections in the app.

### Q42. [Intermediate] What is database replication (primary-replica)?

**Level: Intermediate**

**Short answer:** Replication copies data changes from one database server (the primary, also called master) to one or more other servers (replicas, also called slaves). Writes go to the primary. Reads can be spread across replicas. It gives read scaling and a standby copy for failover.

**Explanation**

```text
                    writes
 App  ------------------------------>  PRIMARY
  |                                        |
  |                         binary log (every change, in order)
  |                                        |
  |                      +-----------------+-----------------+
  |                      v                                   v
  |                  REPLICA 1                           REPLICA 2
  +---- reads ------>  (applies the log)  <---- reads ---- (applies the log)
```

MySQL flow:

1. The primary writes each committed change to the binary log (binlog).
2. Each replica's I/O thread downloads the binlog events into a relay log.
3. The replica's SQL thread (or several parallel worker threads) replays them.

Modes:

| Mode | How it works | Trade-off |
|------|--------------|-----------|
| Asynchronous (default) | Primary commits and replies without waiting for replicas | Fastest. Replicas can lag. A primary crash can lose the last transactions |
| Semi-synchronous | Primary waits until at least one replica has received (not applied) the change | Less data loss, a little slower |
| Synchronous / group replication | Majority of nodes must confirm before commit | Strongest, higher write latency. MySQL Group Replication, Galera, PostgreSQL synchronous standby |

Other ideas:

- **Primary-primary (multi-primary):** two nodes accept writes. Needs conflict handling. Complex.
- **Statement vs row-based binlog:** row-based (default) logs actual row changes, so it is safer than logging SQL text.
- PostgreSQL streams the WAL (write-ahead log) to standbys (physical replication) or uses logical replication per table. Oracle uses Data Guard (physical standby) and GoldenGate (logical).

Using replicas from Spring Boot: route reads to a replica with an `AbstractRoutingDataSource`, choosing the replica when the transaction is `readOnly = true`. Or use a driver that does this (MySQL Connector/J replication mode) or a proxy (ProxySQL, MaxScale).

**Follow-up questions**

- *Do replicas help with write scaling?* No. Every write still happens on the primary and is replayed on every replica. For write scaling, use sharding.
- *What happens when the primary fails?* Promote a replica to primary (failover), repoint the app and other replicas. Tools: orchestrator, MySQL InnoDB Cluster, Patroni (PostgreSQL), cloud managed failover.
- *What is split brain?* Two nodes both think they are the primary and accept writes, causing diverging data. Prevent it with fencing and a quorum (majority) before promotion.

### Q43. [Intermediate] What is replication lag and how do you deal with it?

**Level: Intermediate**

**Short answer:** Replication lag is the delay between a change committing on the primary and appearing on a replica. A user who writes and then reads from a replica can see old data. Solutions: read from the primary right after a write, use session stickiness, or wait for the replica to catch up.

**Explanation**

```text
t=0   App writes: UPDATE profile SET name='Ali'     -> PRIMARY commits
t=0.1 App reads : SELECT name FROM profile ...      -> REPLICA still has 'Old'   (stale read)
t=1.0 Replica applies the change                     -> now 'Ali'
```

Common causes: heavy write bursts, a long-running transaction replayed serially on the replica, an under-powered replica, large batch jobs, network delay, single-threaded apply.

How to measure:

```sql
SHOW REPLICA STATUS;     -- MySQL 8.0.22+ (SHOW SLAVE STATUS in older versions)
-- Look at Seconds_Behind_Source
```

PostgreSQL: `pg_stat_replication` (lag columns on the primary), `now() - pg_last_xact_replay_timestamp()` on the standby.

How to handle it:

| Technique | Idea |
|-----------|------|
| Read-your-writes | After a user writes, send that user's reads to the primary for a short window (a few seconds) |
| Route critical reads to the primary | Balance checks, order confirmation, anything that must be current |
| Wait for position | Replica waits until it applied the write's binlog position or GTID (`WAIT_FOR_EXECUTED_GTID_SET`) |
| Semi-synchronous replication | Reduces loss but does not remove apply lag |
| Parallel replication workers | `replica_parallel_workers` speeds replay |
| Keep transactions small | Large transactions delay apply |
| Accept eventual consistency | Fine for feeds, dashboards, search |

**Follow-up questions**

- *Is lag an issue for failover?* Yes. If a replica is behind when promoted, transactions that were only on the old primary are lost. Semi-sync helps.
- *Can a replica serve a report that needs exact current numbers?* No. Use the primary, or accept a stated delay.
- *How do you avoid a monitoring blind spot?* Alert on lag over a threshold (for example 5 seconds) and remove lagging replicas from the read pool automatically.

### Q44. [Intermediate] What is the difference between partitioning and sharding?

**Level: Intermediate**

**Short answer:** Partitioning splits one big table into smaller pieces inside a single database server. Sharding splits the data across multiple separate database servers. Partitioning helps manageability and query speed on one machine. Sharding is for scaling beyond one machine's capacity.

**Explanation**

```text
PARTITIONING (one server)                 SHARDING (many servers)
+-------------------------------+         +-----------+  +-----------+  +-----------+
|  DB server                    |         | Shard 1   |  | Shard 2   |  | Shard 3   |
|   orders                      |         | users     |  | users     |  | users     |
|    +- p_2024 (Jan..Dec 2024)  |         | 1..1M     |  | 1M..2M    |  | 2M..3M    |
|    +- p_2025                  |         | own CPU,  |  | own CPU,  |  | own CPU,  |
|    +- p_2026                  |         | RAM, disk |  | RAM, disk |  | RAM, disk |
+-------------------------------+         +-----------+  +-----------+  +-----------+
 same CPU, memory, disk                    each shard is a full DB; the app or a
 the DB handles it transparently           proxy decides which shard to talk to
```

| | Partitioning | Sharding |
|---|--------------|----------|
| Where data lives | Same server and same database | Different servers |
| Transparent to the app | Yes. You query the table normally | No (or only via a router). The app must know the shard |
| Main benefit | Partition pruning (read only the relevant partitions), fast bulk deletion (drop a partition), easier maintenance | More storage, CPU and write throughput |
| Joins and transactions | Normal | Cross-shard ones are hard |
| Complexity | Low | High |

Partition types:

- **Range:** by value ranges (dates). Most common.
- **List:** by explicit values (country).
- **Hash:** by a hash of a column, even spread.
- **Composite:** for example range then hash.

```sql
-- MySQL range partitioning by year
CREATE TABLE order_history (
    order_id   BIGINT NOT NULL,
    order_date DATE   NOT NULL,
    amount     DECIMAL(10,2),
    PRIMARY KEY (order_id, order_date)      -- partition column must be in every unique key
)
PARTITION BY RANGE (YEAR(order_date)) (
    PARTITION p2024 VALUES LESS THAN (2025),
    PARTITION p2025 VALUES LESS THAN (2026),
    PARTITION pmax  VALUES LESS THAN MAXVALUE
);

-- Only partition p2025 is read (partition pruning)
SELECT SUM(amount)
FROM order_history
WHERE order_date >= '2025-01-01'
  AND order_date <  '2026-01-01';

-- Remove a year instantly instead of a huge DELETE
ALTER TABLE order_history DROP PARTITION p2024;
```

Note: MySQL requires that every unique key (including the primary key) contains the partition column. PostgreSQL has declarative partitioning (`PARTITION BY RANGE`), and Oracle has partitioning as a licensed option.

**Follow-up questions**

- *When would you partition?* A very large table (hundreds of millions of rows), time-based data with a retention policy, queries that nearly always filter on the partition key.
- *Does partitioning always make queries faster?* No. A query that does not filter on the partition key must check all partitions, which can be slower than one big index.
- *When do you need sharding instead?* When one server cannot handle the data volume or the write rate, even after indexing, caching, read replicas and vertical scaling.
- *Vertical vs horizontal scaling?* Vertical: a bigger machine, simple but has a limit. Horizontal: more machines (replicas, shards).

### Q45. [Intermediate] How do you use Redis as a cache in front of a database?

**Level: Intermediate**

**Short answer:** Put Redis (an in-memory key-value store) in front of the database. The app checks Redis first. On a hit, it returns immediately. On a miss, it reads the database, stores the result in Redis with a time-to-live (TTL) and returns it. This pattern is called cache-aside.

**Explanation**

```text
            1. GET product:42
  App ---------------------------> Redis
   |  <--- hit: value ------------  (return immediately, ~1 ms or less)
   |
   |      miss: nothing found
   | 2. SELECT * FROM products WHERE id = 42   ---> Database
   | 3. SET product:42 <value> EX 300  (5 min TTL) --> Redis
   v 4. return value
```

```java
// Spring Boot cache abstraction with Redis
@Cacheable(value = "products", key = "#id")
public Product getProduct(Long id) {
    return productRepository.findById(id).orElseThrow();
}

@CacheEvict(value = "products", key = "#product.id")
public Product update(Product product) {
    return productRepository.save(product);
}
```

Caching strategies:

| Strategy | How it works | Notes |
|----------|--------------|-------|
| Cache-aside (lazy loading) | App reads cache, then DB on miss, then fills cache | Most common. Cache only holds requested data. First request is slow |
| Write-through | Write to cache and DB together | Cache always fresh, writes slower |
| Write-behind (write-back) | Write to cache, DB updated later asynchronously | Fast writes, risk of loss if the cache dies |
| Read-through | The cache layer itself loads from the DB on a miss | Simpler app code |

Problems to know:

- **Stale data:** DB changed but cache did not. Mitigate with TTL, evict or update the key on write.
- **Cache stampede (thundering herd):** a hot key expires and thousands of requests hit the DB at once. Fixes: a lock so only one request rebuilds, random jitter on TTLs, refresh ahead of expiry.
- **Cache penetration:** requests for keys that do not exist always miss and hit the DB. Fix: cache the "not found" result briefly, or use a Bloom filter.
- **Cache avalanche:** many keys expire together. Add random jitter to TTLs.
- **Consistency:** the update order DB then evict cache can still race. Accept short staleness, or use short TTLs for critical data.
- **Eviction:** when memory is full Redis removes keys by policy (`allkeys-lru`, `volatile-ttl`, and others).

**Follow-up questions**

- *What would you not cache?* Highly volatile data needing exact values (account balances), per-user data with low reuse, or huge objects.
- *Redis persistence?* Optional (RDB snapshots, AOF log). Treat the cache as disposable and the database as the source of truth.
- *Why is Redis fast?* Data is in memory, simple data structures, mostly single-threaded command execution, no disk reads on the hot path.
- *Local cache vs Redis?* A local cache (Caffeine) is fastest but each app instance has its own copy. Redis is shared and consistent across instances. Many systems use both.

### Q46. [Intermediate] What is the CAP theorem and eventual consistency?

**Level: Intermediate**

**Short answer:** CAP says that when a network partition (nodes cannot talk to each other) happens, a distributed system must choose between Consistency (every read sees the latest write) and Availability (every request gets a response). You cannot have both during a partition. Eventual consistency means replicas may disagree for a short time but will converge if no new writes arrive.

**Explanation**

- **Consistency (C):** all nodes return the same, latest data.
- **Availability (A):** every non-failing node answers every request.
- **Partition tolerance (P):** the system keeps working when messages between nodes are lost. Real networks do fail, so P is a must. The real choice is C or A when a partition happens.

```text
Two data centers, network link breaks:

   [Node A]  ---X---  [Node B]

 Choose CP: refuse requests that cannot be confirmed (errors, but never stale or conflicting data)
 Choose AP: both nodes keep answering and accepting writes (always responds, but data may differ;
            reconcile later)
```

| Style | Examples | Behavior |
|-------|----------|----------|
| CP | Single-primary relational DBs, ZooKeeper, etcd, HBase, MongoDB (default majority) | Strong consistency, may reject requests during a partition |
| AP | Cassandra, DynamoDB (default), CouchDB | Always responds, eventually consistent |

- Eventual consistency example: you update your profile, and another user in another region still sees the old name for a second.
- Tunable consistency: Cassandra and DynamoDB let you choose per query, for example quorum reads and writes (W + R > N gives a strong read).
- A single MySQL primary with async replicas: writes are consistent on the primary, reads from replicas are eventually consistent.
- PACELC extension: even without a partition, there is a trade-off between latency and consistency.

**Follow-up questions**

- *Is "CAP consistency" the same as ACID consistency?* No. CAP consistency means all nodes see the same latest value (linearizability). ACID consistency means constraints hold.
- *Is a relational database CA?* A single-node database has no partitions to tolerate. Once replicated or distributed, you must choose.
- *Where is eventual consistency fine?* Likes, view counts, feeds, search indexes. Not for payments or inventory limits.
- *How are conflicts resolved in AP systems?* Last-write-wins timestamps, vector clocks, or CRDTs (data types that merge automatically).

### Q47. [Advanced] How do you choose a shard key, and what is a hot shard?

**Level: Advanced**

**Short answer:** The shard key is the column that decides which shard stores a row. A good key has high cardinality, spreads data and traffic evenly, and appears in most queries so each query hits one shard. A hot shard is a shard that gets far more load than the others.

**Explanation**

Ways to map a key to a shard:

```text
1. Range-based:   user_id 1-1M -> shard 1, 1M-2M -> shard 2, ...
                  + range scans work, easy to reason about
                  - the newest range gets all new writes (hot shard)

2. Hash-based:    shard = hash(user_id) mod N
                  + even spread
                  - range queries hit all shards; changing N moves almost all keys

3. Directory-based: a lookup table says user 42 -> shard 3
                  + fully flexible, can move single tenants
                  - the lookup service is a dependency and must be fast and highly available
```

Shard routing example:

```text
Request: GET /users/12345
   |
   v
Router / app:  shard = hash(12345) mod 4 = 1
   |
   +--> Shard 0
   +--> Shard 1  <-- query goes here only
   +--> Shard 2
   +--> Shard 3
```

Choosing a key:

| Good key | Why |
|----------|-----|
| `user_id` / `customer_id` for user-centered apps | High cardinality, most queries filter by user, related rows (orders, addresses) can live together |
| `tenant_id` in a SaaS | Each tenant's data is together. Watch for one huge tenant |

| Bad key | Why |
|---------|-----|
| `created_at` / auto-increment id with range sharding | All new writes go to the last shard |
| `country` or `status` | Few values, very uneven (one country has most users) |
| A column not in most queries | Every query must scatter-gather across all shards |

Hot shard causes and fixes:

- A celebrity user or giant tenant: give them a dedicated shard, or split their key further (add a suffix).
- Monotonic keys on range sharding: use hash sharding, or a composite key.
- Skewed hash: more virtual shards (many small logical shards mapped to fewer servers).

**Follow-up questions**

- *Why is changing the shard key almost impossible later?* Every row must move and every query path changes. Choose carefully, based on the main access pattern.
- *How do you keep related data together?* Use the same shard key in related tables (co-location), so a user's orders live on the user's shard and joins stay local.
- *How do you generate ids across shards?* Not with per-shard auto-increment (collisions). Use Snowflake-style ids (timestamp + machine id + sequence), UUIDv7, or a central id service. Or auto-increment with different offsets per shard.

### Q48. [Advanced] How do consistent hashing and resharding work?

**Level: Advanced**

**Short answer:** With simple `hash mod N`, changing N (adding a shard) remaps almost every key. Consistent hashing places both servers and keys on a ring, so adding or removing a server moves only about 1/N of the keys. Virtual nodes smooth out the distribution.

**Explanation**

```text
Hash ring (positions 0 .. 2^32, wrapping around)

            0
        A  /   \  key k1 -> goes clockwise to the next node
      ---*-------*---  B
     /               \
    *  C              *  <- k2
     \               /
      ---*-------*---
           D

Rule: a key belongs to the first node found going clockwise from the key's position.
Add node E between A and B: only keys between A and E move to E. All other keys stay.
Remove node C: only C's keys move to the next node clockwise.
```

Comparison for 4 shards growing to 5:

| Method | Keys that move |
|--------|---------------|
| `hash(key) mod N` | About 80 percent |
| Consistent hashing | About 20 percent (1/5) |

- **Virtual nodes:** each physical server is placed at many points on the ring (for example 100 to 200). This evens out load and lets stronger servers take more points.
- Used in: Cassandra, DynamoDB, Redis Cluster (uses 16,384 fixed hash slots, a related idea), memcached client libraries.

Resharding (splitting or adding shards) in practice:

1. Create new shards. Use many logical shards from the start (for example 256 logical shards on 4 servers), so adding a server means moving whole logical shards, not re-splitting rows.
2. Copy data to the new location in the background (snapshot plus replication stream).
3. Keep both in sync, then cut over writes for that range, and verify.
4. Update the routing map. Delete old data later.
5. Do it with no downtime using dual writes or a CDC pipeline. Vitess (for MySQL) automates this.

**Follow-up questions**

- *Why is resharding painful?* Data movement, keeping writes consistent during the move, changing routing, and ids and unique constraints that were global.
- *How do you reduce the pain up front?* Many logical shards, directory-based routing, and a shard key present in all tables.
- *Is consistent hashing used for relational sharding?* Sometimes. Many systems prefer a lookup table or fixed logical shards, because they offer easy tenant moves.

### Q49. [Advanced] What are the problems with cross-shard queries, joins and transactions?

**Level: Advanced**

**Short answer:** Once data is on different servers, a query that needs several shards must run on each of them and merge the results (scatter-gather). Joins across shards, global unique constraints and multi-shard transactions are slow, complex, or not available. Design so most operations touch one shard.

**Explanation**

```text
SELECT COUNT(*) FROM orders WHERE status = 'PAID'      (status is not the shard key)

 Router --> Shard 1 : count = 120   \
        --> Shard 2 : count = 95     >--> router adds: 120 + 95 + 140 = 355
        --> Shard 3 : count = 140   /
 Slow: waits for the slowest shard, and load is multiplied by the number of shards.
```

| Problem | Why it hurts | Common answer |
|---------|--------------|---------------|
| Scatter-gather query | Latency is the slowest shard. More shards means more load per query | Include the shard key in queries. Use a separate search or analytics store for global queries |
| Cross-shard join | Rows to join are on different servers | Co-locate related tables by the same shard key. Replicate small reference tables (countries, currencies) to every shard. Join in the application |
| Global ORDER BY / LIMIT / pagination | Each shard must return its top N+offset rows, then merge | Keyset pagination, or serve from a search index |
| Cross-shard transaction | No single commit point | Design to avoid it. Otherwise saga, or 2PC (slow) |
| Global unique constraint (unique email) | Each shard enforces only its own rows | A separate lookup table keyed by email, or make email the shard key for that table |
| Aggregations (SUM, AVG) | Need combining partial results (AVG needs sum and count) | Router or analytics layer |
| Schema changes | Must run on every shard | Migration tooling and versioning |

Before sharding, try in this order: query and index tuning, caching, read replicas, vertical scaling, partitioning, archiving old data. Sharding is a last resort because of the permanent complexity. Alternatives: distributed SQL databases that shard for you (CockroachDB, TiDB, Google Spanner, YugabyteDB, Vitess for MySQL).

**Follow-up questions**

- *How do you do analytics on sharded data?* Stream changes (CDC) into a warehouse or column store (BigQuery, ClickHouse, Snowflake) and query there.
- *What is a reference table?* A small table that every shard needs (countries, product categories). Copy it to all shards, so joins stay local.
- *Application-level vs proxy sharding?* App-level (routing in your code or a library such as ShardingSphere) is flexible but spreads logic. A proxy (Vitess, ProxySQL) hides it, at the cost of another component.


---

## 5. Design: SQL vs NoSQL, keys, normalization

### Q50. [Basic] SQL vs NoSQL: what is the difference and when do you choose each?

**Level: Basic**

**Short answer:** SQL (relational) databases store data in tables with a fixed schema, support joins and ACID transactions. NoSQL databases are a group of different models (document, key-value, wide-column, graph) with flexible schemas and usually easier horizontal scaling, often with weaker consistency. Choose by data shape, access pattern and consistency needs, not by fashion.

**Explanation**

| Type | Examples | Data model | Good for |
|------|----------|-----------|----------|
| Relational (SQL) | MySQL, PostgreSQL, Oracle | Tables, rows, foreign keys | Orders, payments, inventory, anything with relations and strict correctness |
| Document | MongoDB, Couchbase | JSON documents | Catalogs, content, profiles with varying fields |
| Key-value | Redis, DynamoDB | Key to value | Cache, sessions, counters, simple fast lookups |
| Wide-column | Cassandra, HBase | Rows with flexible columns, partitioned by key | Massive write volume, time series, IoT |
| Graph | Neo4j | Nodes and edges | Social graphs, recommendations, fraud rings |
| Search | Elasticsearch | Inverted index | Full-text search, log analysis |

| | SQL | NoSQL |
|---|-----|-------|
| Schema | Fixed, enforced by the database | Flexible, often enforced in app code |
| Relationships and joins | Strong support | Limited or none. Data is often duplicated (denormalized) |
| Transactions | Full ACID across tables | Varies. Often single-document or single-key |
| Scaling | Vertical first, replicas for reads, sharding is hard | Designed to scale horizontally |
| Query flexibility | Ad hoc queries with any filter or join | Queries designed around known access patterns |
| Consistency | Strong by default | Often eventual (tunable) |

Choose SQL when: data is relational, you need transactions and constraints, queries vary (reports), the data fits on one primary plus replicas.
Choose NoSQL when: you have a huge write or data volume, the schema changes a lot, access is simple key lookups, you need low latency at massive scale, or the data is naturally a graph or document.

Most real systems use both: a relational database as the source of truth, Redis for caching, Elasticsearch for search.

**Follow-up questions**

- *Can SQL scale?* Yes. Many large systems run on sharded MySQL or PostgreSQL. It takes more effort, and distributed SQL databases (CockroachDB, Spanner, TiDB) reduce it.
- *Does NoSQL mean no schema?* The schema moves into the application. You still have a data shape, and old documents may have old formats.
- *Why not use MongoDB for a banking ledger?* Money transfers need multi-row ACID guarantees, constraints and audit. A relational database is the natural fit (though MongoDB now supports multi-document transactions).
- *What is polyglot persistence?* Using different databases for different parts of one system, each for what it does best.

### Q51. [Basic] What are primary, foreign and unique keys, and what constraints exist?

**Level: Basic**

**Short answer:** A primary key uniquely identifies each row (unique and NOT NULL, one per table). A foreign key points to the primary key of another table and keeps references valid. A unique key forbids duplicates but allows NULL. Constraints are rules the database enforces on every write.

**Explanation**

| Constraint | What it does |
|------------|--------------|
| PRIMARY KEY | Unique and NOT NULL. Identifies a row. Creates an index. Only one per table (may be composite) |
| FOREIGN KEY | Value must exist in the referenced table (or be NULL). Prevents orphan rows |
| UNIQUE | No two rows share the value. Several per table. NULLs allowed (see the NULL question) |
| NOT NULL | The column must have a value |
| CHECK | A condition must be true (`salary > 0`). MySQL enforces it from 8.0.16 |
| DEFAULT | Value used when none is given |

```sql
CREATE TABLE orders (
    order_id    INT PRIMARY KEY,
    customer_id INT NOT NULL,
    amount      DECIMAL(10,2) NOT NULL CHECK (amount >= 0),
    status      VARCHAR(20)   NOT NULL DEFAULT 'PENDING',
    order_ref   VARCHAR(30)   UNIQUE,
    CONSTRAINT fk_orders_customer
        FOREIGN KEY (customer_id)
        REFERENCES customers (customer_id)
        ON DELETE RESTRICT
        ON UPDATE CASCADE
);
```

Foreign key actions when the parent row is deleted or updated:

| Action | Meaning |
|--------|---------|
| RESTRICT / NO ACTION | Block the change if children exist (default) |
| CASCADE | Apply the change to the child rows too (deleting a customer deletes their orders) |
| SET NULL | Set the child column to NULL |
| SET DEFAULT | Set the child column to its default (not supported by InnoDB) |

Key types in design:

- **Natural key:** a real-world value (email, national id). Can change, may be long.
- **Surrogate key:** a generated value (auto-increment or UUID) with no business meaning. Stable and short. Usually preferred as primary key, with a unique constraint on the natural key.
- **Composite key:** more than one column together (`order_id, product_id` in an order-items table).

**Follow-up questions**

- *Primary key vs unique key?* A table has one primary key, never NULL. It can have many unique keys, which may allow NULL. In InnoDB the primary key is also the clustered index.
- *Do you always use foreign keys?* They protect integrity but cost a little on writes and complicate sharding and bulk loads. Many large systems skip them and enforce the rule in code. Smaller and most single-database systems should use them.
- *Auto-increment vs UUID?* Auto-increment is compact and index-friendly but guessable and awkward across shards. UUID v4 is random and bloats indexes. Time-ordered ids (UUIDv7, Snowflake) give both global uniqueness and sorted inserts.

### Q52. [Intermediate] What is normalization? Explain 1NF, 2NF, 3NF and BCNF.

**Level: Intermediate**

**Short answer:** Normalization organizes tables so each fact is stored once, which prevents update, insert and delete anomalies. 1NF: atomic values, no repeating groups. 2NF: no partial dependency on part of a composite key. 3NF: no non-key column depends on another non-key column. BCNF: every determinant is a candidate key.

**Explanation**

A table that breaks the rules:

```text
orders_flat
order_id | customer_name | customer_city | products          | product_price
---------|---------------|---------------|-------------------|--------------
 1       | Acme Corp     | Dhaka         | Pen, Notebook     | 10, 40
 2       | Acme Corp     | Dhaka         | Pen               | 10
```

Problems (anomalies): customer city is repeated, so an update must change many rows (update anomaly). A customer with no orders cannot be stored (insert anomaly). Deleting the last order loses the customer (delete anomaly).

**1NF (First normal form):** every column holds one atomic value and there are no repeating groups. Fix `products` and `product_price` lists by making one row per product in an order.

```text
order_id | product | price | customer_name | customer_city        (key: order_id + product)
 1       | Pen     | 10    | Acme Corp     | Dhaka
 1       | Notebook| 40    | Acme Corp     | Dhaka
```

**2NF:** be in 1NF, and every non-key column depends on the whole composite key, not only part of it. `price` depends only on `product`, and `customer_*` depends only on `order_id`. Split them:

```text
products(product_id PK, name, price)
orders(order_id PK, customer_name, customer_city)
order_items(order_id, product_id, quantity)    PK (order_id, product_id)
```

**3NF:** be in 2NF, and no non-key column depends on another non-key column (no transitive dependency). In `orders`, `customer_city` depends on `customer_name`, which depends on `order_id`. Split customers out:

```text
customers(customer_id PK, name, city)
orders(order_id PK, customer_id FK, order_date)
```

Rule of thumb: every non-key column depends on "the key, the whole key, and nothing but the key".

**BCNF (Boyce-Codd normal form), brief:** a stricter 3NF. For every dependency X determines Y, X must be a candidate key (a column set that could be the primary key). It matters in tables with overlapping candidate keys. Example: `(student, subject, teacher)` where each teacher teaches one subject: teacher determines subject, but teacher is not a key. Split into `(teacher, subject)` and `(student, teacher)`.

```sql
-- 3NF result for the practice data
SELECT o.order_id,
       c.name,
       c.city,
       o.amount
FROM orders o
JOIN customers c
    ON c.customer_id = o.customer_id;
```

**Follow-up questions**

- *Why normalize?* Less duplicate data, no update anomalies, smaller tables, and integrity through foreign keys.
- *What is the downside?* More tables, so more joins on reads.
- *How far do you normalize in practice?* Usually to 3NF for transactional systems, then denormalize on purpose for specific performance needs.
- *What is a functional dependency?* "A determines B" means the same A value always has the same B value. For example `customer_id` determines `city`.

### Q53. [Intermediate] What is denormalization and when do you use it?

**Level: Intermediate**

**Short answer:** Denormalization deliberately stores redundant data, or pre-computed values, to avoid expensive joins and speed up reads. The price is more storage, more complex writes, and the risk that copies get out of sync.

**Explanation**

Common forms:

| Technique | Example | Keeps in sync by |
|-----------|---------|------------------|
| Copy a column | Store `customer_name` on orders for fast display | App code on update, or accept that it is a snapshot (a historical name at order time is correct) |
| Pre-computed aggregate | `orders.total_amount` instead of summing order_items every time | Update in the same transaction, or a trigger |
| Summary table | `daily_sales(day, total)` | Scheduled job or event-driven updates |
| Materialized view | Stored result of a join | Refresh (PostgreSQL, Oracle) |
| Embedded documents | NoSQL orders with items inside one document | The write replaces the whole document |

```sql
-- Normalized: sum every time
SELECT customer_id,
       SUM(amount) AS total_spent
FROM orders
WHERE status = 'PAID'
GROUP BY customer_id;

-- Denormalized: read one stored value (kept up to date on every PAID order)
SELECT customer_id,
       total_spent
FROM customer_stats;
```

Use it when: reads far outnumber writes, a join is proven to be the bottleneck, reporting dashboards, data needs to be a historical snapshot.
Avoid it when: data changes often, correctness is critical, and you have not measured a real problem.

**Follow-up questions**

- *Normalize first, or denormalize first?* Normalize first, measure, then denormalize specific hot paths.
- *How do you prevent denormalized data drifting?* Update inside the same transaction, add a periodic reconciliation job comparing to the source, or rebuild from the source of truth.
- *Where is denormalization the norm?* Data warehouses (star schema), NoSQL, and read models in CQRS.

### Q54. [Advanced] Design questions: how do you store money, handle soft deletes, and choose IDs?

**Level: Advanced**

**Short answer:** Store money as integer minor units (cents) or DECIMAL, never FLOAT. Use soft deletes carefully with partial unique rules. Choose ids that are compact, unique across systems and roughly time-ordered.

**Explanation**

**Money**

```sql
-- Correct: exact
amount DECIMAL(19,4)        -- or BIGINT storing cents (12050 = 120.50)

-- Wrong: floating point has rounding errors
amount DOUBLE               -- 0.1 + 0.2 = 0.30000000000000004
```

Store the currency code too, and in Java use `BigDecimal`, not `double`.

**Soft delete**

```sql
ALTER TABLE customers
    ADD COLUMN deleted_at DATETIME NULL;

SELECT *
FROM customers
WHERE deleted_at IS NULL;
```

Issues: every query must filter it (Hibernate `@SQLRestriction` / `@Where`), the unique constraint on email also blocks re-registering a deleted user (PostgreSQL: partial unique index `WHERE deleted_at IS NULL`; MySQL: a unique index on `(email, deleted_at)` does not work while `deleted_at` is NULL for active rows, because NULLs never count as equal, so use a generated column that equals `email` for active rows and NULL for deleted rows, with a unique index on it), tables keep growing (archive old rows).

**Choosing IDs**

| ID type | Pros | Cons |
|---------|------|------|
| Auto-increment BIGINT | Small, ordered, fast inserts | Guessable, hard across shards or services, leaks volume |
| UUID v4 | Unique anywhere, unguessable | 16 bytes (36 as text), random inserts fragment indexes |
| UUIDv7 / ULID | Unique, time-ordered, index-friendly | Needs library support, reveals creation time |
| Snowflake id (64-bit: time + machine + sequence) | Compact, sortable, generated without the DB | Needs machine id management and clock care |

**Other common design points**

- Use the smallest sufficient data type (INT vs BIGINT, DATE vs DATETIME). Smaller rows mean more rows per page and more of the table in memory.
- Store timestamps in UTC.
- Use `utf8mb4` in MySQL (real UTF-8, includes emoji). The old `utf8` is only 3 bytes.
- Many-to-many relationships need a join table with a composite primary key.
- Audit columns: `created_at`, `updated_at`, `created_by`.
- Enums: a lookup table or a CHECK constraint is easier to change than a MySQL ENUM type.

**Follow-up questions**

- *Why is FLOAT bad for money?* It stores binary approximations, so sums drift by tiny amounts. DECIMAL stores exact decimal values.
- *How do you deal with idempotent payment requests?* Store a client-supplied idempotency key with a unique constraint, so a retry hits the constraint and returns the earlier result.
- *How do you store a hierarchy or tags?* See the hierarchy question for trees. Tags: a many-to-many join table, not a comma-separated column (which breaks 1NF and cannot be indexed).
