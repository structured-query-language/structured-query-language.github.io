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

## Example 4. Moving Average (MA) of the 3 weeks Windows of data.

### Analysis Goal

Take all the items in a brand and looking back 3 weeks including current (so 3 rows for each item) and calculate the average of the cycle length column. 

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
    range between interval '3 weeks' preceding and current row
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




## Example 5. Rolling Average of the 3 days Window of data.

### Analysis Goal
Get the rolling average of three days as per sales of the item.

### Input Table

| sales_date | daily_sales|salesman|items|
|------------|-----------|--------|------|
| 2021-12-12 | 12000.30  | Max    | KCR  |
| 2021-12-12 | 32.30     | Max    | Crux |
| 2021-12-12 | 13000.30  | Max    | Xray |
| 2021-12-13 | 14000.30  | Kyle   | KCR  |
| 2021-12-13 | 14000.30  | Kyle   | Crux |
| 2021-12-13 | 99000.30  | Kyle   | XRay |
| 2021-12-14 | 2340.30   | Peter  | XRay |
| 2021-12-14 | 1200.30   | Peter  | Crux |
| 2021-12-14 | 22000.30  | Peter  | KCR  |
| 2021-12-15 | 132000.30 | Remo   | Crux |
| 2021-12-15 | 124000.30 | Rexy   | KCR  |
| 2021-12-15 | 120500.30 | Tom    | Xray |
| 2021-12-16 | 122000.30 | Felis  | Crux |
| 2021-12-16 | 120300.30 | Felis  | KCR  |
| 2021-12-16 | 120040.30 | Max    | Xray |
| 2021-12-17 | 120005.30 | Rubert | KCR  |
| 2021-12-17 | 120.30    | Travis | Crux |
| 2021-12-18 | 200.30    | Peter  | XRay |
| 2021-12-18 | 200.30    | Peter  | Crux |
| 2021-12-18 | 200.30    | Peter  | KCR  |
| 2021-12-19 | 200.30    | Peter  | XRay |
| 2021-12-19 | 500.30    | Peter  | KCR  |
| 2021-12-19 | 500.30    | Peter  | CRUX |
| 2021-12-20 | 200.30    | Peter  | XRay |
| 2021-12-20 | 500.30    | Peter  | KCR  |
| 2021-12-20 | 500.30    | Peter  | CRUX |

### SQL Query

```sql
select 
  items
  , sales_date
  , avg(daily_sales) over (partition by items 
      order by sales_date
      range between interval '2 days' preceding and current row
  ) as three_day_moving_average
from sales_info
```

### Query Output

|items|sales_date |three_day_rolling_average|
|------|------------|--------------|
| XRay | 2021-12-13 | 99000.30000  |
| XRay | 2021-12-14 | 50670.30000  |
| XRay | 2021-12-18 | 200.30000    |
| XRay | 2021-12-19 | 200.30000    |
| XRay | 2021-12-20 | 200.30000    |
| Xray | 2021-12-12 | 13000.30000  |
| Xray | 2021-12-15 | 120500.30000 |
| Xray | 2021-12-16 | 120270.30000 |
| CRUX | 2021-12-19 | 500.30000    |
| CRUX | 2021-12-20 | 500.30000    |
| KCR  | 2021-12-12 | 12000.30000  |
| KCR  | 2021-12-13 | 13000.30000  |
| KCR  | 2021-12-14 | 16000.30000  |
| KCR  | 2021-12-15 | 53333.63333  |
| KCR  | 2021-12-16 | 88766.96666  |
| KCR  | 2021-12-17 | 121435.30000 |
| KCR  | 2021-12-18 | 80168.63333  |
| KCR  | 2021-12-19 | 40235.30000  |
| KCR  | 2021-12-20 | 400.30000    |
| Crux | 2021-12-12 | 32.30000     |
| Crux | 2021-12-13 | 7016.30000   |
| Crux | 2021-12-14 | 5077.63333   |
| Crux | 2021-12-15 | 49066.96666  |
| Crux | 2021-12-16 | 85066.96666  |
| Crux | 2021-12-17 | 84706.96666  |
| Crux | 2021-12-18 | 40773.63333  |
