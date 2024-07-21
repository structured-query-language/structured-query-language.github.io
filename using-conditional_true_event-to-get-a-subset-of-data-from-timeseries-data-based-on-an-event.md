# Using conditional_true_event to get a subset of data from timeseries data based on an event

When working with Timeseries data it is common to retrieve a subset of data based on the occurance of event. For e.g. you may need to get the data between occurance of two observations or eliminate all pre-event observations in the table when a particular event is observed. 

conditional_true_event can be used to achieve this. Example

## Input Data

|period|obsevation|
|------------|----|
| 2020-01-01 | 1  |
| 2020-01-02 | 2  |
| 2020-01-03 | 3  |
| 2020-01-04 | 4  |
| 2020-01-05 | 5  |
| 2020-01-06 | 0  |
| 2020-01-07 | 6  |
| 2020-01-08 | 7  |
| 2020-01-09 | 0  |
| 2020-01-10 | 9  |
| 2020-01-11 | 10 |
| 2020-01-12 | 11 |
| 2020-01-13 | 12 |

## Analysis Goal 1
Retreive all the values since the _first_ occurance of `0` to the next occurance of `0`

## SQL Query

```sql
select *
from periods
qualify conditional_true_event(event = 0) over (order by period asc) = 1;
```

## Query Output

|period|observation|
|------------|----|
| 2020-01-06 | 0 |
| 2020-01-07 | 6 |
| 2020-01-08 | 7 |

## Analysis Goal 2
Retreive all the values since the _second_ occurance of `0`

## SQL Query

```sql
select *
from periods
qualify conditional_true_event(event = 0) over (order by period asc) = 2;
```

## Query Output

|period|observation|
|------------|----|
| 2020-01-09 | 0  |
| 2020-01-10 | 9  |
| 2020-01-11 | 10 |
| 2020-01-12 | 11 |
| 2020-01-13 | 12 |


