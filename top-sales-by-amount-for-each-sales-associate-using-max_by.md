# MAX_BY() aggregate function in Snowflake
Snowflake recently added support for the `max_by()` aggregate function. This function can used to get the the top metrics by dimension. For e.g. if you need to get the top 5 sales for each Sales Associate, you can use the following query:

## Query using MAX_BY()

```sql
select 
  sales_associate
  , max_by(order_amount, order_amount, 5) as top_five_sales_in_dollars
from orders
group by 1;
```

## Query output

| SALES_ASSOCIATE | TOP_FIVE_SALES_IN_DOLLARS       |
|-----------------|---------------------------------|
| Anna            | [   199,   50,   20,   3,   2 ] |
| Chelsea         | [   30,   20,   12,   10,   5 ] |

## Query without MAX_BY()

```
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


## Raw Data

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
