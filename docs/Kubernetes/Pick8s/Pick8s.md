---
layout: default
title: 그림으로 배우는 쿠버네티스
nav_order: 2
parent: Kubernetes
permalink: docs/Kubernetes/Pick8s
---
[그림으로 배우는 쿠버네티스](https://inf.run/GMWj)

{% for post in site.tags.Pick8s reversed %}
{% unless post.tags contains "UF" %}
<li><a href="{{ post.url }}">{{ post.title }}</a></li>
{% endunless %}
{% endfor %}