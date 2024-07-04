# User Defined Aggregate Functions (UDAF) in Google Bigquery

User Defined Aggregate Functions (UDAF) are now available in Google BigQuery. Here is an example of defining a UDAF to calculate the [Geometric Mean](better-than-average-calculating-geometric-means-using-SQL) of a column of data. 

### Defining the UDAF

```sql
CREATE TEMP AGGREGATE FUNCTION geometric_mean(
  column_values float64
)
RETURNS float64
AS
(
  EXP(SUM(LN(column_values))/COUNT(column_values))
);

with test_data as (
  SELECT 1 AS col1 
  UNION ALL
  SELECT 3
  UNION ALL
  SELECT 5
)
select geometric_mean(col1) from test_data;
```

### Calling the UDAF 

```sql
select geometric_mean(col1) from test_data;
```



