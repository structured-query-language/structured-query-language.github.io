# Split a column value into multiple columns based on delimiter using PIVOT

## Input Data

| ROW_VALUE                                      |
|------------------------------------------------|
| Bob,CSE 510,CSE 594,CSE 310                    |
| Angela,CSE 580,CSE 594,CSE 320,CSE 510,CSE 310 |



## Query

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

### Query Output

| SEQ | 1      | 2       | 3       | 4       | 5       | 6       |
|-----|--------|---------|---------|---------|---------|---------|
| 2   | Angela | CSE 580 | CSE 594 | CSE 320 | CSE 510 | CSE 310 |
| 1   | Bob    | CSE 510 | CSE 594 | CSE 310 |         |         |



# See also:
<ul id="recent-articles">
{% for page in site.pages %}
    {% if page.title contains "PIVOT" %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>

