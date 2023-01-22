# MAX_BY

When analyzing Clickstream data, one is often interested in the last few pages visited before the visitor exited the site. Snowflake has powerful aggregation function called max_by that can be used to get array of the last few pages visited by session_id. 

## Query

```
select
  session_id
  , max_by(URI, visit_timestamp, 2)
from scratch.saqib_ali.webstream
group by session_id;
```

## Output

| SESSION_ID                           | MAX_BY(URI, VISIT_TIMESTAMP, 2)                   |
|--------------------------------------|---------------------------------------------------|
| 9d1937da-92ee-462f-968e-2fc6d910e185 | ["donate.html","plant_a_tree_campaign_a.html"]    |
| 3ce56b32-0a97-43a7-9227-e1c9bf4786ee | ["donate.html","plant_a_tree_campaign_a.html"]    |
| 74abb734-d272-475a-873d-33caf655addc | ["donate.html","plant_a_tree_campaign_a.html"]    |
| c23d473c-99ef-4e28-a79c-c0a35e0a27cc | ["donate.html","plant_a_tree_campaign_b.html"]    |
| b0e2ff35-7e55-4b7b-a246-8857fdf05c76 | ["donate.html","plant_a_tree_campaign_a.html"]    |
| 41f4d2ca-8d08-4c80-b407-9b97e6bd558e | ["campaigns.html","plant_a_tree_campaign_b.html"] |
| 2c587331-ab47-4bd6-b57f-05af3896fa56 | ["plant_a_tree_campaign_b.html","index.html"]     |
| f597f443-4098-4ee4-9538-7076e114085c | ["donate.html","plant_a_tree_campaign_b.html"]    |
| 022310c3-6f5a-4b65-8de2-df67ae3bcc9c | ["plant_a_tree_campaign_a.html","index.html"]     |
| eae477e9-0dad-4450-a08f-5361603f46ec | ["plant_a_tree_campaign_b.html","index.html"]     |
