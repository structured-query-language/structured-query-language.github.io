# Calculating percentages using RATIO_TO_REPORT

`RATIO_TO_REPORT` Window function can be used to calculate percentage of the entire dataset or partitioned data.

## An illustrative example

Let’s look at the dataset we will be using for this tutorial:

```sql
select
    player_id,
    team,
    position,
    goals,
from soccer_players
```

![Copy of Copy of favicon](https://github.com/user-attachments/assets/b953283b-3504-42ab-80d8-202c03d14cd7)

Our data is about a soccer game where each row represents a player id, with columns for id, their team, and how many goals they have scored.

If we wanted to work out what percentage of all goal each player has scored, we can use `ratio_to_report()` window function as following:

```sql
select
    player_id,
    team,
    position,
    goals,
    ratio_to_report(goals) over () as overall_goals_percentage
from soccer_players
order by team, position;
```

![Copy of Copy of Copy of favicon](https://github.com/user-attachments/assets/c0219bc2-6030-4fcb-a34e-c8523c48d07c)


The `over()` in the query above tells the database to expand the _window_ in which the `ratio_to_report()` operates to the whole dataset.

The `ratio_to_report()` window function takes an expression (usually a column) as input and calculates the ratio of that expression to the window that is defined (in this case, the whole dataset).

## Using ratio_to_report() to Calculate Partitioned Percentages

Let’s say we were not only interested in each players overall contribution to goals, but also their contribution to goals for their specific team. We can then add a partition by to the _window_:

```sql
select
    player_id,
    team,
    position,
    goals,
    ratio_to_report(goals) over (team) as overall_goals_percentage
from soccer_players
order by team, position;
```

![Copy of favicon](https://github.com/user-attachments/assets/29fa93bc-61d8-436a-8d92-fc539fdbd243)

# See also:
<ul id="recent-articles">
{% for page in site.pages %}
    {% if page.title contains "RATIO_TO_REPORT" %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>
