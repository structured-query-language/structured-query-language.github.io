# Effectivity Satellite

In Data Vault 2.0 Effectivity Satellites are artifacts that are exclusively used to Track the temporal relevance of a relationship based on a Driving Key. As such, they hang from a Link Table.

 
So what is an Effectivity Satellite?

For an Effectivity Satellite, a Driving Key needs to be defined. For e.g. let's say we have a Link for Opportunities and relations to Accounts. The Account on Opportunity can change overtime. A common scenario is that the Opportunity is Assigned to a Global Parent account (e.g. Seagate Technology), and may get re-assigned to the Account Subsidiary (e.g. Lyve Labs) and get re-assigned to the Global Parent (Seagate Technology) at a later date

This will be tracked in the LINK as following:

## LINK_OPPORTUNITY_ACCOUNT

| LINK_HK  |OPPORTUNITY_HK| OPPORTUNITY| ACCOUNT_HK | ACCOUNT            | LOAD_DATE           |
|----------|--------------|------------|------------|--------------------|---------------------|
| 8913b4fa | c89470       | 111        | e184de02   | Lyve Labs          | 2023-02-21 14:43:05 |
| 8cee5f97 | c89470       | 111        | 92b3503    | Seagate Technology | 2023-02-21 14:53:57 |

Now the 3rd step, i.e. 111 switching back to Seagate Technology will not be captured in this LINK as a new row in this LINK

An Effectivity SAT can be used to Track effectivity (temporal relevance) of the aforementioned Opportunity to Account relationships.


## SATE_OPPORTUNITY_ACCOUNT

| LINK_HK |OPPORTUNITY_HK|OPPORTUNITY|ACCOUNT_HK|ACCOUNT           |START_DATE         |END_DATE           |LOAD_DATE          |
|---------|--------------|-----------|----------|------------------|-------------------|-------------------|-------------------|
| 8913b4fa|c89470        | 111       | e184de02 |Lyve Labs         |2023-02-21 14:43:05|9999-12-31 0:00:00 |2023-02-21 14:43:05|
| 8cee5f97|c89470        | 111       | 92b3503  |Seagate Technology|2023-02-21 14:53:57|9999-12-31 0:00:00 |2023-02-21 14:53:57|
| 8913b4fa|c89470        | 111       | e184de02 |Lyve Labs         |2023-02-21 14:43:05|2023-02-21 14:53:57|2023-02-21 14:53:57|
| 8cee5f97|c89470        | 111       | 92b3503  |Seagate Technology|2023-02-21 14:53:57|2023-02-21 15:00:31|2023-02-21 15:00:31|
| 8913b4fa|c89470        | 111       | e184de02 |Lyve Labs         |2023-02-21 15:38:30|9999-12-31 0:00:00 |2023-02-21 15:38:30|
| 8cee5f97|c89470        | 111       | 92b3503  |Seagate Technology|2023-02-21 15:44:42|9999-12-31 0:00:00 |2023-02-21 15:44:42|
| 8913b4fa|c89470        | 111       | e184de02 |Lyve Labs         |2023-02-21 15:38:30|2023-02-21 15:44:42|2023-02-21 15:44:42|

1. When the relationship is first recorded only 1 record is inserted (row 1 in the above table)
2. When there is a change in a relationship it must be based on the driving key (OPPORTUNITY), we track its change vs the Secondary Foreign Key in the Relationship (ACCOUNT). To do that we end_date one record and insert a new "active record"; i.e. 2 records inserted. (row 2 and 3 in the above table)

# See Also
1. [Using MATCH_RECOGNIZE to analyze data in a Data Vault Effectivity Satellite](using-MATCH_RECOGNIZE-to-analyze-data-in-a-data-vault-effectivity-satellite.md)
