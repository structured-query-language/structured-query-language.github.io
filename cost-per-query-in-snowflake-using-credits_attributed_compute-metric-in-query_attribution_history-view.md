# Cost per Query in Snowflake using credits_attributed_compute metric in query_attribution_history View

Snowflake has introduced a new View `QUERY_ATTRIBUTION_HISTORY` in `SNOWFLAKE.ACCOUNT_USAGE`. This View holds the Snowflake Credits consumption attributed to a Query execution for recurrent queries as following: 

```sql
select
  credits_used_query_acceleration
  , credits_attributed_compute
  , query_parameterized_hash
from SNOWFLAKE.ACCOUNT_USAGE.QUERY_ATTRIBUTION_HISTORY
```

This View can be joined with `QUERY_HISTORY` View using [query_parameterized_hash](query_hash-and-query_parameterized_hash-in-snowflake.md) as JOIN Key to get an average of the Snowflake Credits consumed for a given query.

```sql
select 
  query_parameterized_hash
  , query_history.warehouse_size
  , avg(credits_attributed_compute)
  , max(credits_attributed_compute)
  , min(credits_attributed_compute)
  , left(any_value(query_history.query_text), 80) as query_text
from SNOWFLAKE.ACCOUNT_USAGE.QUERY_ATTRIBUTION_HISTORY
inner join SNOWFLAKE.ACCOUNT_USAGE.query_history using (query_parameterized_hash)
group by all;
order by credits_attributed_compute desc;
```

|![carbon(10)](https://github.com/user-attachments/assets/f7661c5a-b1c0-43b5-a501-ba818a414358)|
|:-:|
|Using `query_parameterized_hash` to JOIN `QUERY_ATTRIBUTION_HISTORY` and `QUERY_HISTORY` |

