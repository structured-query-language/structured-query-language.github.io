# ANY keyword in the Snowflake's PIVOT IN list

## Using ANY keyword
```sql
select *
from performance_reviews
pivot (avg(rating) for skill in (ANY))
```

| Name         | Acting | Comedy |
|--------------|--------|--------|
| Groucho Marx | 5      | 4.5    |
| Harpo Marx   |        | 3      |


## Using a Subquery
```sql
select *
from performance_reviews
pivot (avg(rating) for skill in (select skill from performance_reviews where rating > 4));
```

| Name         | Acting | Comedy |
|--------------|--------|--------|
| Groucho Marx | 5      | 4.5    |
| Harpo Marx   |        | 3      |
