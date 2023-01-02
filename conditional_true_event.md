# CONDITIONAL_TRUE_EVENT

`CONDITIONAL_TRUE_EVENT` is a powerful, yet underutilized Window Function in #Snowflake. `CONDITIONAL_TRUE_EVENT` let's you cumulatively count the occurrences of a TRUE event in a WINDOW. 

Here is a example of how to use it to count number of days the stock price was higher than the previous highest value of the Stock for a given Window.

```sql
select
  symbol
  , date
  , close
  , row_number() over (partition by symbol order by date) as day
  , conditional_true_event (close > lag(close) over (partition by symbol order by date)) 
      over (partition by symbol order by date) as days_of_increase
from us_stock_market_data_for_data_science.public.stock_history
where symbol='SNOW';
```

## Same query without using `conditional_true_event`

```sql
with increase_flagged as (
  select 
    symbol
    , date
    , close
    , close > lag(close) over (partition by symbol order by date) as increase_flag
    from us_stock_market_data_for_data_science.public.stock_history
)
select 
  symbol
  , date
  , close
  , count_if(increase_flag) over(partition by symbol order by date) as days_of_increase
from increase_flagged  
where symbol='SNOW';
```

