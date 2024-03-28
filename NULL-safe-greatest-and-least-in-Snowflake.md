# NULL-safe GREATEST and LEAST now available in Snowflake

Snowflake now supports `GREATEST_IGNORE_NULLS` and `LEAST_IGNORE_NULLS`. In the past if you used `LEAST` or `GREATEST` on a list of expressions that contained `NULL` these functions always returned `NULL`, which was very problematic. You had do to something like `COALESCE(GREATEST(BILL_DATE, SHIP_DATE), SHIP_DATE, BILL_DATE)` to correctly handle `NULLs`. This was cumbersome. Now you can simply use `GREATEST_IGNORE_NULLS` or `LEAST_IGNORE_NULLS`. For e.g.


### Input data

| ORDER_NUMBER | BILL_DATE  | SHIP_DATE  |
|--------------|------------|------------|
| 111          | 2023-01-01 | 2023-01-05 |
| 222          | 2023-01-01 |            |


### SQL Query
```sql
select 
  order_number
  , greatest_ignore_nulls(bill_date, ship_date) as last_activity_date
from orders;
```

### Output

| ORDER_NUMBER | LAST_ACTIVITY_DATE |
|--------------|--------------------|
| 111          | 2023-01-05         |
| 222          | 2023-01-01         |
