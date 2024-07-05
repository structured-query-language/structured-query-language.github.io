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
|---|----|------------|----|----|
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
