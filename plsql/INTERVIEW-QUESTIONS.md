# Oracle PL/SQL Interview Questions

Interview prep for a Java backend developer (3+ years) who is new to PL/SQL. Every question has a short answer first, then a simple explanation, code, and likely follow-up questions.

Examples use the same `employees` table as `PLSQL.md`:

```sql
CREATE TABLE employees (
    id            NUMBER PRIMARY KEY,
    name          VARCHAR2(50),
    salary        NUMBER,
    department_id NUMBER
);

-- Data used in the examples:
-- 1, 'Rahim', 50000, 10
-- 2, 'Karim', 60000, 10
-- 3, 'Sumi',  45000, 20
```

General SQL and database topics (joins, indexes, transactions theory, sharding) are in a separate file. This file covers PL/SQL and Oracle-specific topics only.

Code in this file was written for study. It was not run while writing it, so test examples in your own database. Version-specific claims are marked with the Oracle version.

---

## Index

- [Topic 1: Basics, Blocks and Transactions](#topic-1)
  - Basic
    - [B1. What is PL/SQL and why use it?](#b1)
    - [B2. What is the structure of a PL/SQL block?](#b2)
    - [B3. Anonymous block vs named block?](#b3)
    - [B4. How do you declare variables and constants?](#b4)
    - [B5. What are %TYPE and %ROWTYPE?](#b5)
    - [B6. Control flow and loops in PL/SQL](#b6)
    - [B7. SELECT INTO and its exceptions](#b7)
  - Intermediate
    - [I1. COMMIT, ROLLBACK and SAVEPOINT in PL/SQL](#i1)
    - [I2. Collections: associative arrays, nested tables, VARRAYs](#i2)
  - Advanced
    - [A1. Autonomous transactions](#a1)
- [Topic 2: Cursors and Exceptions](#topic-2)
  - Basic
    - [B8. Implicit vs explicit cursors](#b8)
    - [B9. Cursor attributes](#b9)
    - [B10. What is a cursor FOR loop?](#b10)
    - [B11. Predefined exceptions and basic exception handling](#b11)
  - Intermediate
    - [I3. Parameterized cursors](#i3)
    - [I4. REF CURSOR and SYS_REFCURSOR](#i4)
    - [I5. FOR UPDATE and WHERE CURRENT OF](#i5)
    - [I6. User-defined exceptions, RAISE_APPLICATION_ERROR, PRAGMA EXCEPTION_INIT](#i6)
    - [I7. SQLCODE, SQLERRM and exception propagation](#i7)
  - Advanced
    - [A2. Exception handling best practices](#a2)
- [Topic 3: Procedures, Functions, Packages and Triggers](#topic-3)
  - Basic
    - [B12. Procedure vs function](#b12)
    - [B13. IN, OUT and IN OUT parameters](#b13)
  - Intermediate
    - [I8. When to use a function vs a procedure, and calling a function from SQL](#i8)
    - [I9. Packages: spec, body and why use them](#i9)
    - [I10. Package state](#i10)
    - [I11. Overloading](#i11)
    - [I12. Triggers: BEFORE/AFTER, row vs statement level](#i12)
    - [I13. :NEW and :OLD](#i13)
    - [I14. What is the mutating table error?](#i14)
  - Advanced
    - [A3. Compound triggers](#a3)
    - [A4. Definer rights vs invoker rights](#a4)
- [Topic 4: Performance and Advanced Topics](#topic-4)
  - Basic
    - [B14. What is DBMS_OUTPUT?](#b14)
  - Intermediate
    - [I15. Calling PL/SQL from Java with CallableStatement](#i15)
    - [I16. Running jobs with DBMS_SCHEDULER](#i16)
  - Advanced
    - [A5. What is BULK COLLECT and why is it faster?](#a5)
    - [A6. BULK COLLECT with LIMIT](#a6)
    - [A7. What is FORALL?](#a7)
    - [A8. FORALL with SAVE EXCEPTIONS](#a8)
    - [A9. Dynamic SQL and EXECUTE IMMEDIATE](#a9)
    - [A10. SQL injection and DBMS_ASSERT](#a10)
    - [A11. How do you tune PL/SQL performance?](#a11)
    - [A12. Pipelined table functions](#a12)

---

<a id="topic-1"></a>

# Topic 1: Basics, Blocks and Transactions

---

## Basic questions (Topic 1)

---

<a id="b1"></a>

### B1. What is PL/SQL and why use it?

**Level: Basic**

**Short answer:** PL/SQL (Procedural Language extension to SQL) is Oracle's programming language that runs inside the database. It adds variables, IF, loops, error handling and stored code on top of SQL.

**Explanation:**

- SQL describes what data you want. It has no loops or variables.
- PL/SQL lets you write logic around SQL statements and keep that logic in the database.
- The PL/SQL engine runs procedural code. It sends each SQL statement to the separate SQL engine.

Why use it:

| Benefit | What it means |
|---------|---------------|
| Fewer network round trips | One call from the application runs many statements inside the database |
| Reusable stored code | Procedures, functions and packages are saved once and called by many applications |
| Tight SQL integration | SQL types and table columns map directly to PL/SQL variables |
| Error handling | `EXCEPTION` blocks catch runtime errors |
| Security | You can grant `EXECUTE` on a procedure without granting access to the tables |

```text
Application (Java)           Database
+-----------+   1 call    +--------------------------+
|           | ----------> | PL/SQL block             |
|           |             |   SQL 1                  |
|           |             |   SQL 2                  |
|           | <---------- |   SQL 3                  |
+-----------+   1 result  +--------------------------+

Without PL/SQL: 3 calls and 3 results over the network.
```

**Follow-up questions**

- **Is PL/SQL case sensitive?** No for keywords and identifiers. Text values inside quotes are case sensitive.
- **Where is PL/SQL code stored?** Named code is stored in the database and visible in `USER_SOURCE`. Anonymous blocks are not stored.
- **What are the downsides?** Business logic moves into the database, which makes version control, testing and database portability harder. Heavy logic in the database also uses database CPU, which is hard to scale out.
- **What is the difference between PL/SQL and T-SQL?** T-SQL is the Microsoft SQL Server equivalent. PL/SQL is Oracle only.

---

<a id="b2"></a>

### B2. What is the structure of a PL/SQL block?

**Level: Basic**

**Short answer:** A block has up to four parts: `DECLARE` (optional), `BEGIN` (required), `EXCEPTION` (optional) and `END;`. Only `BEGIN ... END;` is required.

**Explanation:**

```sql
DECLARE
    v_count NUMBER := 0;
BEGIN
    SELECT COUNT(*)
    INTO v_count
    FROM employees;

    DBMS_OUTPUT.PUT_LINE('Rows: ' || v_count);
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Error: ' || SQLERRM);
END;
/
-- Output (with the sample data): Rows: 3
```

| Part | Required | What it does |
|------|----------|--------------|
| `DECLARE` | No | Declares variables, constants, cursors and local subprograms |
| `BEGIN` | Yes | Holds the executable statements |
| `EXCEPTION` | No | Handles errors raised in the `BEGIN` section |
| `END;` | Yes | Closes the block. The `;` is required |

- The `/` on its own line is a SQL*Plus / SQL Developer command that runs the block. It is not PL/SQL syntax.
- Blocks can be nested. An inner block can have its own `EXCEPTION` section.
- In a stored procedure or function, you do not write `DECLARE`. The part between `AS`/`IS` and `BEGIN` is the declaration section.

**Follow-up questions**

- **Can a block have no executable statement?** No. You need at least one statement. Use `NULL;` as a placeholder.
- **Can you nest blocks?** Yes. Nested blocks give local variable scope and a place for local error handling.
- **Does the `EXCEPTION` section catch errors from the `DECLARE` section?** No. An error while initializing a variable is raised to the enclosing block. See [I7](#i7).

---

<a id="b3"></a>

### B3. Anonymous block vs named block?

**Level: Basic**

**Short answer:** An anonymous block has no name, is not stored in the database and is compiled each time it runs. A named block (procedure, function, package, trigger) is stored in the database and can be called by name.

**Explanation:**

| Item | Anonymous block | Named block |
|------|-----------------|-------------|
| Has a name | No | Yes |
| Stored in the database | No | Yes |
| Can take parameters | No (a client can send bind variables) | Yes |
| Can be called from other code | No | Yes |
| Typical use | Testing, one-off scripts, quick checks | Application logic |

Named block types:

| Type | What it is |
|------|-----------|
| Procedure | Stored block that performs an action |
| Function | Stored block that returns one value |
| Package | A group of procedures, functions, variables and cursors |
| Trigger | A block that fires automatically on an event |

A **local subprogram** is a procedure or function declared inside another block's `DECLARE` section. It exists only while that block runs:

```sql
DECLARE
    FUNCTION double_it(p_n NUMBER) RETURN NUMBER IS
    BEGIN
        RETURN p_n * 2;
    END;
BEGIN
    DBMS_OUTPUT.PUT_LINE(double_it(21));
END;
/
-- Output: 42
```

**Follow-up questions**

- **How do you see compile errors in a stored procedure?** Run `SHOW ERRORS` in SQL*Plus, or query `USER_ERRORS`.
- **How do you see the source of a stored procedure?** Query `USER_SOURCE` for the name.
- **What happens when a stored procedure becomes INVALID?** It is recompiled automatically on the next call if possible. It becomes invalid when an object it depends on changes.

---

<a id="b4"></a>

### B4. How do you declare variables and constants?

**Level: Basic**

**Short answer:** Declare them in the `DECLARE` section as `name datatype [:= value]`. Use `CONSTANT` for a value that must never change.

**Explanation:**

```sql
DECLARE
    v_name    VARCHAR2(50)  := 'Rahim';     -- text
    v_salary  NUMBER(10,2)  := 50000;       -- number with 2 decimals
    v_active  BOOLEAN       := TRUE;        -- PL/SQL only type
    v_today   DATE          := SYSDATE;     -- current date and time
    c_tax     CONSTANT NUMBER := 0.15;      -- cannot be changed
    v_bonus   NUMBER NOT NULL := 0;         -- NOT NULL needs an initial value
    v_unset   NUMBER;                       -- default is NULL
BEGIN
    DBMS_OUTPUT.PUT_LINE(v_salary * c_tax);
END;
/
-- Output: 7500
```

Rules:

- `:=` assigns a value. `=` compares.
- An uninitialized variable is `NULL`.
- A `CONSTANT` or `NOT NULL` variable must be initialized when declared.
- `DEFAULT` can be used in place of `:=` when declaring.
- Scope: a variable is visible in its block and in nested blocks. An inner variable with the same name hides the outer one.

A common trap is naming a variable the same as a column. In SQL inside the block, the column name wins:

```sql
DECLARE
    name VARCHAR2(50) := 'Rahim';
BEGIN
    DELETE FROM employees
    WHERE name = name;      -- compares the column to itself: matches ALL rows
END;
/
```

Use a prefix such as `v_` for variables and `p_` for parameters to avoid this.

**Follow-up questions**

- **Can a table column be BOOLEAN?** Not in older Oracle versions. A PL/SQL variable can be BOOLEAN, but a table column cannot. Oracle 23ai adds a SQL `BOOLEAN` type (version-specific).
- **What is the default value of a variable?** `NULL`.
- **What does `v_x NUMBER NOT NULL;` do without a value?** It fails to compile. A `NOT NULL` variable needs an initial value.

---

<a id="b5"></a>

### B5. What are %TYPE and %ROWTYPE?

**Level: Basic**

**Short answer:** `%TYPE` gives a variable the same data type as a column or another variable. `%ROWTYPE` gives a record variable that has the same fields as a table row or cursor row.

**Explanation:**

```sql
DECLARE
    v_salary employees.salary%TYPE;     -- same type as the column
    v_emp    employees%ROWTYPE;         -- one whole row
BEGIN
    SELECT *
    INTO v_emp
    FROM employees
    WHERE id = 1;

    v_salary := v_emp.salary;
    DBMS_OUTPUT.PUT_LINE(v_emp.name || ' earns ' || v_salary);
END;
/
-- Output: Rahim earns 50000
```

| Attribute | Gives you | Access fields with |
|-----------|-----------|--------------------|
| `%TYPE` | One variable with a column's type | the variable name |
| `%ROWTYPE` | A record with all columns of a row | `record.column_name` |

Why use them:

- If the column type changes (for example `VARCHAR2(50)` to `VARCHAR2(100)`), your code adapts without edits.
- It avoids `VALUE_ERROR` from a variable that is too small.

**Follow-up questions**

- **Does `%TYPE` copy the NOT NULL constraint?** No. It copies the data type and size. In `%ROWTYPE` also, constraints are not copied.
- **Can `%ROWTYPE` be based on a cursor?** Yes: `r my_cursor%ROWTYPE;`.
- **Can you insert a whole record?** Yes: `INSERT INTO employees VALUES v_emp;` inserts the record. Updating with `SET ROW = v_emp` is also allowed.

---

<a id="b6"></a>

### B6. Control flow and loops in PL/SQL

**Level: Basic**

**Short answer:** PL/SQL has `IF / ELSIF / ELSE`, `CASE`, and three loops: basic `LOOP`, `WHILE`, and `FOR`. Use `EXIT` to leave a loop and `CONTINUE` to skip to the next iteration.

**Explanation:**

```sql
DECLARE
    v_salary NUMBER := 55000;
BEGIN
    IF v_salary >= 60000 THEN
        DBMS_OUTPUT.PUT_LINE('High');
    ELSIF v_salary >= 45000 THEN
        DBMS_OUTPUT.PUT_LINE('Medium');
    ELSE
        DBMS_OUTPUT.PUT_LINE('Low');
    END IF;
END;
/
-- Output: Medium
```

Loops:

```sql
BEGIN
    -- FOR: fixed range. The counter i is declared automatically.
    FOR i IN 1..3 LOOP
        DBMS_OUTPUT.PUT_LINE('for ' || i);
    END LOOP;

    -- FOR in reverse
    FOR i IN REVERSE 1..3 LOOP
        DBMS_OUTPUT.PUT_LINE('rev ' || i);
    END LOOP;
END;
/
-- Output: for 1, for 2, for 3, rev 3, rev 2, rev 1 (one per line)
```

```sql
DECLARE
    i NUMBER := 0;
BEGIN
    -- WHILE: test first, then run
    WHILE i < 3 LOOP
        i := i + 1;
        CONTINUE WHEN i = 2;               -- skip the rest for i = 2 (Oracle 11g and later)
        DBMS_OUTPUT.PUT_LINE('while ' || i);
    END LOOP;

    -- Basic LOOP: runs until EXIT
    i := 0;
    LOOP
        i := i + 1;
        EXIT WHEN i > 2;
        DBMS_OUTPUT.PUT_LINE('loop ' || i);
    END LOOP;
END;
/
-- Output: while 1, while 3, loop 1, loop 2 (one per line)
```

| Loop | Use when |
|------|----------|
| `FOR` | You know the range, or you loop over a query result |
| `WHILE` | You test a condition before each pass |
| Basic `LOOP` | You need to run at least once and exit from the middle (manual cursor fetch) |

**Follow-up questions**

- **What happens when an `IF` condition is NULL?** The condition is treated as not true, so the `ELSE` branch runs. Use `IS NULL` to test for NULL.
- **Difference between a `CASE` statement and a `CASE` expression?** A statement runs code (`END CASE;`). An expression returns a value (`END`) and can be used inside SQL.
- **Can you change the FOR loop counter inside the loop?** No. It is read-only.
- **Is there a `GOTO`?** Yes, but avoid it.

---

<a id="b7"></a>

### B7. SELECT INTO and its exceptions

**Level: Basic**

**Short answer:** `SELECT ... INTO` copies a query result into variables. It must return exactly one row. Zero rows raise `NO_DATA_FOUND`. More than one row raises `TOO_MANY_ROWS`.

**Explanation:**

```sql
DECLARE
    v_name employees.name%TYPE;
BEGIN
    SELECT name
    INTO v_name
    FROM employees
    WHERE department_id = 10;     -- Rahim and Karim: two rows

    DBMS_OUTPUT.PUT_LINE(v_name);
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('No row found');
    WHEN TOO_MANY_ROWS THEN
        DBMS_OUTPUT.PUT_LINE('More than one row found');
END;
/
-- Output: More than one row found
```

| Rows returned | Result |
|---------------|--------|
| 0 | `NO_DATA_FOUND` (ORA-01403) |
| 1 | Works |
| 2 or more | `TOO_MANY_ROWS` (ORA-01422) |

Important cases:

- A query with an aggregate and no `GROUP BY` always returns one row, so `NO_DATA_FOUND` is not raised. `SELECT MAX(salary) INTO v FROM employees WHERE id = 999;` gives `v = NULL`.
- To handle "may not exist", wrap the `SELECT INTO` in its own small block and catch `NO_DATA_FOUND` there.

```sql
DECLARE
    v_salary NUMBER;
BEGIN
    BEGIN
        SELECT salary
        INTO v_salary
        FROM employees
        WHERE id = 999;
    EXCEPTION
        WHEN NO_DATA_FOUND THEN
            v_salary := 0;           -- default value, then continue
    END;

    DBMS_OUTPUT.PUT_LINE(v_salary);
END;
/
-- Output: 0
```

**Follow-up questions**

- **How do you read many rows?** Use a cursor (see [B8](#b8)) or `BULK COLLECT INTO` (see [A5](#a5)).
- **Does `BULK COLLECT INTO` raise `NO_DATA_FOUND`?** No. It returns an empty collection.
- **Does `SELECT INTO` in a function called from SQL behave differently?** `NO_DATA_FOUND` raised inside a function called from SQL can silently turn into a NULL result in some cases, so handle it explicitly inside the function.

---

## Intermediate questions (Topic 1)

---

<a id="i1"></a>

### I1. COMMIT, ROLLBACK and SAVEPOINT in PL/SQL

**Level: Intermediate**

**Short answer:** A transaction starts at the first DML statement and ends with `COMMIT` or `ROLLBACK`. `SAVEPOINT` marks a point inside the transaction. `ROLLBACK TO savepoint_name` undoes only the work after that point.

**Explanation:**

```sql
BEGIN
    INSERT INTO employees (id, name, salary, department_id)
    VALUES (4, 'Hasan', 40000, 20);

    SAVEPOINT after_hasan;

    INSERT INTO employees (id, name, salary, department_id)
    VALUES (5, 'Nila', 52000, 20);

    ROLLBACK TO after_hasan;          -- undoes only Nila

    COMMIT;                           -- saves Hasan
END;
/
-- Result: employee 4 exists, employee 5 does not.
```

| Statement | Effect |
|-----------|--------|
| `COMMIT` | Makes the changes permanent and releases locks |
| `ROLLBACK` | Undoes all changes since the last commit |
| `SAVEPOINT name` | Marks a point in the transaction |
| `ROLLBACK TO name` | Undoes changes after the savepoint. The transaction stays open |

A common pattern in a procedure:

```sql
CREATE OR REPLACE PROCEDURE transfer_raise(
    p_from_id IN NUMBER,
    p_to_id   IN NUMBER,
    p_amount  IN NUMBER
) AS
BEGIN
    SAVEPOINT start_transfer;

    UPDATE employees
    SET salary = salary - p_amount
    WHERE id = p_from_id;

    UPDATE employees
    SET salary = salary + p_amount
    WHERE id = p_to_id;
EXCEPTION
    WHEN OTHERS THEN
        ROLLBACK TO start_transfer;
        RAISE;                         -- let the caller know
END transfer_raise;
/
```

Good practice: let the **caller** (the code that owns the whole unit of work) decide when to `COMMIT`. A procedure that commits by itself cannot be combined with other work in one transaction.

**Follow-up questions**

- **What commits automatically?** DDL statements (`CREATE`, `ALTER`, `DROP`, `TRUNCATE`) issue an implicit commit before and after.
- **Does a failed statement roll back the whole transaction?** No. Only that statement is rolled back (statement-level atomicity). Earlier statements in the transaction stay.
- **Should you `COMMIT` inside a loop?** Usually not. It is slower and breaks atomicity. It can also cause `ORA-01555 snapshot too old` and `ORA-01002` with `FOR UPDATE` cursors. If you must, commit in large batches.
- **How do Java and PL/SQL transactions interact?** JDBC defaults to auto-commit. Turn it off (`conn.setAutoCommit(false)`) when a transaction calls several procedures.

---

<a id="i2"></a>

### I2. Collections: associative arrays, nested tables, VARRAYs

**Level: Intermediate**

**Short answer:** A collection is a list of elements of the same type held in memory. PL/SQL has three kinds: associative arrays (key-value, PL/SQL only), nested tables (unbounded, can be stored in a database column) and VARRAYs (fixed maximum size, ordered).

**Explanation:**

| Item | Associative array | Nested table | VARRAY |
|------|-------------------|--------------|--------|
| Declared as | `TABLE OF t INDEX BY PLS_INTEGER` (or `VARCHAR2`) | `TABLE OF t` | `VARRAY(n) OF t` |
| Index | Number or string, can be sparse | Number from 1, can become sparse after `DELETE` | Number from 1, always dense |
| Maximum size | None | None | Fixed (`n`) |
| Needs initialization | No | Yes (constructor) | Yes (constructor) |
| Can be a table column | No | Yes | Yes |
| Can be used in SQL (`TABLE(...)`) | No | Yes (when a schema-level type) | Yes (when a schema-level type) |
| Typical use | In-memory lookup, bulk processing | Pass sets of data, store a list in a column | Small, ordered, bounded list |

```sql
DECLARE
    -- associative array: key is the employee id (text) in this example
    TYPE t_salary_map IS TABLE OF NUMBER INDEX BY VARCHAR2(20);
    v_map t_salary_map;

    -- nested table
    TYPE t_names IS TABLE OF VARCHAR2(50);
    v_names t_names := t_names('Rahim', 'Karim');       -- initialized with a constructor

    -- varray, maximum 3 elements
    TYPE t_top3 IS VARRAY(3) OF VARCHAR2(50);
    v_top t_top3 := t_top3('A', 'B');
BEGIN
    v_map('Rahim') := 50000;
    v_map('Sumi')  := 45000;
    DBMS_OUTPUT.PUT_LINE(v_map('Sumi'));                -- 45000

    v_names.EXTEND;                                     -- add an empty slot
    v_names(3) := 'Sumi';
    DBMS_OUTPUT.PUT_LINE(v_names.COUNT);                -- 3

    v_top.EXTEND;
    v_top(3) := 'C';
    DBMS_OUTPUT.PUT_LINE(v_top.LIMIT);                  -- 3
END;
/
-- Output:
-- 45000
-- 3
-- 3
```

Collection methods:

| Method | What it does |
|--------|--------------|
| `COUNT` | Number of elements |
| `EXISTS(i)` | TRUE if element `i` exists |
| `FIRST` / `LAST` | Lowest / highest index |
| `NEXT(i)` / `PRIOR(i)` | Next / previous existing index (use these for sparse collections) |
| `EXTEND` | Add elements (nested table and VARRAY) |
| `DELETE` / `DELETE(i)` | Remove all / one element |
| `TRIM` | Remove elements from the end (nested table and VARRAY) |
| `LIMIT` | Maximum size (VARRAY only) |

Common errors:

| Error | Cause |
|-------|-------|
| `COLLECTION_IS_NULL` | Using a nested table or VARRAY that was never initialized |
| `SUBSCRIPT_BEYOND_COUNT` | Index past the end, without `EXTEND` |
| `NO_DATA_FOUND` | Reading an associative array key that was never set |

**Follow-up questions**

- **Which one do you use with `BULK COLLECT`?** Any of them. Associative arrays and nested tables are most common (see [A5](#a5)).
- **Which one can go into a table column?** Nested tables and VARRAYs, if declared as schema-level types with `CREATE TYPE`.
- **How do you loop over a sparse collection?** `i := c.FIRST; WHILE i IS NOT NULL LOOP ... i := c.NEXT(i); END LOOP;`.
- **What is a PL/SQL record?** A structure with named fields, like `%ROWTYPE`. A collection of records holds many rows.

---

## Advanced questions (Topic 1)

---

<a id="a1"></a>

### A1. Autonomous transactions

**Level: Advanced**

**Short answer:** An autonomous transaction is an independent transaction started inside another one. It has its own `COMMIT` and `ROLLBACK`, and it does not affect the calling transaction. You declare it with `PRAGMA AUTONOMOUS_TRANSACTION`.

**Explanation:**

Main use: logging or auditing that must be saved even if the main transaction rolls back.

```sql
CREATE TABLE error_log (
    logged_on DATE,
    message   VARCHAR2(4000)
);

CREATE OR REPLACE PROCEDURE log_error(
    p_message IN VARCHAR2
) AS
    PRAGMA AUTONOMOUS_TRANSACTION;
BEGIN
    INSERT INTO error_log (logged_on, message)
    VALUES (SYSDATE, p_message);

    COMMIT;                  -- required: commits only this insert
END log_error;
/

BEGIN
    INSERT INTO employees (id, name)
    VALUES (50, 'Test');

    log_error('Something happened');

    ROLLBACK;                -- undoes employee 50, but the log row stays
END;
/
```

```text
Main transaction                 Autonomous transaction
INSERT employee 50
call log_error ----------------> INSERT log row
                                 COMMIT   (log row saved)
<---------------------------------
ROLLBACK  (employee 50 undone, log row remains)
```

Rules:

- The autonomous block must end with `COMMIT` or `ROLLBACK`, or Oracle raises `ORA-06519`.
- It does not see uncommitted changes of the calling transaction. This can cause surprises, including a deadlock if it tries to change the same rows the caller locked.
- It can be used on a procedure, function, trigger or a package subprogram (not on a whole package).

**Follow-up questions**

- **Where is it appropriate?** Error logging, audit trails, sequence-like counters.
- **Where is it dangerous?** Business data changes. Partial work is saved while the main transaction rolls back, which breaks consistency.
- **Can an autonomous transaction in a trigger avoid the mutating table error?** It can, but it reads the old state of the table. This is risky. Use a compound trigger instead.

---

<a id="topic-2"></a>

# Topic 2: Cursors and Exceptions

---

## Basic questions (Topic 2)

---

<a id="b8"></a>

### B8. Implicit vs explicit cursors

**Level: Basic**

**Short answer:** A cursor is a pointer to a query's result set in memory. An implicit cursor is created and managed by Oracle for each DML statement and `SELECT INTO`. An explicit cursor is declared and controlled by you with `OPEN`, `FETCH` and `CLOSE`, and is used for queries that return many rows.

**Explanation:**

An **implicit cursor** is named `SQL`. You can read its attributes after a DML statement:

```sql
BEGIN
    UPDATE employees
    SET salary = salary + 1000
    WHERE department_id = 10;

    DBMS_OUTPUT.PUT_LINE(SQL%ROWCOUNT || ' rows updated');
END;
/
-- Output: 2 rows updated
```

An **explicit cursor** has four steps:

```text
DECLARE  -->  OPEN  -->  FETCH (repeat)  -->  CLOSE
  |            |             |                   |
define the   run the     read the next       free the
query        query       row into variables  memory
```

```sql
DECLARE
    CURSOR c_emp IS
        SELECT id, name
        FROM employees
        WHERE department_id = 10;

    v_id   employees.id%TYPE;
    v_name employees.name%TYPE;
BEGIN
    OPEN c_emp;

    LOOP
        FETCH c_emp INTO v_id, v_name;
        EXIT WHEN c_emp%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE(v_id || ' ' || v_name);
    END LOOP;

    CLOSE c_emp;
END;
/
-- Output:
-- 1 Rahim
-- 2 Karim
```

| Item | Implicit | Explicit |
|------|----------|----------|
| Who manages it | Oracle | You |
| Used for | DML, `SELECT INTO` | Queries with many rows |
| Name | `SQL` | Your name |
| Number of rows | Single row for `SELECT INTO` | Any number |

**Follow-up questions**

- **What happens if you forget `CLOSE`?** The cursor stays open until the session ends or the block ends. Too many open cursors raise `ORA-01000: maximum open cursors exceeded`. Cursors opened in a subprogram are closed automatically when the variable goes out of scope, but do not rely on that.
- **Is a cursor FOR loop implicit or explicit?** It uses an explicit-style cursor, but Oracle does the open, fetch and close for you. See [B10](#b10).
- **What errors can happen with explicit cursors?** `CURSOR_ALREADY_OPEN` (opening an open cursor) and `INVALID_CURSOR` (fetching from or closing a cursor that is not open).
- **Does a cursor read a snapshot?** Yes. The result set is fixed at the time of `OPEN`. It is consistent as of that moment, even if other sessions commit changes later.

---

<a id="b9"></a>

### B9. Cursor attributes

**Level: Basic**

**Short answer:** Attributes tell you the state of a cursor: `%FOUND`, `%NOTFOUND`, `%ROWCOUNT`, `%ISOPEN`. Use `cursor_name%ATTR` for explicit cursors and `SQL%ATTR` for the implicit cursor.

**Explanation:**

| Attribute | Explicit cursor | Implicit cursor (`SQL`) |
|-----------|-----------------|-------------------------|
| `%FOUND` | TRUE if the last `FETCH` returned a row | TRUE if the last DML affected at least one row |
| `%NOTFOUND` | TRUE if the last `FETCH` returned no row | TRUE if the last DML affected no rows |
| `%ROWCOUNT` | Number of rows fetched so far | Number of rows affected by the last DML |
| `%ISOPEN` | TRUE if the cursor is open | Always FALSE (Oracle closes it right after the statement) |

State before and after the first fetch:

| Moment | `%FOUND` | `%NOTFOUND` | `%ROWCOUNT` |
|--------|----------|-------------|-------------|
| After `OPEN`, before first `FETCH` | NULL | NULL | 0 |
| After a `FETCH` that got a row | TRUE | FALSE | 1, 2, 3 ... |
| After a `FETCH` past the last row | FALSE | TRUE | unchanged |

```sql
DECLARE
    CURSOR c_emp IS
        SELECT name
        FROM employees
        ORDER BY id;

    v_name employees.name%TYPE;
BEGIN
    IF NOT c_emp%ISOPEN THEN
        OPEN c_emp;
    END IF;

    LOOP
        FETCH c_emp INTO v_name;
        EXIT WHEN c_emp%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE(c_emp%ROWCOUNT || ': ' || v_name);
    END LOOP;

    CLOSE c_emp;
END;
/
-- Output:
-- 1: Rahim
-- 2: Karim
-- 3: Sumi
```

**Follow-up questions**

- **Why `EXIT WHEN %NOTFOUND` right after `FETCH`?** If you test it before the fetch, the value is NULL on the first pass. If you process the row before the test, you process the last row twice.
- **What is `SQL%ROWCOUNT` after a `SELECT INTO`?** 1 if a row was found.
- **Why is `SQL%ISOPEN` always FALSE?** Oracle closes the implicit cursor as soon as the statement ends.
- **Can you use `cursor%ROWCOUNT` in a cursor FOR loop?** Yes. It works on the loop's cursor name when the cursor is declared. For an inline query there is no name to use, so keep your own counter.

---

<a id="b10"></a>

### B10. What is a cursor FOR loop?

**Level: Basic**

**Short answer:** A cursor FOR loop opens the cursor, fetches each row into a record, and closes the cursor automatically. It is the shortest and safest way to process every row of a query.

**Explanation:**

```sql
BEGIN
    FOR r IN (
        SELECT name, salary
        FROM employees
        WHERE department_id = 10
        ORDER BY id
    )
    LOOP
        DBMS_OUTPUT.PUT_LINE(r.name || ' ' || r.salary);
    END LOOP;
END;
/
-- Output:
-- Rahim 50000
-- Karim 60000
```

- `r` is a record created for the loop. Do not declare it. It exists only inside the loop.
- It can use an inline query (shown above) or a declared cursor: `FOR r IN c_emp LOOP`.
- There is no `OPEN`, `FETCH`, `CLOSE` or `EXIT WHEN %NOTFOUND`. Oracle handles them.
- In Oracle 10g and later, with the default optimization level, Oracle fetches in batches of 100 rows behind the scenes (version-specific). You get some bulk-fetch benefit for free.

When to prefer each:

| Situation | Choice |
|-----------|--------|
| Process every row, simple logic | Cursor FOR loop |
| Need fine control (exit early on a condition, fetch into collections) | Explicit `OPEN` / `FETCH` / `CLOSE` |
| Loop only to run DML per row | Consider one set-based SQL statement instead, or `FORALL` (see [A7](#a7)) |

**Follow-up questions**

- **Does a cursor FOR loop close the cursor if an exception occurs?** Yes. It closes the cursor when the loop ends, including when an exception leaves it.
- **Can you exit a cursor FOR loop early?** Yes, with `EXIT` or `EXIT WHEN`. The cursor is closed.
- **Can you update rows inside the loop?** Yes. You can run DML on the table. For row-by-row updates of the fetched rows with locking, see [I5](#i5).

---

<a id="b11"></a>

### B11. Predefined exceptions and basic exception handling

**Level: Basic**

**Short answer:** An exception is a runtime error. Oracle names the common ones (predefined exceptions such as `NO_DATA_FOUND`). You catch them in the `EXCEPTION` section with `WHEN ... THEN`, and `WHEN OTHERS` catches everything else.

**Explanation:**

Common predefined exceptions:

| Exception | ORA code | Raised when |
|-----------|----------|-------------|
| `NO_DATA_FOUND` | ORA-01403 | `SELECT INTO` returns no rows |
| `TOO_MANY_ROWS` | ORA-01422 | `SELECT INTO` returns more than one row |
| `DUP_VAL_ON_INDEX` | ORA-00001 | Insert breaks a primary key or unique constraint |
| `ZERO_DIVIDE` | ORA-01476 | Division by zero |
| `VALUE_ERROR` | ORA-06502 | Value too big for the variable, or a bad conversion |
| `INVALID_NUMBER` | ORA-01722 | A text value that is not a number is converted in SQL |
| `CURSOR_ALREADY_OPEN` | ORA-06511 | `OPEN` on a cursor that is already open |
| `INVALID_CURSOR` | ORA-01001 | Operation on a cursor that is not open |
| `LOGIN_DENIED` | ORA-01017 | Invalid user or password |
| `COLLECTION_IS_NULL` | ORA-06531 | Using a collection that was never initialized |
| `SUBSCRIPT_BEYOND_COUNT` | ORA-06533 | Index larger than the collection size |

```sql
BEGIN
    INSERT INTO employees (id, name, salary, department_id)
    VALUES (1, 'Duplicate', 1000, 10);          -- id 1 already exists
EXCEPTION
    WHEN DUP_VAL_ON_INDEX THEN
        DBMS_OUTPUT.PUT_LINE('Employee id already exists');
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Unexpected: ' || SQLERRM);
        RAISE;                                   -- pass the error up
END;
/
-- Output: Employee id already exists
```

Rules:

- Handlers are checked from top to bottom. The first match runs.
- `WHEN OTHERS` must be last.
- After a handler finishes, control leaves the block. It does not go back to the failing line.
- If no handler matches, the exception goes to the caller (see [I7](#i7)).

**Follow-up questions**

- **Is `WHEN OTHERS THEN NULL` acceptable?** No. It hides every error. Log and re-raise instead. See [A2](#a2).
- **Can one handler catch two exceptions?** Yes: `WHEN NO_DATA_FOUND OR TOO_MANY_ROWS THEN`.
- **Can you continue after an error in a loop?** Wrap the statement in an inner `BEGIN ... EXCEPTION ... END;` block inside the loop.

---

## Intermediate questions (Topic 2)

---

<a id="i3"></a>

### I3. Parameterized cursors

**Level: Intermediate**

**Short answer:** A parameterized cursor takes parameters in its declaration, so you can reuse one cursor with different values each time you open it.

**Explanation:**

```sql
DECLARE
    CURSOR c_emp_by_dept(p_dept NUMBER, p_min_salary NUMBER DEFAULT 0) IS
        SELECT name, salary
        FROM employees
        WHERE department_id = p_dept
        AND salary >= p_min_salary
        ORDER BY id;
BEGIN
    DBMS_OUTPUT.PUT_LINE('Dept 10:');
    FOR r IN c_emp_by_dept(10) LOOP
        DBMS_OUTPUT.PUT_LINE('  ' || r.name);
    END LOOP;

    DBMS_OUTPUT.PUT_LINE('Dept 10, salary at least 55000:');
    FOR r IN c_emp_by_dept(10, 55000) LOOP
        DBMS_OUTPUT.PUT_LINE('  ' || r.name);
    END LOOP;
END;
/
-- Output:
-- Dept 10:
--   Rahim
--   Karim
-- Dept 10, salary at least 55000:
--   Karim
```

- Parameters are `IN` only. In the cursor, you cannot assign to them.
- The cursor parameter list gives only the type, with no length: `p_dept NUMBER`.
- You can pass values with `OPEN c_emp_by_dept(10);` or inside a `FOR` loop as above.

**Follow-up questions**

- **Why not just use a PL/SQL variable in the cursor's `WHERE`?** You can, if the variable is visible at the `OPEN`, but a parameter makes the dependency clear and allows reopening with different values.
- **Can you reopen the same cursor with new parameters?** Yes, after `CLOSE`. In a FOR loop it is automatic.
- **Are parameter values bound when the cursor opens?** Yes. Values are fixed at `OPEN`. Later changes to the variable do not affect an open cursor.

---

<a id="i4"></a>

### I4. REF CURSOR and SYS_REFCURSOR

**Level: Intermediate**

**Short answer:** A `REF CURSOR` is a cursor variable. It points to a query result and can be passed around and returned to a caller, for example to a Java application. `SYS_REFCURSOR` is Oracle's ready-made weak `REF CURSOR` type.

**Explanation:**

A normal (static) cursor has a fixed query in the declaration. A cursor variable gets its query at run time with `OPEN ... FOR`.

| Type | Meaning |
|------|---------|
| Strong `REF CURSOR` (`TYPE t IS REF CURSOR RETURN employees%ROWTYPE`) | Has a fixed row shape. The compiler checks it |
| Weak `REF CURSOR` (`TYPE t IS REF CURSOR`, or `SYS_REFCURSOR`) | Can point to any query. Errors show up at run time |

```sql
CREATE OR REPLACE PROCEDURE get_dept_employees(
    p_dept   IN  NUMBER,
    p_result OUT SYS_REFCURSOR
) AS
BEGIN
    OPEN p_result FOR
        SELECT id, name, salary
        FROM employees
        WHERE department_id = p_dept
        ORDER BY id;
END get_dept_employees;
/

-- PL/SQL caller
DECLARE
    v_cur    SYS_REFCURSOR;
    v_id     employees.id%TYPE;
    v_name   employees.name%TYPE;
    v_salary employees.salary%TYPE;
BEGIN
    get_dept_employees(10, v_cur);

    LOOP
        FETCH v_cur INTO v_id, v_name, v_salary;
        EXIT WHEN v_cur%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE(v_id || ' ' || v_name);
    END LOOP;

    CLOSE v_cur;      -- the caller is responsible for closing
END;
/
-- Output:
-- 1 Rahim
-- 2 Karim
```

Static cursor vs cursor variable:

| Item | Static cursor | `REF CURSOR` variable |
|------|---------------|-----------------------|
| Query fixed at compile time | Yes | No |
| Can be passed as a parameter or returned | No | Yes |
| Can be returned to Java | No | Yes (`ResultSet`) |
| Can be used in a cursor FOR loop | Yes | No |

**Follow-up questions**

- **Who closes the cursor?** The code that fetches it. In Java, closing the `ResultSet` closes it.
- **Can you build the query dynamically?** Yes: `OPEN p_result FOR v_sql USING p_dept;`. See [A9](#a9).
- **Can you store a REF CURSOR in a table column?** No.
- **When is it better than a collection?** When the result is large. A cursor streams rows as you fetch. A collection holds everything in memory.

---

<a id="i5"></a>

### I5. FOR UPDATE and WHERE CURRENT OF

**Level: Intermediate**

**Short answer:** `FOR UPDATE` in a cursor query locks the selected rows when the cursor opens. `WHERE CURRENT OF cursor_name` then updates or deletes the row the cursor is currently on.

**Explanation:**

```sql
DECLARE
    CURSOR c_emp IS
        SELECT id, salary
        FROM employees
        WHERE department_id = 10
        FOR UPDATE;
BEGIN
    FOR r IN c_emp LOOP
        UPDATE employees
        SET salary = r.salary * 1.10
        WHERE CURRENT OF c_emp;
    END LOOP;

    COMMIT;     -- releases the locks
END;
/
```

- Rows are locked at `OPEN`, not at each fetch. Other sessions that try to change them must wait.
- `COMMIT` or `ROLLBACK` releases the locks.
- Options after `FOR UPDATE`:

| Option | Behavior |
|--------|----------|
| `FOR UPDATE` | Wait forever if a row is locked by another session |
| `FOR UPDATE NOWAIT` | Raise `ORA-00054` at once if a row is locked |
| `FOR UPDATE WAIT 5` | Wait up to 5 seconds, then raise an error |
| `FOR UPDATE SKIP LOCKED` | Skip locked rows and return the others (often used for job queues) |

**Follow-up questions**

- **What happens if you `COMMIT` inside the loop?** The locks are released and the cursor can no longer fetch. You may get `ORA-01002: fetch out of sequence`.
- **Is `WHERE CURRENT OF` required?** No. You can update by primary key. `CURRENT OF` uses the row's physical address (ROWID) and avoids a second lookup.
- **Why lock rows at all?** To stop another session changing a row between your read and your write (lost update).

---

<a id="i6"></a>

### I6. User-defined exceptions, RAISE_APPLICATION_ERROR, PRAGMA EXCEPTION_INIT

**Level: Intermediate**

**Short answer:** You can declare your own exception and `RAISE` it. `RAISE_APPLICATION_ERROR(code, message)` sends a custom error to the caller with a code from -20000 to -20999. `PRAGMA EXCEPTION_INIT` gives a name to an Oracle error that has no predefined name.

**Explanation:**

**1. User-defined exception:**

```sql
DECLARE
    e_low_salary EXCEPTION;
    v_salary     NUMBER := 500;
BEGIN
    IF v_salary < 1000 THEN
        RAISE e_low_salary;
    END IF;
EXCEPTION
    WHEN e_low_salary THEN
        DBMS_OUTPUT.PUT_LINE('Salary is below minimum');
END;
/
-- Output: Salary is below minimum
```

**2. RAISE_APPLICATION_ERROR:**

```sql
CREATE OR REPLACE PROCEDURE set_salary(
    p_id     IN NUMBER,
    p_salary IN NUMBER
) AS
BEGIN
    IF p_salary < 0 THEN
        RAISE_APPLICATION_ERROR(-20001, 'Salary cannot be negative');
    END IF;

    UPDATE employees
    SET salary = p_salary
    WHERE id = p_id;
END set_salary;
/

BEGIN
    set_salary(1, -5);
END;
/
-- Output (error):
-- ORA-20001: Salary cannot be negative
```

- Codes -20000 to -20999 are reserved for application use.
- A Java caller receives a `SQLException` with `getErrorCode()` = 20001 and the message text `ORA-20001: Salary cannot be negative`.
- A third argument `TRUE` keeps the earlier errors in the error stack. The default `FALSE` replaces them.

**3. PRAGMA EXCEPTION_INIT:** A **pragma** is an instruction to the compiler. This one links a name to an error number.

```sql
DECLARE
    e_null_value EXCEPTION;
    PRAGMA EXCEPTION_INIT(e_null_value, -1400);     -- ORA-01400: cannot insert NULL
BEGIN
    INSERT INTO employees (id, name)
    VALUES (NULL, 'No Id');
EXCEPTION
    WHEN e_null_value THEN
        DBMS_OUTPUT.PUT_LINE('Id is required');
END;
/
-- Output: Id is required
```

Summary:

| Tool | Use it to |
|------|-----------|
| `RAISE exception_name` | Raise a declared exception (visible only inside PL/SQL) |
| `RAISE_APPLICATION_ERROR` | Send a numbered error with a message to the calling application |
| `PRAGMA EXCEPTION_INIT` | Catch an Oracle error by name instead of using `WHEN OTHERS` |

**Follow-up questions**

- **Difference between `RAISE` and `RAISE_APPLICATION_ERROR`?** `RAISE` raises a named exception with no message. `RAISE_APPLICATION_ERROR` attaches a code and a message, so a client can read them. A user-defined exception that reaches the client unhandled shows as `ORA-06510: PL/SQL: unhandled user-defined exception`.
- **Can you use `RAISE_APPLICATION_ERROR` in a trigger?** Yes. This is the normal way to reject a change. See [I12](#i12).
- **What is `RAISE;` with no name?** It re-raises the current exception inside a handler.
- **Where do you declare shared exceptions?** In a package spec, so every program can use the same names.

---

<a id="i7"></a>

### I7. SQLCODE, SQLERRM and exception propagation

**Level: Intermediate**

**Short answer:** `SQLCODE` returns the error number and `SQLERRM` returns the error message of the current exception. If a block has no matching handler, the exception moves up to the enclosing block, then to the caller, until something handles it or it reaches the client.

**Explanation:**

| Item | Value |
|------|-------|
| `SQLCODE` | Error number. 0 means no error. 100 is `NO_DATA_FOUND`. 1 is for user-defined exceptions. Oracle errors are negative (for example -1 for ORA-00001) |
| `SQLERRM` | Message text for the current error. `SQLERRM(code)` gives the message for any code |

You cannot use `SQLCODE` or `SQLERRM` directly inside a SQL statement. Assign them to variables first:

```sql
DECLARE
    v_code NUMBER;
    v_msg  VARCHAR2(512);
BEGIN
    INSERT INTO employees (id, name)
    VALUES (1, 'Duplicate');
EXCEPTION
    WHEN OTHERS THEN
        v_code := SQLCODE;
        v_msg  := SUBSTR(SQLERRM, 1, 512);
        DBMS_OUTPUT.PUT_LINE(v_code || ' : ' || v_msg);
END;
/
-- Output: -1 : ORA-00001: unique constraint (SCHEMA.SYS_Cnnnn) violated
```

**Propagation:**

```text
Outer block
+-------------------------------------------+
| BEGIN                                     |
|   Inner block                             |
|   +-------------------------------+       |
|   | BEGIN                         |       |
|   |   error happens here          |       |
|   | EXCEPTION                     |       |
|   |   WHEN other_error THEN ...   | <-- no match
|   | END;                          |       |
|   +---------------|---------------+       |
|                   | not handled: goes up  |
|                   v                       |
| EXCEPTION                                 |
|   WHEN OTHERS THEN ...                    | <-- handled here
| END;                                      |
+-------------------------------------------+
If nothing handles it, it goes to the caller, and finally to the client (Java).
```

Special cases:

| Case | Behavior |
|------|----------|
| Error in the `DECLARE` section (for example a bad initial value) | The handler of the same block does not catch it. It goes to the enclosing block |
| Error raised inside an exception handler | The same block does not catch it. It goes to the enclosing block |
| Unhandled exception at the top | Oracle rolls back the changes of the failed top-level call (statement-level rollback). Earlier committed work stays |

Useful helpers for the full picture:

- `DBMS_UTILITY.FORMAT_ERROR_STACK` returns the full error message stack.
- `DBMS_UTILITY.FORMAT_ERROR_BACKTRACE` returns the line numbers where the error started. Call it inside the handler.

**Follow-up questions**

- **Why is `SQLERRM` limited?** It returns at most 512 bytes, so long messages are cut. `FORMAT_ERROR_STACK` returns more.
- **Does the handler continue after the failing line?** No. Control leaves the block that handled the exception.
- **If an inner block handles an exception, does the outer block know?** No, unless the handler re-raises it with `RAISE;`.
- **What happens to the DML done before the error?** In a block that handles the exception, the earlier DML stays in the transaction. You decide to `COMMIT` or `ROLLBACK`. A failed single DML statement is rolled back as a whole by Oracle.

---

## Advanced questions (Topic 2)

---

<a id="a2"></a>

### A2. Exception handling best practices

**Level: Advanced**

**Short answer:** Catch only the exceptions you can handle. Log the full error and re-raise anything unexpected. Never use `WHEN OTHERS THEN NULL`. Keep the original error stack.

**Explanation:**

| Do | Do not |
|----|--------|
| Catch specific exceptions first (`NO_DATA_FOUND`) | `WHEN OTHERS THEN NULL;` |
| In `WHEN OTHERS`, log and `RAISE;` | Swallow errors and return success |
| Capture the backtrace in the handler | Use `SQLERRM` alone, which has no line numbers |
| Use `RAISE;` to re-raise | Use `RAISE_APPLICATION_ERROR` just to rethrow (it replaces the stack unless the third argument is `TRUE`) |
| Put shared exception names in a package spec | Repeat `-20xxx` numbers all over the code |
| Handle errors per row in an inner block when the loop must continue | Wrap a whole loop in one handler and lose the context |

Pattern:

```sql
CREATE OR REPLACE PROCEDURE process_employee(
    p_id IN NUMBER
) AS
    v_salary employees.salary%TYPE;
BEGIN
    SELECT salary
    INTO v_salary
    FROM employees
    WHERE id = p_id;

    -- ... more work ...
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        RAISE_APPLICATION_ERROR(-20100, 'Employee ' || p_id || ' not found');
    WHEN OTHERS THEN
        log_error(                                         -- autonomous logger from A1
            'process_employee(' || p_id || '): ' ||
            DBMS_UTILITY.FORMAT_ERROR_STACK ||
            DBMS_UTILITY.FORMAT_ERROR_BACKTRACE
        );
        RAISE;                                             -- the caller still sees the error
END process_employee;
/
```

Continue a loop after a row fails:

```sql
BEGIN
    FOR r IN (SELECT id FROM employees) LOOP
        BEGIN
            process_employee(r.id);
        EXCEPTION
            WHEN OTHERS THEN
                NULL;      -- the error is already logged inside process_employee
        END;
    END LOOP;
END;
/
```

**Follow-up questions**

- **Why is `WHEN OTHERS THEN NULL` so bad?** The transaction continues in an unknown state and nobody knows an error happened.
- **How do you get the line number of an error?** `DBMS_UTILITY.FORMAT_ERROR_BACKTRACE` inside the handler. In Oracle 12c and later `UTL_CALL_STACK` is also available (version-specific).
- **Should procedures return error codes or raise exceptions?** Raise exceptions. Return codes can be ignored by the caller.
- **What does Java see?** A `SQLException` with the `ORA-` message and error code. Map `-20xxx` codes to your application's exceptions.

---

<a id="topic-3"></a>

# Topic 3: Procedures, Functions, Packages and Triggers

---

## Basic questions (Topic 3)

---

<a id="b12"></a>

### B12. Procedure vs function

**Level: Basic**

**Short answer:** A function must return one value and can be used inside expressions and SQL. A procedure performs an action, has no return value, and is called as its own statement.

**Explanation:**

| Item | Procedure | Function |
|------|-----------|----------|
| Returns a value | No (can pass values back through `OUT` parameters) | Yes, exactly one, using `RETURN` |
| Return type declared | No | Yes: `RETURN NUMBER` |
| How it is called | As a statement: `my_proc(1);` | In an expression: `x := my_func(1);` |
| Callable from SQL `SELECT` | No | Yes, with restrictions (see [I8](#i8)) |
| Best for | Actions: insert, update, send, process | Calculations that return a value |

```sql
CREATE OR REPLACE PROCEDURE give_raise(
    p_id      IN NUMBER,
    p_percent IN NUMBER
) AS
BEGIN
    UPDATE employees
    SET salary = salary + salary * p_percent / 100
    WHERE id = p_id;
END give_raise;
/

CREATE OR REPLACE FUNCTION get_annual_salary(
    p_id IN NUMBER
) RETURN NUMBER AS
    v_salary employees.salary%TYPE;
BEGIN
    SELECT salary
    INTO v_salary
    FROM employees
    WHERE id = p_id;

    RETURN v_salary * 12;
END get_annual_salary;
/

BEGIN
    give_raise(1, 10);                                      -- statement
    DBMS_OUTPUT.PUT_LINE(get_annual_salary(1));             -- inside an expression
END;
/
-- Output: 660000   (50000 raised by 10 percent = 55000, times 12)
```

**Follow-up questions**

- **Can a procedure return a value?** Only through an `OUT` or `IN OUT` parameter.
- **Can a function have an `OUT` parameter?** Yes, but then it cannot be called from SQL. It is poor style, so avoid it.
- **Can a function have no `RETURN` statement?** The compiler accepts it, but at run time it raises `ORA-06503: PL/SQL: Function returned without value` if it ends without returning.
- **Can a procedure use `RETURN`?** Yes, `RETURN;` without a value exits the procedure early.

---

<a id="b13"></a>

### B13. IN, OUT and IN OUT parameters

**Level: Basic**

**Short answer:** The mode says which direction data flows. `IN` passes a value in (read only, the default). `OUT` sends a value back to the caller. `IN OUT` does both.

**Explanation:**

| Mode | Direction | Can the subprogram assign to it? | Value at start |
|------|-----------|----------------------------------|----------------|
| `IN` | Caller to subprogram | No | The value passed |
| `OUT` | Subprogram to caller | Yes | `NULL` (the old value is ignored) |
| `IN OUT` | Both | Yes | The value passed |

```sql
CREATE OR REPLACE PROCEDURE get_emp_info(
    p_id     IN  NUMBER,
    p_name   OUT VARCHAR2,
    p_salary IN OUT NUMBER      -- caller passes a bonus, gets total salary back
) AS
    v_base NUMBER;
BEGIN
    SELECT name, salary
    INTO p_name, v_base
    FROM employees
    WHERE id = p_id;

    p_salary := v_base + p_salary;
END get_emp_info;
/

DECLARE
    v_name   VARCHAR2(50);
    v_amount NUMBER := 1000;
BEGIN
    get_emp_info(1, v_name, v_amount);
    DBMS_OUTPUT.PUT_LINE(v_name || ' ' || v_amount);
END;
/
-- Output: Rahim 51000
```

**Follow-up questions**

- **What is the default mode?** `IN`.
- **Can you give a default value to a parameter?** Only `IN` parameters: `p_percent IN NUMBER DEFAULT 10`.
- **Positional vs named notation?** Positional passes by order: `give_raise(1, 10)`. Named passes by name: `give_raise(p_percent => 10, p_id => 1)`. Named is clearer when many parameters have defaults.
- **What is `NOCOPY`?** A hint that passes `OUT` / `IN OUT` parameters by reference instead of by copy. It helps with large collections. If an exception occurs, the changes made to the parameter may be kept, since there is no copy to restore.

---

## Intermediate questions (Topic 3)

---

<a id="i8"></a>

### I8. When to use a function vs a procedure, and calling a function from SQL

**Level: Intermediate**

**Short answer:** Use a function to compute and return one value, usually without changing data. Use a procedure to perform an action that changes data. A function can be called from SQL, but Oracle restricts what it may do.

**Explanation:**

Guide:

| Need | Use |
|------|-----|
| A calculation or a lookup used in an expression or a query | Function |
| Insert, update, delete, send, process | Procedure |
| An action and a result | Procedure with an `OUT` parameter, or a function that does not change data |

Calling a function from SQL:

```sql
CREATE OR REPLACE FUNCTION annual_salary(
    p_monthly IN NUMBER
) RETURN NUMBER
DETERMINISTIC AS
BEGIN
    RETURN p_monthly * 12;
END annual_salary;
/

SELECT
    name,
    annual_salary(salary) AS yearly
FROM employees
WHERE annual_salary(salary) > 600000
ORDER BY name;
-- Output:
-- NAME    YEARLY
-- Karim   720000
```

Restrictions for a function called from a query:

| Rule | Detail |
|------|--------|
| No DML in a query | A function used in `SELECT` cannot run `INSERT`, `UPDATE` or `DELETE` (`ORA-14551`) |
| No `COMMIT` / `ROLLBACK` | Transaction control is not allowed |
| Parameters must be `IN` | `OUT` and `IN OUT` parameters are not allowed |
| SQL data types only | Return type and parameters must be SQL types. A PL/SQL `BOOLEAN` cannot be used (before Oracle 23ai) |
| Cannot call a function that breaks these rules from SQL | The rules apply down the call chain |

Performance warnings:

- The function runs once per row. Each call switches from the SQL engine to the PL/SQL engine and back (a **context switch**). Millions of rows means millions of switches.
- A function around an indexed column in `WHERE` can stop the index being used. A function-based index can help.
- `DETERMINISTIC` tells Oracle the same input always gives the same output, so it may cache results. Only use it when this is true.
- Oracle 12c and later allow a `WITH FUNCTION ... SELECT` clause to define a function inside one query, which reduces the switch cost (version-specific).
- Oracle 12c and later also support `PRAGMA UDF` to make a function cheaper to call from SQL (version-specific).

**Follow-up questions**

- **Can a function change data when called from PL/SQL?** Yes. The restriction applies only when it is called from SQL.
- **Can you call a procedure from a `SELECT`?** No.
- **Which gives a better design?** Put reusable pure calculations in functions. Put data-changing workflows in procedures, and group both in a package.

---

<a id="i9"></a>

### I9. Packages: spec, body and why use them

**Level: Intermediate**

**Short answer:** A package groups related procedures, functions, variables, constants, cursors and types into one unit. It has a specification (spec) with the public declarations and a body with the code. Callers see only the spec.

**Explanation:**

```text
+----------------------- PACKAGE emp_pkg ------------------------+
|                                                                |
|  SPEC (public)             BODY (hidden implementation)        |
|  - give_raise(...)         - code of give_raise                |
|  - get_salary(...)         - code of get_salary                |
|  - c_max_raise constant    - is_valid_percent (private helper) |
|                            - g_calls_count (private variable)  |
+----------------------------------------------------------------+
Callers use: emp_pkg.give_raise(...)
```

```sql
CREATE OR REPLACE PACKAGE emp_pkg AS
    c_max_raise CONSTANT NUMBER := 50;           -- public constant

    PROCEDURE give_raise(
        p_id      IN NUMBER,
        p_percent IN NUMBER
    );

    FUNCTION get_salary(
        p_id IN NUMBER
    ) RETURN NUMBER;
END emp_pkg;
/

CREATE OR REPLACE PACKAGE BODY emp_pkg AS

    -- private: not in the spec, so only this package can call it.
    -- It is defined before the code that uses it.
    FUNCTION is_valid_percent(p_percent NUMBER) RETURN BOOLEAN IS
    BEGIN
        RETURN p_percent BETWEEN 0 AND c_max_raise;
    END is_valid_percent;

    PROCEDURE give_raise(
        p_id      IN NUMBER,
        p_percent IN NUMBER
    ) IS
    BEGIN
        IF NOT is_valid_percent(p_percent) THEN
            RAISE_APPLICATION_ERROR(-20010, 'Raise percent out of range');
        END IF;

        UPDATE employees
        SET salary = salary + salary * p_percent / 100
        WHERE id = p_id;
    END give_raise;

    FUNCTION get_salary(
        p_id IN NUMBER
    ) RETURN NUMBER IS
        v_salary employees.salary%TYPE;
    BEGIN
        SELECT salary
        INTO v_salary
        FROM employees
        WHERE id = p_id;

        RETURN v_salary;
    END get_salary;

END emp_pkg;
/

BEGIN
    emp_pkg.give_raise(1, 10);
    DBMS_OUTPUT.PUT_LINE(emp_pkg.get_salary(1));
END;
/
-- Output: 55000
```

Why use packages:

| Reason | Explanation |
|--------|-------------|
| Encapsulation | Private helpers are hidden. Callers use only the spec |
| Easier change | Changing only the body does not invalidate programs that call the package |
| Performance | The whole package loads into memory on first call, so later calls avoid disk reads |
| Overloading | Several subprograms with the same name (see [I11](#i11)) |
| Shared state | Package variables keep values for the session (see [I10](#i10)) |
| Organization | Related code lives in one place, with shared types, constants and exceptions |

**Follow-up questions**

- **Can a package have a spec without a body?** Yes, if the spec has only constants, types and exceptions (no subprograms).
- **What happens when you change the spec?** Everything that depends on it becomes invalid and must recompile.
- **What if the body does not match the spec?** The body fails to compile. Every subprogram in the spec needs a matching implementation.
- **What is the initialization block?** A `BEGIN ... END` at the bottom of the body that runs once per session when the package is first used.
- **Do private subprograms need a forward declaration?** If they are called before they are defined, yes. Otherwise define them first.

---

<a id="i10"></a>

### I10. Package state

**Level: Intermediate**

**Short answer:** Variables declared at package level keep their values for the lifetime of the session. Each session has its own copy.

**Explanation:**

```sql
CREATE OR REPLACE PACKAGE counter_pkg AS
    g_count NUMBER := 0;                    -- public package variable
    PROCEDURE increment;
END counter_pkg;
/

CREATE OR REPLACE PACKAGE BODY counter_pkg AS
    PROCEDURE increment IS
    BEGIN
        g_count := g_count + 1;
    END increment;
END counter_pkg;
/

BEGIN
    counter_pkg.increment;
    counter_pkg.increment;
    DBMS_OUTPUT.PUT_LINE(counter_pkg.g_count);
END;
/
-- Output: 2
-- Run another anonymous block in the SAME session and g_count is still 2.
-- A different session starts again at 0.
```

Key points:

| Fact | Meaning |
|------|---------|
| State is per session | Two sessions never see each other's package variables |
| State lasts until the session ends | A `COMMIT` does not reset it |
| It is stored in the session memory (PGA) | Large package state costs memory for every session |
| It is lost if the package is recompiled | The session gets `ORA-04068: existing state of packages has been discarded` |

Typical uses: caching a small lookup, holding constants, holding a session context such as "current user".

Java relevance: with a **connection pool**, the same database session is reused by many requests. Package state can leak from one request to the next. Reset it at the start of each request, or avoid session state for request data.

**Follow-up questions**

- **How do you fix `ORA-04068`?** Call again. The first call after the discard fails, and the next one reinitializes the package. Applications often retry once.
- **Is package state shared between sessions?** No. To share data between sessions use a table or `DBMS_ALERT` / `DBMS_PIPE`, not package variables.
- **What is `PRAGMA SERIALLY_REUSABLE`?** It makes package state last only for one database call, then be reset. It reduces memory for large state. It also prevents using the package state across calls.
- **Is a package constant state?** A constant never changes, so it is safe to use.

---

<a id="i11"></a>

### I11. Overloading

**Level: Intermediate**

**Short answer:** Overloading means two or more subprograms with the same name but different parameter lists, inside the same package (or the same block). Oracle picks the right one from the arguments at compile time.

**Explanation:**

```sql
CREATE OR REPLACE PACKAGE print_pkg AS
    PROCEDURE show(p_value IN NUMBER);
    PROCEDURE show(p_value IN VARCHAR2);
    PROCEDURE show(p_value IN DATE);
END print_pkg;
/

CREATE OR REPLACE PACKAGE BODY print_pkg AS
    PROCEDURE show(p_value IN NUMBER) IS
    BEGIN
        DBMS_OUTPUT.PUT_LINE('number: ' || p_value);
    END show;

    PROCEDURE show(p_value IN VARCHAR2) IS
    BEGIN
        DBMS_OUTPUT.PUT_LINE('text: ' || p_value);
    END show;

    PROCEDURE show(p_value IN DATE) IS
    BEGIN
        DBMS_OUTPUT.PUT_LINE('date: ' || TO_CHAR(p_value, 'YYYY-MM-DD'));
    END show;
END print_pkg;
/

BEGIN
    print_pkg.show(42);
    print_pkg.show('hello');
END;
/
-- Output:
-- number: 42
-- text: hello
```

Rules:

| Allowed | Not allowed |
|---------|-------------|
| Different number of parameters | Different return type only |
| Different data types (from different families) | Different parameter mode only (`IN` vs `OUT`) |
| Different parameter names, when called with named notation | Two types from the same family, such as `INTEGER` and `NUMBER` |

- Overloading works only in packages and local subprograms. A standalone schema-level procedure cannot be overloaded.
- Be careful with calls such as `show(NULL)`. The compiler cannot decide which one and raises an error.

**Follow-up questions**

- **Why overload?** One easy name for the same idea with different input types, like `print` in many languages.
- **Is it the same as Java overloading?** Very similar, with fewer type distinctions. Oracle treats related types as one family, so some overloads Java allows are rejected.

---

<a id="i12"></a>

### I12. Triggers: BEFORE/AFTER, row vs statement level

**Level: Intermediate**

**Short answer:** A trigger is a stored PL/SQL block that fires automatically on an event such as `INSERT`, `UPDATE` or `DELETE`. It runs `BEFORE` or `AFTER` the event, and either once per row (`FOR EACH ROW`) or once per statement.

**Explanation:**

Trigger options:

| Option | Choices | Meaning |
|--------|---------|---------|
| Timing | `BEFORE`, `AFTER`, `INSTEAD OF` | When it runs. `INSTEAD OF` is used on views |
| Event | `INSERT`, `UPDATE [OF column]`, `DELETE` | What fires it. Combine with `OR` |
| Level | `FOR EACH ROW` or none | Row-level runs once per affected row. Statement-level runs once |
| Condition | `WHEN (condition)` | Row-level only. Fire only if the condition is true |

Order of firing for one `UPDATE` that changes 2 rows:

```text
BEFORE statement trigger          (once)
   BEFORE row trigger             (row 1)
   AFTER  row trigger             (row 1)
   BEFORE row trigger             (row 2)
   AFTER  row trigger             (row 2)
AFTER statement trigger           (once)
```

Choosing BEFORE or AFTER:

| Use | When |
|-----|------|
| `BEFORE` row | Validate data or change `:NEW` values before they are saved |
| `AFTER` row | Actions that need the final saved row, such as writing an audit record |
| `BEFORE` statement | Check a rule that applies to the whole statement (for example block weekends) |
| `AFTER` statement | Summary work after all rows are done |

```sql
CREATE OR REPLACE TRIGGER trg_emp_validate
BEFORE INSERT OR UPDATE OF salary ON employees
FOR EACH ROW
WHEN (NEW.salary < 0)                                -- no colon inside WHEN
BEGIN
    RAISE_APPLICATION_ERROR(-20001, 'Salary cannot be negative');
END trg_emp_validate;
/

CREATE OR REPLACE TRIGGER trg_emp_no_weekend
BEFORE INSERT OR UPDATE OR DELETE ON employees       -- statement level, no FOR EACH ROW
BEGIN
    IF TO_CHAR(SYSDATE, 'DY', 'NLS_DATE_LANGUAGE=ENGLISH') IN ('SAT', 'SUN') THEN
        RAISE_APPLICATION_ERROR(-20002, 'No changes on weekends');
    END IF;
END trg_emp_no_weekend;
/
```

Rules and tips:

- A trigger runs in the same transaction as the statement that fired it. If the trigger raises an error, the statement fails and is rolled back.
- A trigger cannot use `COMMIT` or `ROLLBACK` (unless it is an autonomous transaction, see [A1](#a1)).
- Use `INSERTING`, `UPDATING`, `DELETING` inside one trigger to see which event fired it.
- Control the order of triggers of the same type with `FOLLOWS` (Oracle 11g and later).
- Disable or enable: `ALTER TRIGGER trg_name DISABLE;`.
- Prefer constraints (`NOT NULL`, `CHECK`, foreign keys) for simple rules. Triggers are hidden side effects and are hard to debug and slow down bulk loads.

**Follow-up questions**

- **What is an `INSTEAD OF` trigger?** It replaces the DML on a view, so that you can make a complex view updatable.
- **Can a trigger fire another trigger?** Yes (cascading). Keep it simple to avoid loops.
- **Why can triggers hurt performance?** A row-level trigger runs for every row. A load of one million rows runs it one million times.
- **Can a trigger send an email or call a web service?** Technically yes, but it is bad practice: it slows the transaction, and the transaction might still roll back. Put the event in a queue table and process it later.
- **What are system / DDL triggers?** Triggers on events such as `LOGON`, `CREATE`, `DROP`. Used for auditing.

---

<a id="i13"></a>

### I13. :NEW and :OLD

**Level: Intermediate**

**Short answer:** In a row-level trigger, `:OLD` holds the row values before the change and `:NEW` holds the values after the change. They exist only in row-level triggers.

**Explanation:**

| Event | `:OLD` | `:NEW` |
|-------|--------|--------|
| `INSERT` | All fields NULL | The new row |
| `UPDATE` | The row before the update | The row after the update |
| `DELETE` | The row being deleted | All fields NULL |

```sql
CREATE TABLE salary_audit (
    emp_id     NUMBER,
    old_salary NUMBER,
    new_salary NUMBER,
    changed_on DATE
);

CREATE OR REPLACE TRIGGER trg_salary_audit
AFTER UPDATE OF salary ON employees
FOR EACH ROW
BEGIN
    INSERT INTO salary_audit (emp_id, old_salary, new_salary, changed_on)
    VALUES (:OLD.id, :OLD.salary, :NEW.salary, SYSDATE);
END trg_salary_audit;
/

UPDATE employees
SET salary = 55000
WHERE id = 1;

SELECT
    emp_id,
    old_salary,
    new_salary
FROM salary_audit;
-- Output:
-- EMP_ID  OLD_SALARY  NEW_SALARY
--      1       50000       55000
```

Changing `:NEW` in a `BEFORE` trigger:

```sql
CREATE OR REPLACE TRIGGER trg_emp_fix_name
BEFORE INSERT OR UPDATE ON employees
FOR EACH ROW
BEGIN
    :NEW.name := INITCAP(TRIM(:NEW.name));      -- ' rahim ' is saved as 'Rahim'
END trg_emp_fix_name;
/
```

Rules:

- You can assign to `:NEW` only in a `BEFORE` row trigger.
- `:OLD` is read only.
- Inside the trigger's `WHEN` clause, write `NEW` and `OLD` without the colon.
- You can rename them: `REFERENCING NEW AS n OLD AS o`, then use `:n.salary`.

**Follow-up questions**

- **Can you use `:NEW` in a statement-level trigger?** No. It does not exist there.
- **Can you change `:NEW` in an `AFTER` trigger?** No. It raises an error, because the row is already saved.
- **How do you know a column was really changed?** Compare `:OLD.col` and `:NEW.col`, and handle NULL carefully. `UPDATE OF col` fires even if the value is set to the same value.

---

<a id="i14"></a>

### I14. What is the mutating table error?

**Level: Intermediate**

**Short answer:** `ORA-04091: table is mutating` is raised when a row-level trigger reads or modifies the same table that is being changed by the statement that fired the trigger. Oracle blocks it because the table is in the middle of a change and the result would be unpredictable.

**Explanation:**

```sql
CREATE OR REPLACE TRIGGER trg_bad_example
BEFORE UPDATE OF salary ON employees
FOR EACH ROW
DECLARE
    v_max NUMBER;
BEGIN
    SELECT MAX(salary)
    INTO v_max
    FROM employees;                  -- reads the table being updated

    IF :NEW.salary > v_max * 2 THEN
        RAISE_APPLICATION_ERROR(-20003, 'Raise too big');
    END IF;
END trg_bad_example;
/

UPDATE employees
SET salary = salary + 1000;
-- ORA-04091: table EMPLOYEES is mutating, trigger/function may not see it
```

```text
UPDATE touches rows 1, 2, 3
  |
  +--> row trigger fires for row 1
         reads the EMPLOYEES table  --> rows 2 and 3 are not changed yet
                                         the table is half changed
                                         ==> Oracle raises ORA-04091
```

Details:

- It applies to row-level triggers. A statement-level trigger is not affected (the table is not mid-change before or after the statement).
- It also applies to a function that the triggering table calls.
- Special case: a row-level trigger on a single-row `INSERT ... VALUES` can read its own table without the error, but do not rely on that.

How to avoid it:

| Fix | How |
|-----|-----|
| Use `:NEW` / `:OLD` only | Do not query the same table if the value is already in the row |
| Compound trigger (11g and later) | Collect the row data in the row section, process it in the `AFTER STATEMENT` section. See [A3](#a3) |
| Move the rule out of the trigger | Use a constraint, or do the check in a procedure that the application calls |
| Autonomous transaction trigger | Hides the error but reads the table as it was before the transaction. It is risky, so avoid it |

**Follow-up questions**

- **Why does Oracle raise it?** A row trigger could see the table in a half-updated state that changes with the processing order. Oracle forbids this to give consistent results.
- **Does a foreign key check count?** Foreign key checks in triggers on child and parent tables can raise it in some cases. The compound trigger is the standard fix.
- **Is a trigger's own table always mutating?** Inside a row-level trigger, yes, for `UPDATE` and `DELETE`, and for both `BEFORE` and `AFTER` timing. Inside a statement-level trigger, no.

---

## Advanced questions (Topic 3)

---

<a id="a3"></a>

### A3. Compound triggers

**Level: Advanced**

**Short answer:** A compound trigger (Oracle 11g and later) is one trigger with sections for several timing points: before statement, before row, after row, after statement. The sections share variables, which solves the mutating table problem.

**Explanation:**

Idea: collect row data in the row section, then use it in the `AFTER STATEMENT` section when the table is no longer mutating.

```sql
CREATE OR REPLACE TRIGGER trg_emp_compound
FOR UPDATE OF salary ON employees
COMPOUND TRIGGER

    TYPE t_id_tab IS TABLE OF employees.id%TYPE INDEX BY PLS_INTEGER;
    g_ids t_id_tab;                       -- shared by all sections
    v_max NUMBER;

    AFTER EACH ROW IS
    BEGIN
        g_ids(g_ids.COUNT + 1) := :NEW.id;        -- only remember the id
    END AFTER EACH ROW;

    AFTER STATEMENT IS
    BEGIN
        SELECT MAX(salary)
        INTO v_max
        FROM employees;                            -- safe here, no error

        DBMS_OUTPUT.PUT_LINE(
            g_ids.COUNT || ' rows updated, highest salary is ' || v_max
        );
    END AFTER STATEMENT;

END trg_emp_compound;
/

UPDATE employees
SET salary = salary + 1000;
-- Output: 3 rows updated, highest salary is 61000
```

| Section | Runs |
|---------|------|
| `BEFORE STATEMENT` | Once, before any row is changed |
| `BEFORE EACH ROW` | For every row, before the change |
| `AFTER EACH ROW` | For every row, after the change |
| `AFTER STATEMENT` | Once, after all rows are changed |

Benefits: shared state, bulk processing (use `FORALL` in the `AFTER STATEMENT` section to write audit rows), and the variables are reset after each statement.

**Follow-up questions**

- **Does the state persist after the statement?** No. The trigger's variables are freed when the statement ends.
- **Can `:NEW` be used in the `AFTER STATEMENT` section?** No. Only in the row sections.

---

<a id="a4"></a>

### A4. Definer rights vs invoker rights

**Level: Advanced**

**Short answer:** By default a stored program runs with the privileges of its owner (definer rights). With `AUTHID CURRENT_USER` it runs with the privileges of the user who calls it (invoker rights).

**Explanation:**

| Item | Definer rights (default, `AUTHID DEFINER`) | Invoker rights (`AUTHID CURRENT_USER`) |
|------|--------------------------------------------|----------------------------------------|
| Runs with privileges of | The owner of the procedure | The caller |
| Unqualified table names resolve in | The owner's schema | The caller's schema |
| Roles | Not active inside (only direct grants to the owner) | Active (the caller's roles) |
| Typical use | Give users controlled access to data without table grants | One shared routine that works on each user's own tables |

```sql
CREATE OR REPLACE PROCEDURE count_my_rows
AUTHID CURRENT_USER AS
    v_count NUMBER;
BEGIN
    SELECT COUNT(*)
    INTO v_count
    FROM employees;                     -- resolves to the CALLER's employees table

    DBMS_OUTPUT.PUT_LINE(v_count);
END count_my_rows;
/
```

Definer rights are a security tool: grant `EXECUTE` on the procedure, and users can do only what the procedure does, not read the table freely.

**Follow-up questions**

- **Why do roles not work in definer rights?** Roles are disabled during execution. Only privileges granted directly to the owner apply.
- **Which is safer?** Definer rights limit what callers can do. Invoker rights limit what the procedure can do. Both are tools, and the right one depends on the goal.
- **Which one is the default?** Definer rights.

---

<a id="topic-4"></a>

# Topic 4: Performance and Advanced Topics

---

## Basic questions (Topic 4)

---

<a id="b14"></a>

### B14. What is DBMS_OUTPUT?

**Level: Basic**

**Short answer:** `DBMS_OUTPUT` is a built-in package that prints text for debugging. `DBMS_OUTPUT.PUT_LINE('text')` stores the line in a buffer and the client (SQL*Plus, SQL Developer) shows it after the block ends.

**Explanation:**

```sql
SET SERVEROUTPUT ON;

BEGIN
    DBMS_OUTPUT.PUT_LINE('Hello');
END;
/
-- Output: Hello
```

- `SET SERVEROUTPUT ON` is a client setting. Without it you see nothing.
- The output appears only after the block finishes. It is not real-time.
- The buffer has a size limit. The default is 20,000 bytes in older versions, and unlimited in 10g Release 2 and later when you use `SET SERVEROUTPUT ON SIZE UNLIMITED`. A single line is limited to 32,767 bytes.
- It is for development. A Java application will not see it.

**Follow-up questions**

- **How do you log in production?** Write to a log table (often with an autonomous transaction, see [A1](#a1)) or use a logging framework such as the Logger utility.
- **Can you print a BOOLEAN?** Not directly. Convert it first, for example with `CASE`.
- **Why did my output not show?** The most common reason is `SET SERVEROUTPUT ON` was not run.

---

## Intermediate questions (Topic 4)

---

<a id="i15"></a>

### I15. Calling PL/SQL from Java with CallableStatement

**Level: Intermediate**

**Short answer:** Use JDBC `CallableStatement` with the call escape syntax: `{call proc(?, ?)}` for a procedure and `{? = call func(?)}` for a function. Register `OUT` parameters with `registerOutParameter`.

**Explanation:**

Procedure with `IN` parameters (from [B12](#b12)):

```java
String sql = "{call give_raise(?, ?)}";
try (CallableStatement cs = conn.prepareCall(sql)) {
    cs.setInt(1, 1);                     // p_id
    cs.setBigDecimal(2, new BigDecimal("10"));   // p_percent
    cs.execute();
}
```

Function with a return value:

```java
String sql = "{? = call emp_pkg.get_salary(?)}";
try (CallableStatement cs = conn.prepareCall(sql)) {
    cs.registerOutParameter(1, Types.NUMERIC);   // the return value is parameter 1
    cs.setInt(2, 1);
    cs.execute();
    BigDecimal salary = cs.getBigDecimal(1);
}
```

Procedure with a `SYS_REFCURSOR` `OUT` parameter (from [I4](#i4)):

```java
String sql = "{call get_dept_employees(?, ?)}";
try (CallableStatement cs = conn.prepareCall(sql)) {
    cs.setInt(1, 10);
    cs.registerOutParameter(2, oracle.jdbc.OracleTypes.CURSOR);   // Types.REF_CURSOR in newer drivers
    cs.execute();
    try (ResultSet rs = (ResultSet) cs.getObject(2)) {
        while (rs.next()) {
            System.out.println(rs.getString("name"));
        }
    }
}
```

Errors: a `RAISE_APPLICATION_ERROR(-20001, ...)` arrives as a `SQLException`. Use `e.getErrorCode()` (20001) to decide what to do.

| Need | JDBC |
|------|------|
| `IN` parameter | `setXxx(index, value)` |
| `OUT` parameter | `registerOutParameter(index, type)` then `getXxx(index)` |
| `IN OUT` parameter | Both |
| Return value of a function | Parameter 1 with `?` before `=` |
| Anonymous block | `conn.prepareCall("BEGIN ... END;")` |

**Follow-up questions**

- **Why use a stored procedure from Java?** Fewer round trips for multi-step logic, and the logic is shared by all applications.
- **How do you close cursors in Java?** Close the `ResultSet`, `CallableStatement` and `Connection` with try-with-resources. Unclosed ones cause `ORA-01000: maximum open cursors exceeded`.
- **How do you pass an array to PL/SQL?** With Oracle JDBC `ARRAY` types (`createOracleArray`) mapped to a schema-level collection type.
- **Does a Java batch (`addBatch`) do the same as `FORALL`?** Both reduce round trips for many rows. `PreparedStatement` batching is the client-side way. `FORALL` is the PL/SQL-side way.

---

<a id="i16"></a>

### I16. Running jobs with DBMS_SCHEDULER

**Level: Intermediate**

**Short answer:** `DBMS_SCHEDULER` is Oracle's built-in package for running PL/SQL code or programs automatically at set times or intervals. It replaces the older `DBMS_JOB`.

**Explanation:**

```sql
BEGIN
    DBMS_SCHEDULER.CREATE_JOB(
        job_name        => 'NIGHTLY_CLEANUP',
        job_type        => 'PLSQL_BLOCK',
        job_action      => 'BEGIN DELETE FROM salary_audit WHERE changed_on < SYSDATE - 365; COMMIT; END;',
        start_date      => SYSTIMESTAMP,
        repeat_interval => 'FREQ=DAILY;BYHOUR=2;BYMINUTE=0;BYSECOND=0',
        enabled         => TRUE,
        comments        => 'Remove old audit rows at 2 AM'
    );
END;
/
```

| Item | Meaning |
|------|---------|
| `job_type` | `PLSQL_BLOCK`, `STORED_PROCEDURE`, `EXECUTABLE` (an OS program) |
| `repeat_interval` | Calendar syntax such as `FREQ=HOURLY;INTERVAL=2` or `FREQ=WEEKLY;BYDAY=MON` |
| `enabled` | Whether it starts running right away |

Manage and monitor:

```sql
EXEC DBMS_SCHEDULER.RUN_JOB('NIGHTLY_CLEANUP');
EXEC DBMS_SCHEDULER.DISABLE('NIGHTLY_CLEANUP');
EXEC DBMS_SCHEDULER.DROP_JOB('NIGHTLY_CLEANUP');

SELECT
    job_name,
    status,
    actual_start_date
FROM user_scheduler_job_run_details
ORDER BY actual_start_date DESC;
```

**Follow-up questions**

- **Privilege needed?** `CREATE JOB`.
- **Why use it instead of `DBMS_JOB`?** More features: calendar schedules, logging, chains of jobs, resource control. `DBMS_JOB` is legacy.
- **Where do you find why a job failed?** `USER_SCHEDULER_JOB_RUN_DETAILS`.
- **Why not use cron or a Java scheduler?** Database jobs run next to the data and need no extra service. A Java scheduler (Quartz, Spring `@Scheduled`) is easier to deploy and monitor with the application.

---

## Advanced questions (Topic 4)

---

<a id="a5"></a>

### A5. What is BULK COLLECT and why is it faster?

**Level: Advanced**

**Short answer:** `BULK COLLECT` fetches many rows into a collection with one call to the SQL engine, instead of one row at a time. It is faster because it avoids many context switches between the PL/SQL engine and the SQL engine.

**Explanation:**

Oracle has two engines. The PL/SQL engine runs procedural code. The SQL engine runs SQL. Every SQL statement in a PL/SQL block is handed from one engine to the other. This hand-over is a **context switch**. Each one has a small cost, and in a loop that cost adds up.

```text
Row by row (slow):
  PL/SQL engine                    SQL engine
  FOR each row ...
     fetch row 1  ------------->   return row 1
     fetch row 2  ------------->   return row 2
     ...                           (N switches)

BULK COLLECT (fast):
  PL/SQL engine                    SQL engine
  fetch ALL rows ------------->    return all rows in one batch
                                   (1 switch)
```

```sql
DECLARE
    TYPE t_emp_tab IS TABLE OF employees%ROWTYPE;
    v_emps t_emp_tab;
BEGIN
    SELECT *
    BULK COLLECT INTO v_emps
    FROM employees
    WHERE department_id = 10;

    DBMS_OUTPUT.PUT_LINE('Fetched ' || v_emps.COUNT || ' rows');

    FOR i IN 1..v_emps.COUNT LOOP
        DBMS_OUTPUT.PUT_LINE(v_emps(i).name);
    END LOOP;
END;
/
-- Output:
-- Fetched 2 rows
-- Rahim
-- Karim
```

Notes:

- `BULK COLLECT` works with `SELECT INTO`, `FETCH`, and DML with `RETURNING ... BULK COLLECT INTO`.
- If no rows match, the collection is empty (`COUNT = 0`). `NO_DATA_FOUND` is not raised.
- The whole result is held in memory (the session's PGA memory). For a very large table, this can use too much memory. Use `LIMIT` (next question).
- Collections used with `BULK COLLECT` are filled from index 1 and are dense.

**Follow-up questions**

- **What is a context switch?** The hand-over between the PL/SQL engine and the SQL engine.
- **Is a cursor FOR loop already bulk?** In Oracle 10g and later, with the default optimization level (`PLSQL_OPTIMIZE_LEVEL = 2`), a cursor FOR loop fetches 100 rows at a time behind the scenes (version-specific). It still does not help DML inside the loop.
- **Can you bulk collect into several collections?** Yes, one per column: `SELECT id, name BULK COLLECT INTO v_ids, v_names FROM employees;`. (A single collection of records is also fine.)
- **What are the risks?** Memory. A million rows in a collection can use a large part of the session memory.

---

<a id="a6"></a>

### A6. BULK COLLECT with LIMIT

**Level: Advanced**

**Short answer:** `LIMIT n` fetches at most `n` rows per `FETCH`, in a loop. It keeps the speed benefit of bulk fetching while using a fixed, small amount of memory.

**Explanation:**

```sql
DECLARE
    CURSOR c_emp IS
        SELECT id, salary
        FROM employees;

    TYPE t_emp_tab IS TABLE OF c_emp%ROWTYPE;
    v_emps t_emp_tab;
BEGIN
    OPEN c_emp;

    LOOP
        FETCH c_emp BULK COLLECT INTO v_emps LIMIT 1000;
        EXIT WHEN v_emps.COUNT = 0;               -- see the note below

        FOR i IN 1..v_emps.COUNT LOOP
            NULL;                                  -- process v_emps(i)
        END LOOP;
    END LOOP;

    CLOSE c_emp;
END;
/
```

```text
Table: 2,500 rows,  LIMIT 1000

FETCH 1 --> 1000 rows  (COUNT = 1000)
FETCH 2 --> 1000 rows  (COUNT = 1000)
FETCH 3 -->  500 rows  (COUNT = 500)   <-- fewer than the limit, %NOTFOUND is TRUE here
FETCH 4 -->    0 rows  (COUNT = 0)     --> exit
```

**Important:** exit with `EXIT WHEN v_emps.COUNT = 0`, not with `EXIT WHEN c_emp%NOTFOUND` placed right after the fetch. On the last batch, which is smaller than the limit, `%NOTFOUND` is already TRUE. You would exit before processing those last rows. If you use `%NOTFOUND`, put it after the processing code at the end of the loop.

| Item | Guidance |
|------|----------|
| Batch size | 100 to 1000 is a common choice. Larger gives little extra speed and uses more memory |
| Memory | Bounded by `LIMIT` times the row size |
| Without `LIMIT` | The entire result set goes into memory |

**Follow-up questions**

- **Why not fetch everything at once?** Memory use grows with the table. A large table can cause memory errors or slow the whole server.
- **What happens if the table has fewer rows than the limit?** One `FETCH` returns all of them. The next returns `COUNT = 0`.
- **Can you use `LIMIT` with `SELECT ... BULK COLLECT INTO` (not a cursor)?** No. `LIMIT` works only with `FETCH`.

---

<a id="a7"></a>

### A7. What is FORALL?

**Level: Advanced**

**Short answer:** `FORALL` sends a whole collection of values to the SQL engine in one go to run one `INSERT`, `UPDATE`, `DELETE` or `MERGE` many times. It is much faster than a `FOR` loop with the same DML inside, because it uses one context switch instead of one per row.

**Explanation:**

Slow way:

```sql
BEGIN
    FOR i IN 1..v_ids.COUNT LOOP
        UPDATE employees
        SET salary = salary * 1.1
        WHERE id = v_ids(i);                   -- one context switch per row
    END LOOP;
END;
```

Fast way:

```sql
DECLARE
    TYPE t_id_tab IS TABLE OF employees.id%TYPE;
    v_ids t_id_tab;
BEGIN
    SELECT id
    BULK COLLECT INTO v_ids
    FROM employees
    WHERE department_id = 10;

    FORALL i IN 1..v_ids.COUNT
        UPDATE employees
        SET salary = salary * 1.1
        WHERE id = v_ids(i);

    DBMS_OUTPUT.PUT_LINE('Total rows updated: ' || SQL%ROWCOUNT);
    COMMIT;
END;
/
-- Output: Total rows updated: 2
```

Rules:

| Rule | Detail |
|------|--------|
| `FORALL` is not a loop | It is one statement with an index range. It has no `LOOP`/`END LOOP` |
| One DML statement only | The body is a single `INSERT`, `UPDATE`, `DELETE` or `MERGE` |
| Index must be dense | The range `1..COUNT` must have no gaps. Use `INDICES OF` or `VALUES OF` for sparse collections (Oracle 10g and later) |
| Can not use the index in an expression | Use `v_ids(i)`, not `v_ids(i) + 1` in older versions |
| Row counts per iteration | `SQL%BULK_ROWCOUNT(i)` gives the rows affected for element `i` |
| Total rows | `SQL%ROWCOUNT` gives the total |

Insert example using collections of values:

```sql
DECLARE
    TYPE t_num_tab  IS TABLE OF NUMBER;
    TYPE t_name_tab IS TABLE OF VARCHAR2(50);
    v_ids   t_num_tab  := t_num_tab(10, 11, 12);
    v_names t_name_tab := t_name_tab('Anik', 'Bina', 'Chandra');
BEGIN
    FORALL i IN 1..v_ids.COUNT
        INSERT INTO employees (id, name)
        VALUES (v_ids(i), v_names(i));
    COMMIT;
END;
/
```

Another form for sparse collections:

```sql
FORALL i IN INDICES OF v_ids
    DELETE FROM employees
    WHERE id = v_ids(i);
```

**Follow-up questions**

- **If one element fails, what happens?** By default, the first error stops the `FORALL` and Oracle rolls back the rows that this `FORALL` statement already processed. Use `SAVE EXCEPTIONS` to continue (see [A8](#a8)).
- **Why not just write one SQL statement?** If one SQL statement can do the job (`UPDATE ... WHERE department_id = 10`), use it. It is faster than `FORALL`. Use `FORALL` when each row needs different values from collections.
- **`FORALL` vs Java `addBatch`?** Same idea (many rows, few round trips) but done inside the database versus the client driver.
- **Does `FORALL` fire triggers?** Yes. Row-level triggers still fire per row.

---

<a id="a8"></a>

### A8. FORALL with SAVE EXCEPTIONS

**Level: Advanced**

**Short answer:** `SAVE EXCEPTIONS` lets a `FORALL` keep going when some rows fail. Oracle records each failure in `SQL%BULK_EXCEPTIONS` and raises one error (`ORA-24381`) at the end, which you catch.

**Explanation:**

```sql
DECLARE
    TYPE t_num_tab IS TABLE OF NUMBER;
    v_ids t_num_tab := t_num_tab(20, 1, 21);            -- id 1 already exists
    e_bulk_errors EXCEPTION;
    PRAGMA EXCEPTION_INIT(e_bulk_errors, -24381);
BEGIN
    FORALL i IN 1..v_ids.COUNT SAVE EXCEPTIONS
        INSERT INTO employees (id, name)
        VALUES (v_ids(i), 'Bulk user');

    COMMIT;
EXCEPTION
    WHEN e_bulk_errors THEN
        FOR j IN 1..SQL%BULK_EXCEPTIONS.COUNT LOOP
            DBMS_OUTPUT.PUT_LINE(
                'Element ' || SQL%BULK_EXCEPTIONS(j).ERROR_INDEX ||
                ' failed: ' || SQLERRM(-SQL%BULK_EXCEPTIONS(j).ERROR_CODE)
            );
        END LOOP;
        COMMIT;                              -- keep the rows that succeeded
END;
/
-- Output:
-- Element 2 failed: ORA-00001: unique constraint (...) violated
```

| Item | Meaning |
|------|---------|
| `SQL%BULK_EXCEPTIONS.COUNT` | Number of failed elements |
| `SQL%BULK_EXCEPTIONS(j).ERROR_INDEX` | Index of the failed element in your collection |
| `SQL%BULK_EXCEPTIONS(j).ERROR_CODE` | The Oracle error number (positive, so negate it for `SQLERRM`) |

After the `FORALL` with `SAVE EXCEPTIONS`, you choose to `COMMIT` the good rows or `ROLLBACK` all.

**Follow-up questions**

- **Does `SAVE EXCEPTIONS` stop on the first error?** No. It processes all elements, then raises `ORA-24381`.
- **Alternative in SQL only?** `INSERT ... LOG ERRORS INTO err_table` (DML error logging, Oracle 10g Release 2 and later).
- **Does it work for all DML?** Yes: `INSERT`, `UPDATE`, `DELETE`, `MERGE`.

---

<a id="a9"></a>

### A9. Dynamic SQL and EXECUTE IMMEDIATE

**Level: Advanced**

**Short answer:** Dynamic SQL is a SQL statement built as text and run at run time. `EXECUTE IMMEDIATE` runs it. Use it when the statement is not known at compile time, for example DDL or a variable table name. Use bind variables for values.

**Explanation:**

Static SQL is checked at compile time. DDL such as `CREATE TABLE` is not allowed directly in a PL/SQL block, so it needs dynamic SQL.

```sql
DECLARE
    v_count NUMBER;
    v_table VARCHAR2(30) := 'EMPLOYEES';
BEGIN
    -- DDL
    EXECUTE IMMEDIATE 'CREATE TABLE temp_log (msg VARCHAR2(100))';

    -- DML with bind variable (:1) and USING
    EXECUTE IMMEDIATE
        'INSERT INTO temp_log (msg) VALUES (:1)'
        USING 'started';

    -- Query returning one value: INTO
    EXECUTE IMMEDIATE
        'SELECT COUNT(*) FROM ' || v_table || ' WHERE department_id = :1'
        INTO v_count
        USING 10;

    DBMS_OUTPUT.PUT_LINE(v_count);

    EXECUTE IMMEDIATE 'DROP TABLE temp_log';
END;
/
-- Output: 2
```

| Clause | Purpose |
|--------|---------|
| `INTO` | Receive a single-row result |
| `USING` | Pass bind variable values (`IN`, `OUT` or `IN OUT`) |
| `BULK COLLECT INTO` | Receive many rows |
| `RETURNING INTO` | Get values from a DML statement |

For a multi-row dynamic query, use `OPEN cursor_variable FOR sql_text USING ...` (see [I4](#i4)).

**Bind variables** are placeholders (`:1`, `:name`) that carry values. They matter for two reasons:

- **Performance:** Oracle parses a statement text once and reuses the plan. If you write values into the text (`WHERE id = 5`, `WHERE id = 6`) every statement looks new, so Oracle parses each (a **hard parse**), which is slow and fills the shared memory.
- **Security:** bind values are never treated as SQL code (see [A10](#a10)).

You cannot bind identifiers such as table or column names. Only values can be bound. Identifiers must be concatenated, so validate them (see [A10](#a10)).

**Follow-up questions**

- **Are variables in static SQL bound?** Yes. A PL/SQL variable used inside static SQL is automatically a bind variable.
- **Does `EXECUTE IMMEDIATE` commit?** DML does not. DDL commits implicitly.
- **`EXECUTE IMMEDIATE` vs `DBMS_SQL`?** `DBMS_SQL` is the older, more manual API. Use it only when the number or types of columns are unknown at compile time.
- **When should you avoid dynamic SQL?** When static SQL works. Static SQL is checked at compile time and shows dependencies.

---

<a id="a10"></a>

### A10. SQL injection and DBMS_ASSERT

**Level: Advanced**

**Short answer:** SQL injection happens when user input is concatenated into a SQL string and changes what the statement does. Prevent it with bind variables for values, and `DBMS_ASSERT` to validate names (identifiers) that must be concatenated.

**Explanation:**

Unsafe:

```sql
CREATE OR REPLACE PROCEDURE find_emp_unsafe(
    p_name IN VARCHAR2
) AS
    v_sql VARCHAR2(500);
BEGIN
    v_sql := 'SELECT COUNT(*) FROM employees WHERE name = ''' || p_name || '''';
    EXECUTE IMMEDIATE v_sql;
END find_emp_unsafe;
/

-- Input:  x' OR '1'='1
-- Statement becomes:  SELECT COUNT(*) FROM employees WHERE name = 'x' OR '1'='1'
-- It matches all rows.
```

Safe: bind the value.

```sql
CREATE OR REPLACE PROCEDURE find_emp_safe(
    p_name IN VARCHAR2
) AS
    v_count NUMBER;
BEGIN
    EXECUTE IMMEDIATE
        'SELECT COUNT(*) FROM employees WHERE name = :1'
        INTO v_count
        USING p_name;
END find_emp_safe;
/
```

When a table or column name comes from input, bind variables cannot be used. Validate it with `DBMS_ASSERT`:

| Function | What it checks |
|----------|----------------|
| `DBMS_ASSERT.SIMPLE_SQL_NAME(s)` | `s` is a valid simple SQL name. Raises an error otherwise |
| `DBMS_ASSERT.SQL_OBJECT_NAME(s)` | `s` is the name of an existing object |
| `DBMS_ASSERT.ENQUOTE_NAME(s)` | Wraps `s` in double quotes safely |
| `DBMS_ASSERT.ENQUOTE_LITERAL(s)` | Wraps `s` in single quotes safely |

```sql
DECLARE
    v_table VARCHAR2(128) := 'EMPLOYEES';
    v_count NUMBER;
BEGIN
    EXECUTE IMMEDIATE
        'SELECT COUNT(*) FROM ' || DBMS_ASSERT.SQL_OBJECT_NAME(v_table)
        INTO v_count;
    DBMS_OUTPUT.PUT_LINE(v_count);
END;
/
-- Output: 3
-- With input such as 'EMPLOYEES; DROP TABLE x' it raises ORA-44002 (invalid object name).
```

Best rule: if you can, use a fixed list of allowed names (a `CASE` or `IF` that maps input to known names), not user text.

**Follow-up questions**

- **Does a Java `PreparedStatement` prevent injection in PL/SQL?** It protects the call to the procedure. Injection can still happen inside the procedure if it builds dynamic SQL from the parameter.
- **Can a procedure without dynamic SQL be injected?** No, static SQL is safe.
- **What else reduces risk?** Give the executing user minimum privileges, and consider invoker rights (see [A4](#a4)).

---

<a id="a11"></a>

### A11. How do you tune PL/SQL performance?

**Level: Advanced**

**Short answer:** Reduce context switches and round trips. Prefer one SQL statement over a row-by-row loop, use `BULK COLLECT` and `FORALL` when loops are needed, use bind variables, and measure with a profiler before changing anything.

**Explanation:**

Checklist, in order of impact:

| Technique | Why it helps |
|-----------|--------------|
| Replace row-by-row loops with one set-based SQL statement | One statement does the work in the SQL engine. No context switches. |
| `BULK COLLECT` with `LIMIT` for reading, `FORALL` for writing | Cuts context switches when logic cannot be written in SQL |
| Bind variables, no concatenated values | Avoids repeated hard parses |
| Avoid calling PL/SQL functions per row in SQL | Each call switches engines. Inline the logic, or use `WITH FUNCTION` / `PRAGMA UDF` (12c and later) |
| Do not `COMMIT` inside loops | Commit once, or in large batches |
| Use `%TYPE` / `%ROWTYPE`, correct data types | Avoids implicit conversions, which can block index use |
| Use `PLS_INTEGER` / `SIMPLE_INTEGER` for integer loops | Faster than `NUMBER` for pure integer math |
| `NOCOPY` for large `OUT` collections | Passes by reference, not by copy |
| Keep indexed columns free of functions in `WHERE` | Lets the optimizer use the index |
| Use a package for related code | Loaded once, used many times |

Example of the biggest win:

```sql
-- Slow: row by row ("slow-by-slow")
BEGIN
    FOR r IN (SELECT id FROM employees WHERE department_id = 10) LOOP
        UPDATE employees
        SET salary = salary * 1.1
        WHERE id = r.id;
    END LOOP;
END;
/

-- Fast: one statement
BEGIN
    UPDATE employees
    SET salary = salary * 1.1
    WHERE department_id = 10;
END;
/
```

Tools to find the slow part:

- `DBMS_PROFILER` and `DBMS_HPROF`: time spent per line or per subprogram.
- `DBMS_UTILITY.GET_TIME` or `DBMS_UTILITY.GET_CPU_TIME`: simple timers.
- `EXPLAIN PLAN` and SQL trace for the SQL inside PL/SQL.
- `PLSQL_OPTIMIZE_LEVEL` (default 2) lets the compiler optimize code, including automatic bulk fetching in cursor FOR loops.

**Follow-up questions**

- **What is "slow-by-slow" processing?** A loop that handles one row per iteration, doing a SQL statement each time.
- **Is PL/SQL itself slow?** Pure PL/SQL logic is fast. The slow part is usually SQL called many times, or a poor query.
- **Will bulk processing always win?** Only when a loop is unavoidable. A single SQL statement beats both.

---

<a id="a12"></a>

### A12. Pipelined table functions

**Level: Advanced**

**Short answer:** A pipelined function returns rows one by one as it creates them with `PIPE ROW`, so a query can start consuming rows before the function finishes. You call it in SQL with `SELECT * FROM TABLE(function_name(...))`.

**Explanation:**

A normal table function builds the whole collection and returns it at the end. This uses memory. A pipelined function streams the rows.

```sql
CREATE OR REPLACE TYPE num_tab AS TABLE OF NUMBER;
/

CREATE OR REPLACE FUNCTION gen_numbers(
    p_max IN NUMBER
) RETURN num_tab PIPELINED AS
BEGIN
    FOR i IN 1..p_max LOOP
        PIPE ROW (i);              -- send one row to the caller now
    END LOOP;

    RETURN;                        -- no value, just ends the function
END gen_numbers;
/

SELECT *
FROM TABLE(gen_numbers(3));
-- Output:
-- COLUMN_VALUE
-- 1
-- 2
-- 3
```

| Item | Meaning |
|------|---------|
| `PIPELINED` | Marks the function as streaming |
| `PIPE ROW(x)` | Sends one row to the query |
| `TABLE(...)` | Lets SQL treat the function result as a table |
| `RETURN;` | Ends with no value |

Use cases: transforming or generating large sets of rows, ETL (extract, transform, load) steps, where memory should stay small.

**Follow-up questions**

- **Why not return a normal collection?** It must be fully built in memory before the first row is available.
- **Can a pipelined function be used in a join?** Yes, like any table source.
- **Is it parallel capable?** It can run in parallel if declared with `PARALLEL_ENABLE` and a cursor parameter (advanced, not usually asked).
