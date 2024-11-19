# UNPIVOT multiple columns in Snowflake

|![Untitled drawing(22)](https://github.com/user-attachments/assets/390dec7d-97a4-403a-87cd-9aece7ae1025)|
|:--:|
|Transposing Wide Table into a Long Table using UNPIVOT|

## Example 1: Unpivot 3 Columns
Suppose you have a Wide Table format Performance Ratings data as following:

| NAME         | ACTING_RATING | ACTING COMMENTS | COMEDY RATING | COMEDY COMMENTS | MUSICAL PERFORMANCE RATING | MUSICAL PERFORMANCE COMMENTS |
|--------------|---------------|-----------------|---------------|-----------------|----------------------------|------------------------------|
| Groucho Marx | 4             | Awesome         | 3             | Super           | 4                          | Really good                  |
| Harpo Marx   | 4             | Awesome         | 4             | Awesome         | 4                          |                              |
| Groucho Marx | 4             |                 | 3             | Thumbs up       | 4                          | Nice                         |
| Harpo Marx   | 4             | Best Acting     | 4             | Nice            | 4                          | Best performance Award!      |

and you need to convert it to a Long Table Format as following

| NAM          | SKILL                      | SKILL_RATING | COMMENTS                | SKILL_COMMENTS               | 
|--------------|----------------------------|--------------|-------------------------|------------------------------|
| Groucho Marx | ACTING_RATING              | 4            | Awesome                 | ACTING_COMMENTS              |                      
| Groucho Marx | COMEDY_RATING              | 3            | Super                   | COMEDY_COMMENTS              |                      
| Groucho Marx | MUSICAL_PERFORMANCE_RATING | 4            | Really good             | MUSICAL_PERFORMANCE_COMMENTS |                      
| Harpo Marx   | ACTING_RATING              | 4            | Awesome                 | ACTING_COMMENTS              |                      
| Harpo Marx   | COMEDY_RATING              | 4            | Awesome                 | COMEDY_COMMENTS              |                      
| Harpo Marx   | MUSICAL_PERFORMANCE_RATING | 4            |                         | MUSICAL_PERFORMANCE_COMMENTS |                      
| Groucho Marx | ACTING_RATING              | 4            |                         | ACTING_COMMENTS              |                      
| Groucho Marx | COMEDY_RATING              | 3            | Thumbs up               | COMEDY_COMMENTS              |                      
| Groucho Marx | MUSICAL_PERFORMANCE_RATING | 4            | Nice                    | MUSICAL_PERFORMANCE_COMMENTS |                      
| Harpo Marx   | ACTING_RATING              | 4            | Best Acting             | ACTING_COMMENTS              |                      
| Harpo Marx   | COMEDY_RATING              | 4            | Nice                    | COMEDY_COMMENTS              |                      
| Harpo Marx   | MUSICAL_PERFORMANCE_RATING | 4            | Best performance Award! | MUSICAL_PERFORMANCE_COMMENTS |                      

This can be achieve by using the UNPIVOT function as following

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

## Example 2: Unpivot 2 Columns
Suppose you have a Wide Table format Children Guardian contact information that you want to convert to a Long Table format

|![Untitled drawing(23)](https://github.com/user-attachments/assets/632b638b-23f6-4a83-98c6-fce8c7b79967)|
|:--:|
|Transposing Guardian Contact Information to a Long table format which is easier to analyze.|

You can use the following SQL to transpose this into a long table format as following:

```sql
select child_name, guardian_name, guardian_email
from student_guardian_info
unpivot include nulls (guardian_name for name in (guardian1_name, guardian2_name))
unpivot include nulls (guardian_email for email in (guardian1_email, guardian2_email))
--Following where clause is added to filter the unmatched rows
where split(name, '_')[0] = split(email, '_')[0]

```


| CHILD_NAME      | GUARDIAN_NAME     | GUARDIAN_EMAIL  |
|-----------------|-------------------|-----------------|
| Lynn Evans      | Amrika Hernandez  | amrika@fun.com  |
| Lynn Evans      | Steve Evans       | steve@fun.com   |
| Steph Andersson | Anders Bloom      | anders@fun.com  |
| Steph Andersson | Stephen McDonalds | Stephen@fun.com |

# See also:
<ul id="recent-articles">
{% for page in site.pages %}
    {% if page.title contains "PIVOT" %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>


