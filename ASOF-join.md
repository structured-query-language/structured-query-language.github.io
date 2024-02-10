# ASOF JOIN

ASOF JOIN in Snowflake joins two different time-series measures. For each row in the first time-series, the ASOF JOIN takes from the second time-series a timestamp that meets both of the following criteria:

> The timestamp is the closest to the first timestamp.
> The timestamp is strictly prior or after or equal to the first timestamp.

## Example

Given the following tables:

### stock_prices

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

We have another table `stock_portfolio` containing portfolio holdings at various points in time:

### stock_portfolio

| SYMBOL | NUMBER_OF_SHARES | TRANSACTION_TIMESTAMP   |
|--------|------------------|-------------------------|
| TSLA   | 100.00           | 2024-02-02 14:29:20.264 |
| TSLA   | 77.00            | 2024-02-02 14:30:51.040 |
| TSLA   | 56.00            | 2024-02-02 14:45:41.192 |
| TSLA   | 86.00            | 2024-02-02 15:06:48.475 |
| TSLA   | 60.00            | 2024-02-02 15:23:12.587 |

> Note that the `transaction_timestamp` from `stock_portfolio` does not align with the `stock_price_asof` in the `stock_prices` table.

### SQL Query
An ASOF JOIN query can look like the following:

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
    The timestamp of the asks record is prior to or equal to the timestamp of the bids record.
