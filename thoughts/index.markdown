---
layout: thoughts
title: Home
section: Home

feed: atom.xml
keywords: Thoughts, Operating Systems, Compilers, Languages
---

Thoughts on The Fields of My Interest
=====================================

The fields of my interest including Operating Systems, Compilers,
Languages, etc.

A complete archive of [past](past.html) posts, is available via the link
above.

A [feed][] of the most recent posts is also available.
[![Feed icon](/files/css/feed-icon-14x14.png){:title="Atom feed of recent posts" .right}][feed]

[feed]: /thoughts/atom.xml

Recent Posts
------------

{% for post in site.categories.thoughts limit:5 %}
<div class="section list">
  <h1>{{ post.date | date_to_string }}</h1>
  <p class="line">
  <a class="title" href="{{ post.url }}">{{ post.title }}</a>
  <a class="comments" href="{{ post.url }}#disqus_thread">View Comments</a>
  </p>
  <p class="excerpt">{{ post.excerpt }}</p>
</div>
{% endfor %}

<p>
<a href="past.html">Older Posts &rarr;</a>
</p>
