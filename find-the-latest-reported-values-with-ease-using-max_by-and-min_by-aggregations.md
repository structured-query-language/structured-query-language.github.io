# Find the latest reported values with ease using MAX_BY and MIN_BY aggregations

`MAX_BY` and `MIN_BY` aggregation functions allow users to quickly and easily search the value of one column based on the minimum or maximum value of another column, making them useful for analyzing trends, identifying outliers, or simply understanding the range of values within a dataset. An example use case is getting the latest measurement by using the time column and max_by(measurement, time).

## Basic Example

`MIN_BY` and `MAX_BY` functions allow you to find the minimum or maximum value in a given column based on the values in another column. For example, if you have a table with three columns, order_number, sales associate and order_amount, you can use the min_by(order_number, order_amount) to find the row with the order with the lowest price for each sales associate:

###  Input Table

| ORDER_NUMBER | SALES_ASSOCIATE | ORDER_AMOUNT |
|--------------|-----------------|--------------|
| 1            | Chelsea         | 10           |
| 2            | Chelsea         | 12           |
| 3            | Chelsea         | 5            |
| 4            | Chelsea         | 20           |
| 5            | Chelsea         | 30           |
| 6            | Anna            | 3            |
| 7            | Anna            | 2            |
| 8            | Anna            | 1            |
| 9            | Anna            | 199          |
| 10           | Anna            | 50           |
| 11           | Anna            | 20           |


### SQL Query

```sql
select 
  sales_associate
  , min_by(order_number, order_amount)
from orders
group by all;
```

| SALES_ASSOCIATE | ORDER_WITH_MINIMUM_AMOUNT      |
|-----------------|--------------------------------|
|Chelsea|3|
|Anna|8|


### Query explaination

`max_by(returned_value, maximized_value)` and `min_by(returned_value, minimized_value)` return the value of the first column for which the value of the second column is maximized or minimized. If multiple rows maximize or minimize the result of the second column, the output will be non-deterministic.

## Advaced example

Now let's say you need to get the top 5 sales for each Sales Associate, you can use the following query:

### Query using MAX_BY()

```sql
select 
  sales_associate
  , max_by(order_amount, order_amount, 5) as top_five_sales_in_dollars
from orders
group by 1;
```

### Query output

| SALES_ASSOCIATE | TOP_FIVE_SALES_IN_DOLLARS       |
|-----------------|---------------------------------|
| Anna            | [   199,   50,   20,   3,   2 ] |
| Chelsea         | [   30,   20,   12,   10,   5 ] |


### Query without MAX_BY()

```sql
with partitioned_data as(
  select 
    sales_associate
    , order_amount
  from orders
  qualify row_number() over (partition by sales_associate 
    order by order_amount desc ) < 5
)
select 
  sales_associate
  , array_agg(order_amount) 
from partitioned_data
group by 1
```

## Screenshot(s)
![image](https://user-images.githubusercontent.com/121721444/218340894-7be2cbdd-6e13-4f54-ba3b-0eb653c15d65.png)


# See also
<ul id="recent-articles">
{% for page in site.pages %}
    {% if (page.title contains "MAX_BY"  or page.title contains "MIN_BY") %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>
