# sql-master

Order of execution of SQL commands/clauses:

1. FROM, UPDATE
2. JOIN
3. WHERE
4. GROUP BY
5. HAVING
6. SELECT
7. DISTINCT
8. ORDER BY
9. LIMIT, TOP

When it comes to writing SQL, there are two kinds of orders.
First, the order in which the commands/clauses are executed,
and Second, the order in which they can be written. Both are
different. A command having higher priority can be come later than a command than that has lower priority. E.g. SELECT has lower priority than WHERE but we can't write a query with WHERE clause first and SELECT later. Commands and clauses can only
be written in a certain order (i.e. what commes after what etc.).
When writing queries, we have to be wary of both the orders - how they execute in relation to each other and what positions they can be written in relation to each other.

# 1. Creating a table

```sql
CREATE table amazon_orders
(
    order_id integer,
    order_date date,
    product_name varchar(100),
    total_price decimal(10, 2),
    payment_method varchar(20)
);
```

# 2. Deleting a table

```sql
DROP TABLE AMAZON_ORDERS;
```

# 3. Inserting into the table

```sql
--inserts two rows
INSERT INTO amazon_orders VALUES
(
    1,
    '2022-10-01',
    'Baby Milk',
    30.50,
    'UPI'
), (
    2,
    '2023-11-11',
    'Soap',
    12.00,
    'Bank'
);
```

# 4. Limiting rows

```sql
SELECT TOP 2 *
FROM AMAZON_ORDERS;
```

# 5. Data sorting

```sql
SELECT *
FROM AMAZON_ORDERS
ORDER BY ORDER_DATE DESC, TOTAL_PRICE ASC;
```

# 6. DDL (Data Definition Language)

CREATE, DROP, ALTER

# 7. DML (Data Manipulation Language)

INSERT, UPDATE, DELETE

# 8. DQL (Data Query Language)

SELECT

# 9. Delete data (not table)

```sql
DELETE FROM AMAZON_ORDERS;

DELETE FROM AMAZON_ORDERS
WHERE ORDER_ID>=100;
```

# 10. Altering table

```sql
ALTER TABLE AMAZON_ORDERS ALTER COLUMN ORDER_DATE DATETIME;

ALTER TABLE AMAZON_ORDERS ADD USER_NAME VARCHAR(20);

ALTER TABLE AMAZON_ORDERS DROP COLUMN CATEGORY;
```

# 11. Constraints - NOT NULL, CHECK, UNIQUE, DEFAULT, PRIMARY KEY, FOREIGN KEY

(`UNIQUE` key cannot be duplicate but can be null, `PRIMARY KEY` is unique as well as not null. We can have multiple `UNIQUE` key constrains in a table, but there can only be one `PRIMARY` key constraint)
(PRIMARY KEY constraint is `UNIQUE KEY + NOT NULL` contraint combined)

```sql
CREATE TABLE AMAZON_ORDERS
(
    ORDER_ID INTEGER NOT NULL UNIQUE,
    ORDER_DATE DATE,
    PRODUCT_NAME VARCHAR(100),
    TOTAL_PRICE DECIMAL(6, 2),
    PAYMENT_METHOD VARCHAR(20) CHECK (PAYMENT_METHOD IN ('UPI', 'CREDIT CARD')),
    DISCOUNT INTEGER CHECK (DISCOUNT <= 20),
    CATEGORY VARCHAR(20) DEFAULT 'Mens Wear'
)

CREATE TABLE AMAZON_ORDERS
(
    ORDER_ID INTEGER,
    ORDER_DATE DATE,
    PRODUCT_NAME VARCHAR(100),
    TOTAL_PRICE DECIMAL(6, 2),
    PAYMENT_METHOD VARCHAR(20),
    DISCOUNT INTEGER CHECK (DISCOUNT <= 20),
    CATEGORY VARCHAR(20)
    PRIMARY KEY (ORDER_ID)
)

CREATE TABLE AMAZON_ORDERS
(
    ORDER_ID INTEGER,
    ORDER_DATE DATE,
    PRODUCT_NAME VARCHAR(100),
    TOTAL_PRICE DECIMAL(6, 2),
    PAYMENT_METHOD VARCHAR(20),
    DISCOUNT INTEGER CHECK (DISCOUNT <= 20),
    CATEGORY VARCHAR(20)
    PRIMARY KEY (ORDER_ID, PRODUCT_NAME)
)
```

# 12. Update

```sql
UPDATE AMAZON_ORDERS
SET DISCOUNT=10;

UPDATE AMAZON_ORDERS
SET PRODUCT_NAME='JEANS', PAYMENT_METHOD='CREDIT CARD'
WHERE PRODUCT_NAME='PAJAMAS';

UPDATE AMAZON_ORDERS
SET DISCOUNT=25
WHERE ORDER_ID=20;
```

# 13. SELECT Statement in depth

```sql
SELECT *
FROM ORDERS
WHERE CUSTOMER_NAME LIKE 'A_a%'
ORDER BY CUSTOMER_NAME;
```

```sql
SELECT DISTINCT ORDER_DATE FROM ORDERS
ORDER BY ORDER_DATE

--distinct combination
SELECT DISTINCT ORDER_DATE, ORDER_NAME
FROM ORDERS
ORDER BY ORDER_DATE

--check duplicate rows (Only those rows which are exactly same will not be selected here)
SELECT DISTINCT *
FROM ORDERS;
```

```sql
--filters
SELECT *
FROM ORDERS
WHERE SHIP_MODE = 'First Class';


--Order of execution:
--FROM, WHERE, SELECT , ORDER BY, TOP 5
SELECT TOP 5 ORDER_DATE, QUANTITY
FROM ORDERS
WHERE QUANTITY >= 5
ORDER BY QUANTITY;

-- multiple values for same column - use `IN`
SELECT *
FROM ORDERS
WHERE SHIP_MODE IN ('First Class' , 'Same Day');

SELECT *
FROM ORDERS
WHERE SHIP_MODE = 'First Class' and SEGMENT = 'Consumer';

-- multiple values for different columns - use `OR`
SELECT *
FROM ORDERS
WHERE SHIP_MODE = 'First Class' OR SEGMENT = 'Consumer';

--second character is `l` (add suitable number of underscore)
SELECT *
FROM ORDERS
WHERE CUSTOMER_NAME like '_l%';

--third char should be either a/l/b
SELECT *
FROM ORDERS
WHERE CUSTOMER_NAME like 'Co[alb]%';

--second character should not be a/l/b/o
SELECT *
FROM ORDERS
WHERE CUSTOMER_NAME like 'C[^alb]%';

--second character can be anything between a-f
SELECT *
FROM ORDERS
WHERE CUSTOMER_NAME like 'C[a-f]%'

--not like
SELECT *
FROM ORDERS
WHERE CUSTOMER_NAME NOT LIKE 'A%n';

SELECT *
FROM ORDERS
WHERE NOT (CUSTOMER_NAME LIKE 'A%' AND CUSTOMER_NAME LIKE '%n');


--null values in where. (can't do with `=`, need to use NOT NULL)
SELECT *
FROM ORDERS
WHERE CITY IS NOT NULL;

SELECT *
FROM ORDERS
WHERE CITY IS NULL;

```

# 14. Aggregation in depth

```sql

--num of records in table
SELECT COUNT(*) as cnt
FROM ORDERS;

--sum all the values in SALES column.
SELECT SUM(SALES) as total_sales
FROM ORDERS;

--select max, min and avg values.
SELECT MAX(SALES) as max_sale, MIN(PROFIT) as min_sale, AVG(PROFIT) as avg_profit
FROM ORDERS;

--all the aggregations in the select will be at region level (for each region)
SELECT REGION, COUNT(*) as cnt, SUM(SALES) as total_sales,
MAX(SALES) as max_sales, MIN(PROFIT) as min_profit
FROM ORDERS
GROUP BY REGION;

--When using a non aggreate column in SELECT, it must be present in GROUP BY as well.
SELECT REGION, CATEGORY, SUM(SALES) as total_sales
FROM ORDERS
GROUP BY REGION, CATEGORY;


--order of execution:
--FROM, WHERE, GROUP BY, AGGREGATE, SELECT, ORDER BY, TOP
SELECT TOP 2 REGION, SUM(SALES) as total_sales
FROM ORDERS
WHERE PROFIT > 50
GROUP BY REGION
ORDER BY total_sales DESC;


--HAVING clause. Used when filtering on aggregated values.
--Can't use WHERE clause on Aggreate funcs.
--i.e. When filtering on a column of table, use WHERE
--and when filtering on a aggregated function, use HAVING.
--Order of execution:
--FROM, GROUP BY, HAVING, SELECT, ORDER BY.
SELECT SUB_CATEGORY, SUM(SALES) as total_sales
FROM ORDERS
GROUP BY SUB_CATEGORY
HAVING SUM(SALES) > 100000
ORDER BY total_sales DESC;

--Order of execution:
--FROM, WHERE, GROUP BY, HAVING, SELECT, ORDER BY, TOP
SELECT TOP 5 SUB_CATEGORY, SUM(SALES) as total_sales
FROM ORDERS
WHERE PROFIT > 50
GROUP BY SUB_CATEGORY
HAVING SUM(SALES) > 100000
ORDER BY total_sales DESC;

--count doesn't count null values if present in the column.
SELECT COUNT(CITY)
FROM ORDERS
```

# 15. Joins in Depth

```sql
--For Joins, in general, the table that comes
--before the JOIN keyword is the left table
--and the table that comes after the JOIN keyword
--is the right table.


--Inner join orders table with returns table.
--With inner join, only intersection area (i.e. present in both) tables is shown. Default join is inner join.
SELECT o.ORDER_ID, o.PRODUCT_ID, r.RETURN_REASON
FROM ORDERS o
INNER JOIN RETURN_ORDERS r ON o.ORDER_ID = r.ORDER_ID --order in this `=` doesn't matter. What matters is the type of JOIN  (LEFT, RIGHT, INNER, etc.) and the table that comes before the join (in left join) or the table that comes after join (in right join).

--Left join
--With left join, well get a row for each row of the
--left table even if there's no match. When there's no
--match, the values of the right table's column will be NULL.
SELECT o.ORDER_ID, o.PRODUCT_ID, r.RETURN_REASON
FROM ORDERS o
LEFT JOIN RETURN_ORDERS r ON o.ORDER_ID = r.ORDER_ID

--Get the orders that aren't present in RETURN_ORDERS table.
--Order or execution:
--FROM, JOIN, WHERE, SELECT.
SELECT o.ORDER_ID, o.PRODUCT_ID, r.RETURN_REASON
FROM ORDERS o
LEFT JOIN RETURN_ORDERS r ON o.ORDER_ID = r.ORDER_ID
WHERE r.RETURN_REASON is NULL;

--cross join (join every record on table1 to every record of table2)
SELECT *
FROM EMPLOYEE
INNER JOIN DEPT ON 1=1 --always true
ORDER BY EMPLOYEE.EMP_ID

--full outer join.
--Sort of a union (BUT NOT UNION). Intersection (inner join), (left join) plus all items in left (non matching in right will be NULL), (right join) plus all items in right (non matching in left will be NULL).
SELECT *
FROM DEPT d
FULL OUTER JOIN EMPLOYEE e ON e.dept_id = d.dept_id;

--print names of the departments where
--none of the employees have the same salary.
--order of execution:
--FROM, JOIN, GROUP BY, HAVING, SELECT.
SELECT d.DEP_NAME
FROM EMPLOYEES e
LEFT JOIN DEPT d ON e.DEPT_ID = d.DEP_ID
GROUP BY d.DEP_ID
HAVING COUNT(DISTINCT SALARY) = COUNT(1)

--multiple joins (can join as many tables as we want. No limit.)
--First ORDERS and RETURN_ORDERS will be joined, then the resultant will be joined with the next join and so on and so forth.
SELECT o.ORDER_ID, o.PRODUCT_ID, r.RETURN_REASON, p.MANAGER
FROM ORDERS o
INNER JOIN RETURN_ORDERS r ON o.order_id=r.order_id
INNER JOIN PEOPLE p ON o.region=p.region;

--self join. Join a table with itself (using INNER JOIN, LEFT JOIN etc.) Its like making two copies of the same table and joining them. Idea of self referencing (good database design technique)
--Usually used when we have columns in table referening to another column of the same table. e.g employee table will have emp_id, emp_name and manager_id. manager_id is basically referring to emp_id column because every manager is also an employee.
SELECT e1.EMP_ID, e1.EMP_NAME as employee_name, e2.EMP_NAME as manager_name
FROM EMPLOYEE e1
INNER JOIN EMPLOYEE e2 on e1.MANAGER_ID=e2.EMP_ID;
```
