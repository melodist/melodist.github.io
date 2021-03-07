---
layout: default
title: MSA
nav_order: 3
---

MSA(MicroService Architecture) 관련 학습 내용

{% for post in site.tags.MSA reversed %}
{% unless post.tags contains "UF" %}
  <li><a href="{{ post.url }}">{{ post.title }}</a></li>
{% endunless %}
{% endfor %}