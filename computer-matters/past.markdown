---
layout: computer-matters
title: Archives
section: Past

feed: atom.xml
keywords: Computer, Software, Coding, Programming
---

Archives
========

This is the complete archive of posts from _[Computer Matters](/computer-matters)_
in reverse chronological order.

{% for post in site.categories.computer-matters %}
<div class="section list">
  <h1>{{ post.date | date_to_string }}</h1>
  <p class="line">
  <a class="title" href="{{ post.url }}">{{ post.title }}</a>
  <a class="comments" href="{{ post.url }}#disqus_thread">View Comments</a>
  </p>
  <p class="excerpt">{{ post.excerpt }}</p>
</div>
{% endfor %}
