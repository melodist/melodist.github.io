---
layout: default
title: melodist Dev Records
nav_order: 1
description: "Intro Page"
permalink: /
---
# Recent Posts
{% assign sorted-posts = site.posts | sort: 'post_date' %}
{%- for post in sorted-posts limit: 10 -%}
{% unless post.tags contains "UF" %}
  <li><a href="{{post.url}}">{{ post.title }}</a></li>
{% endunless %}
{%- endfor -%}