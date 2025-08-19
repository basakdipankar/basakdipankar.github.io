---
layout: page
title: Blog
permalink: /blog/
---

<div class="blog-container">
  <div class="blog-main">
    <h1>Blog</h1>
    
    {% for post in site.posts %}
      <div class="blog-post">
        <h2><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
        <p class="post-meta">{{ post.date | date: "%B %d, %Y" }} . 
        {% for category in post.categories %}
          <a href="/tags/#{{ category | slugify }}">{{ category | replace: '-', ' ' | capitalize }}</a>{% unless forloop.last %}, {% endunless %}
        {% endfor %}
        </p>
        {% if post.excerpt %}
          <p class="post-excerpt">{{ post.excerpt | strip_html | truncatewords: 30 }}</p>
        {% endif %}
      </div>
    {% endfor %}
  </div>
  
  <div class="blog-sidebar">
    <div class="sidebar-section">
      <h3>Blog Categories</h3>
      <ul class="category-list">
        {% assign categories = site.categories | sort %}
        {% for category in categories %}
          <li><a href="/tags/#{{ category[0] | slugify }}">{{ category[0] | replace: '-', ' ' | capitalize }}</a> ({{ category[1] | size }})</li>
        {% endfor %}
      </ul>
    </div>
    
    <div class="sidebar-section">
      <h3>Connect</h3>
      <ul class="connect-list">
        <li><a href="https://www.linkedin.com/in/dipankar-basak/">LinkedIn</a></li>
        <li><a href="https://github.com/basakdipankar">GitHub</a></li>
        <li><a href="mailto:dbasak2013@gmail.com">Email</a></li>
      </ul>
    </div>
  </div>
</div>

<div class="blog-footer">
  <p><a href="https://www.linkedin.com/in/dipankar-basak/">LinkedIn</a> | <a href="https://github.com/basakdipankar">GitHub</a> | <a href="mailto:dbasak2013@gmail.com">Email</a></p>
  <p>© 2024 <a href="/">Dipankar Basak</a>. All rights reserved.</p>
</div>
