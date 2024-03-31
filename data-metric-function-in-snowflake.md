# Data Metric Function in Snowflake
Data Metric Functions (DMF) allow you to automate data quality validation and monitoring. DMFs allow you to:

1. Create custom quality metric rules as a reusable functions
2. Apply rules to one or more columns
3. Specific how frequently you want the quality metrics to be calculated
4. Execute metric functions ad-hoc (for testing) or incorporate into your pipelines
5. Metrics are executed by the serverless backend, no need to keep your warehouse running
6. View and manage all of your quality metrics in a single place

Snowflake has the following DMF pre-defined:

1. `FRESHNESS`
2. `NULL_COUNT`
3. `DUPLICATE_COUNT`
4. `UNIQUE_COUNT`
5. `ROW_COUNT`

## Attaching a pre-defined Data Metric Function to a Table

The following `ALTER` will attach a built-in Data Metric Function `NULL_COUNT` to the `EMAIL_ADDRESS` column in the `CUSTOMER_DIM` table

```sql
ALTER TABLE customer_dim ADD DATA METRIC FUNCTION NULL_COUNT ON (EMAIL_ADDRESS);
```

Tell Snowflake to evaluate the DMF every hour:
```sql
ALTER TABLE customer_dim SET DATA_METRIC_SCHEDULE = '1 HOUR';
```

Inpect the quality metrics:
```sql
SELECT * FROM SNOWFLAKE.LOCAL.DATA_QUALITY_MONITORING_RESULTS
```


## Defining Custom Data Metric Functions (DMF)

Creating a Data Metric Function is as simple as creating a UDF.  The function takes a new TABLE data type with one or more column arguments and returns a single result value.

```sql
CREATE DATA METRIC FUNCTION IF NOT EXISTS count_of_nulls (
  ARG_T TABLE (ARG_C1 STRING, ARG_C2 STRING, ARG_C3 STRING ) ) RETURNS NUMBER
AS
$$
SELECT COUNT_IF(ARG_C2 IS NULL OR ARG_C2 IS NULL  OR ARG_C3 IS NULL  ) from ARG_T’;
$$;
```

All Data Metric Functions are added to your warehouse so other users can reuse them in a consistent manner without having to recreate the same rules over and over.

You can see which metrics are available by running:

```sql
SHOW DATA METRIC FUNCTIONS IN <DATABASE_NAME>.<SCHEMA_NAME>;
```
## Attaching custom Data Metric Function to a Table

You attach the custom Data Metric Function to the columns you want to evaluate:

```sql
ALTER TABLE CUSTOMER_DIM
  ADD DATA METRIC FUNCTION count_of_nulls ON (FIRST_NAME, LAST_NAME, EMAIL_ADDRESS);
```


Next, you set the interval at which metrics should be calculated. This is configured at the table level and can be as frequent as 1 minute.

```sql
ALTER TABLE some_table SET DATA_METRIC_SCHEDULE = '1 HOUR';
```

You can test DMFs by executing them ad-hoc or include them directly in your data pipelines running on Snowflake for in-line validation. This is how you execute a DMF:

``` sql
SELECT MY_DB.DATA_METRICS.count_of_nulls (
  SELECT FIRST_NAME, LAST_NAME, EMAIL_ADDRESS FROM CUSTOMER_DIM
) AS VALUE;
```
