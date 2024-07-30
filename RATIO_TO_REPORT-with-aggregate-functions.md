# RATIO_TO_REPORT with Aggregate Functions

`RATIO_TO_REPORT` is a powerful Snowflake function that lets you calculate the ratio of a value to the various aggregations of data. The following query, for example, uses `RATIO_TO_REPORT` to calculate the Number of Sales, Amount of Sales and Average Sales as a Percentage for each Sales Associate

##### Input Data

| TRANSACTION_ID | AMOUNT | ASSOCIATE|
|----------------|--------|----------|
| 1              | 100    | Scott    |
| 2              | 200    | Scott    |
| 3              | 20     | Scott    |
| 4              | 200    | Angela   |
| 5              | 200    | Angela   |
| 6              | 500    | Bob      |

#### RATIO_TO_REPORT query

```sql
select 
 sales_associate
 , 100 * ratio_to_report(count(*)) over() as "NUMBER_OF_SALES_%"
 , 100 * ratio_to_report(sum(amount)) over() as "AMOUNT_OF_SALES_%"
 , 100 * ratio_to_report(avg(amount)) over() as "MEAN_OF_SALES_%"
from sales
group by sales_associate;
```

### Output

| ASSOCIATE | NUMBER_OF_SALES_% | AMOUNT_OF_SALES_% |
|-----------|-------------------|-------------------|
| Scott     | 50                | 26.2295           |
| Angela    | 33.3333           | 32.7869           |
| Bob       | 16.6667           | 40.9836           |

