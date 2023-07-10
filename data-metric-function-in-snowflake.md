Snowflake’s new Data Metric Functions allows you to automate data quality validation and monitoring. Lets see how to configure and use them.

Note, this functionality is currently in private preview.

DMFs allow you to:

1. Create custom quality metric rules as a reusable functions
2. Apply rules to one of more columns
3. Specific how frequently you want the quality metrics to be calculated
4. Execute metric functions ad-hoc (for testing) or incorporate into your pipelines
5. Metrics are executed by the serverless backend, no need to keep your warehouse running
6. View and manage all of your quality metrics in a single place

Creating a DMF is as simple as creating a UDF.  The function takes a new TABLE data type with one or more column arguments and returns a single result value.

CREATE DATA METRIC FUNCTION IF NOT EXISTS count_of_nulls (
  ARG_T TABLE (ARG_C NUMBER) ) RETURNS NUMBER
AS
  ‘SELECT COUNT_IF(ARG_C IS NULL) from ARG_T’
;

All DMFs are added to your warehouse so other users can reuse them in a consistent manner without having to recreate the same rules over and over.

You can see which metrics are available by running:

SHOW DATA METRIC FUNCTIONS IN MY_DB.MY_SCHEMA;

Now you need to attach the DMF to the columns you want to evaluate, like this:

ALTER TABLE some_table ADD DATA METRIC FUNCTION count_of_nulls ON (EMAIL_COL);

Next, you set the interval at which metrics should be calculated. This is configured at the table level and can be as frequent as 1 minute.

ALTER TABLE some_table SET DATA_METRIC_SCHEDULE = ‘1 MINUTE’’;

That’s it.  You can query the metrics results by querying the DATA_METRIC_RESULTS table housed in the schema where tables to be validated reside.

SELECT to_number(VALUE) as count, TABLE_NAME, COLUMN_NAMES, 
FROM my_db.my_schema.data_metric_results
WHERE COLUMN_NAME = ‘EMAIL_COL’
ORDER BY SCHEDULED_TIME DESC;

You can test DMFs by executing them ad-hoc or include them directly in your data pipelines running on Snowflake for in-line validation. This is how you execute a DMF:

SELECT MY_DB.DATA_METRICS.count_of_nulls (
  SELECT email_col FROM CUSTOMERS
) AS VALUE;
