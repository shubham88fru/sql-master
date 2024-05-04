# sql-master

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

# 2. Inserting into the table

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
