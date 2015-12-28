---
layout: default
title: "分类:Categories"
---

<div>
{% for cat in site.categories %}
    <h2>{{ cat[0] }}({{ cat[1].size }})</h2>
    <ul class="category-list">
   {% for post in cat[1] %} 
   <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href ="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
    </ul>
{% endfor %}
</div>
