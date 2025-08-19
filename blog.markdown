---
layout: page
title: Blog
permalink: /blog/
---

# Blog

{% for post in site.posts %}
  <h2><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
  <p class="post-meta">{{ post.date | date: "%B %d, %Y" }} . 
  {% for category in post.categories %}
    <a href="/tags/#{{ category | slugify }}">{{ category | replace: '-', ' ' | capitalize }}</a>{% unless forloop.last %}, {% endunless %}
  {% endfor %}
  </p>
  {% if post.excerpt %}
    <p>{{ post.excerpt | strip_html | truncatewords: 30 }}</p>
  {% endif %}
  <hr>
{% endfor %}

---

### Blog Categories

{% assign categories = site.categories | sort %}
{% for category in categories %}
* [{{ category[0] | replace: '-', ' ' | capitalize }}](/tags/#{{ category[0] | slugify }}) ({{ category[1] | size }})
{% endfor %}

### Connect

* [LinkedIn](https://www.linkedin.com/in/dipankar-basak/)
* [GitHub](https://github.com/basakdipankar)
* [Email](mailto:dbasak2013@gmail.com)
