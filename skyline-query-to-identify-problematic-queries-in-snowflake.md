# Skyline Query to identify problematic queries in Snowflake

Selecting the correct Warehouse size in Snowflake can be tricky. If your queries are spilling over to the Remote Storage then you either need to re-write the query or increase the Warehouse size. But how do you identify these problematic queries that are running on undersized Warehouse? Identifying these queries is a Maximum Vector problem that be solved using [Skyline queries](skyline-query-pareto-set-maxima.md). A [Skyline query](skyline-query-pareto-set-maxima.md) finds points in a vector space that dominate other points. Here is how a [Skyline Query](skyline-query-pareto-set-maxima.md) can be used to identify Snowflake queries that are problematic.

![image](https://github.com/structured-query-language/structured-query-language.github.io/assets/121721444/bce0ffe5-fee2-4d8e-964d-935b5f714b4c)

## Skyline Query

```sql
with query_history as (
  select 
    query_text
    , bytes_spilled_to_remote_storage
    , decode(warehouse_size, 'X-Small', 1, 'Small', 2, 'Medium', 3, 'Large', 4, 'X-Large', 5) as warehouse_size
    from snowflake.account_usage.query_history
    where warehouse_size is not null and bytes_spilled_to_remote_storage > 1000
    order by start_time desc
    limit 10000
)
select 
  query_text 
  , bytes_spilled_to_remote_storage
  , warehouse_size
from query_history as o
where not exists ( -- Skyline query to identify worst performing queries
  select 1
  from query_history as i
  where i.warehouse_size <= o.warehouse_size and i.bytes_spilled_to_remote_storage >= o.bytes_spilled_to_remote_storage
  and (i.warehouse_size < o.warehouse_size or i.bytes_spilled_to_remote_storage > o.bytes_spilled_to_remote_storage)
);
```

## Query Output

| QUERY_TEXT        | bytes_spilled_to_remote_storage (GB) | WAREHOUSE_SIZE |
|-------------------|--------------------------------------|----------------|
| select * from ... | 4                                    | 1              |
| select * from ... | 5                                    | 4              |


# See also
<ul id="recent-articles">
{% for page in site.pages %}
    {% if page.title contains "Skyline" %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>
