# DECODE Function in Snowflake

`IFF` is a conditional expression Snowflake function that evaluates to a Single-level `if-then-else` expression. However `IFF` only allows a single condition. What if you need to implement a `if-then-elseif-else-endif`? Most people will opt to use `CASE STATEMENT`. While `CASE STATEMENT` will do the trick, it is not most optimal solution for a `if-then-elseif-else-endif` or even a `if-then-elseif-else-if-else-endif` conditional expression. `DECODE` function is a versatile Snowflake Conditional Expression Function that lets you implement `if-then-elseif-else-if-else-endif` expression without the use of a `CASE STATEMENT`. Here is an example:


## Raw Data
```
create or replace table orders (order_date date, item_id number, amount number);

insert into orders values 
  ('2022-11-21', 1, 20)
  , ('2022-11-22', 1, 20)
  , ('2022-11-23', 1, 20)
  , ('2022-11-23', 1, 20)
  , ('2022-11-23', 1, 20)
  , ('2022-11-24', 1, 20)
  , ('2022-11-25', 1, 20)
  , ('2022-11-25', 1, 20)
  , ('2022-11-25', 1, 20)
  , ('2022-11-25', 1, 20)
  , ('2022-11-25', 1, 20)
  , ('2022-11-26', 1, 20)
  , ('2022-11-27', 1, 20)
  , ('2022-11-27', 1, 20)
  , ('2022-11-28', 1, 20)
  , ('2022-11-29', 1, 20)
  , ('2022-11-30', 1, 20)
  , ('2022-11-30', 1, 20);
```
## DECODE conditional expression function to implement if-then-elseif-else-endif

```
select
  order_date::date
  , decode(
    extract('dayofweek_iso', order_date)
    , 1, '1 - Monday'
    , 2, '2 - Tuesday'
    , 3, '3 - Wednesday'
    , 4, '4 - Thursday'
    , 5, '5 - Friday'
    , 6, '6 - Saturday'
    , 7, '7 - Sunday'
  ) as day_of_week
  , sum(amount)
from orders
where order_date::date between '2022-11-21' and  '2022-11-27'
group by 1, 2
order by day_of_week
```

| ORDER_DATE::DATE | DAY_OF_WEEK   | SUM(AMOUNT) |
|------------------|---------------|-------------|
| 2022-11-21       | 1 - Monday    | 20          |
| 2022-11-22       | 2 - Tuesday   | 20          |
| 2022-11-23       | 3 - Wednesday | 60          |
| 2022-11-24       | 4 - Thursday  | 20          |
| 2022-11-25       | 5 - Friday    | 100         |
| 2022-11-26       | 6 - Saturday  | 20          |
| 2022-11-27       | 7 - Sunday    | 40          |


![image](https://user-images.githubusercontent.com/121721444/211611863-53c2ee59-9d38-477d-91ba-fcbcc92e556a.png)

# See also
<ul id="recent-articles">
{% for page in site.pages %}
    {% if (page.title contains "DECODE") %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>


