---
layout: article
title: "It knows where I live: the code."
date: 2013-12-20T12:00:00
modified:
excerpt:
categories: code
tags: code
image:
  feature:
  teaser: code_teaser.png
  thumb: code_teaser.png
ads: false
---

######NB for complete beginners:

- [Install R](http://www.r-project.org/) – follow the ‘Getting Started’ instructions.
- Install [RStudio](http://www.rstudio.com/), an excellent, free interactive environment for R.
- In RStudio, install the packages you need to run the scripts below, by copying and pasting the following:

{% highlight R linenos %}
install.packages(“ggmap”)
install.packages(“ggplot2″)
install.packages(“jsonlite”)
install.packages(“plyr”)
{% endhighlight %}

######And on to the good stuff...
1) Download the .json location data from Google Takeout.

2) Import the data into R, get it into a useful format, and plot it.  I haven’t included the code for the animated gif, as it gets a bit more involved (and to be perfectly honest, my code get’s all messy and horrible because I got tired :P). In short: make a function to prude a series of images, then stitch them all into a .gif using ImageMagick (not in R). If you’re interested, just ask.

{% highlight R linenos %}
# Load in the raw data from the JSON file ----------------------------------------------------
require(jsonlite)
require(plyr)

raw = fromJSON('/Path/To/LocationHistory.json')

# Get the 'locations' part of the list
locs = raw$locations

# these are all the columns that it contains...
names(locs)
# they're in various formats...
lapply(locs,class)

# Get columns into useful formats -----------------------------------------

ldf = data.frame(t=rep(0,nrow(locs)))

# time is in POSIX * 1000 (milliseconds) format, convert it to useful scale...
ldf$t = as.numeric(locs$timestampMs)/1000
class(ldf$t) = 'POSIXct'

# lat/lon are xE7, convert them to usable numbers...
ldf$lat = as.numeric(locs$latitudeE7/1E7)
ldf$lon = as.numeric(locs$longitudeE7/1E7)

# Accuracy doesn't need changing.
ldf$accuracy = locs$accuracy

# Activity guesses (it can tell when you're on a bike?!) are in a list... we can unpack these lists to get the most likely activity for each location (takes a while, depending on the size of your dataset).

# get the most likely activity type and confidence for each time point.
act = laply(locs$activitys, function(f) {
 if(is.null(f[[1]])) data.frame(activity=NA,confidence=NA,stringsAsFactors=F) else data.frame(activity=f[[2]][[1]][[1]][1],confidence=f[[2]][[1]][[2]][1],stringsAsFactors=F)
},.progress="text")

# combine activity data with the main dataset
ldf$activity = as.character(act[,1])
ldf$confidence = as.numeric(act[,2])

# Velocity, altitude and heading need no alteration:
ldf$velocity = locs$velocity
ldf$altitude = locs$altitude
ldf$heading = locs$heading

# We now have a data.frame 'ldf', which contains all the location data google has on us!

# And plot it... ----------------------------------------

require(ggplot2)
require(ggmap)

EU = get_map(c(0,48),5,source='google')

ggmap(EU) + geom_point(data=ldf,aes(lon,lat),colour='red')

# Let's just look at the UK stuff...

# first, define a quick funciton to clip the data to the current map
mapclip = function(df,map) {
 coord = attr(map,'bb')
 return(subset(df,lon>coord$ll.lon & lon<coord$ur.lon & lat>coord$ll.lat & lat<coord$ur.lat))
}

UK = get_map('Oxford, UK',7,scale=2)
ldf.uk = mapclip(ldf,UK)

ggmap(UK) + geom_point(data=ldf.uk,aes(x=lon,y=lat),colour='red',size=2)

# Let's have a look at Cambridge...

cam = get_map('Cambridge, UK',13,scale=4)
clip = coord_map(projection='mercator',xlim=c(0.1,0.15),ylim=c(52.19,52.23))
ldf.cam = mapclip(ldf,cam)
ggmap(cam) + geom_point(data=ldf.cam,aes(x=lon,y=lat),colour='red',size=1,alpha=0.5) + clip

# And what do I do in cambridge?
ldf.cam.act = subset(ldf.cam,activity %in% c("inVehicle","onBicycle","onFoot","still"))
ggmap(cam) + geom_point(data=ldf.cam.act,aes(x=lon,y=lat,colour=activity),size=2) + scale_colour_manual(values=c('red','orange','yellow','black')) + clip

# A little closer? And add in the non-active markers, to get more of a trend.
camclose = get_map('Cambridge, UK',15,scale=4)
ldf.cam.act.close = mapclip(ldf.cam.act,camclose)
ldf.cam.close = mapclip(ldf,camclose)
ggmap(camclose) + geom_point(data=ldf.cam.close,aes(lon,lat),colour='purple',alpha=0.6) + geom_point(data=ldf.cam.act.close,aes(x=lon,y=lat,colour=activity),size=2) + scale_colour_manual(values=c('red','orange','yellow','black'))
{% endhighlight %}
