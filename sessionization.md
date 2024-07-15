# Sessionization

Sessionization is the act of turning event-based data into sessions. It is widely used in several domains, such as: Web Analytics and Trip Analytics. Basically given a "user id" or a "machine id", the question is: how can we recreate sessions? Or more precisely, how do we choose the boundaries of a session? 

A session, for example, can be defined by threshold time such as if the next action is in a range of time greater than `T`, it defines the start of a new session. Sessionization of event-based data can be easily implemented in Snowflake using the powerful `CONDITIONAL_TRUE_EVENT` Window function. The following query creates session boundaries using `T=50` mins i.e. if more than 50 minutes have elapsed between the events for a given visitor a new Session is defined.

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
