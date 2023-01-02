# MATCH_RECOGNIZE

Pattern Matching is key to Funnel Analytics. Snowflake's `MATCH_RECOGNIZE` clause can perform Pattern Matching over a set of rows. `MATCH_RECOGNIZE` does this by assigning labels to events, finding the events within an ordered partition, and pulling out any sequences that match the given pattern. The following query uses `MATCH_RECOGNIZE` to sessionize clickstream data to identify sessions that begin with an `ENTRY` to the site, then have
a series of `ONSITE` page views, and end with a `CHECKOUT_START`. 


```
select 
  post_visid_high || ':' || post_visid_low as visitor_id  
  , date_time, referrer, page_url
from clickstream_data 
match_recognize (
partition by post_visid_high || ':' || post_visid_low
order by date_time
all rows per match
pattern (entry onsite+ checkout_start)
define 
  entry as referrer is null or referrer not like '%buy.com%',
  onsite as page_url like '%buy.com%' 
    and page_url not like 'https://buy.com/checkout/%',
  checkout_start as page_url like 'https://buy.com/checkout/%'
);
```


![image](https://user-images.githubusercontent.com/121721444/210282428-7fdf6c5d-5b28-475e-b094-51581b4def14.png)
