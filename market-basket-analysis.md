# Association Rule Mining using MATCH_RECOGNIZE
What is the most frequently bought item with Infant Formula and Infant Diapers? Association Rule Mining is a key to Market Basket Analysis. While Association Rule Mining is a complex topic, we can use SQL to figure out what items most frequently bought together. 

## Association Rule

An association rule is a implication expression of the form
`X → Y`, where `X` and `Y` are itemset. X is the Antecedent and Y is the Consequent

Example of Association Rules

```
{Infant Diaper} → {Coke},
{Milk, Bread} → {Eggs, Coke},
{Coke, Bread} → {Infant Milk}
Antecedent → Consequent
```

Now let's assume we take `{Infant Diaper, Infant Milk}` as the Antecedent, and we want to figure out the Consequent i.e. what ITEM is most often bought with Infant Diaper and Infant Milk.




## Association Rule Mining using SQL
Association Rule are, by definition, extracted from the data i.e. they are core properties of the Data. We are not looking for correlation in the Itemsets in a Rule. This makes Relational Databases a good platform for Association Rule Mining. While there are lot of Python and R packages that are designed for Association Rule Mining, we want start exploring the use of SQL to perform Exploratory Data Mining excercise.

Here is our sample order data:
```sql
select order_number, listagg(item, ', ') from orders
group by 1;
```

| ORDER_NUMBER | LISTAGG(ITEM, ', ')                                       |
|--------------|-----------------------------------------------------------|
| 3            | infant formula, infant diaper, coffee, coke               |
| 7            | infant formula, infant diaper, mountain dew, coke, coffee |
| 1            | bread, infant formula                                     |
| 4            | bread, infant formula, infant diaper, coffee              |
| 6            | infant formula, infant diaper, mountain dew               |
| 5            | infant formula, infant diaper, coke                       |
| 2            | bread, infant diaper, coffee, eggs                        |



The followiing query will look at the Items that bought together with Infant Diaper and Infant Milk. 

## MATCH_RECOGNIZE for Market Basket Analysis
```sql
with recursive r(n, order_number, item) as (
    select 1, order_number, item from orders
    union all
    select n+1, order_number, item from r where n <= 100
)
select third_item, count(distinct order_number) as frequency 
from r
match_recognize (
    partition by order_number  order by n
    measures
      item3.item as third_item
    after match skip to next row
    pattern (permute(item1, item2, item3*))
    define item1 as item = 'infant diaper'
    , item2 as item = 'infant formula'
    , item3 as item not in ('infant formula', 'infant diaper')
)
where third_item is not null
group by third_item;
```

| THIRD_ITEM   | FREQUENCY |
|--------------|-----------|
| mountain dew | 2         |
| coke         | 3         |
| coffee       | 3         |
| bread        | 1         |

Based on the above query, we can extract the following Association Rule:
```
{Infant Milk, Infant Diaper} → {Coke}
{Infant Milk, Infant Diaper} → {Coffee}
```

This doesn't mean that there is correlation between purchase of Infant Milk, Infant Diaper and Coffee. This is just a property if of the Data.
