# User Defined Aggregate Functions (UDAF) in Google Bigquery


```sql
CREATE AGGREGATE FUNCTION ScaledSum(
  dividend DOUBLE,
  divisor DOUBLE NOT AGGREGATE)
RETURNS DOUBLE
AS
(
  SUM (dividend) / divisor
);

with test_data as (
  SELECT 1 AS col1 
  UNION ALL
  SELECT 3 
  UNION ALL
  SELECT 5

)
SELECT ScaledSum(col1, 2) AS scaled_sum
FROM test_data;
```



