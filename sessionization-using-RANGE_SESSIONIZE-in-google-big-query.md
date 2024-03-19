
## page_visit table

We would like sessionize the following website logs based on the continous time spent on the website and the pages visited during that session.

| user_id | page_url      | entry_time          | exit_time           |
|---------|---------------|---------------------|---------------------|
| saqib   | /index        | 2024-03-19 17:13:19 | 2024-03-19 17:16:19 |
| saqib   | /plant_a_tree | 2024-03-19 17:16:19 | 2024-03-19 17:19:19 |
| saqib   | /plant_a_tree | 2024-03-19 18:43:19 | 2024-03-19 18:44:19 |
| saqib   | /build_a_well | 2024-03-19 18:44:19 | 2024-03-19 18:48:19 |
| saqib   | /donate       | 2024-03-19 18:48:19 | 2024-03-19 18:53:19 |

## SQL

In the following query, a table of sessionized data is produced for `page_visits`, and only ranges that meet or overlap are sessionized:

```sql
with page_visits_range as (
  select
  page_visits.user_id
  , page_visits.page_url
  , range(entry_time, exit_time) as duration
  from page_visits
)
select *
FROM
RANGE_SESSIONIZE(
  TABLE page_visits_range,
  'duration',
  ['user_id']
)
order by session_range
;
```

## Query Output

| user_id | page_url      | duration                                   | session_range                              | Notes          |
|---------|---------------|--------------------------------------------|--------------------------------------------|----------------|
| saqib   | /index        | [2024-03-19T17:13:18, 2024-03-19T17:16:18) | [2024-03-19T17:13:18, 2024-03-19T17:19:18) | First Session  |
| saqib   | /plant_a_tree | [2024-03-19T17:16:18, 2024-03-19T17:19:18) | [2024-03-19T17:13:18, 2024-03-19T17:19:18) | First Session  |
| saqib   | /plant_a_tree | [2024-03-19T18:43:18, 2024-03-19T18:44:18) | [2024-03-19T18:43:18, 2024-03-19T18:53:18) | Second Session |
| saqib   | /build_a_well | [2024-03-19T18:44:18, 2024-03-19T18:48:18) | [2024-03-19T18:43:18, 2024-03-19T18:53:18) | Second Session |
| saqib   | /donate       | [2024-03-19T18:48:18, 2024-03-19T18:53:18) | [2024-03-19T18:43:18, 2024-03-19T18:53:18) | Second Session |
