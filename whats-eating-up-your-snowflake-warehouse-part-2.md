# What’s eating up your Snowflake Virtual Warehouse - Part II

Managing Warehouses in Snowflake can be tricky. Most queries will become slower as the data in your Warehouse increases. However they should not get worse by a large magnitude. Queries that executed multiple times, and are become slower at a rapid pace usually eat up the Warehouse. To address this, you need to first identify queries that are getting worse by time i.e. time they execute, the execution time increases.

## query_hash and query_parameterized_hash in Snowflake query_history
To identify such queries you can use the newly released `query_hash` and `query_parameterized_hash` in Snowflake. Snowflake produces a unique hash for each query. The `query_hash` column contains a hash value that is computed, based on the canonicalized text of the SQL statement. Repeated queries that have exactly the same query text have the same `query_hash` values. Whereas, `query_parameterized_hash` contains a hash value that is computed based on the parameterized query, which means the version of the query after literals are parameterized. For e.g., the following two queries will produce the same `query_parameterized_hash`

```sql
select * from customers where full_name = 'Saqib';
select * from customers where full_name = 'Ali';
```

## match_recognize

Armed with `query_parameterized_hash` we will write a `match_recognize` query to identify queries that are getting worse progressively.  Snowflake’s `MATCH_RECOGNIZE` clause can perform Pattern Matching over a set of rows. `MATCH_RECOGNIZE` does this by assigning labels to events, finding the events within an ordered partition, and pulling out any sequences that match the given pattern. In our case, we need a identify a sequence of execution of the same query where the execution time is increasing.

### match_recognize SQL query

```sql
with query_history as (
select
  query_parameterized_hash
  , execution_time/60000 as execution_time_in_mins
  , first_value(query_text)
    over (partition by query_parameterized_hash order by start_time asc) as query_text
  , start_time
from snowflake.account_usage.query_history
where and execution_time_in_mins  > 1
  and start_time >=   DATEADD(day ,-10, sysdate())
  and not query_text ilike any ('commit %', 'show terse %', 'begin %', 'use %', 'create %')
)

select *
from query_history
match_recognize(
partition by query_parameterized_hash order by start_time asc
all rows per match
pattern (UPTREND{3,}$)
DEFINE
  UPTREND AS execution_time_in_mins  > lag(execution_time_in_mins) * 1.05
)
order by query_parameterized_hash, start_time asc;
;
```

The `MATCH_RECOGNIZE` clause has several different parts. The `DEFINE` part contains the variable definition, which can later be used in the other parts. We defined the following variables:

1. `UPTREND` – We defined as the execution_time_in_mins greater than the previous execution of the same queriy. We are also ignoring small increases of 5% in execution time i.e. multiplying by 1.05.
 
The `pattern` we used is the following: `UPTREND{3,}$`

It means that we are looking for queries that whose execution time increased in progression  3 or more times, and the `$` indicates that latest execution time is also slower that the previous one

The `MEASURES` part is used to define the output structure.

|query_parameterized_hash|query_text|execution_time_in_mins|start_time|
|------------------------|----------|----------------------|----------|
|56e30c043…              |select …… |2.2                   |…|
|56e30c043…              |select …… |2.4                   |…|
|56e30c043…              |select …… |2.7                   |…|
|56e30c043…              |select …… |3.0                   |…|
|3b54fe894…              |select …… |1.2                   |…|
|3b54fe894…              |select …… |2.2                   |…|
|3b54fe894…              |select …… |4.0                   |…|
