# Searching for objects across Databases in Snowflake

Snowflake's `INFORMATION_SCHEMA` provides a metadata access to all the objects in a Database. You can look for TABLES, SCHEMAs etc. However the `INFORMATION_SCHEMA` is at the Database level i.e. you can only search at the Database level.

What if you need to find objects across all the databases in your Snowflake? Snowflake provides an easy way to achieve this using the `system$ngram_search` Function in Snowflake. 


## system$ngram_search Syntax
```sql
select system$ngram_search('{search string}', {number of objects to return});
```

## system$ngram_search Example
The above query will list all the databases, schemas and tables that have the string `customer` in it:

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


