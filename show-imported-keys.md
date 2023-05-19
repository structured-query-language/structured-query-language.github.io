# Getting Foreign Keys on Tables in Snowflake

## Create tables with Foreign Keys in Snowflake

```sql
use scratch.saqib_ali;
create or replace table customers(
  customer_id int
  , name string
  , constraint primary_key primary key (customer_id));
create or replace table items(
  item_id int
  , item_description string
  , constraint primary_key primary key (item_id));
create or replace table orders(
  order_id int
  , customer_id int
  , item_id int
  , CONSTRAINT customer_foreign_key FOREIGN KEY (customer_id) REFERENCES customers (customer_id)
  , constraint item_foreign_key FOREIGN KEY (item_id) references items(item_id));
```

## Query to show foreign keys
```sql
SHOW IMPORTED KEYS;
```

## Query Output


| created_on                    | pk_database_name | pk_schema_name | pk_table_name | pk_column_name | fk_database_name | fk_schema_name | fk_table_name | fk_column_name | key_sequence | update_rule | delete_rule | fk_name              | pk_name     | deferrability  | rely  | comment |
|-------------------------------|------------------|----------------|---------------|----------------|------------------|----------------|---------------|----------------|--------------|-------------|-------------|----------------------|-------------|----------------|-------|---------|
| 2023-05-19 06:31:28.442 -0700 | SCRATCH          | SAQIB_ALI      | CUSTOMERS     | CUSTOMER_ID    | SCRATCH          | SAQIB_ALI      | ORDERS        | CUSTOMER_ID    | 1            | NO ACTION   | NO ACTION   | CUSTOMER_FOREIGN_KEY | PRIMARY_KEY | NOT DEFERRABLE | FALSE |         |
| 2023-05-19 06:31:28.442 -0700 | SCRATCH          | SAQIB_ALI      | ITEMS         | ITEM_ID        | SCRATCH          | SAQIB_ALI      | ORDERS        | ITEM_ID        | 1            | NO ACTION   | NO ACTION   | ITEM_FOREIGN_KEY     | PRIMARY_KEY | NOT DEFERRABLE | FALSE |         |

