# sql-master

Order of execution of SQL commands/clauses:

1. FROM, UPDATE
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
);
```

# 2. Deleting a table

```sql
DROP TABLE AMAZON_ORDERS;
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
