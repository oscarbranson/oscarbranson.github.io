---
layout: archive
title: "Code"
date:
modified:
excerpt:
image:
  feature:
  teaser:
  thumb:
ads: false
gitflag: true
---

<div class="tiles">
{% for post in site.categories.code %}
  {% include post-grid.html %}
{% endfor %}
</div><!-- /.tiles -->
