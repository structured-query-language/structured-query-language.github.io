# Retrieving first instance of change event using conditional_change_event

CONDITIONAL_CHANGE_EVENT is a versatile Snowflake function that returns a window event number for each row when the value of the expression is different from the value in the previous row.

This can be a very powerful function to use when analyzing events data or time-series data. For e.g. if you need to retrieve the first `update_date` by Support Engineer per Case Status change in the following data:

|![Screen Shot 2024-12-04 at 9 09 14 PM](https://github.com/user-attachments/assets/bef4184b-5bbf-4a32-b95c-51666a2e4b09)|
|:--:|
|We need to retrieve the first `status_date` by Support Engineer per Case Status change|

```
with support_cases as (
  select *
  , conditional_change_event (status || support_engineer) over (partition by case_number order by update_date asc) as dense_sequence
  from support_case
)
select * from support_cases
qualify row_number() over (partition by case_number, dense_sequence order by update_date)  = 1;
```


