---
layout: page
title: Fedeoo博客
tagline: Supporting tagline
---
{% include JB/setup %}

所有文章列表.

<ul class="posts">
  {% for post in site.posts %}
    <li>
        <span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a><br/>
        {% if post.layout %}
            <span class="excerpt">{{ post.excerpt }}</span>
        {% endif %}
    </li>
  {% endfor %}
</ul>

## To-Do

