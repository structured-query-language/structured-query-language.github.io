# Using TRANSFORM function in Snowflake to extract values from a json array

TRANSFORM function in Snowflake can be used for advanced array transformations. You can apply a function or lambda expression to each element in an array and return a new array with the transformed values. Here's the basic syntax:

```sql
TRANSFORM(input_array, expr)
```

```sql
SELECT TRANSFORM([1, 2, 3], x -> x * 2);
-- Result: [2, 4, 6]
```

## An Illustrative Example
Suppose we have a table that has Coupons as a JSON Array for each Sales Order as following

|order_id|coupon_json_array|
|-----|--------------------------------------------------------------------------|
| 111 | [{Coupon:"bbb", Discount: -1 }, {Coupon:"aaa", Type:FreeShip}]     |
| 222 | [{Coupon:"ccc", Discount: -2}]                                        |
| 333 | [{Coupon:"ccc", Discount: -2}, {Coupon:"aaa"}, {Coupon:"eee"} ] |
| 444 |                                                                          |


For each Order, we need to get the comma-separate list of Coupons, if there are any as following

order_id | coupons_applied
-- | --
222 | ccc
333 | ccc, aaa, eee
111 | bbb, aaa
444 |  
444 |  

### Using the TRANSFORM function with a Lamba expression

We can using the TRANSFORM function to apply a Lambda Fuction to each element in the JSON array to extract the Coupon as following:

```sql
SELECT
    order_id,
    TRANSFORM(
       parse_json(orders.coupon_json_array)::ARRAY,
       promo OBJECT -> promo:"Coupon"
    ) as coupons_applied
FROM orders;
```

| order_id | coupons_applied               |
|----------|-------------------------------|
| 111      | [   "bbb",   "aaa" ]          |
| 222      | [   "ccc" ]                   |
| 333      | [   "ccc",   "aaa",   "eee" ] |
| 444      |                               |

### Using the Lateral Flatten

The same can be achieved using the Lateral Flatten in Snowflake:

```sql

select order_id, listagg (f.value:Coupon, ', ')
from orders
, lateral flatten(input => parse_json(orders.coupon_json_array)::variant, OUTER => TRUE) as f
group by all
```

How this will be a lot slower compare to using the TRANSFORM function.
