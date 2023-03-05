# MATCH_RECOGNIZE and Data Vault Effectivity Satellite
Effectivity Satellite is a powerful Data Vault artifact that is used to track the effectivity of a relationship. For e.g. it can be used to track the relationship between:
1. Order and Customer, or 
2. Opportunity and Account, or 
3. Subscription and Customer etc. 
 
A Data Vault Effectivity Satellite hangs from the LINK, and only contains the Driving Business Key, Secondary Foreign Key and the Start Date and the End Date for a the Relationship.


## Example of an Effectitity Satellite

| CUSTOMER_BK | SUBSCRIPTION_BK | START_DATE | END_DATE   |
|-------------|-----------------|------------|------------|
| Scott       | Personal        | 2021-01-01 | 9999-12-31 |
| Scott       | Personal        | 2021-01-01 | 2021-06-01 |
| Scott       | Free            | 2021-06-01 | 9999-12-31 |
| Bob         | Business        | 2021-02-01 | 9999-12-31 |
| Angela      | Premium         | 2021-02-01 | 9999-12-31 |
| Angela      | Premium         | 2021-02-01 | 2021-03-01 |
| Angela      | Free            | 2021-03-01 | 9999-12-31 |
| Ryan        | Free            | 2021-03-01 | 9999-12-31 |

The above Effectivity Satellite is used to track the type of subscription a Customer has purchased. The `CUSTOMER_BK` is the Driving Key, and the `SUBSCRIPTION_BK` is the Secondary Foreign Key. The `START_DATE` and the `END_DATE` indicate when the Subscription was purchased and when it was ended. 

> **Note:** Since Data Vault 2.0 is an insert only pattern, we do not update the existing record to set the `END_DATE`, instead we insert a new record with the `END_DATE` set.

