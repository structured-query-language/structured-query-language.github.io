# SQL islands and gaps

A common problem in analyzing a Log data from applications is to create sessions from logged user activity. A user may use the app for couple of hours in day and then come back to it the next day. The log will register the user activity, but the logs will not tell us when the session ended for the day, and a new session started the next day. Typically a sessions are based on _m_ minutes of activity followed by _n_ minutes of no activity. _n_ could be 10 minutes, for example. This is also known as [Gaps-and-Islands](sessionization.md) problem in Computer Science.

## Sample log data

|username|log_timestmap|
|--------|-------------------------|
| Angela | 2020-08-07 20:10:00.000 |
| Scott  | 2020-08-07 20:10:00.000 |
| Bob    | 2020-08-07 20:10:00.000 |
| Bob    | 2020-08-07 20:20:00.000 |
| Angela | 2020-08-07 20:20:00.000 |
| Scott  | 2020-08-07 20:20:00.000 |
| Bob    | 2020-08-07 20:30:00.000 |
| Angela | 2020-08-07 20:30:00.000 |
| Scott  | 2020-08-07 20:30:00.000 |
| Angela | 2020-08-07 20:40:00.000 |
| Scott  | 2020-08-07 20:40:00.000 |
| Bob    | 2020-08-07 20:50:00.000 |
| Angela | 2020-08-07 20:50:00.000 |
| Scott  | 2020-08-07 20:50:00.000 |
| Bob    | 2020-08-07 21:00:00.000 |
| Bob    | 2020-08-07 21:10:00.000 |
| Scott  | 2020-08-07 22:00:00.000 |
| Scott  | 2020-08-07 22:20:00.000 |
| Scott  | 2020-08-07 22:30:00.000 |

### Analysis Goal / Desired Output

From the above App Log, let's say we need to display

1. user
2. begin_timestamp (begin of the session)
3. end_timestamp (end of the session)

|![user-sessions](https://github.com/user-attachments/assets/84350eff-9394-45e0-8250-3fad502d1787)|
|:--:| 
|*Figure 1*. Sessionized User Activity. No activity for 10 minutes or more indicates and end of session.|

We will define the _end of a session_ to be 10+ minutes i.e. If there is no activity for more than 10 minutes, the session should be considered as ended.

## Using MATCH_RECOGNIZE to Sessionize

Below we will use SQL's [MATCH_RECOGNIZE](applied-overview-of-MATCH_RECOGNIZE-clause.md) to _sessionize_ this data

```sql
SELECT username,
       session_start
       , session_end
FROM   app_log
       MATCH_RECOGNIZE(
         PARTITION BY username
         ORDER BY log_timestamp
         MEASURES
           first_value(log_timestamp) AS session_start,
           last_value(log_timestamp) AS session_end
         PATTERN (session_start continuous_activity * )
         DEFINE
           continuous_activity AS log_timestamp <= dateadd('minute', 10,  lag(log_timestamp))
       )
```


### Query output

|Username|session_start|session_end|
|--------|-------------------------|-------------------------|
| Scott  | 2020-08-07 20:10:00.000 | 2020-08-07 20:50:00.000 |
| Scott  | 2020-08-07 22:00:00.000 | 2020-08-07 22:07:00.000 |
| Scott  | 2020-08-07 22:20:00.000 | 2020-08-07 22:30:00.000 |
| Bob    | 2020-08-07 20:10:00.000 | 2020-08-07 20:30:00.000 |
| Bob    | 2020-08-07 20:50:00.000 | 2020-08-07 21:10:00.000 |
| Angela | 2020-08-07 20:10:00.000 | 2020-08-07 20:50:00.000 |

## Using CONDITIONAL_TRUE_EVENT to Sessionize

Another way to _sessionize_ this App Log is to use [CONDITIONAL_TRUE_EVENT](conditional_true_event.md) in SQL

```sql
select 
  username
  , log_timestamp 
  , datediff(
      minute
      , lag(log_timestamp) over (partition by username order by log_timestamp asc)
      , log_timestamp
    ) as minutes_since_last_action
  , conditional_true_event(minutes_since_last_action > 10) 
      over (partition by username order by log_timestamp asc)
    as session_count
from app_log;
```

### Query output

|usernane|log_timestamp|minutes_since_last_action|session_count|
|--------|-------------------------|----|---|
| Scott  | 2020-08-07 20:10:00.000 |    | 0 |
| Scott  | 2020-08-07 20:20:00.000 | 10 | 0 |
| Scott  | 2020-08-07 20:30:00.000 | 10 | 0 |
| Scott  | 2020-08-07 20:40:00.000 | 10 | 0 |
| Scott  | 2020-08-07 20:50:00.000 | 10 | 0 |
| Scott  | 2020-08-07 22:00:00.000 | 70 | 1 |
| Scott  | 2020-08-07 22:07:00.000 | 7  | 1 |
| Scott  | 2020-08-07 22:20:00.000 | 13 | 2 |
| Scott  | 2020-08-07 22:30:00.000 | 10 | 2 |
| Bob    | 2020-08-07 20:10:00.000 |    | 0 |
| Bob    | 2020-08-07 20:20:00.000 | 10 | 0 |
| Bob    | 2020-08-07 20:30:00.000 | 10 | 0 |
| Bob    | 2020-08-07 20:50:00.000 | 20 | 1 |
| Bob    | 2020-08-07 21:00:00.000 | 10 | 1 |
| Bob    | 2020-08-07 21:10:00.000 | 10 | 1 |
| Angela | 2020-08-07 20:10:00.000 |    | 0 |
| Angela | 2020-08-07 20:20:00.000 | 10 | 0 |
| Angela | 2020-08-07 20:30:00.000 | 10 | 0 |
| Angela | 2020-08-07 20:40:00.000 | 10 | 0 |
| Angela | 2020-08-07 20:50:00.000 | 10 | 0 |




# See also
<ul id="recent-articles">
{% for page in site.pages %}
    {% if (page.title contains "Sessionization" or page.title contains "conditional_true_event" or page.title contains "Conditional True Event" or page.title contains "MATCH_RECOGNIZE" or page.title contains "Gaps-and-Islands") %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>
