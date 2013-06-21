---
layout: page
title: dhb
tagline: 
---
{% include JB/setup %}

<p>Still working on setting up this site, mostly experimenting with Jekyll and JB</p>

<h3>Hacks</h3>
<ul class="hacks">
  {% for page in site.pages %}
    {% if page.type == "hack" %}
      <li><a href="{{ BASE_PATH }}{{ page.url }}">{{ page.title }}</a></li>
    {% endif %}
  {% endfor %}
</ul>

<h3>Projects</h3>
<ul class="projects">
  {% for page in site.pages %}
    {% if page.type == "project" %}
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
