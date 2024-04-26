# A/B Analysis on Streaming Data using MATCH_RECOGNIZE
A/B testing is key to eliminating the guesswork out of website optimization. A/B Analysis on Streaming Data enables data-backed decision in real-time. [MATCH_RECOGNIZE](applied-overview-of-MATCH_RECOGNIZE-clause.md) in SnowflakeDB can be used to perform A/B Analysis on Streaming web analytics data and optimize the Campaigns for higher conversion rates.
 
In the following example, we have defined the successful conversion `PATTERN` as 
1. Visitor visits the home page;
2. Visitor is displayed either `plant_a_tree_campaign_a` or `plant_a_tree_campaign_b`
3. Visitor makes a donation.

All three steps must be completed for a successful conversion.

## Query
We will use the following [MATCH_RECOGNIZE](applied-overview-of-MATCH_RECOGNIZE-clause.md) Query to get the conversion rate for `CAMPAIGN_PAGE_A` vs. `CAMPAIGN_PAGE_B`

```sql
select session_id, action
from scratch.saqib_ali.webstream
match_recognize(
    partition by session_id
    order by visit_timestamp
    measures
      classifier() as action
    all rows PER MATCH
    PATTERN (VISIT (CAMPAIGN_PAGE_A | CAMPAIGN_PAGE_B) DONATE)
    DEFINE
          VISIT as iff(URI='index.html',  TRUE,  FALSE),
          CAMPAIGN_PAGE_A as iff(URI='plant_a_tree_campaign_a.html',  TRUE,  FALSE),
          CAMPAIGN_PAGE_B as iff(URI='plant_a_tree_campaign_b.html',  TRUE,  FALSE),
          DONATE as iff(URI='donate.html',  TRUE,  FALSE)
);
```

## Output resultset

| ACTION          | CONVERSIONS |
|-----------------|-------------|
| CAMPAIGN_PAGE_A | 4           |
| CAMPAIGN_PAGE_B | 1           |


## Input raw data

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

# Screenshots
## Screenshot 1
![image](https://user-images.githubusercontent.com/121721444/213082729-3658b4e2-691c-4f2a-9fcd-83d2ff795cfd.png)
## Screenshot 2
![image](https://user-images.githubusercontent.com/121721444/213083242-d4f3cd17-cff7-47f1-8c0c-2824ea19666b.png)


# See also:
<ul id="recent-articles">
{% for page in site.pages %}
    {% if page.title contains "MATCH_RECOGNIZE" %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>

