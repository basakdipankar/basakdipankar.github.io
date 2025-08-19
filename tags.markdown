---
layout: page
title: Tags
permalink: /tags/
---

# Blog Tags

Browse posts by category:

{% assign categories = site.categories | sort %}
{% for category in categories %}
  <h3>{{ category[0] | replace: '-', ' ' | capitalize }}</h3>
  <ul>
    {% for post in category[1] %}
      <li>
        <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
        <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>
      </li>
    {% endfor %}
  </ul>
{% endfor %}

---

## All Categories

{% for category in site.categories %}
  <span class="tag">
    <a href="#{{ category[0] | slugify }}">{{ category[0] | replace: '-', ' ' | capitalize }}</a>
    ({{ category[1] | size }})
  </span>
{% endfor %}

<style>
.tag {
  display: inline-block;
  background: #f1f1f1;
  padding: 4px 8px;
  margin: 2px;
  border-radius: 3px;
  font-size: 0.9em;
}

.tag a {
  text-decoration: none;
  color: #333;
}

.tag:hover {
  background: #e1e1e1;
}
</style>
