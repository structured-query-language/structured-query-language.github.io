# Correlated pattern definitions and Snowflake's MATCH_RECOGNIZE
Snowflake currently does not support Correlated pattern definition in `MATCH_RECOGNIZE`. You will get the following error message:

ERROR: `Unsupported feature 'Correlated pattern definitions'.` 

```sql
select * from order_customer
match_recognize(
    partition by order_number
    order by load_date
    one row per match
    pattern (init modified+ reversed)
    define
      init as customer_id = customer_id,
      modified as customer_id <> init.customer_id,
      reversed as customer_id = init.customer_id
);
```

However this query can be re-written using the FIRST_VALUE() function as following:

```sql
select * from order_customer
match_recognize(
    partition by order_number
    order by load_date
    one row per match
    pattern (init modified+ init)
    define
      init as customer_id = first_value(customer_id),
      modified as customer_id <> first_value(customer_id)
      
);
```

# Raw Data

| ORDER_NUM | CUSTOMER | LOAD_DATE               |
|-----------|----------|-------------------------|
| 111       | aaa      | 2023-02-09 04:49:41.335 |
| 111       | bbb      | 2023-02-09 04:49:42.338 |
| 111       | aaa      | 2023-02-09 04:49:43.278 |
| 222       | aaa      | 2023-02-09 04:49:44.213 |
| 222       | bbb      | 2023-02-09 04:49:45.254 |
| 333       | aaa      | 2023-02-09 04:49:46.334 |
| 333       | bbb      | 2023-02-09 04:49:47.101 |
| 333       | ccc      | 2023-02-09 04:49:48.196 |

# Generate Raw Data

```sql
create or replace table order_customer (order_number number, customer_id varchar(80), load_date timestamp);
insert into order_customer values (111, 'aaa', CURRENT_TIMESTAMP);
insert into order_customer values (111, 'bbb', CURRENT_TIMESTAMP);
insert into order_customer values (111, 'aaa', CURRENT_TIMESTAMP);
insert into order_customer values (222, 'aaa', CURRENT_TIMESTAMP);
insert into order_customer values (222, 'bbb', CURRENT_TIMESTAMP);
insert into order_customer values (333, 'aaa', CURRENT_TIMESTAMP);
insert into order_customer values (333, 'bbb', CURRENT_TIMESTAMP);
insert into order_customer values (333, 'ccc', CURRENT_TIMESTAMP);

select * from order_customer;
```

# Screenshots

![image](https://user-images.githubusercontent.com/121721444/217828368-7003e644-f0c5-4594-a2c4-757e01f49912.png)

