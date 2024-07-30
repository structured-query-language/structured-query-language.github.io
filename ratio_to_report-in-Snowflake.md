# RATIO_TO_REPORT in Snowflake

`RATIO_TO_REPORT` is a powerful #snowflakedb Window function that lets you calculate the ratio of a value to the sum of the values in a window. The following query uses the `RATIO_TO_REPORT` function to calculate the percentage of the sales by channel (online vs. store) for each day.

### Raw Order Data

```sql
create or replace table scratch.saqib_ali.TRANSACTIONS (order_id number, amount number, transaction_date date, channel varchar(20));
insert into scratch.saqib_ali.TRANSACTIONS values (1, 10, '2022-12-01', 'store');
insert into scratch.saqib_ali.TRANSACTIONS values (2, 20, '2022-12-01', 'online');
insert into scratch.saqib_ali.TRANSACTIONS values (3, 20, '2022-12-01', 'store');
insert into scratch.saqib_ali.TRANSACTIONS values (4, 20, '2022-12-02', 'store');
insert into scratch.saqib_ali.TRANSACTIONS values (5, 30, '2022-12-02', 'store');
insert into scratch.saqib_ali.TRANSACTIONS values (6, 30, '2022-12-02', ' store');
insert into scratch.saqib_ali.TRANSACTIONS values (7, 20, '2022-12-02', 'store');
```

```sql
select * from scratch.saqib_ali.TRANSACTIONS;
```

| ORDER_ID | AMOUNT | TRANSACTION_DATE | CHANNEL |
|----------|--------|------------------|---------|
| 1        | 10     | 2022-12-01       | store   |
| 2        | 20     | 2022-12-01       | online  |
| 3        | 20     | 2022-12-01       | store   |
| 4        | 20     | 2022-12-02       | store   |
| 5        | 30     | 2022-12-02       | store   |
| 6        | 30     | 2022-12-02       | store   |
| 7        | 20     | 2022-12-02       | store   |


## SQL Query using the RATIO_TO_REPORT
```sql
select
  transaction_date
  , channel
  , sum(amount) as total_sales
  , ratio_to_report(total_sales) over (partition by transaction_date) 
    as channel_percentage
from scratch.saqib_ali.TRANSACTIONS 
group by transaction_date, channel;
```
### Query output

| TRANSACTION_DATE | CHANNEL | TOTAL_SALES | CHANNEL_PERCENTAGE |
|------------------|---------|-------------|--------------------|
| 2022-12-01       | store   | 30          | 0.6                |
| 2022-12-01       | online  | 20          | 0.4                |
| 2022-12-02       | store   | 100         | 1                  |



## SQL Query without using the RATIO_TO_REPORT for comparison
```sql
select
  transaction_date
  , channel
  , sum(amount) as total_by_channel
  , sum(total_by_channel) over (partition by transaction_date) as total_online_and_store
  , ((total_by_channel / total_online_and_store) * 100)::NUMBER || '%' as channel_percentage
from scratch.saqib_ali.TRANSACTIONS 
group by transaction_date, channel;
```

| TRANSACTION_DATE | CHANNEL | TOTAL_BY_CHANNEL | TOTAL_ONLINE_AND_STORE | CHANNEL_PERCENTAGE |
|------------------|---------|------------------|------------------------|--------------------|
| 2022-12-01       | store   | 30               | 50                     | 60%                |
| 2022-12-01       | online  | 20               | 50                     | 40%                |
| 2022-12-02       | store   | 100              | 100                    | 100%               |



# See also:
<ul id="recent-articles">
{% for page in site.pages %}
    {% if page.title contains "RATIO_TO_REPORT" %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>

