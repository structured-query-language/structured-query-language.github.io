# MAX_BY() and MIN_BY() with Window Function


## Input Data

| ORDER_NUMBER | SALES_ASSOCIATE | ORDER_AMOUNT |
|--------------|-----------------|--------------|
| 1            | Chelsea         | 10           |
| 2            | Chelsea         | 12           |
| 3            | Chelsea         | 5            |
| 4            | Chelsea         | 20           |
| 5            | Chelsea         | 30           |
| 6            | Anna            | 3            |
| 7            | Anna            | 2            |
| 8            | Anna            | 1            |
| 9            | Anna            | 199          |
| 10           | Anna            | 50           |
| 11           | Anna            | 20           |


## Query

```sql
select distinct
  sales_associate
  , max_by(order_amount, order_amount, 2) 
    over (partition by sales_associate) as top_2_sales_by_amount
  , min_by(order_amount, order_amount, 2)
    over (partition by sales_associate) as lowest_2_sales_by_amount
from orders;
```

## Output

| SALES_ASSOCIATE | TOP_2_SALES_BY_AMOUNT | LOWEST_2_SALES_BY_AMOUNT |
|-----------------|-----------------------|---------------------------|
| Chelsea         | [   30,   20 ]        | [   5,   10 ]             |
| Anna            | [   199,   50 ]       | [   1,   2 ]              |

# See also
<ul id="recent-articles">
{% for page in site.pages %}
    {% if (page.title contains "MAX_BY"  or page.title contains "MIN_BY") %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>
