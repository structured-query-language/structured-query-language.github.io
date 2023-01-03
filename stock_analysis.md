Pattern Matching is key to anomaly detection and trend analysis. It is quite common to query the Data Warehouse to find patterns in data. One commonly overlooked SQL clause that allows detection of patterns in relational data is `MATCH_RECOGNIZE`.

`MATCH_RECOGNIZE` clause is available in modern Warehouse Databases like #Snowflake and #Azure. Here is a sample query to identify stocks that show an increasing stock price trend for the most number of days. This is a simple example, but regex powered `MATCH_RECOGNIZE` can be used for sophisticated trend analysis.


```sql
USE US_STOCKS_DAILY;
// use ZEPL_STOCKS;
SELECT date, close from STOCK_HISTORY where DATE >= '2018-12-10' and DATE <= '2019-08-07' and symbol='OPER'
order by date ASC;


with stocks as
(select * FROM  US_STOCKS_DAILY.PUBLIC.STOCK_HISTORY WHERE DATE>'2022-01-01')
select symbol, days_of_increasing_stock_price, STREAK_START_DATE, STREAK_END_DATE
from stocks
match_recognize(
--    limit duration(minute, 400)
    partition by symbol
    order by DATE
    measures
        first(DATE) as STREAK_START_DATE,
        last(DATE) as STREAK_END_DATE,
        count(*) as days_of_increasing_stock_price
    ONE ROW PER MATCH
    PATTERN (INCREASE+)
    DEFINE
          INCREASE as close > LAG(close)
)
order by days_of_increasing_stock_price DESC;
```
