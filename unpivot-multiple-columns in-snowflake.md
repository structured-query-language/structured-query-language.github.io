# UNPIVOT multiple columns in Snowflake

Data can be organized in different ways, for example, in a short/wide or tall/skinny/narrow/long format, but still contain the same information. It  is often easier to visualize data organized in a long table format, that is, when the values are collected in just a few value columns. Unpivoting is one way to transform data from a wide table to a long table format. This unpivoted data can then be consumed by Tablue, Looker, Power BI etc for building visualizations.


|<img src="https://github.com/user-attachments/assets/390dec7d-97a4-403a-87cd-9aece7ae1025" width="75%">|
|:--:|
|When you unpivot, you unpack the attribute-value pairs and re-orient them into flattened columns for easier analysis|

## Multi-column Unpivot example 1
Suppose you have a Wide Table format Performance Ratings that you need to covert to a Long Table format:

|<img src="https://github.com/user-attachments/assets/fe058626-62c3-4155-a2aa-288b6c84c798" width="75%">|
|:--:|
|Unpivoting to convert Skill Rating and Skill Comments to a Flattened view of the data as Key-value pairs|

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

## Multi-column Unpivot example 2

In this example we have a Wide Table with Children Guardian contact information. We have two guardians listed for each child, and an email address for each guardian.  Instead of two columns for guardians names and two for email addresses, we would like to reduce this to one of each: Child Name, Guardian Name, Guardian Email Address as following:

|<img src="https://github.com/user-attachments/assets/632b638b-23f6-4a83-98c6-fce8c7b79967" width="75%">|
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


