with `_fivetran_deleted` include in the `hashdiff`, the deleted record gets loaded in the Satellite, which tells us that the Order has been removed from the source

| order_number | item_id | _fivetran_deleted | load_date | hashdiff |
|--------------|---------|-------------------|-----------|----------|
| 111          | aaa     | FALSE             | 01-01-1999| abc      |
| 111          | aaa     | TRUE              | 01-02-1999| xyz      |


with `_fivetran_deleted` not include in the hashdiff the deleted record never gets loaded in the Satellite. We will not have a way to tell if the order was deleted or not.

| order_number | item_id | _fivetran_deleted | load_date | hashdiff |
|--------------|---------|-------------------|-----------|----------|
| order_number | item_id | _fivetran_deleted | load_date | hashdiff |
| 111          | aaa     | FALSE             | 01-01-1999| abc      |
