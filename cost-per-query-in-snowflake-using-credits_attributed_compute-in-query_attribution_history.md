# Cost per Query in Snowflake using credits_attributed_compute in query_attribution_history

Snowflake has introduced a new View `QUERY_ATTRIBUTION_HISTORY` in `SNOWFLAKE.ACCOUNT_USAGE`. This View holds the Snowflake Credits consumption attributed to a single Query execution in Snowflake. 

```
select
  credits_used_query_acceleration
  , credits_attributed_compute
  , query_parameterized_hash
from SNOWFLAKE.ACCOUNT_USAGE.QUERY_ATTRIBUTION_HISTORY
```

This VIEW can be joined with `QUERY_HISTORY` View using `query_parameterized_hash` as JOIN Key to get an average of the Snowflake Credits consumed for a given query.

```
select 
  query_parameterized_hash
  , avg(credits_attributed_compute)
  , max(credits_attributed_compute)
  , min(credits_attributed_compute)
from SNOWFLAKE.ACCOUNT_USAGE.QUERY_ATTRIBUTION_HISTORY
inner join SNOWFLAKE.ACCOUNT_USAGE.query_history using (query_parameterized_hash)
group by all;
```
