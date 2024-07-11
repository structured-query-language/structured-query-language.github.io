# Calculating Geometric Mean of values in a column using SQL

To calculate the Geometric Mean, you have to first multiply all values and then take the nth root of that number, where n is the number of values in the column. There is no function in SQL to calculate the geometric mean. However the following recursive CTE can be used to calculate the Geometric Mean of a column of data.

## Recursive CTE SQL
```sql
with recursive test_data as (
  SELECT 1 AS col1 
  UNION ALL
  SELECT 3
  UNION ALL
  SELECT 5
)
, numbers_to_multiply as (
  SELECT array_agg(col1) as number_array 
  FROM test_data
)
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
select power(multiplied, 1/array_index) as geometric_mean
from multiply
order by array_index desc limit 1;
```

### Query output

|geometric_mean|
|--------------|
|2.46621207433047|


# See also
<ul id="recent-articles">
{% for page in site.pages %}
    {% if page.title contains "Geometric Mean" %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>


