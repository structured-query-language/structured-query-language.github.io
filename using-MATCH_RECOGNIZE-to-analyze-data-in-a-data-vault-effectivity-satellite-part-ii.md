# MATCH_RECOGNIZE and Data Vault Effectivity Satellite - Part II
[Effectivity Satellite](data-vault-effectivity-satellite.md) is a powerful Data Vault artifact that is used to track the effectivity of a relationship. For e.g. it can be used to track the relationship between:
1. Order and Customer, or 
2. Opportunity and Account, or 
3. Subscription Tier and Customer etc. 

> A Data Vault [Effectivity Satellite](data-vault-effectivity-satellite.md) hangs from the LINK, and only contains the **Driving Business Key**, **Secondary Foreign Key** and the **Start Date** and **End Date** for the given Relationship.


## Example of an Effectitity Satellite

| CUSTOMER_BK | SUBSCRIPTION_TIER_BK | START_DATE | END_DATE   |
|-------------|----------------------|------------|------------|
| Scott       | Personal             | 2021-01-01 | 9999-12-31 |
| Scott       | Personal             | 2021-01-01 | 2021-06-01 |
| Scott       | Free                 | 2021-06-01 | 9999-12-31 |
| Bob         | Business             | 2021-02-01 | 9999-12-31 |
| Angela      | Premium              | 2021-02-01 | 9999-12-31 |
| Angela      | Premium              | 2021-02-01 | 2021-03-01 |
| Angela      | Free                 | 2021-03-01 | 9999-12-31 |
| Angela      | Free                 | 2021-03-01 | 2021-05-15 |
| Angela      | Premium              | 2021-05-15 | 9999-12-31 |
| Ryan        | Free                 | 2021-03-01 | 9999-12-31 |

The above [Effectivity Satellite](data-vault-effectivity-satellite.md) is used to track the subscription tier a Customer has purchased. The `CUSTOMER_BK` is the Driving Key, and the `SUBSCRIPTION_TIER_BK` is the Secondary Foreign Key. The `START_DATE` and the `END_DATE` indicate when the Subscription was purchased and when it was ended. 

> **Note:** Since Data Vault 2.0 is an insert only pattern, we do not update the existing record to set the `END_DATE`, instead we insert a new record with the `END_DATE` set.

# Effectivity Satellite and Data Analysis
The structure of an [Effectivity Satellite](data-vault-effectivity-satellite.md) lends itself to Longitudinal Data Analysis. You can identify patterns, perform timeseries analysis or even use the data directly in statistical learning model. 

## MATCH_RECOGNIZE and pattern recognition

In relational systems, a row pattern recognition task is to detect a sequence of ordered rows from an input table that match a specified pattern. For example, a financial service provider needs to identify sequences of suspicious transactions that match known patterns of criminal activities; an e-commerce site analyzes the steps taken by customers from landing through a social media referrer to a successful purchase. The MATCH_RECOGNIZE clause in SQL allows users to search for patterns in rows of data using a powerful and expressive syntax that is based on RegEx.

One way to analyze data in an [Effectivity Satellite](data-vault-effectivity-satellite.md) is to use SQL's [MATCH_RECOGNIZE](applied-overview-of-MATCH_RECOGNIZE-clause.md) clause.

[MATCH_RECOGNIZE](applied-overview-of-MATCH_RECOGNIZE-clause.md) clause in a SQL Query allows us to:
1. Define patterns using REGEX convention
2. Match longitudinal data against those patterns
3. Identify records that match the patterns

In the MATCH_RECOGNIZE clause the pattern is constructed from basic building blocks, called pattern variables, to which operators (quantifiers and other modifiers) like Kleene Star `*` and Kleene Plus `+`  and  can be applied. The whole pattern must be enclosed in paranthesis. For example a pattern can be defined as following:
```
(paid+ free)
```


The following [MATCH_RECOGNIZE](applied-overview-of-MATCH_RECOGNIZE-clause.md) query can be used on the above [Effectivity Satellite](data-vault-effectivity-satellite.md) to identity Customer that switched from a PAID Wordpress tier to FREE tier. 

```sql
with effectivity_sat as(
select * 
from sate_customer_subscription where end_date = '9999-12-31'
) 
select * 
from effectivity_sat
match_recognize(
  partition by customer_bk
  order by start_date
  measures
  classifier() as action
  all rows per match  
  pattern (initial_subscription_tier modified_subscription_tier+ reveral_of_subscription_tier)
  define 
    initial_subscription as subscription_tier_bk = first_value(subscription_tier_bk)
    , change_in_subscription as subscription_tier_bk <> first_value(subscription_tier_bk)
    , reveral_of_subscription_tier as subscription_tier_bk = first_value(subscription_tier_bk)
);
```

There are three tiers of PAID plans-- Personal, Premium and Business. A Customer may switch between these tiers multiple times before finally downgrading to a FREE tier.


### Query Output

The query identified the following Customers started with a Subscription Tier, switched to a different Subscription Tier, and then eventually _switched back_ to the original Subscription Tier.

| CUSTOMER_BK | SUBSCRIPTION_TIER_BK | START_DATE | END_DATE   | ACTION                       |
|-------------|----------------------|------------|------------|------------------------------|
| Angela      | Premium              | 2021-02-01 | 9999-12-31 | INITIAL_SUBSCRIPTION_TIER    |
| Angela      | Free                 | 2021-03-01 | 9999-12-31 | MODIFIED_SUBSCRIPTION_TIER   |
| Angela      | Premium              | 2021-05-15 | 9999-12-31 | REVERAL_OF_SUBSCRIPTION_TIER |


# See also
1. [Data Vault Effectivity Satellite](data-vault-effectivity-satellite.md) 
1. [Applied overview of MATCH_RECOGNIZE clause](applied-overview-of-MATCH_RECOGNIZE-clause.md)
1. [MATCH_RECOGNIZE](match_recognize.md)
1. [Stock Analysis using MATCH_RECOGNIZE](stock-analysis.md)
1. [A/B Analysis on Streaming Data using MATCH_RECOGNIZE](A-B-analysis-on-streaming-data-using-MATCH_RECOGNIZE.md)
1. [Correlated pattern definitions and Snowflake's MATCH_RECOGNIZE](correlated-pattern-definitions-and-MATCH_RECOGNIZE.md)

> <a href="{{ site.github.repository_url }}/edit/{{ site.github.source.branch }}/{{ page.path }}">Edit this page on GitHub</a>

