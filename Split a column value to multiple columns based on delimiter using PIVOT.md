# Split a column value to multiple columns based on delimiter in Snowflake

Recently I had to work with a table that had all the data in a single comma delimted column. The data looked as following:

| ROW_VALUE                                      |
|------------------------------------------------|
| Bob,CSE 510,CSE 594,CSE 310                    |
| Angela,CSE 580,CSE 594,CSE 320,CSE 510,CSE 310 |

One way is to to use split() function in Snowflake. But it doesn't work well when the number of the fields in the comma delimited field is dynamic. 

So I ended up using the Dynamic PIVOT instead

```sql
with unpivoted_data as (
  select *
  from imported_data,
  table(split_to_table(row_value, ',')) as t1
)
select *
from unpivoted_data
pivot (min(value) for index in (ANY));
```

The query will output something like this:

| SEQ | 1      | 2       | 3       | 4       | 5       | 6       |
|-----|--------|---------|---------|---------|---------|---------|
| 2   | Angela | CSE 580 | CSE 594 | CSE 320 | CSE 510 | CSE 310 |
| 1   | Bob    | CSE 510 | CSE 594 | CSE 310 |         |         |
