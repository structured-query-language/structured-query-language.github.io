# How to Predict Customer Churn Using SQL Pattern Detection - MATCH_RECOGNIZE

I recently had a opportunity to work with usage data for a SaaS based Cloud IDE. This Cloud IDE is used by major companies globally. Our goal of the analysis was to figure out if we can predict Customer Churn using the Usage data for the last few months. The nature of the IDE is that the usage at a Customer fluctuates drastically based on the project requirements. However we wanted to identify the Customer with Downtrend in Usage. A sustained Downtrend in Usage _could_ indicate the Customer phasing out the Cloud IDEA and/or switching to a different IDE. 

The criteria for predicting a Churn are:
1. Downtrend in Usage for 3 or more months.
2. No Uptrend followed by an Downtrend.

Based on this criteria we were able to craft a [MATCH_RECOGNIZE](applied-overview-of-MATCH_RECOGNIZE-clause.md) query in Snowflake to get the list of accounts that exhibit this behaviour.

SQL’s MATCH_RECOGNIZE Clause SQL is a great way to perform analysis on your data. SQL is used in many cases to analyze data in data lake, however, when it comes to pattern detection, SQL traditionally hasn’t been used. Introduction of [MATCH_RECOGNIZE](applied-overview-of-MATCH_RECOGNIZE-clause.md) changed that. [MATCH_RECOGNIZE](applied-overview-of-MATCH_RECOGNIZE-clause.md) clause can perform Pattern Matching over a set of rows.

## MATCH_RECOGNIZE SQL query

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

## Query Output

| ACCOUNT | AS_OF_DATE | NUMBER_OF_ACTIVE_USERS |
|---------|------------|------------------------|
| A       | 2023-10-01 | 4                      |
| A       | 2023-11-01 | 3                      |
| A       | 2023-12-01 | 1                      |
| R       | 2023-09-01 | 6                      |
| R       | 2023-10-01 | 5                      |
| R       | 2023-11-01 | 4                      |
| R       | 2023-12-01 | 3                      |


## Appendix

### Raw Data


| ACCOUNT | AS_OF_DATE | NUMBER_OF_ACTIVE_USERS |
|---------|------------|------------------------|
| A       | 2023-01-01 | 4                      |
| A       | 2023-02-01 | 2                      |
| A       | 2023-03-01 | 5                      |
| A       | 2023-04-01 | 10                     |
| A       | 2023-05-01 | 1                      |
| A       | 2023-06-01 | 7                      |
| A       | 2023-07-01 | 3                      |
| A       | 2023-08-01 | 2                      |
| A       | 2023-09-01 | 6                      |
| A       | 2023-10-01 | 4                      |
| A       | 2023-11-01 | 3                      |
| A       | 2023-12-01 | 1                      |
| B       | 2023-01-01 | 2                      |
| B       | 2023-02-01 | 2                      |
| B       | 2023-03-01 | 2                      |
| B       | 2023-04-01 | 1                      |
| B       | 2023-05-01 | 6                      |
| B       | 2023-06-01 | 3                      |
| B       | 2023-07-01 | 1                      |
| B       | 2023-08-01 | 6                      |
| B       | 2023-09-01 | 10                     |
| B       | 2023-10-01 | 9                      |
| B       | 2023-11-01 | 10                     |
| B       | 2023-12-01 | 10                     |
| C       | 2023-01-01 | 4                      |
| C       | 2023-02-01 | 10                     |
| C       | 2023-03-01 | 10                     |
| C       | 2023-04-01 | 3                      |
| C       | 2023-05-01 | 2                      |
| C       | 2023-06-01 | 4                      |
| C       | 2023-07-01 | 6                      |
| C       | 2023-08-01 | 6                      |
| C       | 2023-09-01 | 1                      |
| C       | 2023-10-01 | 1                      |
| C       | 2023-11-01 | 10                     |
| C       | 2023-12-01 | 10                     |
| D       | 2023-01-01 | 6                      |
| D       | 2023-02-01 | 8                      |
| D       | 2023-03-01 | 1                      |
| D       | 2023-04-01 | 1                      |
| D       | 2023-05-01 | 10                     |
| D       | 2023-06-01 | 1                      |
| D       | 2023-07-01 | 4                      |
| D       | 2023-08-01 | 6                      |
| D       | 2023-09-01 | 1                      |
| D       | 2023-10-01 | 4                      |
| D       | 2023-11-01 | 8                      |
| D       | 2023-12-01 | 8                      |
| E       | 2023-01-01 | 9                      |
| E       | 2023-02-01 | 9                      |
| E       | 2023-03-01 | 6                      |
| E       | 2023-04-01 | 10                     |
| E       | 2023-05-01 | 4                      |
| E       | 2023-06-01 | 9                      |
| E       | 2023-07-01 | 2                      |
| E       | 2023-08-01 | 4                      |
| E       | 2023-09-01 | 5                      |
| E       | 2023-10-01 | 8                      |
| E       | 2023-11-01 | 3                      |
| E       | 2023-12-01 | 6                      |
| F       | 2023-01-01 | 7                      |
| F       | 2023-02-01 | 7                      |
| F       | 2023-03-01 | 3                      |
| F       | 2023-04-01 | 3                      |
| F       | 2023-05-01 | 9                      |
| F       | 2023-06-01 | 1                      |
| F       | 2023-07-01 | 7                      |
| F       | 2023-08-01 | 1                      |
| F       | 2023-09-01 | 9                      |
| F       | 2023-10-01 | 3                      |
| F       | 2023-11-01 | 6                      |
| F       | 2023-12-01 | 9                      |
| G       | 2023-01-01 | 5                      |
| G       | 2023-02-01 | 3                      |
| G       | 2023-03-01 | 1                      |
| G       | 2023-04-01 | 9                      |
| G       | 2023-05-01 | 8                      |
| G       | 2023-06-01 | 10                     |
| G       | 2023-07-01 | 2                      |
| G       | 2023-08-01 | 4                      |
| G       | 2023-09-01 | 10                     |
| G       | 2023-10-01 | 4                      |
| G       | 2023-11-01 | 1                      |
| G       | 2023-12-01 | 4                      |
| H       | 2023-01-01 | 4                      |
| H       | 2023-02-01 | 1                      |
| H       | 2023-03-01 | 10                     |
| H       | 2023-04-01 | 1                      |
| H       | 2023-05-01 | 2                      |
| H       | 2023-06-01 | 6                      |
| H       | 2023-07-01 | 10                     |
| H       | 2023-08-01 | 7                      |
| H       | 2023-09-01 | 10                     |
| H       | 2023-10-01 | 7                      |
| H       | 2023-11-01 | 7                      |
| H       | 2023-12-01 | 1                      |
| I       | 2023-01-01 | 5                      |
| I       | 2023-02-01 | 4                      |
| I       | 2023-03-01 | 4                      |
| I       | 2023-04-01 | 3                      |
| I       | 2023-05-01 | 7                      |
| I       | 2023-06-01 | 3                      |
| I       | 2023-07-01 | 1                      |
| I       | 2023-08-01 | 7                      |
| I       | 2023-09-01 | 2                      |
| I       | 2023-10-01 | 10                     |
| I       | 2023-11-01 | 5                      |
| I       | 2023-12-01 | 2                      |
| J       | 2023-01-01 | 7                      |
| J       | 2023-02-01 | 2                      |
| J       | 2023-03-01 | 10                     |
| J       | 2023-04-01 | 1                      |
| J       | 2023-05-01 | 8                      |
| J       | 2023-06-01 | 4                      |
| J       | 2023-07-01 | 10                     |
| J       | 2023-08-01 | 7                      |
| J       | 2023-09-01 | 1                      |
| J       | 2023-10-01 | 9                      |
| J       | 2023-11-01 | 7                      |
| J       | 2023-12-01 | 9                      |
| K       | 2023-01-01 | 8                      |
| K       | 2023-02-01 | 1                      |
| K       | 2023-03-01 | 7                      |
| K       | 2023-04-01 | 8                      |
| K       | 2023-05-01 | 5                      |
| K       | 2023-06-01 | 10                     |
| K       | 2023-07-01 | 3                      |
| K       | 2023-08-01 | 6                      |
| K       | 2023-09-01 | 2                      |
| K       | 2023-10-01 | 7                      |
| K       | 2023-11-01 | 9                      |
| K       | 2023-12-01 | 2                      |
| L       | 2023-01-01 | 1                      |
| L       | 2023-02-01 | 6                      |
| L       | 2023-03-01 | 7                      |
| L       | 2023-04-01 | 9                      |
| L       | 2023-05-01 | 5                      |
| L       | 2023-06-01 | 3                      |
| L       | 2023-07-01 | 2                      |
| L       | 2023-08-01 | 4                      |
| L       | 2023-09-01 | 3                      |
| L       | 2023-10-01 | 5                      |
| L       | 2023-11-01 | 2                      |
| L       | 2023-12-01 | 1                      |
| M       | 2023-01-01 | 1                      |
| M       | 2023-02-01 | 7                      |
| M       | 2023-03-01 | 2                      |
| M       | 2023-04-01 | 5                      |
| M       | 2023-05-01 | 3                      |
| M       | 2023-06-01 | 4                      |
| M       | 2023-07-01 | 6                      |
| M       | 2023-08-01 | 7                      |
| M       | 2023-09-01 | 4                      |
| M       | 2023-10-01 | 2                      |
| M       | 2023-11-01 | 5                      |
| M       | 2023-12-01 | 4                      |
| N       | 2023-01-01 | 6                      |
| N       | 2023-02-01 | 2                      |
| N       | 2023-03-01 | 6                      |
| N       | 2023-04-01 | 7                      |
| N       | 2023-05-01 | 8                      |
| N       | 2023-06-01 | 5                      |
| N       | 2023-07-01 | 10                     |
| N       | 2023-08-01 | 5                      |
| N       | 2023-09-01 | 2                      |
| N       | 2023-10-01 | 2                      |
| N       | 2023-11-01 | 9                      |
| N       | 2023-12-01 | 8                      |
| O       | 2023-01-01 | 1                      |
| O       | 2023-02-01 | 5                      |
| O       | 2023-03-01 | 7                      |
| O       | 2023-04-01 | 5                      |
| O       | 2023-05-01 | 4                      |
| O       | 2023-06-01 | 8                      |
| O       | 2023-07-01 | 4                      |
| O       | 2023-08-01 | 5                      |
| O       | 2023-09-01 | 7                      |
| O       | 2023-10-01 | 10                     |
| O       | 2023-11-01 | 4                      |
| O       | 2023-12-01 | 4                      |
| P       | 2023-01-01 | 6                      |
| P       | 2023-02-01 | 10                     |
| P       | 2023-03-01 | 2                      |
| P       | 2023-04-01 | 10                     |
| P       | 2023-05-01 | 10                     |
| P       | 2023-06-01 | 1                      |
| P       | 2023-07-01 | 7                      |
| P       | 2023-08-01 | 2                      |
| P       | 2023-09-01 | 1                      |
| P       | 2023-10-01 | 10                     |
| P       | 2023-11-01 | 6                      |
| P       | 2023-12-01 | 10                     |
| Q       | 2023-01-01 | 5                      |
| Q       | 2023-02-01 | 6                      |
| Q       | 2023-03-01 | 7                      |
| Q       | 2023-04-01 | 7                      |
| Q       | 2023-05-01 | 6                      |
| Q       | 2023-06-01 | 9                      |
| Q       | 2023-07-01 | 5                      |
| Q       | 2023-08-01 | 2                      |
| Q       | 2023-09-01 | 5                      |
| Q       | 2023-10-01 | 1                      |
| Q       | 2023-11-01 | 3                      |
| Q       | 2023-12-01 | 7                      |
| R       | 2023-01-01 | 5                      |
| R       | 2023-02-01 | 4                      |
| R       | 2023-03-01 | 3                      |
| R       | 2023-04-01 | 3                      |
| R       | 2023-05-01 | 9                      |
| R       | 2023-06-01 | 10                     |
| R       | 2023-07-01 | 6                      |
| R       | 2023-08-01 | 8                      |
| R       | 2023-09-01 | 6                      |
| R       | 2023-10-01 | 5                      |
| R       | 2023-11-01 | 4                      |
| R       | 2023-12-01 | 3                      |
