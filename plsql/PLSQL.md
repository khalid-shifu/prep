# PL/SQL Notes

Beginner-friendly notes with short explanations and examples. Based on Oracle PL/SQL, run in SQL*Plus, SQL Developer or any Oracle client.

## Index

1. [Basics](#1-basics)
   - 1.1 [What is PL/SQL](#11-what-is-plsql)
   - 1.2 [Practice table](#12-practice-table)
   - 1.3 [Block structure](#13-block-structure)
   - 1.4 [Printing output](#14-printing-output)
   - 1.5 [Variables](#15-variables)
   - 1.6 [Conditions](#16-conditions)
   - 1.7 [Loops](#17-loops)
2. [SQL inside PL/SQL](#2-sql-inside-plsql)
   - 2.1 [SELECT INTO](#21-select-into)
   - 2.2 [INSERT, UPDATE, DELETE](#22-insert-update-delete)
   - 2.3 [%TYPE and %ROWTYPE](#23-type-and-rowtype)
3. [Exceptions](#3-exceptions)
4. [Procedures](#4-procedures)
   - 4.1 [Basic procedure](#41-basic-procedure)
   - 4.2 [Procedure with parameters](#42-procedure-with-parameters)
   - 4.3 [IN, OUT and IN OUT](#43-in-out-and-in-out)
5. [Functions](#5-functions)
6. [Cursors](#6-cursors)
   - 6.1 [What is a cursor](#61-what-is-a-cursor)
   - 6.2 [Implicit cursor](#62-implicit-cursor)
   - 6.3 [Cursor FOR loop](#63-cursor-for-loop)
   - 6.4 [Explicit cursor](#64-explicit-cursor)
   - 6.5 [Cursor attributes](#65-cursor-attributes)
   - 6.6 [Cursor with parameters](#66-cursor-with-parameters)
7. [Triggers](#7-triggers)
   - 7.1 [What is a trigger](#71-what-is-a-trigger)
   - 7.2 [Row-level trigger and :OLD / :NEW](#72-row-level-trigger-and-old--new)
   - 7.3 [Validation with RAISE_APPLICATION_ERROR](#73-validation-with-raise_application_error)
   - 7.4 [Statement-level trigger](#74-statement-level-trigger)
   - 7.5 [Trigger tips](#75-trigger-tips)
8. [Packages](#8-packages)
   - 8.1 [What is a package](#81-what-is-a-package)
   - 8.2 [Package specification](#82-package-specification)
   - 8.3 [Package body](#83-package-body)
   - 8.4 [Calling a package](#84-calling-a-package)
9. [Quick Reference and Interview Questions](#9-quick-reference-and-interview-questions)

---

## 1. Basics

### 1.1 What is PL/SQL

- PL/SQL = **P**rocedural **L**anguage extension to **SQL**, made by Oracle.
- Plain SQL runs one statement at a time. PL/SQL lets you wrap many statements together with variables, `IF`, loops, error handling, and reusable code (procedures, functions, packages, triggers) that is stored in the database.
- Benefits: less traffic between the app and the database (one call runs many statements), reusable logic, and better error handling.

### 1.2 Practice table

Most examples below use this table.

```sql
CREATE TABLE employees (
    id            NUMBER PRIMARY KEY,
    name          VARCHAR2(50),
    salary        NUMBER,
    department_id NUMBER
);

INSERT INTO employees VALUES (1, 'Rahim', 50000, 10);
INSERT INTO employees VALUES (2, 'Karim', 60000, 10);
INSERT INTO employees VALUES (3, 'Sumi',  45000, 20);
COMMIT;
```

### 1.3 Block structure

Everything in PL/SQL is written in a **block**. A block has up to three parts:

```sql
DECLARE
    -- 1. variables go here (optional)
BEGIN
    -- 2. your statements go here (required)
EXCEPTION
    -- 3. error handling goes here (optional)
END;
/
```

| Part | Required | Purpose |
|------|----------|---------|
| `DECLARE` | No | Declare variables, constants, cursors |
| `BEGIN ... END` | Yes | The actual code |
| `EXCEPTION` | No | What to do when an error happens |

Rules to remember:
- Every statement ends with `;`, including the final `END;`.
- The `/` on its own line, after the block, tells SQL*Plus "run it now". It is not part of PL/SQL itself.
- A block with no variables does not need `DECLARE` at all.

### 1.4 Printing output

```sql
SET SERVEROUTPUT ON;   -- run once per session, otherwise you see nothing

BEGIN
    DBMS_OUTPUT.PUT_LINE('Hello World');
END;
/
```

- `DBMS_OUTPUT.PUT_LINE(...)` prints a line.
- `SET SERVEROUTPUT ON` is a SQL*Plus / SQL Developer setting. Forgetting it is the most common reason "nothing prints".
- `||` joins (concatenates) text: `'Hello ' || name`.
- You cannot print a `BOOLEAN` directly. Convert it first, or use `IF`.

### 1.5 Variables

Declared in the `DECLARE` section. `:=` assigns a value (plain `=` is only for comparing).

```sql
DECLARE
    age          NUMBER        := 25;          -- integer
    salary       NUMBER(10,2)  := 50.50;       -- decimal, 10 digits total, 2 after the point
    temperature  NUMBER        := -5;          -- negative number
    name         VARCHAR2(50)  := 'John Doe';  -- text up to 50 characters
    gender       CHAR(1)       := 'M';         -- fixed length text
    joining_date DATE          := SYSDATE;     -- current date and time
    is_active    BOOLEAN       := TRUE;
    pi CONSTANT  NUMBER        := 3.14159;     -- cannot be changed later
BEGIN
    DBMS_OUTPUT.PUT_LINE(age);
END;
/
```

| Type | Use for |
|------|---------|
| `NUMBER` | Integers and decimals |
| `VARCHAR2(n)` | Variable-length text (the normal text type in Oracle) |
| `CHAR(n)` | Fixed-length text |
| `DATE` | Date and time |
| `BOOLEAN` | TRUE / FALSE / NULL (PL/SQL only, not a table column type in older Oracle versions) |

### 1.6 Conditions

`IF ... THEN ... END IF;`

```sql
DECLARE
    age NUMBER := 20;
BEGIN
    IF age >= 18 THEN
        DBMS_OUTPUT.PUT_LINE('Adult');
    ELSE
        DBMS_OUTPUT.PUT_LINE('Minor');
    END IF;
END;
/
```

Several conditions use `ELSIF` (spelled exactly like this, no extra E):

```sql
DECLARE
    age NUMBER := 15;
BEGIN
    IF age < 13 THEN
        DBMS_OUTPUT.PUT_LINE('Child');
    ELSIF age < 18 THEN
        DBMS_OUTPUT.PUT_LINE('Teenager');
    ELSE
        DBMS_OUTPUT.PUT_LINE('Adult');
    END IF;
END;
/
```

`CASE` is a cleaner alternative when comparing one value against many:

```sql
DECLARE
    grade CHAR(1) := 'B';
BEGIN
    CASE grade
        WHEN 'A' THEN DBMS_OUTPUT.PUT_LINE('Excellent');
        WHEN 'B' THEN DBMS_OUTPUT.PUT_LINE('Good');
        ELSE          DBMS_OUTPUT.PUT_LINE('Try again');
    END CASE;
END;
/
```

### 1.7 Loops

**FOR loop.** Runs a fixed number of times. The counter `i` is created automatically; do not declare it.

```sql
BEGIN
    FOR i IN 1..5 LOOP
        DBMS_OUTPUT.PUT_LINE(i);
    END LOOP;
END;
/
```

**WHILE loop.** Runs while the condition is true. You must change the variable yourself or it never stops.

```sql
DECLARE
    i NUMBER := 1;
BEGIN
    WHILE i <= 3 LOOP
        DBMS_OUTPUT.PUT_LINE(i);
        i := i + 1;
    END LOOP;
END;
/
```

**Basic LOOP.** Runs forever until you `EXIT`.

```sql
DECLARE
    i NUMBER := 1;
BEGIN
    LOOP
        DBMS_OUTPUT.PUT_LINE(i);
        i := i + 1;
        EXIT WHEN i > 3;
    END LOOP;
END;
/
```

---

## 2. SQL inside PL/SQL

### 2.1 SELECT INTO

Inside PL/SQL a `SELECT` must put its result somewhere: use `INTO` to store it in a variable.

```sql
DECLARE
    emp_salary NUMBER;
BEGIN
    SELECT salary
    INTO emp_salary
    FROM employees
    WHERE id = 1;

    DBMS_OUTPUT.PUT_LINE(emp_salary);
END;
/
```

`SELECT INTO` must return **exactly one row**:
- 0 rows raises `NO_DATA_FOUND`.
- 2 or more rows raises `TOO_MANY_ROWS`.

To handle many rows, use a cursor (see [Cursors](#6-cursors)).

### 2.2 INSERT, UPDATE, DELETE

DML statements work as normal inside a block.

```sql
BEGIN
    INSERT INTO employees (id, name, salary, department_id)
    VALUES (4, 'Hasan', 40000, 20);

    UPDATE employees SET salary = salary + 5000 WHERE id = 1;

    DBMS_OUTPUT.PUT_LINE('Employee added');
    COMMIT;      -- save the changes (ROLLBACK undoes them)
END;
/
```

### 2.3 %TYPE and %ROWTYPE

Let the variable copy its type from a table so you do not have to repeat it (and it stays correct if the table changes).

```sql
DECLARE
    v_salary employees.salary%TYPE;   -- same type as the salary column
    v_emp    employees%ROWTYPE;       -- a whole row: v_emp.id, v_emp.name, ...
BEGIN
    SELECT * INTO v_emp FROM employees WHERE id = 1;
    DBMS_OUTPUT.PUT_LINE(v_emp.name || ' earns ' || v_emp.salary);
END;
/
```

---

## 3. Exceptions

An **exception** is a runtime error. Without handling, it stops the block. The `EXCEPTION` section catches it so you can respond.

```sql
BEGIN
    DBMS_OUTPUT.PUT_LINE(10 / 0);
EXCEPTION
    WHEN ZERO_DIVIDE THEN
        DBMS_OUTPUT.PUT_LINE('Cannot divide by zero');
END;
/
```

Common built-in exceptions:

| Exception | When it happens |
|-----------|-----------------|
| `ZERO_DIVIDE` | Division by zero |
| `NO_DATA_FOUND` | `SELECT INTO` returned no row |
| `TOO_MANY_ROWS` | `SELECT INTO` returned more than one row |
| `DUP_VAL_ON_INDEX` | Duplicate value in a primary or unique key |
| `VALUE_ERROR` | Wrong data type or value too large |
| `OTHERS` | Catches any exception not named before it |

Several handlers plus a catch-all:

```sql
DECLARE
    v_name employees.name%TYPE;
BEGIN
    SELECT name INTO v_name FROM employees WHERE id = 999;
    DBMS_OUTPUT.PUT_LINE(v_name);
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('No employee with that id');
    WHEN TOO_MANY_ROWS THEN
        DBMS_OUTPUT.PUT_LINE('More than one row found');
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Unexpected error: ' || SQLERRM);   -- SQLERRM = error message
END;
/
```

Your own exception:

```sql
DECLARE
    negative_salary EXCEPTION;
    v_salary NUMBER := -100;
BEGIN
    IF v_salary < 0 THEN
        RAISE negative_salary;     -- RAISE throws the exception
    END IF;
EXCEPTION
    WHEN negative_salary THEN
        DBMS_OUTPUT.PUT_LINE('Salary cannot be negative');
END;
/
```

Always put `WHEN OTHERS` last, and avoid using it to silently hide errors.

---

## 4. Procedures

A **procedure** is a named block saved in the database. You write it once and call it many times. It does an action (insert, update, print) and does not have to return a value.

### 4.1 Basic procedure

```sql
CREATE OR REPLACE PROCEDURE greet AS
BEGIN
    DBMS_OUTPUT.PUT_LINE('Hello');
END;
/

-- calling it
BEGIN
    greet;
END;
/
```

Two ways to create:
1. `CREATE PROCEDURE` creates a new procedure and fails if that name already exists.
2. `CREATE OR REPLACE PROCEDURE` creates it if new, or replaces the old definition if it exists. Use this one while developing.

Other things to know:
- `AS` and `IS` mean the same thing here. Both start the declaration section.
- In a procedure you **do not write `DECLARE`**. The part between `AS` and `BEGIN` is already the declaration section.
- Call it from a block, or from SQL*Plus with `EXEC greet;`.

### 4.2 Procedure with parameters

Parameters let you pass values in.

```sql
CREATE OR REPLACE PROCEDURE greet(
    person_name VARCHAR2
) AS
BEGIN
    DBMS_OUTPUT.PUT_LINE('Hello ' || person_name);
END;
/

-- calling / driver
BEGIN
    greet('Rahim');
    greet('Karim');
END;
/
```

- `||` is the concatenate operator (joins text).
- In a parameter you write `VARCHAR2` without a length. The size is decided by the value passed in.
- Variables declared between `AS` and `BEGIN` do use a length.

Example 2, with a local variable:

```sql
CREATE OR REPLACE PROCEDURE add_numbers(
    x NUMBER,
    y NUMBER
) AS
    s NUMBER := 0;          -- no DECLARE needed, AS already starts this section
BEGIN
    s := x + y;
    DBMS_OUTPUT.PUT_LINE('sum = ' || s);
END;
/

BEGIN
    add_numbers(2, 3);
END;
/
```

Avoid naming your own procedure `sum`, `count`, `max` etc. They clash with built-in SQL functions and cause confusing errors.

### 4.3 IN, OUT and IN OUT

A parameter has a **mode** that says which way data flows:

| Mode | Meaning | Can the procedure change it? |
|------|---------|------------------------------|
| `IN` | Value goes **into** the procedure (default if you write nothing) | No, read only |
| `OUT` | Value comes **out** of the procedure to the caller | Yes, it starts empty (NULL) |
| `IN OUT` | Goes in, can be changed, and the new value comes out | Yes |

**OUT example:** a procedure hands a result back through a parameter.

```sql
CREATE OR REPLACE PROCEDURE calculate_sum(
    x      IN  NUMBER,
    y      IN  NUMBER,
    result OUT NUMBER
) AS
BEGIN
    result := x + y;
END;
/

-- calling: the caller declares a variable to receive the result
DECLARE
    total NUMBER;
BEGIN
    calculate_sum(2, 3, total);
    DBMS_OUTPUT.PUT_LINE(total);     -- 5
END;
/
```

**IN OUT example:** the same variable is sent in and updated.

```sql
CREATE OR REPLACE PROCEDURE double_value(
    num IN OUT NUMBER
) AS
BEGIN
    num := num * 2;
END;
/

DECLARE
    val NUMBER := 10;
BEGIN
    double_value(val);
    DBMS_OUTPUT.PUT_LINE(val);       -- 20
END;
/
```

---

## 5. Functions

A **function** is like a procedure, but it **always returns exactly one value** with `RETURN`. Because it returns a value, you can use it inside expressions.

```sql
CREATE OR REPLACE FUNCTION add_two(
    x NUMBER,
    y NUMBER
) RETURN NUMBER AS          -- RETURN NUMBER = the type of value it gives back
BEGIN
    RETURN x + y;           -- sends the value to the caller
END;
/

-- calling from PL/SQL
DECLARE
    total NUMBER;
BEGIN
    total := add_two(2, 3);
    DBMS_OUTPUT.PUT_LINE(total);   -- 5
END;
/

-- calling from plain SQL
SELECT add_two(2, 3) FROM dual;
```

A more realistic function that reads a table:

```sql
CREATE OR REPLACE FUNCTION get_salary(p_id NUMBER) RETURN NUMBER AS
    v_salary NUMBER;
BEGIN
    SELECT salary INTO v_salary FROM employees WHERE id = p_id;
    RETURN v_salary;
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        RETURN NULL;
END;
/

SELECT name, get_salary(id) FROM employees;
```

**Function vs Procedure**

| Function | Procedure |
|----------|-----------|
| Must declare a return type with `RETURN` | Does not declare a return type |
| Returns a value with `RETURN` | Can pass values back only through `OUT` parameters |
| Can be used in expressions, and in SQL `SELECT` (subject to Oracle's rules: a function called from SQL generally cannot change data) | Called as a statement on its own |
| Best for calculating something | Best for doing an action (insert, update, process) |

Functions and procedures share one name space in a schema, so you cannot have a procedure and a function with the same name.

**"Used in expressions" vs "called as a statement", explained**

An **expression** is anything that produces a value: `2 + 3`, `salary * 12`, `UPPER(name)`. A function produces a value, so it can sit anywhere a value can. A procedure produces no value, so it can only stand alone as a complete statement.

Setup used below:

```sql
CREATE OR REPLACE FUNCTION annual_salary(p_monthly NUMBER) RETURN NUMBER AS
BEGIN
    RETURN p_monthly * 12;
END;
/

CREATE OR REPLACE PROCEDURE print_hello(p_name VARCHAR2) AS
BEGIN
    DBMS_OUTPUT.PUT_LINE('Hello ' || p_name);
END;
/
```

**A function can be used inside other code**

```sql
DECLARE
    v_total NUMBER;
BEGIN
    -- 1. on the right side of an assignment
    v_total := annual_salary(5000);                      -- 60000

    -- 2. inside a bigger expression
    v_total := annual_salary(5000) + 1000;               -- 61000

    -- 3. in an IF condition
    IF annual_salary(5000) > 50000 THEN
        DBMS_OUTPUT.PUT_LINE('High earner');
    END IF;

    -- 4. as an argument to another call
    DBMS_OUTPUT.PUT_LINE(annual_salary(5000));
END;
/
```

**A function can be used inside a SQL query**

```sql
-- in the SELECT list
SELECT name, salary, annual_salary(salary) AS yearly FROM employees;

-- in WHERE
SELECT name FROM employees WHERE annual_salary(salary) > 600000;

-- in ORDER BY
SELECT name FROM employees ORDER BY annual_salary(salary) DESC;
```

**A procedure cannot be used like that.** These all fail, because a procedure gives back no value:

```sql
v_total := print_hello('Rahim');                       -- error
SELECT print_hello('Rahim') FROM dual;                 -- error
IF print_hello('Rahim') THEN ...                       -- error
```

A procedure is called as a **statement on its own line**:

```sql
BEGIN
    print_hello('Rahim');        -- correct
END;
/

-- or from SQL*Plus
EXEC print_hello('Rahim');
```

**Oracle's rule for functions called from SQL (the "subject to Oracle's rules" part)**

A function used inside a `SELECT` (or `WHERE`) is not allowed to change data. If it contains `INSERT`, `UPDATE`, `DELETE` or `COMMIT`, Oracle raises an error when you call it from SQL:

```sql
CREATE OR REPLACE FUNCTION bad_function(p_id NUMBER) RETURN NUMBER AS
BEGIN
    UPDATE employees SET salary = salary + 1 WHERE id = p_id;   -- changes data
    RETURN 1;
END;
/

SELECT bad_function(1) FROM dual;
-- ORA-14551: cannot perform a DML operation inside a query
```

The same function **works** when called from a PL/SQL block, because that is not a query:

```sql
DECLARE
    v NUMBER;
BEGIN
    v := bad_function(1);        -- allowed
END;
/
```

Why the rule exists: a query can run a function many times, once per row, in an order the database chooses. Letting it modify data during a query would make results unpredictable.

**Rule of thumb**
- Need a value back to use in an expression or query: write a **function**, and keep it read-only.
- Need to do an action such as insert, update, delete or send a notification: write a **procedure**.
- Need both an action and a result: write a procedure with an `OUT` parameter.

---

## 6. Cursors

### 6.1 What is a cursor

When a query returns **many rows**, PL/SQL needs a way to handle them one at a time. A **cursor** is a pointer to the query's result set that moves row by row. (`SELECT INTO` only works for exactly one row.)

Types:
- **Implicit cursor**: Oracle creates and manages it for you.
- **Explicit cursor**: you declare, open, fetch and close it yourself, for full control.

### 6.2 Implicit cursor

Every DML statement (`INSERT`, `UPDATE`, `DELETE`) and every `SELECT INTO` automatically uses an implicit cursor named `SQL`. You can check how it went with attributes:

```sql
BEGIN
    UPDATE employees SET salary = salary + 1000 WHERE department_id = 10;
    DBMS_OUTPUT.PUT_LINE(SQL%ROWCOUNT || ' rows updated');   -- how many rows were affected

    IF SQL%NOTFOUND THEN
        DBMS_OUTPUT.PUT_LINE('No rows matched');
    END IF;
    COMMIT;
END;
/
```

### 6.3 Cursor FOR loop

The easiest way to loop through many rows. Oracle opens, fetches and closes the cursor automatically, and the loop variable (`emp`) is created for you. No `DECLARE` needed.

```sql
BEGIN
    FOR emp IN (
        SELECT name FROM employees
    )
    LOOP
        DBMS_OUTPUT.PUT_LINE(emp.name);
    END LOOP;
END;
/
```

Strictly speaking, Oracle's term "implicit cursor" refers to the `SQL` cursor used for DML (6.2). This loop form is a **cursor FOR loop**: you do not manage the cursor yourself, which is why it is often called "implicit style".

### 6.4 Explicit cursor

You control each step:

1. `DECLARE`: define the cursor with its query.
2. `OPEN`: run the query and prepare the results.
3. `FETCH`: read the next row into variables.
4. `CLOSE`: free the cursor.

```sql
DECLARE
    CURSOR emp_cursor IS                 -- CURSOR declarations use IS (not AS)
        SELECT name FROM employees;

    v_name VARCHAR2(100);
BEGIN
    OPEN emp_cursor;

    LOOP
        FETCH emp_cursor INTO v_name;
        EXIT WHEN emp_cursor%NOTFOUND;   -- stop when there are no more rows

        DBMS_OUTPUT.PUT_LINE(v_name);
    END LOOP;

    CLOSE emp_cursor;
END;
/
```

Use `%ROWTYPE` to fetch several columns at once:

```sql
DECLARE
    CURSOR emp_cursor IS
        SELECT name, salary FROM employees WHERE department_id = 10;

    r emp_cursor%ROWTYPE;
BEGIN
    OPEN emp_cursor;
    LOOP
        FETCH emp_cursor INTO r;
        EXIT WHEN emp_cursor%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE(r.name || ': ' || r.salary);
    END LOOP;
    CLOSE emp_cursor;
END;
/
```

A declared cursor also works in a FOR loop, and then you skip OPEN/FETCH/CLOSE:

```sql
DECLARE
    CURSOR rec IS
        SELECT name, salary
        FROM employees
        WHERE department_id = 10;
BEGIN
    FOR r IN rec LOOP
        DBMS_OUTPUT.PUT_LINE(r.name || ': ' || r.salary);
    END LOOP;
END;
/
```

### 6.5 Cursor attributes

| Attribute | Meaning |
|-----------|---------|
| `%FOUND` | TRUE if the last fetch/DML got a row |
| `%NOTFOUND` | TRUE if the last fetch got no row (used to exit loops) |
| `%ROWCOUNT` | Number of rows fetched or affected so far |
| `%ISOPEN` | TRUE if the cursor is open |

Use `SQL%...` for implicit cursors and `cursor_name%...` for explicit ones.

### 6.6 Cursor with parameters

Reuse one cursor with different values.

```sql
DECLARE
    CURSOR emp_by_dept(p_dept NUMBER) IS
        SELECT name, salary FROM employees WHERE department_id = p_dept;
BEGIN
    FOR r IN emp_by_dept(10) LOOP
        DBMS_OUTPUT.PUT_LINE('Dept 10: ' || r.name);
    END LOOP;

    FOR r IN emp_by_dept(20) LOOP
        DBMS_OUTPUT.PUT_LINE('Dept 20: ' || r.name);
    END LOOP;
END;
/
```

Rule of thumb: use a **cursor FOR loop** in most cases. Use a manual `OPEN/FETCH/CLOSE` only when you need fine control. And when plain SQL can do the job, prefer SQL over looping row by row.

---

## 7. Triggers

### 7.1 What is a trigger

A **trigger** is a block of PL/SQL that runs **automatically** when something happens on a table (an `INSERT`, `UPDATE` or `DELETE`). You never call it yourself. Typical uses: auditing changes, validating data, filling in default values.

Parts of a trigger definition:

| Part | Options | Meaning |
|------|---------|---------|
| Timing | `BEFORE` / `AFTER` | Run before or after the change |
| Event | `INSERT` / `UPDATE` / `DELETE` | What fires it (can combine with `OR`) |
| Level | `FOR EACH ROW` or none | Row-level runs once per row; statement-level runs once per statement |

### 7.2 Row-level trigger and :OLD / :NEW

Inside a row-level trigger, `:OLD` is the row before the change and `:NEW` is the row after.

| Event | `:OLD` | `:NEW` |
|-------|--------|--------|
| INSERT | not available (NULL) | the new row |
| UPDATE | the row before | the row after |
| DELETE | the row being deleted | not available (NULL) |

Example: log every salary change into an audit table.

```sql
CREATE TABLE salary_audit (
    emp_id     NUMBER,
    old_salary NUMBER,
    new_salary NUMBER,
    changed_on DATE
);

CREATE OR REPLACE TRIGGER trg_salary_audit
AFTER UPDATE OF salary ON employees          -- fires only when the salary column is updated
FOR EACH ROW
BEGIN
    INSERT INTO salary_audit (emp_id, old_salary, new_salary, changed_on)
    VALUES (:OLD.id, :OLD.salary, :NEW.salary, SYSDATE);
END;
/

-- test
UPDATE employees SET salary = 55000 WHERE id = 1;
SELECT * FROM salary_audit;
```

A `BEFORE` trigger can change `:NEW` values before they are saved:

```sql
CREATE OR REPLACE TRIGGER trg_upper_name
BEFORE INSERT ON employees
FOR EACH ROW
BEGIN
    :NEW.name := UPPER(:NEW.name);    -- 'rahim' is stored as 'RAHIM'
END;
/
```

### 7.3 Validation with RAISE_APPLICATION_ERROR

`RAISE_APPLICATION_ERROR(code, message)` stops the statement and sends your own error to the caller. The code must be between -20000 and -20999.

```sql
CREATE OR REPLACE TRIGGER trg_check_salary
BEFORE INSERT OR UPDATE ON employees
FOR EACH ROW
BEGIN
    IF :NEW.salary < 0 THEN
        RAISE_APPLICATION_ERROR(-20001, 'Salary cannot be negative');
    END IF;
END;
/
```

One trigger can handle several events. Inside it, check which one fired with `INSERTING`, `UPDATING`, `DELETING`:

```sql
CREATE OR REPLACE TRIGGER trg_emp_changes
AFTER INSERT OR UPDATE OR DELETE ON employees
FOR EACH ROW
BEGIN
    IF INSERTING THEN
        DBMS_OUTPUT.PUT_LINE('Inserted ' || :NEW.name);
    ELSIF UPDATING THEN
        DBMS_OUTPUT.PUT_LINE('Updated ' || :OLD.name);
    ELSIF DELETING THEN
        DBMS_OUTPUT.PUT_LINE('Deleted ' || :OLD.name);
    END IF;
END;
/
```

### 7.4 Statement-level trigger

Without `FOR EACH ROW` the trigger runs **once per statement**, no matter how many rows it affects. There is no `:OLD` / `:NEW`.

```sql
CREATE OR REPLACE TRIGGER trg_no_weekend
BEFORE INSERT OR UPDATE OR DELETE ON employees
BEGIN
    IF TO_CHAR(SYSDATE, 'DY') IN ('SAT', 'SUN') THEN
        RAISE_APPLICATION_ERROR(-20002, 'Changes are not allowed on weekends');
    END IF;
END;
/
```

### 7.5 Trigger tips

- Manage triggers: `DROP TRIGGER trg_name;`, `ALTER TRIGGER trg_name DISABLE;` / `ENABLE;`.
- Do not use `COMMIT` or `ROLLBACK` inside a trigger. It commits as part of the statement that fired it.
- A row-level trigger cannot read or modify the table it is firing on, or Oracle raises the **mutating table** error (`ORA-04091`). Use `:NEW` / `:OLD` instead, or redesign.
- Triggers are invisible side effects, which makes debugging hard. Keep them small, and prefer constraints (`NOT NULL`, `CHECK`) and normal procedures where they are enough.

---

## 8. Packages

### 8.1 What is a package

A **package** groups related procedures, functions, variables and cursors into one named unit, like a class. Benefits: organization, hiding internal code, and better performance (the whole package loads into memory on first use).

A package has **two parts**, created separately:

| Part | What it contains | Who sees it |
|------|------------------|-------------|
| **Specification** (spec) | Only the **declarations** (names, parameters, return types). The public menu. | Everyone who can use the package |
| **Body** | The **code** for those subprograms, plus any private helpers | Hidden from callers |

### 8.2 Package specification

```sql
CREATE OR REPLACE PACKAGE emp_pkg AS
    PROCEDURE add_employee(
        p_id     NUMBER,
        p_name   VARCHAR2,
        p_salary NUMBER
    );

    FUNCTION get_salary(p_id NUMBER) RETURN NUMBER;

    PROCEDURE give_raise(p_id NUMBER, p_percent NUMBER);
END emp_pkg;
/
```

Only the signature is here, with no code. Anything not listed in the spec is private.

### 8.3 Package body

The body must implement **everything** declared in the spec, with matching names and parameters.

```sql
CREATE OR REPLACE PACKAGE BODY emp_pkg AS

    -- private function: not in the spec, so only this package can use it.
    -- Defined first because it must exist before the code that calls it.
    FUNCTION is_valid_salary(p_salary NUMBER) RETURN BOOLEAN AS
    BEGIN
        RETURN p_salary > 0;
    END is_valid_salary;

    PROCEDURE add_employee(
        p_id     NUMBER,
        p_name   VARCHAR2,
        p_salary NUMBER
    ) AS
    BEGIN
        IF NOT is_valid_salary(p_salary) THEN
            RAISE_APPLICATION_ERROR(-20003, 'Invalid salary');
        END IF;

        INSERT INTO employees (id, name, salary)
        VALUES (p_id, p_name, p_salary);
    END add_employee;

    FUNCTION get_salary(p_id NUMBER) RETURN NUMBER AS
        v_salary NUMBER;
    BEGIN
        SELECT salary INTO v_salary FROM employees WHERE id = p_id;
        RETURN v_salary;
    END get_salary;

    PROCEDURE give_raise(p_id NUMBER, p_percent NUMBER) AS
    BEGIN
        UPDATE employees
        SET salary = salary + salary * p_percent / 100
        WHERE id = p_id;
    END give_raise;

END emp_pkg;
/
```

### 8.4 Calling a package

Use `package_name.item_name`:

```sql
BEGIN
    emp_pkg.add_employee(10, 'Nila', 52000);
    emp_pkg.give_raise(10, 10);
    DBMS_OUTPUT.PUT_LINE(emp_pkg.get_salary(10));    -- 57200
END;
/

SELECT emp_pkg.get_salary(1) FROM dual;
```

Notes:
- Change only the body and callers are unaffected, as long as the spec stays the same. This is why the spec and body are separate.
- Changing the spec invalidates programs that depend on it, so keep specs stable.
- A package can also hold constants and variables, shared by everything in the same session.
- You can overload: several procedures with the same name but different parameters, inside one package.
- Drop with `DROP PACKAGE BODY emp_pkg;` (body only) or `DROP PACKAGE emp_pkg;` (both).

---

## 9. Quick Reference and Interview Questions

**Syntax cheat sheet**

| Task | Syntax |
|------|--------|
| Assign | `x := 5;` |
| Compare | `x = 5` |
| Concatenate | `'a' \|\| 'b'` |
| Print | `DBMS_OUTPUT.PUT_LINE(...)` |
| Else-if | `ELSIF` |
| For loop | `FOR i IN 1..5 LOOP ... END LOOP;` |
| Copy column type | `v employees.salary%TYPE` |
| Copy row type | `r employees%ROWTYPE` |
| Raise own error | `RAISE_APPLICATION_ERROR(-20001, 'msg')` |
| Run block in SQL*Plus | `/` on its own line |

**Common questions (practice answering aloud)**

| # | Question | Short answer |
|---|----------|--------------|
| 1 | What is PL/SQL? | Oracle's procedural extension of SQL: variables, control flow, exceptions, stored code. |
| 2 | Parts of a block? | `DECLARE` (optional), `BEGIN` (required), `EXCEPTION` (optional), `END`. |
| 3 | Procedure vs function? | A function must return one value and can be used in expressions; a procedure performs an action and returns values only via `OUT`. |
| 4 | IN, OUT, IN OUT? | Input only, output only, input that can be modified and returned. |
| 5 | Implicit vs explicit cursor? | Implicit is managed by Oracle (`SQL%ROWCOUNT`); explicit is declared, opened, fetched and closed by you. |
| 6 | What does `%TYPE` / `%ROWTYPE` do? | Copies the datatype of a column / a whole row structure from a table. |
| 7 | `NO_DATA_FOUND` vs `TOO_MANY_ROWS`? | `SELECT INTO` returned 0 rows vs more than 1 row. |
| 8 | What is a trigger? When is it a bad idea? | Code that fires automatically on DML. Overuse hides logic and causes hard-to-debug side effects and mutating table errors. |
| 9 | Row-level vs statement-level trigger? | Row-level (`FOR EACH ROW`) runs per row and has `:OLD` / `:NEW`; statement-level runs once per statement. |
| 10 | Package spec vs body? | Spec is the public declarations; body holds the code and private helpers. |
| 11 | Why use packages? | Organization, encapsulation, shared state, faster loading, easier changes without breaking callers. |
| 12 | `AS` vs `IS`? | Same meaning for procedures, functions and packages; cursor declarations use `IS`. |
| 13 | `CREATE` vs `CREATE OR REPLACE`? | `CREATE` fails if the object exists; `OR REPLACE` overwrites it. |
| 14 | Why prefer a cursor FOR loop? | Shorter, and Oracle handles open, fetch, close and exit automatically. |
