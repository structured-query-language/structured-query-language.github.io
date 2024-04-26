<ul id="recent-articles">
{% for page in site.pages %}
    {% if page.title contains " " %}
    <li>
    <a href="{{ page.url | relative_url }}">{{ page.title | escape }}</a>
    </li>
    {% endif %}
{% endfor %}
</ul>
