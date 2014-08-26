---
layout: page
title : Archive
header : Post Archive
permalink: /archive/
group: navigation
---
{% include JB/setup %}

<div>
{% assign posts_collate = site.posts %}
{% include JB/posts_collate %}
</div>
