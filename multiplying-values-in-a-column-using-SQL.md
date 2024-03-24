
```sql
with recursive test_data as (
  SELECT 1 AS col1 
  UNION ALL
  SELECT -3 
  UNION ALL
  SELECT 5

)
, numbers_to_multiply as (SELECT array_agg(col1) as number_array FROM test_data)
, multiply as (
  select 
    number_array[0] as multiplied
    , 1 as array_index
    , number_array 
  from numbers_to_multiply
  union all
  select 
    multiplied * number_array[array_index]
    , array_index + 1
    , number_array 
  from multiply where array_index < array_length(number_array)
  
)
select * from multiply
order by array_index desc limit 1
;
```
