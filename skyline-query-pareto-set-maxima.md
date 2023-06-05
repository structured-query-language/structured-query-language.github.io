# Skyline Query

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

## Skyline Query in SQL

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
