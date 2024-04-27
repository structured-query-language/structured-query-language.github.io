# SQL ASOF join in Snowflake

## What is an ASOF Join?

Time series data is not always perfectly aligned. Timestamps may be slightly off, or there may be a delay between cause and effect. This can make connecting two sets of ordered data challenging. [ASOF joins](ASOF-join.md) are a tool for solving this and other similar problems.

One of the problems that [ASOF joins](ASOF-join.md) are used to solve is finding the value of a varying property at a specific point in time. 

More generally, however, [ASOF joins](ASOF-join.md) embody some common temporal analytic semantics, which can be cumbersome and slow to implement in standard SQL.

## Stock Portfolio Example Data Set

Let’s start with a Stock Portofolio example. 

We have a table `stock_prices` of stock prices with timestamps:

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

## ASOF Joins in Snowflake

We can compute the value of each holding at that point in time by finding the most recent price before the holding’s timestamp by using an [ASOF join](ASOF-join.md).

### SQL Query

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

This attaches the value of the holding at that time to each row.

### Query Output

| SYMBOL | STOCK_PRICE | NUMBER_OF_SHARES | TRANSACTION_TIMESTAMP   | STOCK_PRICE_ASOF        | VALUE    |
|--------|-------------|------------------|-------------------------|-------------------------|----------|
| TSLA   | 9.93        | 100.00           | 2024-02-02 14:29:20.264 | 2024-02-02 14:29:10.753 | 993.0000 |
| TSLA   | 9.61        | 77.00            | 2024-02-02 14:30:51.040 | 2024-02-02 14:30:43.379 | 739.9700 |
| TSLA   | 9.06        | 56.00            | 2024-02-02 14:45:41.192 | 2024-02-02 14:45:32.556 | 507.3600 |
| TSLA   | 9.72        | 86.00            | 2024-02-02 15:06:48.475 | 2024-02-02 15:06:40.404 | 835.9200 |
| TSLA   | 9.44        | 60.00            | 2024-02-02 15:23:12.587 | 2024-02-02 15:23:07.893 | 566.4000 |


It essentially executes a function defined by looking up nearby values in the `stock_prices` table. 

## Windowing Alternative

Standard SQL can implement this kind of join, but you need to use an `effectivity` aka `state` table using a windowing function as following:

```sql
with stock_price_effectivity as (
  select 
    symbol
    , stock_price_asof as start_time
    , lead (stock_price_asof) over (partition by symbol order by start_time asc) as end_time 
    , price
  from stock_prices
)   

select 
  stock_portfolio.symbol
  , price
  , number_of_shares * price as value
from state
inner join stock_portfolio on state.symbol = stock_portfolio.symbol
  and stock_portfolio.transaction_timestamp between start_time and end_time;
```

> Note that we are first calculating the `effectivity` or `state` of stock price at each of the interval using the `stock_price_asof` timestamp. This is an expensive operation.


Here is what the output of the `stock_price_effectivity` will look like:

| SYMBOL | START_TIME              | END_TIME                | PRICE |
|--------|-------------------------|-------------------------|-------|
| TSLA   | 2024-02-02 14:29:10.753 | 2024-02-02 14:29:52.806 | 9.93  |
| TSLA   | 2024-02-02 14:29:52.806 | 2024-02-02 14:30:43.379 | 9.62  |
| TSLA   | 2024-02-02 14:30:43.379 | 2024-02-02 14:32:35.397 | 9.61  |
| TSLA   | 2024-02-02 14:32:35.397 | 2024-02-02 14:41:38.263 | 9.93  |
| TSLA   | 2024-02-02 14:41:38.263 | 2024-02-02 14:45:00.480 | 9.68  |
| TSLA   | 2024-02-02 14:45:00.480 | 2024-02-02 14:45:32.556 | 9.05  |
| TSLA   | 2024-02-02 14:45:32.556 | 2024-02-02 15:04:29.979 | 9.06  |
| TSLA   | 2024-02-02 15:04:29.979 | 2024-02-02 15:06:40.404 | 9.88  |
| TSLA   | 2024-02-02 15:06:40.404 | 2024-02-02 15:23:07.893 | 9.72  |
| TSLA   | 2024-02-02 15:23:07.893 | 2024-02-02 15:23:21.083 | 9.44  |
| TSLA   | 2024-02-02 15:23:21.083 |                         | 9.13  |

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
