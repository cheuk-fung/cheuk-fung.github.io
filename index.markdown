---
layout: name
title: Home
section: Home

feed: atom.xml
---

<img class='inset right' src='/images/conan.jpg' alt="Picture of {{ site.author }}'s favorite character." width='120px' />

Welcome
=======

I'm Ng Cheuk-fung.

> Do not pray for easy lives.  
> Pray to be stronger men!  
> Do not pray for tasks equal to your powers.  
> Pray for powers equal to your tasks!  
> Then the doing of your work shall be no miracle.  
> But you shall be a miracle.

+-- {.section}
Research
========
It's the mysterious field I'm currently pursuing.
=--

+-- {.section}
Blog
=====
I record part of my ideas, thoughts and notes here.

A complete archive of [past][] posts is available via the link at the
top of the page and a [feed][] of the most recent posts combining all
the following categories is also available.
[![Feed icon](/files/css/feed-icon-14x14.png){:title="Atom feed of recent posts" .right}][feed]

[past]: /past
[feed]: /atom.xml
=--

+-- {.section}
[Thoughts](/thoughts)
---------------------
Thoughts on my interesting fields, including Operating Systems,
Compilers, Languages, etc.
{% for post in site.categories.thoughts limit:5 %}
<ul class="compact recent">
<li>
	<a href="{{ post.url }}" title="{{ post.excerpt }}">{{ post.title }}</a>
	<span class="date">{{ post.date | date_to_string }}</span>
</li>
</ul>
{% endfor %}
=--

+-- {.section}
[Algorithms](/algorithms)
-------------------------
Notes on algorithm design and data structures.
{% for post in site.categories.algorithms limit:5 %}
<ul class="compact recent">
<li>
	<a href="{{ post.url }}" title="{{ post.excerpt }}">{{ post.title }}</a>
	<span class="date">{{ post.date | date_to_string }}</span>
</li>
</ul>
{% endfor %}
=--

+-- {.section}
[Misc](/misc)
-------------
Anything else that I want to write.
{% for post in site.categories.misc limit:5 %}
<ul class="compact recent">
<li>
	<a href="{{ post.url }}" title="{{ post.excerpt }}">{{ post.title }}</a>
	<span class="date">{{ post.date | date_to_string }}</span>
</li>
</ul>
{% endfor %}
=--

+-- {.section}
Licence
=======
Unless specified otherwise, all design and content within this site is 
licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/">Creative Commons Attribution-Noncommercial-Share Alike 3.0 Unported License</a>.
=--
