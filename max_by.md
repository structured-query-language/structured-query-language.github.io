# MAX_BY Analytical Function in Snowflake

When analyzing Clickstream data, one is often interested in the last few pages visited before the visitor exited the site. Snowflake has powerful aggregation function called `max_by` that can be used to get an array of the last few pages visited by `session_id`. 

## Query

```sql
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

## Query Highlights

![image](https://user-images.githubusercontent.com/121721444/213944385-c0cf28bb-6c0d-4eb3-977c-077950902142.png)


## Raw Data

| SESSION_ID                           | URI                          | VISIT_TIMESTAMP |
|--------------------------------------|------------------------------|-----------------|
| 022310c3-6f5a-4b65-8de2-df67ae3bcc9c | index.html                   | 19:12:33        |
| 022310c3-6f5a-4b65-8de2-df67ae3bcc9c | plant_a_tree_campaign_a.html | 19:12:48        |
| 2c587331-ab47-4bd6-b57f-05af3896fa56 | index.html                   | 19:12:46        |
| 2c587331-ab47-4bd6-b57f-05af3896fa56 | plant_a_tree_campaign_b.html | 19:12:58        |
| 3ce56b32-0a97-43a7-9227-e1c9bf4786ee | index.html                   | 19:12:48        |
| 3ce56b32-0a97-43a7-9227-e1c9bf4786ee | plant_a_tree_campaign_a.html | 19:12:55        |
| 3ce56b32-0a97-43a7-9227-e1c9bf4786ee | donate.html                  | 19:13:00        |
| 41f4d2ca-8d08-4c80-b407-9b97e6bd558e | index.html                   | 19:12:56        |
| 41f4d2ca-8d08-4c80-b407-9b97e6bd558e | campaigns.html               | 19:13:05        |
| 41f4d2ca-8d08-4c80-b407-9b97e6bd558e | plant_a_tree_campaign_b.html | 19:13:15        |
| 41f4d2ca-8d08-4c80-b407-9b97e6bd558e | campaigns.html               | 19:13:25        |
| 74abb734-d272-475a-873d-33caf655addc | index.html                   | 19:12:37        |
| 74abb734-d272-475a-873d-33caf655addc | plant_a_tree_campaign_a.html | 19:12:57        |
| 74abb734-d272-475a-873d-33caf655addc | donate.html                  | 19:13:09        |
| 9d1937da-92ee-462f-968e-2fc6d910e185 | index.html                   | 19:12:44        |
| 9d1937da-92ee-462f-968e-2fc6d910e185 | plant_a_tree_campaign_a.html | 19:12:54        |
| 9d1937da-92ee-462f-968e-2fc6d910e185 | donate.html                  | 19:13:06        |
| b0e2ff35-7e55-4b7b-a246-8857fdf05c76 | index.html                   | 19:12:40        |
| b0e2ff35-7e55-4b7b-a246-8857fdf05c76 | plant_a_tree_campaign_a.html | 19:12:49        |
| b0e2ff35-7e55-4b7b-a246-8857fdf05c76 | donate.html                  | 19:12:58        |
| c23d473c-99ef-4e28-a79c-c0a35e0a27cc | index.html                   | 19:12:42        |
| c23d473c-99ef-4e28-a79c-c0a35e0a27cc | plant_a_tree_campaign_b.html | 19:12:58        |
| c23d473c-99ef-4e28-a79c-c0a35e0a27cc | donate.html                  | 19:13:05        |
| eae477e9-0dad-4450-a08f-5361603f46ec | index.html                   | 19:12:48        |
| eae477e9-0dad-4450-a08f-5361603f46ec | plant_a_tree_campaign_b.html | 19:13:04        |
| f597f443-4098-4ee4-9538-7076e114085c | index.html                   | 19:12:54        |
| f597f443-4098-4ee4-9538-7076e114085c | plant_a_tree_campaign_b.html | 19:13:02        |
| f597f443-4098-4ee4-9538-7076e114085c | campaigns.html               | 19:13:19        |
| f597f443-4098-4ee4-9538-7076e114085c | plant_a_tree_campaign_b.html | 19:13:31        |
| f597f443-4098-4ee4-9538-7076e114085c | donate.html                  | 19:13:38        |

# See also
<ul id="recent-articles">
{% for page in site.pages %}
    {% if (page.title contains "MAX_BY"  or page.title contains "MIN_BY") %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>
