---
layout: name
title: Archives
section: Past

feed: ../atom.xml
keywords: Algorithms, Data Structures, Design, Thoughts, Operating
          Systems, Compilers, Languages, Misc, Life
---

Archives
========

This is the complete archive of posts on my entire blog in reverse
chronological order.

{% for post in site.posts %}
<div class="section list">
  <h1>{{ post.date | date_to_string }}</h1>
  <p class="line">
  <a class="title" href="{{ post.url }}">{{ post.title }}</a>
  <a class="comments" href="{{ post.url }}#disqus_thread">View Comments</a>
  </p>
  <p class="excerpt">{{ post.excerpt }}</p>
</div>
{% endfor %}
