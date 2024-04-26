<ul id="recent-articles">
{% for page in site.pages %}
    {% if page.title contains " " %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>










- [Market Basket Analysis using SQL](market-basket-analysis.md)
- [RATIO_TO_REPORT](ratio_to_report.md)
- [CONDITIONAL_TRUE_EVENT](conditional_true_event.md)
- [MATCH_RECOGNIZE](match_recognize.md)
- [Stock Analysis using MATCH_RECOGNIZE](stock-analysis.md)
- [Sessionization using CONDITONAL_TRUE_EVENT](sessionization.md)
- [A/B analysis on streaming data using MATCH_RECOGNIZE](A-B-analysis-on-streaming-data-using-MATCH_RECOGNIZE.md)
- [DECODE](decode.md)
- [MAX_BY](max_by.md)
- [MIN_BY](min_by.md)
- [Using DECODE in SQL JOIN Predicate](using-decode-in-sql-join-predicate.md)
- [Correlated Pattern Definition and Snowflake's MATCH_RECOGNIZE](correlated-pattern-definitions-and-MATCH_RECOGNIZE.md)
- [Top sales by amount for each sales associate using MAX_BY() aggregate function](top-sales-by-amount-for-each-sales-associate-using-max_by.md)
- [Applied overview of MATCH_RECOGNIZE clause](applied-overview-of-MATCH_RECOGNIZE-clause.md)
- [Data Vault Effectivity Satellite](data-vault-effectivity-satellite.md) 
- [Using MATCH_RECOGNIZE to analyze data in a Data Vault Effectivity Satellite](using-MATCH_RECOGNIZE-to-analyze-data-in-a-data-vault-effectivity-satellite.md)
- [NULL-safe comparison in Snowflake SQL](null-safe-comparison-in-snowflake-sql.md)
- [Using MATCH_RECOGNIZE to analyze data in a Data Vault Effectivity Satellite - Part II](using-MATCH_RECOGNIZE-to-analyze-data-in-a-data-vault-effectivity-satellite-part-ii.md)
- [RATIO_TO_REPORT with Aggregate Functions](RATIO_TO_REPORT-with-aggregate-functions.md)
- [MAX_BY() and MIN_BY() with Window Function](max_by-and-min_by-with-window-function.md)
- [SHOW IMPORTED KEYS](show-imported-keys.md)
- [SHOW EXPORTED KEYS](show-imported-keys.md)
- [Getting Foreign Keys on Tables in Snowflakes](show-imported-keys.md)
- [How to find column names in a FK constraint?](show-imported-keys.md)
- [How to get the column names for a foreign key constraint?](show-imported-keys.md)
- [List Foreign Keys in Snowflake](show-imported-keys.md)
- [Skyline query to identify problematic queries in Snowflake](skyline-query-to-identify-problematic-queries-in-snowflake.md)
- [Maximum Vector Problem as a solution to identifying problematic queries in Snowflake](skyline-query-to-identify-problematic-queries-in-snowflake.md)
- [Skyline Query](skyline-query-pareto-set-maxima.md)
- [Pareto Front](skyline-query-pareto-set-maxima.md)
- [Pareto Set](skyline-query-pareto-set-maxima.md)
- [Pareto Frontier](skyline-query-pareto-set-maxima.md)
- [Color Schemes for Surface Plots](color-schemes-for-surface-plots.md)
- [Wrangle data in Snowflake using Python](wrangle-data-in-snowflake-using-python.md)
- [Data Metric Function in Snowflake](data-metric-function-in-snowflake.md)
- [What’s eating up your Snowflake Virtual Warehouse - Part I?](query_hash-and-query_parameterized_hash-in-snowflake.md)
- [What’s eating up your Snowflake Virtual Warehouse - Part II?](whats-eating-up-your-snowflake-warehouse-part-2.md)
- [system$ngram_search](system$ngram_search.md)
- [Searching for objects across Databases in Snowflake](system$ngram_search.md)
- [How to Predict Customer Churn Using SQL Pattern Detection](how-to-predict-customer-churn-using-sql-pattern-detection.md)
- [Snowflake ASOF join](ASOF-and-MATCH_CONDITION-join.md)
- [ASOF join Snowflake](ASOF-join-snowflake.md)
- [ASOF join](ASOF-join.md)
- [Perform joins on timeseries data using ASOF JOIN in Snowflake](perform-joins-on-timeseries-data-using-ASOF-JOIN-in-snowflake.md)
- [Sessionization using RANGE_SESSIONIZE in Google Big Query](sessionization-using-RANGE_SESSIONIZE-in-google-big-query.md)
- [User Defined Aggregate Functions (UDAF) in Google BigQuery](UDAF-in-google-bigquery.md)
- [Calculating Geometric Mean of values in a column using SQL](calculating-geometric-mean-of-values-in-a-column-using-SQL.md)
- [NULL-safe GREATEST and LEAST now available in Snowflake](NULL-safe-greatest-and-least-in-Snowflake.md)
