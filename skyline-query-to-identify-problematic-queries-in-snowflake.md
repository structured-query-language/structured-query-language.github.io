# Skyline query to identify problematic queries in Snowflake

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
