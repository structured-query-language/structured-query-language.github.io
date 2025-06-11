# Retrieving first instance of change event using conditional_change_event

Conditional Change Event (`CONDITIONAL_CHANGE_EVENT`) is a versatile Snowflake function that returns a window event number for each row when the value of the expression is different from the value in the previous row. In other words, this function assigns an event window number to each row, starting from 0, and increments by 1 when the result of evaluating the argument expression on the current row differs from that on the previous row.

This can be a very powerful function to use when analyzing events data or time-series data. For e.g. if you need to retrieve the first `update_date` by Support Engineer per Case Status change in the following data:

|![Screen Shot 2024-12-04 at 9 09 14 PM](https://github.com/user-attachments/assets/bef4184b-5bbf-4a32-b95c-51666a2e4b09)|
|:--:|
|We need the first `status_date` by Support Engineer per Case Status change|

```sql
with support_cases as (
  select *
  , conditional_change_event (status || support_engineer)
      over (partition by case_number order by update_date asc) as change_rank_number
  from support_case
);
```

|![Screen Shot 2024-12-05 at 6 42 19 PM](https://github.com/user-attachments/assets/53f8ed05-4681-4154-a451-7cda5ee3c254)|
|:--:|
|`change_rank_number` increases each time the Support Engineer + Status combination changes|


```sql
with support_cases as (
  select *
  , conditional_change_event (status || support_engineer)
      over (partition by case_number order by update_date asc) as change_rank_number
  from support_case
)
select * from support_cases
qualify row_number() over (partition by case_number, change_rank_number order by update_date)  = 1;
```

|![Screen Shot 2024-12-05 at 7 23 50 PM](https://github.com/user-attachments/assets/f10f24c8-738d-4fa5-8aad-cfc99740397f)|
|:--:|
|first `status_date` by Support Engineer per Case Status change|


# See also
<ul id="recent-articles">
{% for page in site.pages %}
    {% if (page.title contains "conditional_true_event" or page.title contains "Conditional True Event" or page.title contains "Conditional Change Event") or page.title contains "conditional_change_event"%}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>

