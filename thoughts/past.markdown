---
layout: thoughts
title: Archives
section: Past

feed: atom.xml
keywords: Thoughts, Operating Systems, Compilers, Languages,
          Data structures, Algorithms, Design
---

Archives
========

This is the complete archive of posts from _[Thoughts](/thoughts)_
in reverse chronological order.

{% for post in site.categories.thoughts %}
<div class="section list">
  <h1>{{ post.date | date_to_string }}</h1>
  <p class="line">
  <a class="title" href="{{ post.url }}">{{ post.title }}</a>
  <a class="comments" href="{{ post.url }}#disqus_thread">View Comments</a>
  </p>
  <p class="excerpt">{{ post.excerpt }}</p>
</div>
{% endfor %}
