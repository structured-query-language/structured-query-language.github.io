# RATIO_TO_REPORT
`RATIO_TO_REPORT` is a powerful #snowflakedb Window function that lets you calculate the ratio of a value to the sum of the values in a window. The following query uses the `RATIO_TO_REPORT` function to calculate the percentage of the sales by channel (online vs. store) for each day.

```
create or replace table scratch.saqib_ali.TRANSACTIONS (order_id number, amount number, transaction_date date, channel varchar(20));
insert into scratch.saqib_ali.TRANSACTIONS values (1, 10, '2022-12-01', 'store');
insert into scratch.saqib_ali.TRANSACTIONS values (2, 20, '2022-12-01', 'online');
insert into scratch.saqib_ali.TRANSACTIONS values (3, 20, '2022-12-01', 'store');
insert into scratch.saqib_ali.TRANSACTIONS values (4, 20, '2022-12-02', 'store');
insert into scratch.saqib_ali.TRANSACTIONS values (5, 30, '2022-12-02', 'store');
insert into scratch.saqib_ali.TRANSACTIONS values (6, 30, '2022-12-02', ' store');
insert into scratch.saqib_ali.TRANSACTIONS values (7, 20, '2022-12-02', 'store');
```

```
select * from scratch.saqib_ali.TRANSACTIONS;
```


# SQL Query using the RATIO_TO_REPORT
```
select
  transaction_date
  , channel
  , sum(amount) as total_sales
  , ratio_to_report(total_sales) over (partition by transaction_date) 
    as channel_percentage
from scratch.saqib_ali.TRANSACTIONS 
group by transaction_date, channel;
```

# SQL Query without using the RATIO_TO_REPORT for comparision
```
select
  transaction_date
  , channel
  , sum(amount) as total_by_channel
  , sum(total_by_channel) over (partition by transaction_date) as total_online_and_store
  , ((total_by_channel / total_online_and_store) * 100)::NUMBER || '%' as channel_percentage
from scratch.saqib_ali.TRANSACTIONS 
group by transaction_date, channel;
```
