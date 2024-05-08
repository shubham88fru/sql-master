# sql-master

Order of execution of SQL commands:

1. FROM
2. JOIN
3. WHERE
4. GROUP BY
5. HAVING
6. SELECT
7. ORDER BY
8. LIMIT

# 1. Creating a table

```sql
CREATE table amazon_orders
(
    order_id integer,
    order_date date,
    product_name varchar(100),
    total_price decimal(10, 2),
    payment_method varchar(20)
)
```

# 2. Deleting a table

```sql
DROP TABLE AMAZON_ORDERS
```

# 3. Inserting into the table

```sql
INSERT INTO amazon_orders VALUES
(
    1,
    '2022-10-01',
    'Baby Milk',
    30.50,
    'UPI'
)
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
ORDER BY ORDER_DATE DESC, TOTAL_PRICE ASC
```

# 6. DDL (Data Definition Language)

CREATE, DROP

# 7. DML (Data Manipulation Language)

INSERT, UPDATE, DELETE

# 8. DQL (Data Query Language)

SELECT

# 9. Delete data (not table)

```sql
DELETE FROM AMAZON_ORDERS;
```
