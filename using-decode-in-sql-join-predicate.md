# DECODE in SQL JOIN predicate
[DECODE](decode.md) is powerful Snowflake function that can be used to implement `if..then.elseif..then..endif`. It can be used in a SQL `JOIN` predicate as well as following

```sql
select 
  contact_email
  , product_id
  , name
from sat_lead_salesforce
inner join sat_opportunity_salesforce 
  on decode(primary_email_num, 1, email_1, 2, email_2, 3, email_3) 
    = sat_opportunity_salesforce.contact_email;
```
# Related
[DECODE](decode.md)
