# SQL Notes

Workflow: Claude asks a question -> you write the query in chat -> Claude reviews -> notes for gaps go under the topic -> question and solution are added to that topic's table.
Checklist of everything to learn: [SQL_TODO.md](SQL_TODO.md)

## Index

1. [Practice Dataset](#1-practice-dataset)
   - 1.1 [Tables and data](#11-tables-and-data)
   - 1.2 [Setup script](#12-setup-script)
2. [SELECT Basics](#2-select-basics)
   - 2.1 [SELECT, WHERE, ORDER BY, LIMIT](#21-select-where-order-by-limit)
   - 2.2 [NULL handling and CASE](#22-null-handling-and-case)
   - 2.3 [Execution order](#23-execution-order)
3. [Aggregation](#3-aggregation)
   - 3.1 [GROUP BY and HAVING](#31-group-by-and-having)
4. [Joins and Set Operations](#4-joins-and-set-operations)
   - 4.1 [INNER and OUTER joins](#41-inner-and-outer-joins)
   - 4.2 [Self join](#42-self-join)
   - 4.3 [Anti-join and semi-join](#43-anti-join-and-semi-join)
   - 4.4 [UNION, INTERSECT, EXCEPT](#44-union-intersect-except)
5. [Subqueries and CTEs](#5-subqueries-and-ctes)
   - 5.1 [Subqueries](#51-subqueries)
   - 5.2 [CTEs and recursive CTEs](#52-ctes-and-recursive-ctes)
6. [Window Functions](#6-window-functions)
   - 6.1 [Ranking](#61-ranking)
   - 6.2 [LAG, LEAD, running totals](#62-lag-lead-running-totals)
7. [DDL, DML and Constraints](#7-ddl-dml-and-constraints)
8. [Indexing and Performance](#8-indexing-and-performance)
9. [Transactions and Concurrency](#9-transactions-and-concurrency)
10. [Normalization and Schema Design](#10-normalization-and-schema-design)
11. [Classic Interview Patterns](#11-classic-interview-patterns)
12. [Interview Q&A (conceptual)](#12-interview-qa-conceptual)

---

## 1. Practice Dataset

### 1.1 Tables and data

**departments**

| dept_id | dept_name |
|---------|-------------|
| 1 | Engineering |
| 2 | Sales |
| 3 | HR |
| 4 | Finance |

**employees**

| emp_id | name | salary | dept_id | manager_id | hire_date |
|--------|-------|--------|---------|------------|------------|
| 1 | Alice | 90000 | 1 | NULL | 2019-03-01 |
| 2 | Bob | 70000 | 1 | 1 | 2020-06-15 |
| 3 | Carol | 65000 | 1 | 1 | 2021-01-10 |
| 4 | Dave | 50000 | 2 | 1 | 2020-09-20 |
| 5 | Eve | 55000 | 2 | 4 | 2022-02-11 |
| 6 | Frank | 45000 | 3 | 1 | 2021-07-05 |
| 7 | Grace | 70000 | 1 | 2 | 2023-04-18 |
| 8 | Heidi | 48000 | NULL | 4 | 2023-08-01 |

**customers**

| customer_id | name | city |
|-------------|-----------|------------|
| 1 | Acme Corp | Dhaka |
| 2 | Globex | Chittagong |
| 3 | Initech | Dhaka |
| 4 | Umbrella | NULL |
| 5 | Hooli | Sylhet |

**orders** (status: PAID, PENDING, CANCELLED)

| order_id | customer_id | amount | order_date | status |
|----------|-------------|---------|------------|-----------|
| 101 | 1 | 1200.00 | 2025-01-05 | PAID |
| 102 | 1 | 300.50 | 2025-01-20 | PAID |
| 103 | 2 | 800.00 | 2025-02-02 | PENDING |
| 104 | 3 | 50.00 | 2025-02-14 | CANCELLED |
| 105 | 1 | 999.99 | 2025-03-01 | PAID |
| 106 | 3 | 450.00 | 2025-03-09 | PAID |
| 107 | 2 | 150.00 | 2025-03-15 | PAID |

### 1.2 Setup script

PostgreSQL/MySQL compatible. Run in DB Fiddle or locally.

```sql
CREATE TABLE departments (
    dept_id   INT PRIMARY KEY,
    dept_name VARCHAR(50) NOT NULL
);

CREATE TABLE employees (
    emp_id     INT PRIMARY KEY,
    name       VARCHAR(50) NOT NULL,
    salary     INT NOT NULL,
    dept_id    INT REFERENCES departments(dept_id),
    manager_id INT REFERENCES employees(emp_id),
    hire_date  DATE NOT NULL
);

CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name        VARCHAR(50) NOT NULL,
    city        VARCHAR(50)
);

CREATE TABLE orders (
    order_id    INT PRIMARY KEY,
    customer_id INT REFERENCES customers(customer_id),
    amount      DECIMAL(10,2) NOT NULL,
    order_date  DATE NOT NULL,
    status      VARCHAR(20) NOT NULL
);

INSERT INTO departments VALUES (1,'Engineering'),(2,'Sales'),(3,'HR'),(4,'Finance');

INSERT INTO employees VALUES
(1,'Alice',  90000, 1, NULL, '2019-03-01'),
(2,'Bob',    70000, 1, 1,    '2020-06-15'),
(3,'Carol',  65000, 1, 1,    '2021-01-10'),
(4,'Dave',   50000, 2, 1,    '2020-09-20'),
(5,'Eve',    55000, 2, 4,    '2022-02-11'),
(6,'Frank',  45000, 3, 1,    '2021-07-05'),
(7,'Grace',  70000, 1, 2,    '2023-04-18'),
(8,'Heidi',  48000, NULL, 4, '2023-08-01');

INSERT INTO customers VALUES
(1,'Acme Corp','Dhaka'),(2,'Globex','Chittagong'),(3,'Initech','Dhaka'),
(4,'Umbrella',NULL),(5,'Hooli','Sylhet');

INSERT INTO orders VALUES
(101,1,1200.00,'2025-01-05','PAID'),
(102,1, 300.50,'2025-01-20','PAID'),
(103,2, 800.00,'2025-02-02','PENDING'),
(104,3,  50.00,'2025-02-14','CANCELLED'),
(105,1, 999.99,'2025-03-01','PAID'),
(106,3, 450.00,'2025-03-09','PAID'),
(107,2, 150.00,'2025-03-15','PAID');
```


---

## 2. SELECT Basics

### 2.1 SELECT, WHERE, ORDER BY, LIMIT

**Notes**

- `ORDER BY` sorts ascending (`ASC`) by default. For highest first you must write `DESC`.
- `WHERE` filters rows before sorting. `> 50000` excludes exactly 50000 (Dave); use `>=` to include.
- Without `ORDER BY`, row order is not guaranteed.
- Tie-breaker: add a second sort column for stable output, e.g. `ORDER BY salary DESC, name ASC` (Bob and Grace both earn 70000).
- `LIMIT n` (MySQL/Postgres), `TOP n` (SQL Server), `FETCH FIRST n ROWS ONLY` (standard, Oracle, Postgres). `LIMIT` always comes last.
- `LIMIT` cuts exactly n rows, so ties at the boundary are dropped arbitrarily. To keep ties: `FETCH FIRST 3 ROWS WITH TIES` (Postgres 13+, Oracle), or filter with `DENSE_RANK() <= 3` in a subquery (see Window Functions).
- Only add `WHERE` if the question asks for it. Extra filters change the answer.

**Questions and Solutions**

| # | Level | Question | Solution |
|---|-------|----------|----------|
| 1 | Easy | Return `name` and `salary` of employees earning more than 50000, highest salary first. | <pre>SELECT name, salary<br>FROM employees<br>WHERE salary > 50000<br>ORDER BY salary DESC;</pre>(Result: Alice 90000, Bob 70000, Grace 70000, Carol 65000, Eve 55000) |
| 2 | Easy | Return the 3 highest paid employees (`name`, `salary`). | <pre>SELECT name, salary<br>FROM employees<br>ORDER BY salary DESC<br>LIMIT 3;</pre>(Result: Alice 90000, Bob 70000, Grace 70000. The `WHERE salary > 50000` is not needed here.) |

### 2.2 NULL handling and CASE

**Notes**

- NULL means "unknown", not zero or empty string. Any comparison with NULL (`= NULL`, `<> NULL`, `> NULL`) evaluates to UNKNOWN, and `WHERE` keeps only rows where the condition is TRUE. So `dept_id = null` returns zero rows, with no error.
- Use `IS NULL` / `IS NOT NULL`.
- `NULL = NULL` is also UNKNOWN, not TRUE.
- Three-valued logic: TRUE, FALSE, UNKNOWN. `NOT UNKNOWN` is still UNKNOWN.
- `WHERE dept_id <> 1` silently drops rows with NULL dept_id (Heidi). To include them: `WHERE dept_id <> 1 OR dept_id IS NULL`.
- `COALESCE(a, b, c, ...)` returns the first non-NULL argument. Use it to replace NULL with a default: `COALESCE(dept_id, 0)`. Works in all major databases.
- Vendor versions: `IFNULL(a, b)` (MySQL), `ISNULL(a, b)` (SQL Server), `NVL(a, b)` (Oracle). Prefer `COALESCE` since it is standard SQL and takes many arguments.
- All arguments must be a compatible type. `COALESCE(dept_id, 'None')` fails in Postgres because `dept_id` is INT; cast first: `COALESCE(CAST(dept_id AS VARCHAR), 'None')`.
- `NULLIF(a, b)` returns NULL if a = b, else a. Classic use: avoid divide by zero, `total / NULLIF(count, 0)`.
- NULLs in aggregates: `SUM`, `AVG`, `MIN`, `MAX`, `COUNT(col)` ignore NULLs. `COUNT(*)` counts all rows.
- `CASE WHEN ... THEN ... ELSE ... END` is an expression (returns a value), usable in SELECT, WHERE, ORDER BY, GROUP BY and inside aggregates.
- Conditions are checked top to bottom and the **first match wins**, so order the ranges from strictest to loosest. Reversing the two `WHEN`s above would label everyone >= 50000 as Medium.
- Without `ELSE`, unmatched rows get NULL.
- All `THEN`/`ELSE` results should be the same type.
- Two forms: searched (`CASE WHEN cond ...`) and simple (`CASE col WHEN value THEN ...`). The simple form cannot match NULL.
- `level` is a reserved word in Oracle. Prefer a safer alias like `salary_level`.
- Interview favorite: conditional aggregation, e.g. `SUM(CASE WHEN status = 'PAID' THEN amount ELSE 0 END)`.

**Questions and Solutions**

| # | Level | Question | Solution |
|---|-------|----------|----------|
| 3 | Easy | Return the `name` of employees who are not assigned to any department. | <pre>SELECT name<br>FROM employees<br>WHERE dept_id IS NULL;</pre>(Result: Heidi. Your `= null` version returns nothing.) |
| 4 | Easy | Return `name` and `dept_id` for all employees, but show 0 instead of NULL for `dept_id`. | <pre>SELECT name, COALESCE(dept_id, 0) AS dept_id<br>FROM employees;</pre>(Heidi shows 0. Learned: COALESCE returns the first non-NULL value.) |
| 5 | Easy | Return `name` and `city` of all customers, showing `'Unknown'` when the city is NULL. | <pre>SELECT name, COALESCE(city, 'Unknown') AS city<br>FROM customers;</pre>(Umbrella shows Unknown. Mistakes to avoid: wrong table, string case must match what was asked, add an alias or the column is named `coalesce`.) |
| 6 | Easy | Return `name`, `salary` and a `level` column for each employee: `'High'` if salary >= 70000, `'Medium'` if >= 50000, otherwise `'Low'`. | <pre>SELECT name, salary,<br>       CASE<br>           WHEN salary >= 70000 THEN 'High'<br>           WHEN salary >= 50000 THEN 'Medium'<br>           ELSE 'Low'<br>       END AS level<br>FROM employees;</pre>(Bob, Grace, Alice = High; Carol, Dave, Eve = Medium; Frank, Heidi = Low.) |

### 2.3 Execution order

**Notes**

---

## 3. Aggregation

### 3.1 GROUP BY and HAVING

**Notes**

- `GROUP BY col` collapses rows with the same value of `col` into one row per group. Aggregate functions (`COUNT`, `SUM`, `AVG`, `MIN`, `MAX`) are then calculated per group.
- No subquery needed for a simple count per group: the aggregate goes directly in the `SELECT` list, `GROUP BY` goes after `FROM`/`WHERE`.
- `COUNT(*)` takes `*` or a column, never a `SELECT`.
- Every column in `SELECT` must either be in `GROUP BY` or inside an aggregate function.
- `GROUP BY` puts all NULLs together in one group, so the NULL department appears as its own row.
- `WHERE` filters rows **before** grouping, so it cannot use aggregates. `HAVING` filters groups **after** aggregation, so it can. Use `WHERE` for row conditions (`salary > 50000`), `HAVING` for group conditions (`COUNT(*) > 1`). Both can be in one query: `WHERE` first, then `GROUP BY`, then `HAVING`.
- Aliases in `HAVING`: works in MySQL (and SQLite), fails in PostgreSQL, SQL Server and Oracle because `SELECT` is evaluated after `HAVING`. Repeat the expression, `HAVING COUNT(*) > 1`, to be portable.
- Template: `SELECT group_col, AGG(...) FROM table [WHERE ...] GROUP BY group_col [HAVING ...] [ORDER BY ...]`.

**Questions and Solutions**

| # | Level | Question | Solution |
|---|-------|----------|----------|
| 7 | Easy | Return each `dept_id` and the number of employees in it. Include the NULL department as its own row. | <pre>SELECT dept_id, COUNT(&#42;) AS employee_count<br>FROM employees<br>GROUP BY dept_id;</pre>(Result: 1 = 4, 2 = 2, 3 = 1, NULL = 1) |
| 8 | Easy | Return the `dept_id` of departments that have more than 1 employee, with the employee count. | <pre>SELECT dept_id, COUNT(&#42;) AS employee_count<br>FROM employees<br>GROUP BY dept_id<br>HAVING COUNT(&#42;) > 1;</pre>(Result: 1 = 4, 2 = 2. Portable form repeats the aggregate in HAVING instead of using the alias.) |

---

## 4. Joins and Set Operations

### 4.1 INNER and OUTER joins

**Notes**

- `INNER JOIN` keeps only rows that match in both tables. `LEFT JOIN` keeps **all** rows from the left table and fills the right side with NULL when there is no match. `RIGHT JOIN` is the mirror image. `FULL JOIN` keeps unmatched rows from both sides.
- Rule of thumb: put the table whose rows you must not lose on the **left** of a `LEFT JOIN`.
- Comma join (`FROM a, b WHERE a.x = b.x`) is old syntax for an inner join. It is easy to accidentally create a cartesian product by forgetting the `WHERE`, and it cannot express outer joins. Use explicit `JOIN ... ON`.
- Heidi's `dept_id` is NULL, and `NULL = anything` is UNKNOWN, so she can never match in an inner join. That is why the `WHERE e.dept_id = d.dept_id` version drops her.
- When both tables have the same column name (`dept_id`), qualify it with a table alias (`e.dept_id`) or the database raises an "ambiguous column" error.
- `COALESCE` after an outer join fills the NULLs from unmatched rows, but it only works if it wraps the column from the **right** side (`d.dept_name`), and the replacement must be the same type.
- Join type changes the row count: INNER gives 7 rows here, LEFT gives 8.
- **`ON` vs `WHERE` with a LEFT JOIN** (very common interview trap). `ON` decides which rows match during the join; `WHERE` filters the result afterwards.
  - `LEFT JOIN d ON ... WHERE d.dept_name = 'Sales'` returns only Sales employees (2 rows). Unmatched rows have `d.dept_name = NULL`, the filter is not TRUE, so they are removed. The LEFT JOIN silently behaves like an INNER JOIN.
  - `LEFT JOIN d ON e.dept_id = d.dept_id AND d.dept_name = 'Sales'` returns all 8 employees, but only Sales employees get a `dept_name`; everyone else gets NULL.
  - `LEFT JOIN d ON ... WHERE d.dept_id IS NULL` is the one useful WHERE on the right table: it keeps only unmatched rows (anti-join, Heidi only).
  - Rule: condition on the **right** table that should not remove left rows goes in `ON`; condition on the **left** table, or an `IS NULL` anti-join check, goes in `WHERE`.
  - For `INNER JOIN`, `ON` and `WHERE` give identical results, so it is only a style choice.

**Questions and Solutions**

| # | Level | Question | Solution |
|---|-------|----------|----------|
| 9 | Medium | Return each employee's `name` and `dept_name`. Heidi has no department but must still appear, with NULL for the department name. | <pre>SELECT e.name, d.dept_name<br>FROM employees e<br>LEFT JOIN departments d<br>    ON e.dept_id = d.dept_id;</pre>(8 rows. Heidi's dept_name is NULL. Use `COALESCE(d.dept_name, 'Not assigned')` if a label is wanted.) |

### 4.2 Self join

**Notes**

**Questions and Solutions**

| # | Level | Question | Solution |
|---|-------|----------|----------|
| 13 | Medium | Return each employee's `name` and their manager's name as `manager_name`. Employees without a manager must still appear. | _pending_ |

### 4.3 Anti-join and semi-join

**Notes**

- **Anti-join** = rows in A with **no** match in B ("customers with no orders"). Three ways:
  - `LEFT JOIN ... WHERE b.key IS NULL`
  - `WHERE NOT EXISTS (SELECT 1 FROM b WHERE b.key = a.key)`
  - `WHERE a.key NOT IN (SELECT key FROM b)`, which breaks if the subquery returns any NULL (the whole result becomes empty). Prefer the first two.
- The `IS NULL` test must be on a column from the **right** table that is never NULL for real matches (its primary key, e.g. `o.order_id`).
- A plain (inner / comma) join can never produce unmatched rows, so an `IS NULL` check on top of it always returns nothing. You need `LEFT JOIN`.
- Use one alias consistently: if you write `customers e`, you must refer to `e.name`, not `c.name`.
- **Semi-join** = rows in A that have **at least one** match in B ("customers with a PAID order"). Use `WHERE EXISTS (SELECT 1 FROM b WHERE b.key = a.key AND ...)`. It stops at the first match, so it never duplicates A's rows. Alternatives: `IN (subquery)`, or `JOIN` plus `DISTINCT`.
- A plain `JOIN` repeats the left row once per match (one-to-many fan-out): Acme would appear 3 times for its 3 PAID orders. Fix with `DISTINCT`, `GROUP BY`, or `EXISTS`.
- `LEFT JOIN ... WHERE right.col IS NOT NULL` is just an inner join written the long way. Use `INNER JOIN` (or `JOIN`) when you only want matches.
- **Join + aggregate:** join first, filter with `WHERE`, then `GROUP BY`, then aggregate. Group by the key (`c.customer_id`) as well as the name, so two customers with the same name are not merged.
- `ORDER BY` can use a `SELECT` alias (`total_paid`) because it runs after `SELECT`. `WHERE` and `HAVING` cannot (in most databases). An alias is one word: `total_paid`, not `total paid`.
- Before submitting, re-read the query against the question: exact output column names, table names, and typos like `c_customer_id` instead of `c.customer_id`. A query that does not run earns nothing.
- `order` is a reserved word (`ORDER BY`), and the table here is named `orders`. Read table names from the dataset. Filters like "PAID" must be written (`status = 'PAID'`), since the query is not allowed to assume them.

**Questions and Solutions**

| # | Level | Question | Solution |
|---|-------|----------|----------|
| 10 | Medium | Return the `name` of customers who have never placed an order. | <pre>SELECT c.name<br>FROM customers c<br>LEFT JOIN orders o<br>    ON c.customer_id = o.customer_id<br>WHERE o.order_id IS NULL;</pre>(Result: Umbrella, Hooli. Alternative: `WHERE NOT EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id)`.) |
| 11 | Medium | Return the `name` of customers who have at least one `PAID` order. Each customer should appear once. | **Approach 1: EXISTS (semi-join)**<pre>SELECT c.name<br>FROM customers c<br>WHERE EXISTS (<br>    SELECT 1<br>    FROM orders o<br>    WHERE o.customer_id = c.customer_id<br>      AND o.status = 'PAID'<br>);</pre>**Approach 2: join to a deduplicated derived table**<pre>SELECT c.name<br>FROM customers c<br>JOIN (<br>    SELECT DISTINCT customer_id<br>    FROM orders<br>    WHERE status = 'PAID'<br>) paid<br>    ON c.customer_id = paid.customer_id;</pre>(Result: Acme Corp, Globex, Initech. Other options: `SELECT DISTINCT c.name ... JOIN orders ...`, or `WHERE c.customer_id IN (SELECT customer_id FROM orders WHERE status = 'PAID')`.) |
| 12 | Medium | Return each customer `name` with the total `amount` of their `PAID` orders as `total_paid`, highest first. Only customers with PAID orders. | <pre>SELECT c.name, SUM(o.amount) AS total_paid<br>FROM customers c<br>JOIN orders o<br>    ON c.customer_id = o.customer_id<br>WHERE o.status = 'PAID'<br>GROUP BY c.customer_id, c.name<br>ORDER BY total_paid DESC;</pre>(Result: Acme Corp 2500.49, Initech 450.00, Globex 150.00.) |

### 4.4 UNION, INTERSECT, EXCEPT

**Notes**

**Questions and Solutions**

| # | Level | Question | Solution |
|---|-------|----------|----------|

---

## 5. Subqueries and CTEs

### 5.1 Subqueries

**Notes**

**Questions and Solutions**

| # | Level | Question | Solution |
|---|-------|----------|----------|

### 5.2 CTEs and recursive CTEs

**Notes**

**Questions and Solutions**

| # | Level | Question | Solution |
|---|-------|----------|----------|

---

## 6. Window Functions

### 6.1 Ranking

**Notes**

**Questions and Solutions**

| # | Level | Question | Solution |
|---|-------|----------|----------|

### 6.2 LAG, LEAD, running totals

**Notes**

**Questions and Solutions**

| # | Level | Question | Solution |
|---|-------|----------|----------|

---

## 7. DDL, DML and Constraints

**Notes**

**Questions and Solutions**

| # | Level | Question | Solution |
|---|-------|----------|----------|

---

## 8. Indexing and Performance

**Notes**

**Questions and Solutions**

| # | Level | Question | Solution |
|---|-------|----------|----------|

---

## 9. Transactions and Concurrency

**Notes**

**Questions and Solutions**

| # | Level | Question | Solution |
|---|-------|----------|----------|

---

## 10. Normalization and Schema Design

**Notes**

**Questions and Solutions**

| # | Level | Question | Solution |
|---|-------|----------|----------|

---

## 11. Classic Interview Patterns

Nth highest salary, top N per group, duplicates, running totals, gaps and islands, etc.

**Questions and Solutions**

| # | Level | Question | Solution |
|---|-------|----------|----------|

---

## 12. Interview Q&A (conceptual)

| # | Question | Answer |
|---|----------|--------|
