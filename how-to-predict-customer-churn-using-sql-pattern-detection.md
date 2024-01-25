# How to Predict Customer Churn Using SQL Pattern Detection


```sql
select *
from active_users
match_recognize(
partition by account order by as_of_date asc
all rows per match
pattern (DOWNTREND{3,}$)
DEFINE 
  DOWNTREND AS number_of_active_users < lag(number_of_active_users)
);
```
