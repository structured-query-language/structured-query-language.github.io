# RANGE Clause in Window Functions: Practical Examples

RANGE Clause in Window Functions is now available in Snowflake. Here as some practical examples of using RANGE clause in the Window Functions.

## Example 1. Running sum of acquitions in the last 28 days

### Input Table: Acquisitions by Date

|article|qty|date_acquired|
|:---:|----|------------|
| A | 3  | 2019-10-11 |
| A | 5  | 2019-10-08 |
| A | 10 | 2019-10-05 |
| A | 2  | 2019-09-15 |
| A | 1  | 2019-09-09 |
| A | 1  | 2019-09-01 |
| B | 3  | 2019-10-11 |
| B | 2  | 2019-10-08 |
| B | 3  | 2019-10-05 |
| B | 1  | 2019-09-15 |
| B | 4  | 2019-09-09 |
| C | 1  | 2019-10-11 |
| C | 2  | 2019-10-08 |
| C | 1  | 2019-10-05 |
| C | 1  | 2019-09-15 |
| C | 0  | 2019-09-09 |
| C | 4  | 2019-09-01 |
| C | 1  | 2019-08-28 |

### RANGE based Window query to get running sum of acquitions in the last 28 days

```sql
select 
  *
  , sum(qty) over (partition by article
      order by date_acquired
      range between interval '28 day' preceding and current row
    ) as sum_qty_28_days
from acquisitions;
```

### Query Output:

|article|qty|date_acquired|sum_qty_28_days|
|---|----|------------|----|
| A | 1  | 2019-09-01 | 1  |
| A | 1  | 2019-09-09 | 2  |
| A | 2  | 2019-09-15 | 4  |
| A | 10 | 2019-10-05 | 13 |
| A | 5  | 2019-10-08 | 17 |
| A | 3  | 2019-10-11 | 20 |
| C | 1  | 2019-08-28 | 1  |
| C | 4  | 2019-09-01 | 5  |
| C | 0  | 2019-09-09 | 5  |
| C | 1  | 2019-09-15 | 6  |
| C | 1  | 2019-10-05 | 2  |
| C | 2  | 2019-10-08 | 4  |
| C | 1  | 2019-10-11 | 5  |
| B | 4  | 2019-09-09 | 4  |
| B | 1  | 2019-09-15 | 5  |
| B | 3  | 2019-10-05 | 8  |
| B | 2  | 2019-10-08 | 6  |
| B | 3  | 2019-10-11 | 9  |



## Example 2. Count of website visits by visitor_id in a 90 day window

### Input Table: Visitor Data (`visitor_data`)

|VISITOR_ID|DATE_VISITED|
|---|------------|
| 1 | 2022-04-14 |
| 3 | 2022-01-13 |
| 3 | 2022-03-13 |
| 3 | 2022-05-13 |
| 5 | 2022-01-01 |
| 5 | 2022-02-01 |
| 5 | 2022-05-01 |
| 5 | 2022-06-01 |
| 5 | 2022-08-01 |

### SQL Query 

```sql
select 
  *
  , count(*) over (partition by visitor_id
      order by date_visited
      range between interval '90 day' preceding and current row
    ) as count_90_days
from visitor_data;
```

### Query Output:

|VISITOR_ID|DATE_VISITED|COUNT_90_DAYS|
|---|------------|---|
| 3 | 2022-01-13 | 1 |
| 3 | 2022-03-13 | 2 |
| 3 | 2022-05-13 | 2 |
| 1 | 2022-04-14 | 1 |
| 5 | 2022-01-01 | 1 |
| 5 | 2022-02-01 | 2 |
| 5 | 2022-05-01 | 2 |
| 5 | 2022-06-01 | 2 |
| 5 | 2022-08-01 | 2 |


## Example 3. Webpage Views. Running sum of pageviews by customer in the last 60 days

### Input data

|DATE_VIEWED|VIEWS|CUSTOMER_ID|
|------------|----|---|
| 2020-01-01 | 1  | a |
| 2020-01-15 | 2  | b |
| 2020-01-20 | 1  | a |
| 2020-01-25 | 20 | b |
| 2020-02-15 | 1  | a |
| 2020-03-15 | 2  | b |
| 2020-04-15 | 1  | a |
| 2020-05-15 | 2  | b |

### SQL Query

```sql
select 
  *
  , sum(views) over (partition by customer_id
order by date_viewed 
range between interval '59 day' preceding and current row)
from page_views
; 
```
### Query Output

|DATE_VIEWED|VIEWS|CUSTOMER_ID|ROLLING_SUM_OF_VIEWS_LAST_60_DAYS|
|------------|----|---|----|
| 2020-01-01 | 1  | a | 1  |
| 2020-01-20 | 1  | a | 2  |
| 2020-02-15 | 1  | a | 3  |
| 2020-04-15 | 1  | a | 1  |
| 2020-01-15 | 2  | b | 2  |
| 2020-01-25 | 20 | b | 22 |
| 2020-03-15 | 2  | b | 22 |
| 2020-05-15 | 2  | b | 2  |

## Example 4. Moving Average. Moving Average (MA) of the 3 weeks Windows of data.

### Input data

|report_date|item_id|brand|cycle_length|
|------------|-----|---------|---|
| 2023-09-13 | 123 | Apple   | 6 |
| 2023-09-13 | 500 | Apple   | 5 |
| 2023-09-20 | 123 | Apple   | 6 |
| 2023-09-20 | 500 | Apple   | 5 |
| 2023-09-27 | 123 | Apple   | 6 |
| 2023-09-27 | 500 | Apple   | 4 |
| 2023-10-04 | 123 | Apple   | 6 |
| 2023-10-04 | 500 | Apple   | 4 |
| 2023-09-13 | 325 | Samsung | 7 |
| 2023-09-13 | 862 | Samsung | 3 |
| 2023-09-13 | 455 | Samsung | 5 |
| 2023-09-20 | 325 | Samsung | 7 |
| 2023-09-20 | 862 | Samsung | 3 |
| 2023-09-27 | 455 | Samsung | 5 |
| 2023-10-04 | 325 | Samsung | 7 |
| 2023-09-27 | 862 | Samsung | 4 |
| 2023-10-04 | 455 | Samsung | 7 |
| 2023-10-11 | 325 | Samsung | 7 |
| 2023-10-04 | 862 | Samsung | 4 |
| 2023-10-11 | 455 | Samsung | 7 |

### SQL Query

```sql
select 
  *
  , avg(cycle_length) over (partition by brand
    order by report_date
    range between interval '14 days' preceding and current row
  ) as brand_avg_cycle_length_3_weeks
from average_cycle
order by brand, report_date;
```

### Query Output

|report_date|item_id|brand|cycle_length|brand_avg_cycle_length_3_weeks|
|------------|-----|---------|---|-------|
| 2023-09-13 | 500 | Apple   | 5 | 5.500 |
| 2023-09-13 | 123 | Apple   | 6 | 5.500 |
| 2023-09-20 | 123 | Apple   | 6 | 5.500 |
| 2023-09-20 | 500 | Apple   | 5 | 5.500 |
| 2023-09-27 | 123 | Apple   | 6 | 5.333 |
| 2023-09-27 | 500 | Apple   | 4 | 5.333 |
| 2023-10-04 | 123 | Apple   | 6 | 5.166 |
| 2023-10-04 | 500 | Apple   | 4 | 5.166 |
| 2023-09-13 | 455 | Samsung | 5 | 5.000 |
| 2023-09-13 | 862 | Samsung | 3 | 5.000 |
| 2023-09-13 | 325 | Samsung | 7 | 5.000 |
| 2023-09-20 | 325 | Samsung | 7 | 5.000 |
| 2023-09-20 | 862 | Samsung | 3 | 5.000 |
| 2023-09-27 | 455 | Samsung | 5 | 4.857 |
| 2023-09-27 | 862 | Samsung | 4 | 4.857 |
| 2023-10-04 | 325 | Samsung | 7 | 5.285 |
| 2023-10-04 | 455 | Samsung | 7 | 5.285 |
| 2023-10-04 | 862 | Samsung | 4 | 5.285 |
| 2023-10-11 | 325 | Samsung | 7 | 5.857 |
| 2023-10-11 | 455 | Samsung | 7 | 5.857 |
