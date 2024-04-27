# ASOF join in Snowflake

## What Is an ASOF Join?

An ASOF (or "as of") join is a type of join operation used when analyzing two sets of time-series data. It essentially matches each record from one table with the nearest—but not necessarily equal—value from another table based on a chosen column. Snowflake supports this out of the box using using the [ASOF](ASOF-join.md) keyword

The chosen column needs to have some concept of range for the [ASOF](ASOF-join.md) operation to work. You may think of it as being the "closest value," but not exceeding the comparison. In Snowflake it works for Timestamp and Numeric valued columns. 

## Understanding ASOF Joins

[ASOF joins](ASOF-join.md) are a powerful tool when dealing with time-series data. In simple terms, an [ASOF join](ASOF-join.md) will, for each row in the left table, find a corresponding single row in the right table where the key value is less than or equal to the key in the left table.

This is a common operation when dealing with financial data, sensor readings, or other types of time-series data where readings might not align perfectly by timestamp.

For a simple example, consider the real-world question, "What was the temperature yesterday at this time?" It is very unlikely that a temperature reading was taken yesterday at exactly the millisecond that the question is asked today. What we really want is "What was the temperature taken yesterday up to today's time stamp?"

This simple example becomes a lot more complex when we start comparing temperatures day over day, week over week, etc.

## ASOF Joins in Snowflake

### Step 1: Prepare your data

Ensure your data is in the correct format for the [ASOF join](ASOF-join.md). You'll need a timestamp or other monotonically increasing column to use as a key for the join.

Suppose you have two tables, `stock_prices` and `stock_portfolio`, each containing a timestamp column, and you want to join them by stock and the nearest timestamp.

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

> Note that the `transaction_timestamp` from `stock_portfolio` does not align with the `stock_price_asof` in the `stock_prices` table.


### Step 2: Query the data using a ASOF join

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
  on stock_prices.symbol = stock_portfolio.symbol
;
```

This attaches the value of the holding at that time to each row.


| SYMBOL | STOCK_PRICE | NUMBER_OF_SHARES | TRANSACTION_TIMESTAMP   | STOCK_PRICE_ASOF        | VALUE    |
|--------|-------------|------------------|-------------------------|-------------------------|----------|
| TSLA   | 9.93        | 100.00           | 2024-02-02 14:29:20.264 | 2024-02-02 14:29:10.753 | 993.0000 |
| TSLA   | 9.61        | 77.00            | 2024-02-02 14:30:51.040 | 2024-02-02 14:30:43.379 | 739.9700 |
| TSLA   | 9.06        | 56.00            | 2024-02-02 14:45:41.192 | 2024-02-02 14:45:32.556 | 507.3600 |
| TSLA   | 9.72        | 86.00            | 2024-02-02 15:06:48.475 | 2024-02-02 15:06:40.404 | 835.9200 |
| TSLA   | 9.44        | 60.00            | 2024-02-02 15:23:12.587 | 2024-02-02 15:23:07.893 | 566.4000 |


It essentially executes a function defined by looking up nearby values in the `stock_prices` table. 

# See also:
<ul id="recent-articles">
{% for page in site.pages %}
    {% if page.title contains "MATCH_RECOGNIZE" %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>
