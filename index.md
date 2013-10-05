---
layout: page
title: dhb
tagline:
---
{% include JB/setup %}

<h3>Hacks</h3>
<ul class="hacks">
  <p>Shorter projects that aren't likely to be maintained</p>
  {% for page in site.pages %}
    {% if page.type == "hack" %}
      <li><a href="{{ BASE_PATH }}{{ page.url }}">{{ page.title }}</a></li>
    {% endif %}
  {% endfor %}
</ul>

<h3>Projects</h3>
<ul class="projects">
  <p>Cleaner, longer running projects</p>
  {% for page in site.pages %}
    {% if page.type == "project" %}
      <li><a href="{{ BASE_PATH }}{{ page.url }}">{{ page.title }}</a></li>
    {% endif %}
  {% endfor %}
</ul>

<h3>Non Technical</h3>
<ul class="non-technical">
  <p>More prose like, but may involve some technical items, topically</p>
  {% for page in site.pages %}
    {% if page.type == "non-technical" %}
      <li><a href="{{ BASE_PATH }}{{ page.url }}">{{ page.title }}</a></li>
    {% endif %}
  {% endfor %}
</ul>

<h3>All Posts</h3>
<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
