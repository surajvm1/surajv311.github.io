---
layout: page
title: Articles
---

<p class="message">
  Includes, blogs I wrote in distant past, present day musings and learnings.
</p>

### Articles
<ul>
  {% for post in site.posts %}
    {% if post.category == "articles" %}
      <li>
        <a href="{{ post.url }}">{{ post.title }}</a> - <small>{{ post.date | date_to_string }}</small>
      </li>
    {% endif %}
  {% endfor %}
</ul>

### Musings
<ul>
  {% for post in site.posts %}
    {% if post.category == "musings" %}
      <li>
        <a href="{{ post.url }}">{{ post.title }}</a> - <small>{{ post.date | date_to_string }}</small>
      </li>
    {% endif %}
  {% endfor %}
</ul>

### Learnings
<ul>
  {% for post in site.posts %}
    {% if post.category == "learnings" %}
      <li>
        <a href="{{ post.url }}">{{ post.title }}</a> - <small>{{ post.date | date_to_string }}</small>
      </li>
    {% endif %}
  {% endfor %}
</ul>
