# Sessionization, or solving the Gaps-and-Islands Problem using RANGE_SESSIONIZE in Google BigQuery

Google recently added a new Table Function called `RANGE_SESSIONIZE` to Google BigQuery. `RANGE_SESSIONIZE` allows you to sessionize temporal data. Timestamps from events that either meet or overlap form one session.


## Input data

We would like sessionize the following website logs based on the continous time spent on the website and the pages visited during that session.

### page_visits table

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
select
  *
  , dense_rank() over (partition by user_id order by session_range asc) as session_number
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

| user_id | page_url      | duration                                   | session_range                              | session_number |
|---------|---------------|--------------------------------------------|--------------------------------------------|----------------|
| saqib   | /index        | [2024-03-19T17:13:18, 2024-03-19T17:16:18) | [2024-03-19T17:13:18, 2024-03-19T17:19:18) | 1  |
| saqib   | /plant_a_tree | [2024-03-19T17:16:18, 2024-03-19T17:19:18) | [2024-03-19T17:13:18, 2024-03-19T17:19:18) | 1  |
| saqib   | /plant_a_tree | [2024-03-19T18:43:18, 2024-03-19T18:44:18) | [2024-03-19T18:43:18, 2024-03-19T18:53:18) | 2  |
| saqib   | /build_a_well | [2024-03-19T18:44:18, 2024-03-19T18:48:18) | [2024-03-19T18:43:18, 2024-03-19T18:53:18) | 2  |
| saqib   | /donate       | [2024-03-19T18:48:18, 2024-03-19T18:53:18) | [2024-03-19T18:43:18, 2024-03-19T18:53:18) | 2  |



# See also
<ul id="recent-articles">
{% for page in site.pages %}
    {% if (page.title contains "Sessionization" or page.title contains "conditional_true_event" or page.title contains "Conditional True Event") %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>

