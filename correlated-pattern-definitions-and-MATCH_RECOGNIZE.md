# Correlated pattern definitions and Snowflake's MATCH_RECOGNIZE

[MATCH_RECOGNIZE](applied-overview-of-MATCH_RECOGNIZE-clause.md) is a powerful SQL clause that can be used to find patterns in data. 

We will use [MATCH_RECOGNIZE](applied-overview-of-MATCH_RECOGNIZE-clause.md) to find the Orders where the Customer assignment changed and was reverted back. The Order can be assigned to several different Customer before being reverted back to the Original Customer. Here is the raw data:

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

In the above data, you will notice that Order `111` was initially assigned to Customer `aaa`, changed to Customer `bbb`, and then was reverted back to Customer `aaa`.

The following Oracle MATCH_RECOGNIZE query can be used to identify Order `111`

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

However, when I tried to port it to Snowflake, it turned out that Snowflake currently does not support Correlated pattern definition in `MATCH_RECOGNIZE`. 

Snowflake will throw the following error message:

ERROR: `Unsupported feature 'Correlated pattern definitions'.` 

This query can be re-written for Snowflake using the `FIRST_VALUE()` function as following:

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




## Generate Raw Data

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

## Screenshots

![image](https://user-images.githubusercontent.com/121721444/217828368-7003e644-f0c5-4594-a2c4-757e01f49912.png)



# See also:
<ul id="recent-articles">
{% for page in site.pages %}
    {% if page.title contains "MATCH_RECOGNIZE" %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>
