# Searching for objects across Databases in Snowflake

Snowflake's `INFORMATION_SCHEMA` provides a metadata access to all the objects in a Database. You can look for TABLES, SCHEMAs etc. However the `INFORMATION_SCHEMA` is at the Database level i.e. you have to query the `INFORMATION_SCHEMA` of a `DATABASE`.

What if you need to find objects like tables and you want to look across all the databases? Snowflake provides an easy way to achieve this using the `system$ngram_search` Function in Snowflake. The syntax is simple:

```sql
select system$ngram_search('{search string}', {number of objects to return});
```

Example:
```sql
select system$ngram_search('customer', 999);
```
The above query will list all the databases, schemas and tables that have the string `customer` in it as following:

```json
[ {
  "database" : "SCRATCH",
  "schema" : "SAQIB_ALI",
  "domain" : "TABLE",
  "name" : "ORDER_CUSTOMER"
}, {
  "database" : "SCRATCH",
  "schema" : "SAQIB_ALI",
  "domain" : "TABLE",
  "name" : "SATE_CUSTOMER_SUBSCRIPTION"
}, {
  "database" : "SCRATCH",
  "schema" : "SAQIB_ALI",
  "domain" : "TABLE",
  "name" : "CUSTOMERS"
}, {
  "database" : "SCRATCH",
  "schema" : "SAQIB_ALI",
  "domain" : "TABLE",
  "name" : "CUSTOMER_ADDRESS"
}, {
  "database" : "SNOWFLAKE_SAMPLE_DATA",
  "schema" : "TPCH_SF1",
  "domain" : "TABLE",
  "name" : "CUSTOMER"
}, {
  "database" : "SNOWFLAKE_SAMPLE_DATA",
  "schema" : "TPCH_SF100",
  "domain" : "TABLE",
  "name" : "CUSTOMER"
}, {
  "database" : "SNOWFLAKE_SAMPLE_DATA",
  "schema" : "TPCH_SF10",
  "domain" : "TABLE",
  "name" : "CUSTOMER"
}, {
  "database" : "SNOWFLAKE_SAMPLE_DATA",
  "schema" : "TPCDS_SF100TCL",
  "domain" : "TABLE",
  "name" : "CUSTOMER_ADDRESS"
}, {
  "database" : "SNOWFLAKE_SAMPLE_DATA",
  "schema" : "TPCDS_SF100TCL",
  "domain" : "TABLE",
  "name" : "CUSTOMER"
}, {
  "database" : "SNOWFLAKE_SAMPLE_DATA",
  "schema" : "TPCDS_SF100TCL",
  "domain" : "TABLE",
  "name" : "CUSTOMER_DEMOGRAPHICS"
}, {
  "database" : "SNOWFLAKE_SAMPLE_DATA",
  "schema" : "TPCH_SF1000",
  "domain" : "TABLE",
  "name" : "CUSTOMER"
}, {
  "database" : "SNOWFLAKE_SAMPLE_DATA",
  "schema" : "TPCDS_SF10TCL",
  "domain" : "TABLE",
  "name" : "CUSTOMER_DEMOGRAPHICS"
}, {
  "database" : "SNOWFLAKE_SAMPLE_DATA",
  "schema" : "TPCDS_SF10TCL",
  "domain" : "TABLE",
  "name" : "CUSTOMER"
}, {
  "database" : "SNOWFLAKE_SAMPLE_DATA",
  "schema" : "TPCDS_SF10TCL",
  "domain" : "TABLE",
  "name" : "CUSTOMER_ADDRESS"
} ]
```

