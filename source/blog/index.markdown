---
layout: blog
title: Home
section: Home

feed: atom.xml
keywords: blog
---

Ng Cheuk-fung's Personal Blog
=============================

Where I record part of my ideas, thoughts and notes.

A complete archive of [past][] posts is available via the link at the
top of the page and a [feed][] of the most recent posts is also
available.

[![Feed icon](/files/css/feed-icon-14x14.png){:title="Atom feed of recent posts" .right}][feed]

[past]: past.html
[feed]: atom.xml

Recent Posts
------------

{% for post in site.categories.blog limit:5 %}
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
