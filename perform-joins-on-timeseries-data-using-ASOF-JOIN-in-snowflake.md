# Perform joins on timeseries data using ASOF JOIN in Snowflake

Combining two or more tables using values from a related column is one of the most common tasks when analyzing data. This is typically done by matching the values in the related column(s).

But this runs into problems because real world data often does NOT yield perfect matches.

This is particularly true when dealing with streaming data. For instance, imagine that you have two tables. A `stock_portfolio` table showing the time at which different trades for a particular stock were executed and another with the `stock_prices`. Both these tables are recorded with a timestamp with millisecond intervals.

#### stock_prices

| SYMBOL | PRICE | STOCK_PRICE_ASOF        |
|--------|-------|-------------------------|
| TSLA   | 9.93  | 2024-02-02 14:29:10.753 |
| TSLA   | 9.62  | 2024-02-02 14:29:52.806 |
| TSLA   | 9.61  | 2024-02-02 14:30:43.379 |
| TSLA   | 9.93  | 2024-02-02 14:32:35.397 |
| TSLA   | 9.68  | 2024-02-02 14:41:38.263 |
| TSLA   | 9.05  | 2024-02-02 14:45:00.480 |
| TSLA   | 9.06  | 2024-02-02 14:45:32.556 |
| TSLA   | 9.88  | 2024-02-02 15:04:29.979 |
| TSLA   | 9.72  | 2024-02-02 15:06:40.404 |
| TSLA   | 9.44  | 2024-02-02 15:23:07.893 |
| TSLA   | 9.13  | 2024-02-02 15:23:21.083 |

#### stock_portfolio

| SYMBOL | NUMBER_OF_SHARES | TRANSACTION_TIMESTAMP   |
|--------|------------------|-------------------------|
| TSLA   | 100.00           | 2024-02-02 14:29:20.264 |
| TSLA   | 77.00            | 2024-02-02 14:30:51.040 |
| TSLA   | 56.00            | 2024-02-02 14:45:41.192 |
| TSLA   | 86.00            | 2024-02-02 15:06:48.475 |
| TSLA   | 60.00            | 2024-02-02 15:23:12.587 |

For every trade in the `stock_portfolio` tables we would like to find a relevant stock price from the `stock_prices` table i.e. the nearest stock price. This requires us to combine the two tables on the timestamp column.

But we have a problem here. The time stamps for quotes and trades don’t always match.

## Perform inexact joins using ASOF JOIN

We can solve this problem using an [ASOF join](ASOF-join.md). [ASOF join](ASOF-join.md) in Snowflake joins two different time-series measures. For each row in the first time-series, the [ASOF join](ASOF-join.md) takes from the second time-series a timestamp that meets both of the following criteria:

1. The timestamp is the closest to the first timestamp.
2. The timestamp is strictly prior or after or equal to the first timestamp.

[ASOF join](ASOF-join.md) can take the timestamp of from `Table A` and find an entry in `Table B` where the timestamp is closest to the timestamp of the event from `Table A` corresponding to the closest match condition. Equal timestamp values are the closest if available.  

### SQL Query
An [ASOF join](ASOF-join.md) query can look like the following:

```sql
select 
  stock_portfolio.symbol
  , price as stock_price
  , number_of_shares
  , transaction_timestamp
  , stock_price_asof  
  , price * number_of_shares as value
from stock_portfolio
asof join stock_prices
  match_condition (stock_portfolio.transaction_timestamp >= stock_prices.stock_price_asof)
  on stock_prices.symbol = stock_portfolio.symbol ;
```

This is the JOIN result:

| SYMBOL | STOCK_PRICE | NUMBER_OF_SHARES | TRANSACTION_TIMESTAMP   | STOCK_PRICE_ASOF        | VALUE    |
|--------|-------------|------------------|-------------------------|-------------------------|----------|
| TSLA   | 9.93        | 100.00           | 2024-02-02 14:29:20.264 | 2024-02-02 14:29:10.753 | 993.0000 |
| TSLA   | 9.61        | 77.00            | 2024-02-02 14:30:51.040 | 2024-02-02 14:30:43.379 | 739.9700 |
| TSLA   | 9.06        | 56.00            | 2024-02-02 14:45:41.192 | 2024-02-02 14:45:32.556 | 507.3600 |
| TSLA   | 9.72        | 86.00            | 2024-02-02 15:06:48.475 | 2024-02-02 15:06:40.404 | 835.9200 |
| TSLA   | 9.44        | 60.00            | 2024-02-02 15:23:12.587 | 2024-02-02 15:23:07.893 | 566.4000 |


The result has all rows from the `stock_portfolio` table joined with rows from the `stock_prices` table. For each timestamp from the `stock_portfolio` table, the query looks for a timestamp that is equal or prior to it from the `stock_prices` table. If no matching timestamp is found, NULL is inserted.

This query returns all rows from the bids table joined with records from the asks table that meet both the following criterion:

The stock column of the two tables has the same value
The timestamp of the `stock_prices` record is prior to or equal to the timestamp of the `stock_portfolio` record.

# See also:
<ul id="recent-articles">
{% for page in site.pages %}
    {% if page.title contains "ASOF" %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>
