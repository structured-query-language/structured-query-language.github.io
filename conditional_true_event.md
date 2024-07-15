# Conditional True Event

`CONDITIONAL_TRUE_EVENT` is a powerful, yet underutilized Window Function in Snowflake. `CONDITIONAL_TRUE_EVENT` let's you cumulatively count the occurrences of a TRUE event in a WINDOW. 

`CONDITIONAL_TRUE_EVENT` assigns an event window number to each row, starting from 0, and increments the number by 1 when the result of the boolean argument expression evaluates true.

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


| SYMBOL | DATE       | CLOSE      | DAYS_OF_INCREASE |
|--------|------------|------------|------------------|
| SNOW   | 2022-10-19 | 171.04     | 254              |
| SNOW   | 2022-10-20 | 174.04     | 255              |
| SNOW   | 2022-10-21 | 177.1      | 256              |
| SNOW   | 2022-10-24 | 174.77     | 256              |
| SNOW   | 2022-10-25 | 181.82     | 257              |
| SNOW   | 2022-10-26 | 169.45     | 257              |
| SNOW   | 2022-10-27 | 169.11     | 257              |
| SNOW   | 2022-10-28 | 159.69     | 257              |
| SNOW   | 2022-10-31 | 160.3      | 258              |
| SNOW   | 2022-11-01 | 159.5      | 258              |
| SNOW   | 2022-11-02 | 147.75     | 258              |
| SNOW   | 2022-11-03 | 150.47     | 259              |
| SNOW   | 2022-11-04 | 132.31     | 259              |

The result shows that out of 510 days (since listed), the closing price of Snowflake stock was higher than the previous highest value of the stock 259 times. That's is pretty impressive for a Stock!

## Same query without using conditional_true_event

Without `conditional_true_event` support, we would have to use a CTE to achieve the same results:

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


# See also
<ul id="recent-articles">
{% for page in site.pages %}
    {% if (page.title contains "Sessionization" or page.title contains "conditional_true_event" or page.title contains "Conditional True Event") %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>


