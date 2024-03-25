# User Defined Aggregate Functions (UDAF) in Google Bigquery

User Defined Aggregate Functions (UDAF) are now available in Google BigQuery. Here is an example of defining a UDAF to calculate the Geometric Mean of a column of data. 

### Defining the UDAF

```sql
CREATE AGGREGATE FUNCTION geometric_mean(
  col1 DOUBLE
)
RETURNS DOUBLE
AS
(
  EXP(SUM(LN(col1))/COUNT(col1))
);
```

### Calling the UDAF 

```sql
with recursive test_data as (
  SELECT cast (col1 as INT64) as col1
  FROM UNNEST(GENERATE_ARRAY(1, 20)) col1
)
SELECT geometric_mean(col1) AS geo_mean
FROM test_data;
```



