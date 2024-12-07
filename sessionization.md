# Sessionization, or solving the Gaps-and-Islands Problem using conditional_true_event

Sessionization or conventionally _Gaps and Islands_ in Time-series Analysis are terms referring to the same problem of having to reset all parameters of a predictive or retrospective analysis in a time series when the time series has a gap. 

Sessionization is the act of turning event-based data into sessions. It is widely used in several domains, such as: Web Analytics and Trip Analytics. Basically given a "user id" or a "machine id", the question is: how can we recreate sessions? Or more precisely, how do we choose the boundaries of a session? 

Whereas, timeseries analysis, usually uses "Gaps and Islands" - if there are no measurements for a relatively extended interval, this causes a "gap"; and a cluster of several measurements, close, time-wise, to each other, causes an "island".

|![Untitled drawing(21)](https://github.com/user-attachments/assets/ecdad674-9e7b-4568-bdf5-a15dc96233d4)|
|:--:|
|_Figure 1._ A session, for example, can be defined by threshold time such as if the next action is in a range of time greater than `T=50`, it defines the start of a new session. 

## Why do you need sessionization?

Sessionization has many use cases: web traffic patterns, cohort analysis, attribution, etc. Sessions make it easier to group and analyze events and actions users take which helps us understand the customer journey and their interactions with your product. This allows you to optimize your marketing campaigns and user flows to increase conversion rates and bring in more business.

## How do you sessionize in SQL?

You can use the [CONDITIONAL_TRUE_EVENT](conditional_true_event.md) function to easily sessionize events in one line of SQL code.

1. First, you'll need to structure your data so there is one row per event.
2. Next, you'll need to get the timestamp of the previous event for each event, partitioned by _visitor identifier_ or _username_.
4. Next, compare the _previous timestamp_ to the _current timestamp_ in order to calculate how much time elapsed between the two events.
5. If the time between the two events is less than _n_ minutes, these two events are in the same session. If the time between the two events was greater than _n_ minutes, consider these two events to be in separate sessions.

## An illustrative example

For the sake of this analysis, we’ll use a popular variation of a time-based definition: a “session timeout” length, or a maximum length of time between events that still qualifies a user as “active.” This is useful because it lets you analyze user behavior contained to a period of active usage, but it can also be difficult to nail down since users often multi-task among many apps.

Sessionization of event-based data can be easily implemented in Snowflake using the powerful [CONDITIONAL_TRUE_EVENT](conditional_true_event.md) Window function. The following query creates session boundaries using `T=50` mins i.e. if more than 50 minutes have elapsed between the events for a given visitor a new Session is defined.

## Sample Clickstream data

```sql
create or replace table clickstream (post_visid_high number, post_visid_low number, date_time timestamp);
insert into clickstream values (1,0, '2013-09-04T15:49:49');
insert into clickstream values (1,0, '2013-09-04T15:49:58');
insert into clickstream values (1,0, '2013-09-04T15:49:49');
insert into clickstream values (1,0, '2013-09-04T15:49:58');
insert into clickstream values (1,0, '2013-09-04T16:37:11');
insert into clickstream values (1,0, '2013-09-04T16:37:18');
insert into clickstream values (1,0, '2013-09-04T16:39:27');
insert into clickstream values (1,0, '2013-09-04T16:43:57');
insert into clickstream values (1,0, '2013-09-04T20:12:03');
insert into clickstream values (1,0, '2013-09-05T00:00:17');
insert into clickstream values (1,0, '2013-09-05T00:20:35');
insert into clickstream values (2,0, '2013-09-05T00:22:37');
insert into clickstream values (2,0, '2013-09-05T01:19:29');
insert into clickstream values (1,0, '2013-09-05T01:19:39');
insert into clickstream values (1,0, '2013-09-05T01:20:03');
insert into clickstream values (1,0, '2013-09-05T01:20:17');
insert into clickstream values (1,0, '2013-09-05T02:33:42');

select * from clickstream;
```

| POST_VISID_HIGH | POST_VISID_LOW | DATE_TIME               |
|-----------------|----------------|-------------------------|
| 1               | 0              | 2013-09-04 15:49:49.000 |
| 1               | 0              | 2013-09-04 15:49:58.000 |
| 1               | 0              | 2013-09-04 15:49:49.000 |
| 1               | 0              | 2013-09-04 15:49:58.000 |
| 1               | 0              | 2013-09-04 16:37:11.000 |
| 1               | 0              | 2013-09-04 16:37:18.000 |
| 1               | 0              | 2013-09-04 16:39:27.000 |
| 1               | 0              | 2013-09-04 16:43:57.000 |
| 1               | 0              | 2013-09-04 20:12:03.000 |
| 1               | 0              | 2013-09-05 00:00:17.000 |
| 1               | 0              | 2013-09-05 00:20:35.000 |
| 2               | 0              | 2013-09-05 00:22:37.000 |
| 2               | 0              | 2013-09-05 01:19:29.000 |
| 1               | 0              | 2013-09-05 01:19:39.000 |
| 1               | 0              | 2013-09-05 01:20:03.000 |
| 1               | 0              | 2013-09-05 01:20:17.000 |
| 1               | 0              | 2013-09-05 02:33:42.000 |

## Sessionization using conditional_true_event
```sql
select 
  post_visid_high || ':' || post_visid_low as visitor_id
  , date_time
  , datediff(
      minute
      , lag(date_time) over (partition by visitor_id order by date_time asc)
      , date_time
    ) as minutes_since_last_action
  , conditional_true_event(minutes_since_last_action > 50) 
      over (partition by visitor_id order by date_time asc)
    as session_count
from clickstream;

```
### Query output with Sessions

| VISITOR_ID | DATE_TIME               | MINUTES_SINCE_LAST_ACTION | SESSION_COUNT |
|------------|-------------------------|---------------------------|---------------|
| 2:0        | 2013-09-05 00:22:37.000 |                           | 0             |
| 2:0        | 2013-09-05 01:19:29.000 | 57                        | 1             |
| 1:0        | 2013-09-04 15:49:49.000 |                           | 0             |
| 1:0        | 2013-09-04 15:49:49.000 | 0                         | 0             |
| 1:0        | 2013-09-04 15:49:58.000 | 0                         | 0             |
| 1:0        | 2013-09-04 15:49:58.000 | 0                         | 0             |
| 1:0        | 2013-09-04 16:37:11.000 | 48                        | 0             |
| 1:0        | 2013-09-04 16:37:18.000 | 0                         | 0             |
| 1:0        | 2013-09-04 16:39:27.000 | 2                         | 0             |
| 1:0        | 2013-09-04 16:43:57.000 | 4                         | 0             |
| 1:0        | 2013-09-04 20:12:03.000 | 209                       | 1             |
| 1:0        | 2013-09-05 00:00:17.000 | 228                       | 2             |
| 1:0        | 2013-09-05 00:20:35.000 | 20                        | 2             |
| 1:0        | 2013-09-05 01:19:39.000 | 59                        | 3             |
| 1:0        | 2013-09-05 01:20:03.000 | 1                         | 3             |
| 1:0        | 2013-09-05 01:20:17.000 | 0                         | 3             |
| 1:0        | 2013-09-05 02:33:42.000 | 73                        | 4             |

![image](https://user-images.githubusercontent.com/121721444/210683352-da2f5ca0-d444-4b77-8dd7-b8e65b7f73ca.png)


# See also
<ul id="recent-articles">
{% for page in site.pages %}
    {% if (page.title contains "Sessionization" or page.title contains "conditional_true_event" or page.title contains "Conditional True Event" or page.title contains "Gaps-and-Islands" or page.title contains "Conditional Change Event") or page.title contains "conditional_change_event") %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>


