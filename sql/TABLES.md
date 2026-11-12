# SQL Practice Tables

Used by all questions in [SQL.md](SQL.md).

## Index

- [Tables and data](#tables-and-data)
- [Setup script](#setup-script)

## Tables and data

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

## Setup script

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

