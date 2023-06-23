




```sql
with wrangle_data_with_pandas as PROCEDURE (table_name VARCHAR , key_name VARCHAR)
returns TABLE()
language python
runtime_version = '3.10'
packages = ('snowflake-snowpark-python')
handler = 'wrangle_data_with_pandas_defintion'
as
$$
import pandas

def wrangle_data_with_pandas_defintion(session, table_name, channel):
  
  
  # Read the data from the Snowflake table into a Snowflake Dataframe
  df_snowflake = session.table(table_name)

  # Convert Snowflake Dataframe to a Pandas Dataframe
  df_pandas = df_snowflake.to_pandas()

  # Filter Pandas dataframe to rows where the field matches the value
  df_pandas_filtered = df_pandas[df_pandas['CHANNEL'] == channel]

  # Convert filtered Pandas Dataframe back to a Snowflake Dataframe
  df_snowflake_filtered = session.create_dataframe(df_pandas_filtered)
  
  # Return the Snowfake Dataframe in Tabular format
  return df_snowflake_filtered
  
$$
call wrangle_data_with_pandas('scratch.saqib_ali.transactions', 'online')
;
```

