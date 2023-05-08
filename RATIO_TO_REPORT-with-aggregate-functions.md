# RATIO_TO_REPORT
RATIO_TO_REPORT is a powerful Snowflake function that lets you calculate the ratio of a value to the various aggregations of data. The following query, for example, uses RATIO_TO_REPORT to calculate the Number of Sales, Amount of Sales and Average Sales as a percentage for each Sales Associate


```sql
select 
 sales_associate
 , 100 * ratio_to_report(count(*)) over() as "NUMBER_OF_SALES_%"
 , 100 *ratio_to_report(sum(amount)) over() as "AMOUNT_OF_SALES_%"
 , 100 * ratio_to_report(avg(amount)) over() as "MEAN_OF_SALES_%"
from sales
group by sales_associate;
```
