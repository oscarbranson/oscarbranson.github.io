---
layout: archive
title:
date:
modified:
excerpt:
image:
  feature:
  teaser:
  thumb:
ads: false
---

<!-- Map edit link: https://www.google.com/maps/d/u/0/edit?hl=en&authuser=0&mid=ze_XPNXVoqZ8.kWuRmuZxNqX4 -->
<!-- Strava GPX to KML converter: http://www.gpsvisualizer.com/map_input?form=googleearth -->

<embed src="https://www.google.com/maps/d/u/0/embed?mid=ze_XPNXVoqZ8.kWuRmuZxNqX4" width="640" height="420">

<table style="width:100%">
    <col width="">
    <col width="300">
    <tr>
        <th><h2>Adventures</h2></th>
        <th></th>
    </tr>
    <tr>
        <td>
            <div class="tiles">
                {% for post in site.categories.travel %}
                    {% include post-grid.html %}
                {% endfor %}
            </div>
        </td>
        <td>
            <iframe height='454' width='300' frameborder='0' allowtransparency='true' scrolling='no' src='https://www.strava.com/athletes/974683/latest-rides/578203f9f16fcdba02ff41376348c87706492fa1'></iframe>
        </td>
    </tr>
</table>
