---
title: Blog
layout: layout
---

# Articles
{% for post in site.posts %}
<h3>{{post.title}}</h3>
<div>{{post.excerpt}}</div>
<a href="{{post.url}}">Read more</a> - {{post.date | date_to_string}}
{% endfor %}
