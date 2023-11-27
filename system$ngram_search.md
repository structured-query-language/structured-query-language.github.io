# Searching for objects across Databases in Snowflake

Snowflake's `INFORMATION_SCHEMA` provides a metadata access to all the objects in a Database. You can look for TABLES, SCHEMAs etc. However the `INFORMATION_SCHEMA` is at the Database level i.e. you can only search at the Database level.

What if you need to find objects across all the databases in your Snowflake? Snowflake provides an easy way to achieve this using the `system$ngram_search` Function in Snowflake. 


## system$ngram_search Syntax
```sql
select system$ngram_search('{search string}', {number of objects to return});
```

## system$ngram_search Example
The following query will list all the databases, schemas and tables that have the string `customer` in the name:

```sql
select system$ngram_search('customer', 999);
```

### Query output
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
},
…

```

## Converting system$ngram_search output to a table
```sql
select 
  value:database::string
  , value:schema::string
  , value:domain::string
  , value:name::string 
from table (FLATTEN (input => parse_json(system$ngram_search('customer', 5))));
```

### Query Output

| VALUE:DATABASE::STRING | VALUE:SCHEMA::STRING | VALUE:DOMAIN::STRING | VALUE:NAME::STRING         |
|------------------------|----------------------|----------------------|----------------------------|
| SCRATCH                | SAQIB_ALI            | TABLE                | ORDER_CUSTOMER             |
| SCRATCH                | SAQIB_ALI            | TABLE                | SATE_CUSTOMER_SUBSCRIPTION |
| SCRATCH                | SAQIB_ALI            | TABLE                | CUSTOMERS                  |
| SCRATCH                | SAQIB_ALI            | TABLE                | CUSTOMER_ADDRESS           |
| SNOWFLAKE_SAMPLE_DATA  | TPCH_SF1             | TABLE                | CUSTOMER                   |
| SNOWFLAKE_SAMPLE_DATA  | TPCH_SF100           | TABLE                | CUSTOMER                   |
| SNOWFLAKE_SAMPLE_DATA  | TPCH_SF10            | TABLE                | CUSTOMER                   |
| SNOWFLAKE_SAMPLE_DATA  | TPCDS_SF100TCL       | TABLE                | CUSTOMER_ADDRESS           |
| SNOWFLAKE_SAMPLE_DATA  | TPCDS_SF100TCL       | TABLE                | CUSTOMER                   |
| SNOWFLAKE_SAMPLE_DATA  | TPCDS_SF100TCL       | TABLE                | CUSTOMER_DEMOGRAPHICS      |
| SNOWFLAKE_SAMPLE_DATA  | TPCH_SF1000          | TABLE                | CUSTOMER                   |
| SNOWFLAKE_SAMPLE_DATA  | TPCDS_SF10TCL        | TABLE                | CUSTOMER_DEMOGRAPHICS      |
| SNOWFLAKE_SAMPLE_DATA  | TPCDS_SF10TCL        | TABLE                | CUSTOMER                   |
| SNOWFLAKE_SAMPLE_DATA  | TPCDS_SF10TCL        | TABLE                | CUSTOMER_ADDRESS           |
