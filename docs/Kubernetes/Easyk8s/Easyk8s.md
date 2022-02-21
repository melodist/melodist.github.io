---
layout: default
title: 쉽게 배우는 쿠버네티스
nav_order: 1
parent: Kubernetes
permalink: docs/Kubernetes/Easyk8s
---
[쉽게 배우는 쿠버네티스](https://inf.run/93ym)

{% for post in site.tags.Easyk8s reversed %}
{% unless post.tags contains "UF" %}
<li><a href="{{ post.url }}">{{ post.title }}</a></li>
{% endunless %}
{% endfor %}