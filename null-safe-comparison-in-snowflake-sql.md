# Null-safe comparison in Snowflake

At times you need to compare data in a null-safe manner. Snowflake has the `distinct from` that allows you to do null-safe comparison.

Here is an example with using `distinct from`:

```
with table_a as (select 1 as id, 'angela' as name union select 2, 'scott'  union select 3, 'bob')
, table_b as (select 1 as id, 'angela' as name union select 2, 'scottt'  union select 3, null)

select * from table_a
inner join table_b using (id)
where table_a.name is distinct from table_b.name;
```

The output is 

| ID | NAME  | NAME_2 |
|----|-------|--------|
| 2  | scott | scottt |
| 3  | bob   | null   |
