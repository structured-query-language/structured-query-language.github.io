# Null-safe comparison in Snowflake

At times you need to compare data in a null-safe manner. Snowflake has the `distinct from` that allows you to do null-safe comparison.

Here is an example with using `distinct from`:

## Raw Data

### Table A

| ID | NAME   |
|----|--------|
| 1  | angela |
| 2  | scott  |
| 3  | bob    |


### Table B

| ID | NAME   |
|----|--------|
| 1  | angela |
| 2  | scottt |
| 3  | NULL   |


## Query using distinct from
```sql
select * from table_a
inner join table_b using (id)
where table_a.name is distinct from table_b.name;
```

### Output

| ID | NAME  | NAME_2 |
|----|-------|--------|
| 2  | scott | scottt |
| 3  | bob   | NULL   |
> Note that the ID = 3 shows up even though NAME was null TABLE_B for this ID. This is because we used `is distinct from` instead of `<>` or `!=` operator.


## Query using the not-equal operator (<>)
```sql
select * from table_a
inner join table_b using (id)
where table_a.name <> table_b.name;
```

### Output

| ID | NAME  | NAME_2 |
|----|-------|--------|
| 2  | scott | scottt |

> Note that the ID = 3 doesn't show up here as the NAME was null TABLE_B for this ID.


# Generating Sample Data


```sql
with table_a as (select 1 as id, 'angela' as name union select 2, 'scott'  union select 3, 'bob')
, table_b as (select 1 as id, 'angela' as name union select 2, 'scottt'  union select 3, null)

select * from table_a
inner join table_b using (id)
where table_a.name is distinct from table_b.name;
```

> <a href="{{ site.github.repository_url }}/edit/{{ site.github.source.branch }}/{{ page.path }}">Edit this page on GitHub</a>

