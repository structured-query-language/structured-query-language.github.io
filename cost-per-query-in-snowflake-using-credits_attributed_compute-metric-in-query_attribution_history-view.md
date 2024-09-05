# Cost per Query in Snowflake using credits_attributed_compute metric in query_attribution_history View

Snowflake has introduced a new View `QUERY_ATTRIBUTION_HISTORY` in `SNOWFLAKE.ACCOUNT_USAGE`. This View holds the Snowflake Credits consumption attributed to a Query execution for recurrent queries as following: 

```sql
select
  credits_used_query_acceleration
  , credits_attributed_compute
  , query_parameterized_hash
from SNOWFLAKE.ACCOUNT_USAGE.QUERY_ATTRIBUTION_HISTORY
```

This View can be joined with `QUERY_HISTORY` View using [query_parameterized_hash](query_hash-and-query_parameterized_hash-in-snowflake.md) as JOIN Key to get an average of the Snowflake Credits consumed for recurrent queries.

```sql
select 
  query_parameterized_hash
  , query_history.warehouse_size
  , avg(credits_attributed_compute) as avg_credits_attributed_compute
  , max(credits_attributed_compute) as max_credits_attributed_compute
  , min(credits_attributed_compute) as min_credits_attributed_compute
  , count(*) as number_of_executions
  , left(any_value(query_history.query_text), 80) as query_text
from SNOWFLAKE.ACCOUNT_USAGE.QUERY_ATTRIBUTION_HISTORY
inner join SNOWFLAKE.ACCOUNT_USAGE.query_history using (query_parameterized_hash, start_time)
group by all
having avg_credits_attributed_compute > 0
order by number_of_executions * avg_credits_attributed_compute desc;
order by credits_attributed_compute desc;
```

|![carbon(13)](https://github.com/user-attachments/assets/7a1a62fb-0736-4031-b532-c89f8b0e9f3c)|
|:-:|
|Using `query_parameterized_hash` to JOIN `QUERY_ATTRIBUTION_HISTORY` and `QUERY_HISTORY` to get an average of the Snowflake Credits consumed for recurrent queries |

