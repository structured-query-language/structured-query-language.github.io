# Unpivot multiple columns in Snowflake

Suppose you have a Wide Table format Performance Ratings data as following:

| NAME         | ACTING_RATING | ACTING_COMMENTS | COMEDY_RATING | COMEDY_COMMENTS | MUSICAL_PERFORMANCE_RATING | MUSICAL_PERFORMANCE_COMMENTS |
|--------------|---------------|-----------------|---------------|-----------------|----------------------------|------------------------------|
| Groucho Marx | 4             | Awesome         | 3             | Super           | 4                          | Really good                  |
| Harpo Marx   | 4             | Awesome         | 4             | Awesome         | 4                          |                              |
| Groucho Marx | 4             |                 | 3             | Thumbs up       | 4                          | Nice                         |
| Harpo Marx   | 4             | Best Acting     | 4             | Nice            | 4                          | Best performance Award!      |

and you need to convert it to a Long Table Format as following

| NAM          | SKILL                      | SKILL_RATING | COMMENTS                | SKILL_COMMENTS               | SPLIT(SKILL, '-')[1] |
|--------------|----------------------------|--------------|-------------------------|------------------------------|----------------------|
| Groucho Marx | ACTING_RATING              | 4            | Awesome                 | ACTING_COMMENTS              |                      |
| Groucho Marx | COMEDY_RATING              | 3            | Super                   | COMEDY_COMMENTS              |                      |
| Groucho Marx | MUSICAL_PERFORMANCE_RATING | 4            | Really good             | MUSICAL_PERFORMANCE_COMMENTS |                      |
| Harpo Marx   | ACTING_RATING              | 4            | Awesome                 | ACTING_COMMENTS              |                      |
| Harpo Marx   | COMEDY_RATING              | 4            | Awesome                 | COMEDY_COMMENTS              |                      |
| Harpo Marx   | MUSICAL_PERFORMANCE_RATING | 4            |                         | MUSICAL_PERFORMANCE_COMMENTS |                      |
| Groucho Marx | ACTING_RATING              | 4            |                         | ACTING_COMMENTS              |                      |
| Groucho Marx | COMEDY_RATING              | 3            | Thumbs up               | COMEDY_COMMENTS              |                      |
| Groucho Marx | MUSICAL_PERFORMANCE_RATING | 4            | Nice                    | MUSICAL_PERFORMANCE_COMMENTS |                      |
| Harpo Marx   | ACTING_RATING              | 4            | Best Acting             | ACTING_COMMENTS              |                      |
| Harpo Marx   | COMEDY_RATING              | 4            | Nice                    | COMEDY_COMMENTS              |                      |
| Harpo Marx   | MUSICAL_PERFORMANCE_RATING | 4            | Best performance Award! | MUSICAL_PERFORMANCE_COMMENTS |                      |

This can be achieve by using the UNPIVOT function or the UNION ALL in Snowflake

## Using UNPIVOT

```sql
select 
  name
  , skill
  , skill_rating
  , comments
  , skill_comments
from performer_ratings
    unpivot include nulls (skill_rating for skill in (acting_rating,  comedy_rating, musical_performance_rating)) 
    unpivot include nulls (comments for skill_comments in (acting_comments,comedy_comments, musical_performance_comments)) 
--Following where clause is added to filter the unmatched rows
where split(skill, '_')[0] = split(skill_comments, '_')[0];

```


## Using UNION ALL

```sql

select NAME
  , 'ACTING_RATING' as SKILL, ACTING_RATING as SKILL_RATING, ACTING_COMMENTS as SKILL_COMMENTS
from performer_ratings
union all
select NAME
  , 'COMEDY_RATING', COMEDY_RATING, COMEDY_COMMENTS
from performer_ratings
union all
select NAME
  , 'MUSICAL_PERFORMANCE_RATING', MUSICAL_PERFORMANCE_RATING, MUSICAL_PERFORMANCE_COMMENTS
from performer_ratings
;
```
