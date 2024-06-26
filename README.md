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

```sql
CREATE, DROP, ALTER, TRUNCATE

--DDLs can't be rolledback. They are autocommit.
```

# 7. DML (Data Manipulation Language)

```sql
INSERT, UPDATE, DELETE

--Untill we execute a commit, DMLs can be rolled back.
```

# 8. DQL (Data Query Language)

```sql
SELECT
```

# 8+. DCL (Data Control Language)

```sql
GRANT, REVOKE

--e.g. of grant.

--grant select and insert on employees table to `guest` user.
GRANT SELECT, INSERT ON EMPLOYEES TO GUEST;

--grant select and insert on employees to all users.
GRANT SELECT, INSERT ON EMPLOYEES to PUBLIC;

--grant select on all objects in dbo schema.
GRANT SELECT ON schema::dbo TO GUEST;

--revoke select and insert access on employee table from guest.
REVOKE SELECT, INSERT ON EMPLOYEES FROM GUEST;
```

# 8++. TCL (Transaction Control Language)

```sql
COMMIT, ROLLBACK
```

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

# 11. Constraints - NOT NULL, CHECK, UNIQUE, DEFAULT, PRIMARY KEY, FOREIGN KEY, REFERNCES

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

--referential itegrity constraint (a.k.a Foreign key constraint)
--if a column refernces a primary key (for reference constraint the target must be a a primary key) in other table, every value inserted must be present in the other table.

--adding referential integrity constraint on `EMP.DEP_ID`
--every value inserted to DEP_ID must be present in `DEPT.DEP_ID` (except if inserting null)
--`DEPT.DEP_ID` must be aprimary key in DEPT for it to
--be added as a reference constaint in any table.
CREATE TABLE EMP (
    EMP_ID INTEGER,
    EMP_NAME VARCHAR(10),
    DEP_ID INTEGER REFERENCES DEPT(DEP_ID)
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

# 16. SQL Functions in depth

```sql
--concatenate values of a column
SELECT DEPT_ID, STRING_AGG(EMP_NAME, '|') as list_of_employees
FROM EMPLOYEE
GROUP BY DEPT_ID;

--sort the concatenated values
SELECT DEPT_ID, STRING_AGG(EMP_NAME, '|') WITHIN GROUP (ORDER BY SALARY) as list_of_employees
FROM EMPLOYEE
GROUP BY DEPT_ID;

--date functions

--get year from a datetime column
SELECT ORDER_ID, ORDER_DATE, DATEPART(YEAR, ORDER_DATE) as year_of_order_date,
DATEPART(month, ORDER_DATE) as month_of_year,
DATENAME(month, ORDER_DATE) as month_name
FROM ORDERS
WHERE DATEPART(YEAR, ORDER_DATE) = 2020;

--add days, weeks, months etc to a datetime
SELECT ORDER_ID, ORDER_DATE,
DATEADD(day, 5, ORDER_DATE) as ORDER_DATE_5, --add 5 days
DATEADD(week, 5, ORDER_DATE) as ORDER_DATE_WEEK_5, --add 5 wk
DATEADD(day, -5, ORDER_DATE) as ORDER_DATE_MINUS_5 --minus 5 days.
FROM ORDERS;

--diff of dates
SELECT ORDER_ID, ORDER_DATE, SHIP_DATE,
DATEDIFF(day, ORDER_DATE, SHIP_DATE) as date_diff_days,
DATEDIFF(week, ORDER_DATE, SHIP_DATE) as date_diff_week
FROM ORDERS;

--string functions
SELECT ORDER_ID, CUSTOMER_NAME,
LEN(CUSTOMER_NAME) as len_name, --length of string
LEFT(CUSTOMER_NAME,4) as name_4, --4 chars from left.
RIGHT(CUSTOMER_NAME, 5) as name_5, --5 chars from right.
SUBSTRING(CUSTOMER_NAME, 4,5) as substr45, --substring 5 chars from index 4.
CHARINDEX(' ', CUSTOMER_NAME) as space_pos, --first index of ' '
CONCAT(ORDER_ID, '-', CUSTOMER_NAME) as first_pos, --concatenate order id and customer name.
REPLACE(ORDER_ID, 'CA', 'PB') as replaced, --replace all occurrences of CA with PB.
TRANSLATE(CUSTOMER_NAME, 'AG', 'TP') as translated, --change ocurrences of 'A' to 'G' and occurences of 'T' to 'P'
REVERSE(CUSTOMER_NAME) as reversed, --reverse the string.

--null handling functions
SELECT ORDER_ID,
ISNULL(CITY, 'unknown') as is_null_city, --if city value is null, show 'unknown'.
COALESCE(CITY, 'unknown') as coalesce_city, --if city value is null, show 'unknown'.
COALESCE(CITY, STATE, REGION, 'unknown') as multiple_col, --if city is null, check state, if state is also null, check region, and if that is also null, return unknown.
FROM ORDERS;

--cast and round
SELECT TOP 5 ORDER_ID, CAST(SALES AS INT) AS SALES_INT,
ROUND(SALES, 1) AS SALES_INT
FROM ORDERS;
```

# 14. CASE

```sql
SELECT ORDER_ID, PROFIT,
CASE
WHEN PROFIT < 100 THEN 'Low Profit'
WHEN PROFIT < 250 THEN 'Medium Profit'
WHEN PROFIT < 400 THEN 'High Profit'
ELSE 'Very High Profit'
END AS PROFIT_CATEGORY
FROM ORDERS;
```

# 15. UNION, INTERSECT, EXCEPT

```sql
--combine similar tables together into one tables. Don't confuse
-- with joins. For UNION to work, the two tables should have
--same num of columns in their respective selects, and the data type of columns should be compatible.
SELECT * FROM ORDERS_WEST
UNION ALL --combine all rows from each table, no matter if duplicate.
SELECT * FROM ORDERS_EAST;


SELECT * FROM ORDERS_WEST
UNION -- a simple union will ignore duplicate rows. Only uniques rows from each table will be present in the output table.
SELECT * FROM ORDERS_EAST;

SELECT * FROM ORDERS_EAST
INTERSECT --get common rows from both the tables.
SELECT * FROM ORDERS_EAST;

-- select all rows in orders_east except the ones that are
--also present in orders_east.
SELECT * FROM ORDERS_EAST
EXCEPT
SELECT * FROM ORDERS_EAST;

-- only `UNION ALL` gives everything, be it duplicate or now.
--all others like `UNION`, `EXCEPT`, `INTERSECT` will never give
--rows that are duplicate even if they qualify.
```

# 16. VIEWS

```sql
--views are kinda virtual tables.

--create a view.
CREATE VIEW ORDERS_VW AS
SELECT * FROM ORDERS;

--once created, run queries on the view.
SELECT * FROM ORDERS_VW;

--When a view is queried, it simply
--executed the underlying query that was used to
--create the view. So, views act as a nice abstraction
--over complex queries, if you would.
```

# 17. Sub queries

```sql
--sub queries are needed when to get the final answer, we need
--some intermediate result to operate on. We can consider the
--result of a query a 'virtual' table and apply more queries
--over it to get a final result.

--find average sale per order.
SELECT AVG(total_sales_per_order)
FROM (
    SELECT ORDER_ID, SUM(SALES) AS total_sales_per_order
    FROM ORDER
    GROUP BY ORDER_ID
) AS aggregated_by_orders


--all orders whose sales is greater than avg sale per order.
--My soln.
SELECT ORDER_ID
FROM (
    SELECT ORDER_ID, AVG(total_sales_per_order) AS avg_sale_per_order
    FROM (
        SELECT ORDER_ID, SUM(SALES) AS total_sales_per_order
        FROM ORDER
        GROUP BY ORDER_ID
    ) AS aggregated_by_orders
)
WHERE aggregated_by_orders.total_sales_per_order > avg_sale_per_order;

--Ankit's soln.
SELECT ORDER_ID
FROM ORDERS
GROUP BY ORDER_ID
HAVING SUM(SALES) > (
    SELECT AVG(total_sales_per_order) AS avg_sale_per_order
    FROM (
        SELECT ORDER_ID, SUM(SALES) AS total_sales_per_order
        FROM ORDER
        GROUP BY ORDER_ID
    ) AS aggregated_by_orders
)

--We can use sub queries almost anywhere.
--in select, where, joins etc.
```

# 18. Common Table Expressions (CTEs)

```sql
--CTEs are similar to subqueries with the slight
--difference that the subqueries are moved uptop which makes
-- the overall query kinda easier to read and follow.
------------------------
WITH DEP AS
(
    SELECT DEPT_ID, AVG(SALARY) AS AVG_DEP_SALARY
    FROM EMPLOYEE
    GROUP BY DEPT_ID
)

SELECT EMP.*, DEP.*
FROM EMPLOYEE EMP
INNER JOIN DEP
ON EMP.DEPT_ID=DEP.DEPT_ID;
------------------------

------------------------
--we can have multiple CTEs seperated by a comma.
WITH DEP AS
(
    SELECT DEPT_ID, AVG(SALARY) AS AVG_DEP_SALARY
    FROM EMPLOYEE
    GROUP BY DEPT_ID
),
TOTAL_SALARY AS
(
    SELECT SUM(AVG_DEP_SALARY) AS TS
    FROM DEP
)

SELECT E.*, D.*
FROM EMPLOYEE E
INNER JOIN DEP D
ON E.DEPT_ID = D.DEPT_ID;
-------------------------

--we can convert almost any query written using
--subqueries to use CTEs.

```

# 19. Window analytical functions.

```sql
--When it comes to ranking use cases like TOP x etc.
--we are limited to the standard methods (TOP 5 etc) giving
--us results for the entire table. But what if we want such
--stats `over` some window instead of the entire table?
--thats where Window analytical functions are useful.

--generate a ranking number (row number) based on the salary. i.e highest
--salary has the lowest rank (1) and the lowest salary has the
--least rank. NOTE here that the `window` is the entire table.
SELECT *, ROW_NUMBER() OVER(ORDER BY SALARY DESC) AS rank
FROM EMPLOYEE

--generate similar ranking but this time for each department.
--i.e. now the `window` is each department.
SELECT *, ROW_NUMBER() OVER(PARTITION BY DEPT_ID ORDER BY SALARY DESC) AS rank
FROM EMPLOYEE;

---------------------------
--select the top 2 highest salary employees in each department.
WITH CTE AS (
    SELECT *, ROW_NUMBER() OVER (PARTITION BY DEPT_ID ORDER BY SALARY  DESC, EMP_NAME ASC) AS RN
    FROM EMPLOYEE
)

SELECT * FROM CTE
WHERE RN <= 2;
---------------------------

--generate ranking, but same salary should result in same rank.
--same salary will get same rank but the next lower salary will
--will have  a rank after skipping a number (coz two salaries
--got the same rank.)
SELECT *, RANK() OVER(ORDER BY SALARY DESC) as rnk
FROM EMPLOYEE;

--generate the above for each group
SELECT *, RANK() OVER(PARTITION BY DEPT_ID ORDER BY SALARY DESC) as rnk
FROM EMPLOYEE;

--dense rank is similar to rank but without skipping.

--lead window function. Lead -- next.
SELECT *,
LEAD(SALARY, 1) OVER(ORDER BY SALARY DESC) as LEAD_SAL
FROM EMPLOYEE;

--if null, then use default value of 3rd param.
SELECT *,
LEAD(SALARY, 1, 9999) OVER(ORDER BY SALARY DESC) as LEAD_SAL
FROM EMPLOYEE;

--with partition
SELECT *,
LEAD(SALARY, 1, EMP_AGE) OVER(PARTITION BY DEPT_ID ORDER BY SALARY DESC) AS LEAD_SAL
FROM EMPLOYEE;

--lag window function is kinda reverse of lead. lag - previous.

--lead/lag functions are useful in question relating to
--year on year growth etc.

--first_value and last_value window functions.
--within each partition, gives the first and last value respectively.
SELECT *,
FIRST_VALUE(SALARY) OVER(PARTITION BY DEPT_ID ORDER BY EMP_NAME DESC) AS FIRST_VALUE,
LAST_VALUE(SALARY) OVER(PARTITION BY DEPT_ID ORDER BY EMP_NAME DESC) AS LAST_VALUE
FROM EMPLOYEE;

```

# 20. Aggregation with window functions.

```sql
--find the avg salary, min, max etc in each department.
--note we don't have to use order by here because doesn't matter
--for aggregation. It mattered a hell lot when had ranking/ordering usecases like before.
SELECT *,
AVG(SALARY) OVER(PARTITION BY DEPT_ID) AS AVG_SALARY,
MAX(SALARY) OVER(PARTITION BY DEPT_ID) AS MAX_SALARY
FROM EMPLOYEE;

--However, if we use order by in the over clause (when using aggregation) then the result for each row will be running sum, running avg etc based on the order.

--some more complicated ones.
--(check the window functions lecture for revision)
--for every current row, 1 preceeding and 1 next (including curr.)
SELECT *,
SUM(SALARY) OVER(PARTITION BY DEPT_ID ORDER BY EMP_ID ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING) AS ROLLING_3_SALARY
FROM EMPLOYEE;

--both columns below will have same values for each row.
SELECT *,
SUM(SALARY) OVER(PARTITION BY DEPT_ID) AS DEP_RUNNING_SALARY,
SUM(SALARY) OVER(PARTITION BY DEPT_ID ORDER BY EMP_ID ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS TOTAL_SALARY
FROM EMPLOYEE;

--rolling 3 months sale
---------------------------------
WITH month_wise_sales AS
(
    SELECT DATEPART(YEAR, ORDER_DATE) AS YEAR_ORDER, DATEPART(MONTH, ORDER_DATE) AS MONTH_ORDER, SUM(SALES) AS TOTAL_SALES
    FROM ORDERS
    GROUP BY DATEPART(YEAR, ORDER_DATE), DATEPART(MONTH, ORDER_DATE)
)
SELECT YEAR_ORDER, MONTH_ORDER, TOTAL_SALES,
SUM(TOTAL_SALES) OVER(ORDER BY YEAR_ORDER, MONTH_ORDER ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS rolling_3_sales
FROM month_wise_sales;
---------------------------------
```

# 21. Procedures.

```sql
--procedures give us a way to run multiple queries, complex
--queries at once as a group. It's anlogous to shell scripts.
--One can run individual bash commands to acheive an objective,
--or can combine them all in single, reusable script.

----------------------------
--create a procedure
CREATE PROCEDURE SP_EMP
AS
SELECT * FROM EMPLOYEE

--run the procedure
exec SP_EMP;
---------------------------

--------------------------
--accepting params
CREATE PROCEDURE SP_EMP_2(@SALARY int)
AS
SELECT * FROM EMPLOYEE WHERE SALARY > @salary;

--run
exec SP_EMP @salary=100
--------------------------

--insert statements
CREATE PROCEDURE SP_EMP_3(@SALARY int, @DEPT_ID int)
AS
INSERT INTO DEPT VALUES (800, 'HR1');
SELECT * FROM EMPLOYEE WHERE SALARY > @SALARY AND DEPT_ID=@DEPT_ID;
SELECT * FROM DEPT WHERE DEP_ID=@DEPT_ID;

--In views, we can have only one select statement. But with
--procedures, we have literally anything.
```

# 22. Functions

```sql
--custom functions
CREATE FUNCTION fproduct(@a int, @b int)
RETURNS integer
AS
BEGIN
    RETURN (
        SELECT @a*@b
    )
END
```

# 23. Pivot/Unpivot

```sql
--pivot basically is process of converting rows to columns.
--below is normally how we'd do if we want sum of sales in 2020 and 2021 for each category, as column.
SELECT CATEGORY,
SUM(CASE WHEN DATEPART(YEAR, ORDER_DATE)=2020 THEN SALES END) AS SALES_2020,
SUM(CASE WHEN DATEPART(YEAR, ORDER_DATE)=2021 THEN SALES END) AS SALES_2021
FROM ORDERS
GROUP BY CATEGORY;

--the same result can be obtained using pivot function as below
SELECT *
FROM (SELECT CATEGORY, DATEPART(YEAR, ORDER_DATE) AS YOD, SALES FROM ORDERS) t1
PIVOT(SUM(SALES) FOR YOD IN ([2020], [2021])) AS t2
```

# 24. Misc.

```sql
--create a new table from results of a query.
--below query will create a new table `ORDERS_EAST` (of the same
--schema as the result of the query after removing `INSERT INTO ORDERS_EAST`)
--and then insert each row into `ORDERS_EAST`
--We can literally write any query that we would normally write and include
--the `INSERT INTO <TABLE>` statement to put all the result into the new table.
INSERT INTO ORDERS_EAST SELECT * FROM ORDERS WHERE REGION='East';

--TRUNCATE statetement.
--TRUNCATE is same as DELETE in the sense that it is used to
--delete data from a table, however, with DELETE we can give where clause
--etc. to control what is deleted and what is not, but we can't do it with
--TRUNCATE. TRUNCATE will simply delete all rows from a table. It is like
--running `DELETE FROM` without a where clause. Truncate is faster than delete.
TRUNCATE TABLE ORDERS;

--using joins in update statement
UPDATE EMPLOYEE
SET DEP_NAME=d.DEP_NAME
FROM EMPLOYEE e
INNER JOIN DEPT d ON e.DEPT_ID = d.DEP_ID;

--exists and not exists clause.

--whenever the query within exists returns atleast one row, exists
--will return true and that particular row will be part of the output, otherwise it won't be.
SELECT *
FROM EMPLOYEE_BACK e
WHERE EXISTS (SELECT _ FROM DEPT_BACK d WHERE e.DEPT_ID = d.DEP_ID);

--not exits is reverse of exists. Basically whenever the query in parentheses returns true, that row will not be part of output.
SELECT *
FROM EMPLOYEE_BACK e
WHERE NOT EXISTS (SELECT _ FROM DEPT_BACK d WHERE e.DEPT_ID=d.DEP_ID);
```

# 25. Perferomance and Indexes

```sql

--two kinds of indexes - Clustered and Non-clustered.
--When we created a clustered index on a column, internally the
--index sorts the column so that database can search the table in
--binary search fashion. We can only have one clustered index, because we can only sort the rows of a table by one column at a time.

--Non-clustered index is like a hashmap. When we create a non-clustered index on a column, internally a hashmap of sorts will be created where key will be the value in the column and value will be the address of the row of the table where the key resides. This way, finding a column value will be a O(1) lookup.

--when we create a primary key in a table, by default a clustered index will be created on the primary key.

--create a clustered index.
--create clustered index `idx_cust` for the `emp_name` column
--of emp_index table.
create clustered index idx_cust on emp_index(emp_name);

--create a nonclustered index.
create nonclustered index idx_name on emp_index(emp_name);

--drop an index (any - clustered/non-clustered)
--drop the index named `idx_rn` from table emp_index.
drop index idx_rn on emp_index;

--deleting duplicates from a table.

```

# 26. Questions ans solns

```sql
--1. Write a query to print top 5 cities with highest spends
--and their percentage contribution of total credit card spends.

with cte1 as (
    select city, sum(amount) as total_spend
    from credit_card_transactions
    group by city
),
total_spent as (
    select sum(cast(amount as bigint)) as total_amount
    from credit_card_transactions
)
select top 5 cte1.*, (total_spend*1.0/total_amount*100) as '% contri'
from cte1, total_spent
order by total_spend desc;

--2. Write a query to print highest spend month and amount spent
--in that month for each card type.
--i.e. for each card_type, give the month, year of max spending.
with cte as (
    select card_type, datepart(year, transaction_date) yt,
    datepart(month, transaction_date) mt, sum(amount) as total_spend
    from credit_card_transactions
    group by card_type, datepart(year, transaction_date), datepart(month, transaction_date)
)
select * from (
    select *, rank() over(partition by card_type order by total_spend desc) as rn
    from cte
) a where rn = 1;

--3. Write a query to print the transaction deatils (all columns from the table) for each card type when it reaches a cummulative of 1000000 total spends.
with cte as (
    select *, sum(amount) over (partition by card_type order by transaction_date) as total_spend
    from credit_card_transactions
)
select * from (
    select *, rank() over (partition by card_type order by total_spend) as rn
    from cte where total_spend >= 1000000
) a where rn = 1;

--4. Write a query to find city which had lowest percentage spend for gold card type.
with cte as (
    select city, card_type, sum(amount) as amount
    , sum(case when card_type='Gold' then amount end) as gold_amount
    from credit_card_transactions
    group by city, card_type
)
select city, sum(gold_amount)*1.0/sum(amount) as gold_ratio
from cte
group by city
having sum(gold_amount) is not null
order by gold_ratio;
```
