# Snowflake foreign keys

## Entity Relationship (ER) Diagram with Primary Keys and Foreign Key constraints

![SFDC Opportunity-2023-05-21 (1)](https://github.com/structured-query-language/structured-query-language.github.io/assets/121721444/38f15f32-b63e-4254-bbc3-36f92244b246)


## Create tables with Foreign Keys in Snowflake

```sql
use scratch.saqib_ali;
create or replace table customers(
  customer_id int
  , name string
  , constraint primary_key primary key (customer_id));
create or replace table customer_address(
  customer_id int
  , name string
  , CONSTRAINT customer_foreing_key FOREIGN KEY (customer_id) REFERENCES customers (customer_id)
);
create or replace table items(
  item_id int
  , item_description string
  , constraint primary_key primary key (item_id));
create or replace table orders(
  order_id int
  , customer_id int
  , item_id int
  , CONSTRAINT customer_foreing_key FOREIGN KEY (customer_id) REFERENCES customers (customer_id)
  , constraint item_foreign_key FOREIGN KEY (item_id) references items(item_id));
```

## Query to show foreign keys in the Schema
```sql
SHOW IMPORTED KEYS;
SHOW EXPORTED KEYS;
```

| created_on                    | pk_database_name | pk_schema_name | pk_table_name | pk_column_name | fk_database_name | fk_schema_name | fk_table_name    | fk_column_name | key_sequence | update_rule | delete_rule | fk_name              | pk_name     | deferrability  | rely  | comment |
|-------------------------------|------------------|----------------|---------------|----------------|------------------|----------------|------------------|----------------|--------------|-------------|-------------|----------------------|-------------|----------------|-------|---------|
| 2023-05-20 08:33:08.878 -0700 | SCRATCH          | SAQIB_ALI      | CUSTOMERS     | CUSTOMER_ID    | SCRATCH          | SAQIB_ALI      | CUSTOMER_ADDRESS | CUSTOMER_ID    | 1            | NO ACTION   | NO ACTION   | CUSTOMER_FOREING_KEY | PRIMARY_KEY | NOT DEFERRABLE | FALSE |         |
| 2023-05-20 08:33:09.843 -0700 | SCRATCH          | SAQIB_ALI      | CUSTOMERS     | CUSTOMER_ID    | SCRATCH          | SAQIB_ALI      | ORDERS           | CUSTOMER_ID    | 1            | NO ACTION   | NO ACTION   | CUSTOMER_FOREING_KEY | PRIMARY_KEY | NOT DEFERRABLE | FALSE |         |
| 2023-05-20 08:33:09.843 -0700 | SCRATCH          | SAQIB_ALI      | ITEMS         | ITEM_ID        | SCRATCH          | SAQIB_ALI      | ORDERS           | ITEM_ID        | 1            | NO ACTION   | NO ACTION   | ITEM_FOREIGN_KEY     | PRIMARY_KEY | NOT DEFERRABLE | FALSE |         |

## Query to show all the foreign keys in a table
```sql
SHOW IMPORTED KEYS in scratch.saqib_ali.orders;
```

| created_on                    | pk_database_name | pk_schema_name | pk_table_name | pk_column_name | fk_database_name | fk_schema_name | fk_table_name | fk_column_name | key_sequence | update_rule | delete_rule | fk_name              | pk_name     | deferrability  | rely  | comment |
|-------------------------------|------------------|----------------|---------------|----------------|------------------|----------------|---------------|----------------|--------------|-------------|-------------|----------------------|-------------|----------------|-------|---------|
| 2023-05-20 08:33:09.843 -0700 | SCRATCH          | SAQIB_ALI      | CUSTOMERS     | CUSTOMER_ID    | SCRATCH          | SAQIB_ALI      | ORDERS        | CUSTOMER_ID    | 1            | NO ACTION   | NO ACTION   | CUSTOMER_FOREING_KEY | PRIMARY_KEY | NOT DEFERRABLE | FALSE |         |
| 2023-05-20 08:33:09.843 -0700 | SCRATCH          | SAQIB_ALI      | ITEMS         | ITEM_ID        | SCRATCH          | SAQIB_ALI      | ORDERS        | ITEM_ID        | 1            | NO ACTION   | NO ACTION   | ITEM_FOREIGN_KEY     | PRIMARY_KEY | NOT DEFERRABLE | FALSE |         |

## Query to show all the tables that use a Primary Key from a given table in a Foreign Key constraint
```sql
SHOW EXPORTED KEYS in scratch.saqib_ali.customers;
```

| created_on                    | pk_database_name | pk_schema_name | pk_table_name | pk_column_name | fk_database_name | fk_schema_name | fk_table_name    | fk_column_name | key_sequence | update_rule | delete_rule | fk_name              | pk_name     | deferrability  | rely  | comment |
|-------------------------------|------------------|----------------|---------------|----------------|------------------|----------------|------------------|----------------|--------------|-------------|-------------|----------------------|-------------|----------------|-------|---------|
| 2023-05-20 08:33:08.878 -0700 | SCRATCH          | SAQIB_ALI      | CUSTOMERS     | CUSTOMER_ID    | SCRATCH          | SAQIB_ALI      | CUSTOMER_ADDRESS | CUSTOMER_ID    | 1            | NO ACTION   | NO ACTION   | CUSTOMER_FOREING_KEY | PRIMARY_KEY | NOT DEFERRABLE | FALSE |         |
| 2023-05-20 08:33:09.843 -0700 | SCRATCH          | SAQIB_ALI      | CUSTOMERS     | CUSTOMER_ID    | SCRATCH          | SAQIB_ALI      | ORDERS           | CUSTOMER_ID    | 1            | NO ACTION   | NO ACTION   | CUSTOMER_FOREING_KEY | PRIMARY_KEY | NOT DEFERRABLE | FALSE |         |

