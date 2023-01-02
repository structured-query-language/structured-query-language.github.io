# MATCH_RECOGNIZE

Pattern Matching is key to Funnel Analytics. Snowflake's `MATCH_RECOGNIZE` clause can perform Pattern Matching over a set of rows. `MATCH_RECOGNIZE` does this by assigning labels to events, finding the events within an ordered partition, and pulling out any sequences that match the given pattern. The following query uses `MATCH_RECOGNIZE` to sessionize clickstream data to identify sessions that begin with an `ENTRY` to the site, then have
a series of `ONSITE` page views, and end with a `CHECKOUT_START`. 
