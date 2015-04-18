---
layout: article
title: "Publications"
date:
modified:
excerpt:
image:
  feature:
  teaser:
  thumb:
ads: false
---

<!-- {% assign count = '0' %}
{% assign idx = '0' %}
{% for post in site.categories.papers reversed %}
        {% capture count %}{{ count | plus: '1' }}{% endcapture %}
        {% if post.url == page.url %}
            {% capture idx %}{{count}}{% endcapture %}
        {% endif %}
{% endfor %} -->

**Problem with years:** Where does 2014 come from!?<br>
{% for post in site.categories.papers %}
  {% capture this_year %}{{ post.date | date: "%Y" }}{% endcapture %}
  {% capture next_year %}{{ post.previous.date | date: "%Y" }}{% endcapture %}
  {{ this_year }}, {{ next_year}}<br>
{% endfor %}

<ul class="unstyled-list">
<!-- {% assign count = '0' %} -->

{% for post in site.categories.papers %}
  {% capture this_year %}{{ post.date | date: "%Y" }}{% endcapture %}
  {% capture next_year %}{{ post.previous.date | date: "%Y" }}{% endcapture %}

    {% if forloop.first %}
    <h2 id="{{ this_year }}-ref">{{this_year}}</h2>
    <ul>
    {% endif %}

    <li>
        {% if post.openaccess %}<a href="{{ post.purl }}" target="_blank">{% else %}<a href="{{ site.url }}{{ post.url }}">{% endif %}
            {% if post.openaccess %}<img style="float: left" class="oabadge" alt="oabadge" src="/images/open_access.png" width="45">{% endif %}
            <img src="{{ site.url }}/images/{{ post.image.thumb }}" class="preview" alt="preview image">
            <b>{% if post.short-title %}{{ post.short-title }}{% else %}{{ post.title }}{% endif %}</b> {% if post.author %}{{ post.author }}{% endif %}{% if post.year %} ({{ post.year }}){% endif %}{% if post.journal %}<i> {{ post.journal }}</i>{% endif %}{% if post.vol %}<b> {{ post.vol }}</b>{% endif %} {% if post.pg %}, {{ post.pg }}</i>{% endif %}
            <!-- <p><i> {{ post.description }}</i> </p> -->
        </a>
    </li>
    {% if forloop.last %}
    </ul>
    {% else %}
        {% if this_year != next_year %}
        </ul>
        <h2 id="{{ next_year }}-ref">{{next_year}}</h2>
        <ul>
        {% endif %}
    {% endif %}
{% endfor %}
</ul>
