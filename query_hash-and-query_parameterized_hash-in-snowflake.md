# What's eating up your Snowflake Virtual Warehouse? Enter Skyline Query

Managing Warehouses in Snowflake can be tricky. At times you will have badly written queries that are being executed multiple times on a Warehouse. This eats up the Warehouse, and other queries get queued up. A badly written query that only gets executed once is not the problem, but if it gets executed over and over then it becomes a problem. To address this, you need to first identify queries that have a longer execution time and are being executed multiple times.

## query_hash and query_parameterized_hash in Snowflake query_history
To identify such queries you can use the newly released `query_hash` and `query_parameterized_hash` in Snowflake. Snowflake produces a unique hash for each query. The `query_hash` column contains a hash value that is computed, based on the canonicalized text of the SQL statement. Repeated queries that have exactly the same query text have the same `query_hash` values. Whereas, `query_parameterized_hash` contains a hash value that is computed based on the parameterized query, which means the version of the query after literals are parameterized. For e.g., the following two queries will produce the same `query_parameterized_hash`

```sql
select * from customers where full_name = 'Saqib';
select * from customers where full_name = 'Ali';
```

## Maximum Vector Problem or Pareto Front
Identifying queries that have a longer execution time and are executed multiple times is a Maximum Vector problem that be solved using [Skyline queries](skyline-query-pareto-set-maxima.md). A [Skyline query](skyline-query-pareto-set-maxima.md) finds points in a vector space that dominate other points. Here is how a [Skyline Query](skyline-query-pareto-set-maxima.md) can be used to identify Snowflake queries that are problematic.

### Skyline Query

```sql
with query_history as (
  select 
    query_parameterized_hash
    , avg(execution_time/60000) as avg_execution_time_in_minutes
    , count(query_id) as number_of_times_executed
from snowflake.account_usage.query_history
where warehouse_name ilike '{WAREHOUSE_NAME}' -- Warehouse name without the curly braces
  and start_time >=   DATEADD(hour ,-24, sysdate())
  and not query_text ilike any ('commit%', 'show terse%', 'begin%', 'use%', 'create sequence%') -- exclude certain types of queries
group by all
)
, skyline as ( -- using Skyline Query to get the worst performing queries that get executed multiple times.
  select * from query_history as o
  where not exists (
    select 1
    from query_history as i
    where i.avg_execution_time_in_minutes >= o.avg_execution_time_in_minutes and i.number_of_times_executed >= o.number_of_times_executed 
      and (i.avg_execution_time_in_minutes > o.avg_execution_time_in_minutes or i.number_of_times_executed > o.number_of_times_executed)
  )
)
select distinct 
  left(query_text, 200)
  , avg_execution_time_in_minutes
  , number_of_times_executed
from skyline
inner join snowflake.account_usage.query_history using (query_parameterized_hash)
;
```

### Query Output

| QUERY_TEXT        | avg_execution_time_in_minutes        | number_of_times_executed |
|-------------------|--------------------------------------|--------------------------|
| select * from ... | 14                                   | 53                       |
| select * from ... | 18                                   | 38                      |


# See also
[Skyline Query](skyline-query-pareto-set-maxima.md)
