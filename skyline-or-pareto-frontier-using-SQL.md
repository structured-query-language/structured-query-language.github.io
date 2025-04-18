# Skyline or Pareto Front using SQL

```

✧    ✧                                        ✧ 
                                                 ✧
   ✦                 ✧   ▅   ✦                     ▅     
✧        ▁✧              █    ✧  ▃ ✦▇      ✧✦  ☽ ✦ █     
    ▇ ▁  █               █     ▇▅█  █▃    ✦✦       █     
    █▁█▅▁█ ▃▃▁✧▇▃✧ ▁▁  ▁▅█  ▁ ✦███▁ ██▅  ✦      ▇  █ ▇   
    ██████▅███▅██▅▁██▁▁███▁▅█▁▅████▅███▁▁▅▁▅▅▅▁▁█▁▁█▁█▁▁▁
```
Skyline computation is an essential database operation that has many applications in multi-criteria decision making scenarios such as recommender systems. With the growth of data, efficiently finding interesting data within large datasets has become essential. Skyline queries are a method to select a subset of data by returning tuples that are not dominated by any other tuple. A tuple _t_ dominates another tuple _s_ if t is not worse than _s_ in any attribute and is strictly better in at least one. As such, Skyline queries have emerged as an increasingly popular tool for identifying a set of interesting objects that balance different user-specified criteria.

Given a multidimensional data set, where the dimensions correspond to the criteria that need to be balanced, a Skyline query returns a set of interesting data points, aka. skyline points, that are not dominated by any other point in all dimensions. A point _m_ dominates another point _n_, if _m_ is better than or equal to _n_ in all dimensions and strictly better than _n_ in at least one dimension. 


## An Illustrative Example
The best known example use case for a skyline query is a hotel booking scenario where users are looking for hotels. Assume many hotels are available and the user wants to find one based on three criteria: distance to the center, hotel rating and price per night. Further assume that the user is unable to say which of these criteria is more important. So, we need to look for hotels representing a good combination of all three criteria. The skyline consists of all hotels that represent a "good" combinations of the three criteria. For each of the other hotels, there is always at least one hotel in the skyline that is better with respect to the three criteria. So, being presented the skyline, the user gets an overview of the available hotels and can make the final decision with respect to her personal preferences for the three criteria. 

### Example Hotel Dataset

| HOTEL_ID | RATING | PRICE | DISTANCE_FROM_CENTER |
|----------|--------|-------|----------------------|
| a        | 4      | 200   | 3                    |
| b        | 5      | 300   | 4                    |
| c        | 1      | 600   | 5                    |
| d        | 2      | 700   | 1                    |
| e        | 4      | 100   | 2                    |
| f        | 5      | 290   | 3                    |
| g        | 1      | 190   | 5                    |
| h        | 1      | 290   | 5                    |
| i        | 5      | 190   | 1                    |
| j        | 2      | 490   | 5                    |
| k        | 5      | 490   | 1                    |

#### Skyline of the Example Hotel Dataset

|![Screenshot 2024-05-22 at 21-31-38 Online Interactive 3D Scatter Plot](https://github.com/structured-query-language/structured-query-language.github.io/assets/121721444/0055b866-28ab-49bd-8733-ea76eff77e0b)|
|:--:| 
|*Figure 1*. Here Hotels _i_ and _e_ are better or equal to all hotels in all dimensions and strictly better than other hotels in _at least one_ dimension. Thus, _i_ and _e_ form the Skyline in this 3 Dimensional space.|

|![Chart(2)](https://github.com/structured-query-language/structured-query-language.github.io/assets/121721444/1f9538d0-5568-49c9-9ac0-c371fe1a7430)|
|:-:|
|*Figure 2*. Hotels _i_ and _e_ compared hotels _c_ and _f_. While hotel _f_, like hotel _i_, has a rating of _5_, it is more expensinve than hotel _i_ and and farther from the City Center than hotel _i_. So _f_ is not on the Skyline. Hotel _c_ has lower rating, farther from the City Center and pricey, so it is not on the Skyline at all. |


## Skyline using SQL
The following are few different options for calculating the Skyline or Pareto Frontier or Pareto Front using SQL


## Option 1

```sql
SELECT hotel_id, price, rating, distance_from_center FROM hotels AS o WHERE
 not EXISTS(
SELECT 1 FROM hotels AS i WHERE
  i.price <= o.price AND i.rating >= o.rating and i.distance_from_center <= o.distance_from_center
  AND (i.price < o.price OR i.rating > o.rating OR i.distance_from_center < o.distance_from_center)
);
```

### Query Output

| HOTEL_ID | RATING | PRICE | DISTANCE_FROM_CENTER |
|----------|--------|-------|----------------------|
| e        | 4      | 100   | 2                    |
| i        | 5      | 190   | 1                    |


## Option 2

```sql
SELECT p.*
FROM hotels p
LEFT JOIN hotels q
  ON (q.price <= p.price AND q.rating >= p.rating AND q.distance_from_center < p.distance_from_center)
  OR (q.price < p.price AND q.rating >= p.rating AND q.distance_from_center <= p.distance_from_center)
  OR (q.price <= p.price AND q.rating > p.rating AND q.distance_from_center <= p.distance_from_center)
WHERE q.price IS NULL AND q.rating IS NULL and q.distance_from_center is NULL;
```

### Query Output

| HOTEL_ID | RATING | PRICE | DISTANCE_FROM_CENTER |
|----------|--------|-------|----------------------|
| e        | 4      | 100   | 2                    |
| i        | 5      | 190   | 1                    |

## Option 3

```sql
WITH ranked AS (
  SELECT
    *
    , dense_rank() OVER (ORDER BY price ASC, rating DESC, distance_from_center ASC ) AS rank1
    , dense_rank() OVER (ORDER BY rating DESC, price ASC, rating DESC, distance_from_center ASC ) AS rank2
    , dense_rank() OVER (ORDER BY distance_from_center ASC, rating DESC, price ASC, rating DESC) AS rank3

  FROM hotels
)
SELECT *
FROM ranked
WHERE rank1 = 1 OR rank2 = 1 OR rank3 = 1;
```


### Query Output

| HOTEL_ID | RATING | PRICE | DISTANCE_FROM_CENTER | RANK1 | RANK2 | RANK3 |
|----------|--------|-------|----------------------|-------|-------|-------|
| i        | 5      | 190   | 1                    | 2     | 1     | 1     |
| e        | 4      | 100   | 2                    | 1     | 5     | 4     |


## Conclusion
The skyline query processing is widely adopted in many applications that require multi-criteria decision making such asmarket research, location based systems, web services study, etc. The skyline operator filters out a set of interesting points based on a group of evaluation criteria from a large set of points. A point is considered as interesting, if there does not exist a point that is at least as good in all criteria and better in at least one criterion. SQL can be used to perform a Skyline Query on multi-demension datasets stored in Relational Database. We saw 3 differents ways SQL can be used to write a Skyline Query.


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

