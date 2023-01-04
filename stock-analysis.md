# Stock Analysis using MATCH_RECOGNIZE

Pattern Matching is key to anomaly detection and trend analysis. It is quite common to query the Data Warehouse to find patterns in data. One commonly overlooked SQL clause that allows detection of patterns in relational data is `MATCH_RECOGNIZE`.

`MATCH_RECOGNIZE` clause is available in modern Warehouse Databases like #Snowflake and #Azure. Here is a sample query to identify stocks that show an increasing stock price trend for the most number of days. This is a simple example, but regex powered `MATCH_RECOGNIZE` can be used for sophisticated trend analysis.


```sql
with stocks as
(select * from  us_stock_market_data_for_data_science.public.stock_history where date>'2022-01-01')
select symbol, days_of_increasing_stock_price, streak_start_date, streak_end_date
from us_stock_market_data_for_data_science.public.stock_history
match_recognize(
    partition by symbol
    order by date
    measures
        first(date) as streak_start_date,
        last(date) as streak_end_date,
        count(*) as days_of_increasing_stock_price
    one row per match
    pattern (increase+)
    define
          increase as close > lag(close)
)
order by days_of_increasing_stock_price desc;
```
