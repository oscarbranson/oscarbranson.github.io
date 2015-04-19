---
layout: media
title: "It knows where I live?"
modified:
categories: blog main code
share: true
excerpt:
tags: science code
image:
  feature: /it-knows-where-i-live/map-header.png
  teaser: /it-knows-where-i-live/it-knows-teaser.jpeg
  thumb: /it-knows-where-i-live/it-knows-teaser.jpeg
ads: false
date: 2013-12-20T21:00:00
---

I recently came across an intriguing post by fellow Jesuan [James Black](https://epijim.uk/), about making maps out of Google location data. This is the result.

Google is infamous for its data-harvesting practices. It really is quite amazing how much it knows about you. If you have a smartphone, the chances are Google knows almost exactly where you are, almost all of the time.

There is a service called ‘[Google Takeout](https://accounts.google.com/ServiceLogin?service=backup&passive=1209600&)‘, which allows you to download all the data Google has collected about you. I did this. If you have clicked ‘yes’ at all the appropriate moments, Google has a record of: you web browser bookmarks, your contacts, your ‘Hangouts’ (text messages), your YouTube history, your emails, your Google documents, your Google+ social history and photographs, your calendar, and your location. It’s not doing anything wrong here – I’ve agreed to each of these pieces of information being stored by Google, usually by skipping over long ‘terms and conditions’ blurbs to click ‘yes’ and get to where I want to be.

Google uses all this information to ‘improve your Google experience’ – offering you advice about things you might be interested in (i.e. adverts) based on your interests, and where you are. Clever. So with this wealth of data at my fingertips, I thought I’d have a look at what I’d been up to.

The ‘location’ data that I got from google is an astonishing *152379 measurements… Since the start of November?!!* It contains regular updates of latitude, longitude (usually accurate to around 25m) and altitude, an estimate of which direction and how fast I’m moving (if I’m moving), and (most amazingly) a guess at whether I’m sitting still, walking, cycling, or in a vehicle. Incredible. It only had data from the last few months (since I got a shiny new phone), but… well… wow! And quite scary.

So after half an hour of fiddling around in R (a statistical computing language), this is what I got:

<figure class="figcap" >
  <img src="/images/it-knows-where-i-live/overview.jpeg" width="100%">
  <figcaption>Where I’ve been since 20th March 2013, according to Google. I’t definitely missing a few spots, but it’s worryingly comprehensive. A conference in Barcelona, and some trips around the UK.</figcaption>
</figure>

To get the full impact of this kind of data, we have to look a little closer: let’s zoom in to Cambridge, where I spend most of my time.

<figure class="figcap" >
  <img src="/images/it-knows-where-i-live/cambridge.png" width="100%">
  <figcaption>My life in Cambridge… I can see home, work and all the places I spend time around town. Red dots are records of my location. Amazing.</figcaption>
</figure>

And a whole other layer of activity can be revealed by looking at what I was doing in all of these places:

<figure class="figcap" >
  <img src="/images/it-knows-where-i-live/cambridge_movement.png" width="100%">
  <figcaption>Things get really clear here: you can see where I spend most of my time staying still (black dots) is at home, and around my department. The spread of points gets a bit wider here, partly because I move around the department quite a bit, but also I imagine because the walls are pretty thick, which reduces the gps accuracy. My commute to and from work gets really obvious, and I can tie each of the black blobs to an event.</figcaption>
</figure>

And putting this all together:

<figure class="figcap" >
  <img src="/images/it-knows-where-i-live/closeup.png" width="100%">
  <figcaption>Purple dots are ‘uncategorised’ location markers – i.e. Google wasn’t sure what I was doing. In this map, I can link each of the black ‘still’ blobs to specific events: a dinner and lunch at Jesus College, a dinner at Clare college, a trip to the pub to celebrate a friend passing his PhD viva, dinner out with two sets of other friends, 3 pub visits, watching the fireworks, visiting friends in their houses, and doing my christmas shopping! You can get even more detail if you add time into the equation – you can see exactly when I was at all of these places.  And all of this is available for anyone in Google to look at… hmm…</figcaption>
</figure>

And finally, here is a day:

<figure class="figcap" >
  <img src="/images/it-knows-where-i-live/dec12.gif" width="100%">
  <figcaption>Follow the dot! I get to work, go to coffee around 11:20 (watch the dot move!), go out to get some lunch at 1:30pm, head back and eat it in the coffee room, then back to the office at around 2:20pm. At around 6pm I go briefly to the zoology department, then to The Mill pub to meet some friends. After the pub, I have dinner at Clare college, a drink in Clare MCR, then home via Magdelene St. You really can track my movements to the minute.</figcaption>
</figure>

I’m going to steer clear of the morals of this kind of information hoarding, and leave this as a fun, and revealing exercise. I have always been aware that Google have been keeping an eye on me, but the extent of it is staggering!

#####The Nitty-Gritty

I did all this using open source (i.e. free) software. If you're interested in seeing how I did it, take a look at [this post]({% post_url /code/2013-12-20-it-knows-where-i-live-code %}).

