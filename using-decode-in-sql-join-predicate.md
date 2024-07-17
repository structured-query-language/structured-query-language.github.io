# DECODE in SQL JOIN predicate
[DECODE](decode.md) is powerful Snowflake function that can be used to implement `if..then.elseif..then..endif`. It can be used in a SQL `JOIN` predicate as well as following

## Query
```sql
select 
  contact_email
  , product_id
  , name
from sat_lead_salesforce
inner join sat_opportunity_salesforce 
  on decode(
    primary_email_num, 
    1, email_1, 
    2, email_2, 
    3, email_3
  ) = sat_opportunity_salesforce.contact_email;
```

## Query Output

| OPPORTUNITY_ID | PRODUCT_ID | CONTACT_EMAIL         |
|----------------|------------|-----------------------|
| 1              | 1111       | saqib_two@email.tld   |
| 2              | 2222       | saqib_two@email.tld   |
| 3              | 1111       | scott_three@email.tld |
| 4              | 1111       | ben_two@email.tld     |

## Raw Data

### sat_lead_salesforce

| CONTACT_ID | EMAIL_1     | EMAIL_2     | EMAIL_3       | PRIMARY_EMAIL_NUM | NAME      |
|------------|-------------|-------------|---------------|-------------------|-----------|
| 1          | saqib_one@… | saqib_two@… | saqib_three@… | 2                 | Saqib Ali |
| 2          | scott_one@… | scott_two@… | scott_three@… | 3                 | Scott     |
| 3          | ben_one@…   | ben_two@…   | ben_three@…   | 2                 | Ben       |

### sat_opportunity_salesforce

| OPPORTUNITY_ID | PRODUCT_ID | CONTACT_EMAIL         |
|----------------|------------|-----------------------|
| 1              | 1111       | saqib_two@email.tld   |
| 2              | 2222       | saqib_two@email.tld   |
| 3              | 1111       | scott_three@email.tld |
| 4              | 1111       | ben_two@email.tld     |


# Screenshot(s)
![image](https://user-images.githubusercontent.com/121721444/215359078-bc4da876-2450-48f7-a77d-37d4deb57e89.png)


# See also
<ul id="recent-articles">
{% for page in site.pages %}
    {% if (page.title contains "DECODE") %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>
