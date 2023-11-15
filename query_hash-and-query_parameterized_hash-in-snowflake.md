# Using query_hash and query_parameterized_hash in Snowflake to indentify queries eating up a Warehouse

Managing Warehouses in Snowflake can be tricky. At times you will have badly written queries that are being executed multiple times on a Warehouse. This eats up the Warehouse, and other important queries get queued up. A badly written query that only gets executed once is not the problem, but if it gets executed over and over then it becomes a problem. To address this, you need to first identify queries that have a longer execution time and are being executed multiple times.

To identify such queries you can use the newly released `query_hash` and `query_parameterized_hash` in Snowflake. Snowflake produces a unique hash for each query. The `query_hash` column contains a hash value that is computed, based on the canonicalized text of the SQL statement. Repeated queries that have exactly the same query text have the same `query_hash` values. Whereas, `query_parameterized_hash` contains a hash value that is computed based on the parameterized query, which means the version of the query after literals are parameterized. For e.g., the following two queries will produce the same `query_parameterized_hash`

```sql
select * from customers where full_name = 'Saqib';
select * from customers where full_name = 'Ali';
```

Identifying queries that have a longer execution time and are executed multiple times is a Maximum Vector problem that be solved using [Skyline queries](skyline-query-pareto-set-maxima.md). A [Skyline query](skyline-query-pareto-set-maxima.md) finds points in a vector space that dominate other points. Here is how a [Skyline Query](skyline-query-pareto-set-maxima.md) can be used to identify Snowflake queries that are problematic.

![image](https://github.com/structured-query-language/structured-query-language.github.io/assets/121721444/bce0ffe5-fee2-4d8e-964d-935b5f714b4c)

## Skyline Query

```sql
with query_history as (
  select 
    query_text
    , bytes_spilled_to_remote_storage
    , decode(warehouse_size, 'X-Small', 1, 'Small', 2, 'Medium', 3, 'Large', 4, 'X-Large', 5) as warehouse_size
    from snowflake.account_usage.query_history
    where warehouse_size is not null and bytes_spilled_to_remote_storage > 1000
    order by start_time desc
    limit 10000
)
select 
  query_text 
  , bytes_spilled_to_remote_storage
  , warehouse_size
from query_history as o
where not exists ( -- Skyline query to identify worst performing queries
  select 1
  from query_history as i
  where i.warehouse_size <= o.warehouse_size and i.bytes_spilled_to_remote_storage >= o.bytes_spilled_to_remote_storage
  and (i.warehouse_size < o.warehouse_size or i.bytes_spilled_to_remote_storage > o.bytes_spilled_to_remote_storage)
);
```

## Query Output

| QUERY_TEXT        | bytes_spilled_to_remote_storage (GB) | WAREHOUSE_SIZE |
|-------------------|--------------------------------------|----------------|
| select * from ... | 4                                    | 1              |
| select * from ... | 5                                    | 4              |


# See also
[Skyline Query](skyline-query-pareto-set-maxima.md)
