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

<ul class="unstyled-list">

<!-- Work out peculiarities of OA badge not moving (inspect element in chrome to see the styles) -->
<!-- Add google scholar links to papers? -->

{% for post in site.categories.papers %}
    {% capture this_year %}{{ post.year }}{% endcapture %}

    {% if forloop.first %}
      {% capture first_year %}{{ post.year }}{% endcapture %}
      <h2 id="{{ this_year }}-ref">{{this_year}}</h2>
      <hr class="papers-hr">
      <ul>
    {% endif %}

    {% if this_year != first_year %}
      </ul>
        <h2 id="{{ next_year }}-ref">{{this_year}}</h2>
        <hr class="papers-hr">
        {% capture first_year %}{{ post.year }}{% endcapture %}
      <ul>
    {% endif %}

    <li>
        {% if post.openaccess %}<a href="{{ post.purl }}" target="_blank">{% else %}<a href="{{ site.url }}{{ post.url }}">{% endif %}
            <div class="papers-preview">
              {% if post.openaccess %}<img class="oabadge" alt="oabadge" src="/images/open_access.png">{% endif %}
              <img src="{{ site.url }}/images/{{ post.image.thumb }}" class="preview" alt="preview">
            </div>
            <div class="papers">
              <h1>{% if post.short-title %}{{ post.short-title }}{% else %}{{ post.title }}{% endif %}</h1>
              <p>{% if post.author %}{{ post.author }}{% endif %}{% if post.year %} ({{ post.year }}){% endif %}{% if post.journal %}<i> {{ post.journal }}</i>{% endif %}{% if post.vol %}<b> {{ post.vol }}</b>{% endif %} {% if post.pg %}, {{ post.pg }}</i>{% endif %}</p>
            </div>
            <!-- <p><i> {{ post.description }}</i> </p> -->
        </a>
    </li>

    {% if forloop.last %}
      </ul>
    {% endif %}

{% endfor %}
</ul>
