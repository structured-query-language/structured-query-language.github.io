# Support for ANY keyword in the Snowflake's PIVOT IN list

## Input Data

| Name         | Skill  | Rating |
|--------------|--------|--------|
| Groucho Marx | Acting | 5      |
| Groucho Marx | Comedy | 5      |
| Groucho Marx | Comedy | 4      |
| Harpo Marx   | Comedy | 3      |

## Dynamic PIVOT using the ANY keyword in the IN list
```sql
select *
from performance_reviews
pivot (avg(rating) for skill in (ANY))
```

### Query Output

| Name         | Acting | Comedy |
|--------------|--------|--------|
| Groucho Marx | 5      | 4.5    |
| Harpo Marx   |        | 3      |


## Dynamic Pivot using a Subquery in the IN list

```sql
select *
from performance_reviews
pivot (avg(rating) for skill in (select skill from performance_reviews where rating > 4));
```

### Query Output

| Name         | Acting | Comedy |
|--------------|--------|--------|
| Groucho Marx | 5      | 4.5    |
| Harpo Marx   |        | 3      |



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
