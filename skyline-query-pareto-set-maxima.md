# Skyline Query
Skyline, also known as Pareto Frontier or Pareto Set in Business Management or Maxima in Computional Geometry, is a useful technique in multi-criteria analysis and decision support.  Skyline aims to identify points in n-dimensions that are not dominated by any other point. In other words, a Skyline is formed using points that are best in at least one dimension and at least as good as other best points in every other dimension. The following plot illustrates a Skyline in 2-dimensional space

![image](https://github.com/structured-query-language/structured-query-language.github.io/assets/121721444/62ca294d-82b6-4418-a21a-334b182462ad)


## Input data

| HOTEL | DISTANCE_TO_DESTINATION | PRICE |
|-------|-------------------------|-------|
| a     | 4                       | 400   |
| b     | 24                      | 380   |
| c     | 14                      | 340   |
| d     | 36                      | 300   |
| e     | 26                      | 280   |
| f     | 8                       | 260   |
| g     | 40                      | 200   |
| h     | 20                      | 180   |
| i     | 34                      | 140   |
| j     | 28                      | 120   |
| k     | 16                      | 60    |

## Skyline Query using SQL

```sql
select 
  hotel
  , price
  , distance_to_destination
from scratch.saqib_ali.hotels as o
where not exists (
  select 1
  from scratch.saqib_ali.hotels as i
  where i.price <= o.price and i.distance_to_destination <= o.distance_to_destination
  and (i.price < o.price or i.distance_to_destination < o.distance_to_destination)
);
```

| HOTEL | PRICE | DISTANCE_TO_DESTINATION |
|-------|-------|-------------------------|
| a     | 400   | 4                       |
| f     | 260   | 8                       |
| k     | 60    | 16                      |


![image](https://github.com/structured-query-language/structured-query-language.github.io/assets/121721444/62ca294d-82b6-4418-a21a-334b182462ad)

## Skyline using Python

```python
#!pip install paretoset
 
from paretoset import paretoset
import pandas as pd
import matplotlib.pyplot as plt

hotels = pd.DataFrame({"price": [400, 380, 340, 300, 280, 260, 200, 180, 140, 120, 60], 
                       "distance_to_destination": [4, 24, 14, 36, 26, 8, 40, 20, 34, 28, 16]})
skyline = paretoset(hotels, sense=["min", "min"])
skyline_hotels = hotels[skyline]



plt.scatter(hotels.loc[:, 'price'], hotels.loc[:, 'distance_to_destination'], alpha=0.5)
plt.scatter(skyline_hotels.loc[:, 'price'], skyline_hotels.loc[:, 'distance_to_destination'], alpha=0.1, s=300)
plt.plot(skyline_hotels.loc[:, 'price'], skyline_hotels.loc[:, 'distance_to_destination'], alpha=0.1)
plt.xlabel("price")
plt.ylabel("distance to destination")

plt.gca().invert_xaxis()
plt.gca().invert_yaxis()
plt.show()

print(skyline_hotels)
```

|hotel|price|distance_to_destination|
|-----|-----|-----------------------|
| 0   |400  |                     4 |
| 5   |260  |                     8 |
| 10  |60   |                    16 |

# See also
<ul id="recent-articles">
{% for page in site.pages %}
    {% if page.title contains "Skyline" %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>
