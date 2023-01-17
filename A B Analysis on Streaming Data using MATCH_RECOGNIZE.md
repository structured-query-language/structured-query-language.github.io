# A/B Analysis on Streaming Data
A/B testing eliminates the guesswork out of website optimization and enables data-backed decisions. A/B Analysis on Streaming Data allows the optimizers to make quick data-backed decisions on what version of the webpage has a high click through rate. MATCH_RECOGNIZE in SnowflakeDB can be used to perform A/B Analysis on Streaming web metrics and optimize the Campaigns for higher click through rates.

# A/B Analysis using MATCH_RECOGNIZE
In the following example, we have defined the success `PATTERN` as 
1. Visitor visits the home page;
2. Visitor is displayed either `plant_a_tree_campaign_a` or `plant_a_tree_campaign_a`
3. Visitor makes a donation.

All three steps must be completed for a success.

We will use the following MATCH_RECOGNIZE Query to get the click through rate for `CAMPAIGN_PAGE_A` vs. `CAMPAIGN_PAGE_B`

```
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
