---
layout: media
title: Regrexit?
description: Do people really want to change their votes?
modified:
categories: blog main
share: true
excerpt:
tags: politics code
image:
  feature: /brexit/brexit-header.png
  teaser: /brexit/brexit-teaser.png
  thumb: /brexit/brexit-thumb.png
ads: false
date: 2016-06-26
published: false
---

I've been seeing a lot of anecdotes about '[Bregret](http://www.independent.co.uk/news/uk/politics/brexit-anger-bregret-leave-voters-protest-vote-thought-uk-stay-in-eu-remain-win-a7102516.html)' and '[Regrexit](http://www.reuters.com/article/us-britain-eu-brexit-insight-idUSKCN0ZC103)' over the last few days.
Angry, worried Leave voters who thought that their vote "didn't count" and used their vote to protest against a government they do not believe in.
Voters who, in the cold light of Leave campaign U-turns, market crashes and the chilling reality of Brexit are calling for a do-over.
But are they really? Do these people represent an electoral change of heart?
Or are these stories of catastrophic idiocy being dredged up during the last, hopeful gasps of the Remain campaign?

## Brexit Fallout
It appears that Leave voters were swayed by the rhetoric of 'taking back control': regaining 'sovereignity' from brussels, and excluding immigrants from Britain. Many also cited extra funding for the NHS as a primary reason for voting Leave. The Leave campaign promises that fuelled these reasons have been rapidly evaporating in the few days since the referendum result:

- [Nigel Farage backtracked on NHS funding](http://www.independent.co.uk/news/uk/politics/eu-referendum-result-nigel-farage-nhs-pledge-disowns-350-million-pounds-a7099906.html) within just a few hours of the referendum result.
- Leave campaigners dropped jaws when admitting they had ['no plan' for a Brexit](https://www.youtube.com/watch?v=HNe-yHr7uJc).
- Boris Johnson recently [painted a Brexit picture](http://www.telegraph.co.uk/news/2016/06/26/i-cannot-stress-too-much-that-britain-is-part-of-europe--and-alw/) that, depending on how kind the EU wants to be to us, would probably require open borders, sacrificing sovereignity, and possibly even losing the pound (i.e. the Norway option).
- Various [grim economic predictions](http://www.nytimes.com/2016/06/25/upshot/how-brexit-will-affect-the-global-economy-now-and-later.html) concerning Britains global future are appearing around the world.
- Nicola Sturgeon has already begun drawing up legislation for a [second Scottish independence referendum](http://www.independent.co.uk/news/uk/politics/brexit-news-results-latest-nicola-sturgeon-second-scottish-independence-referendum-eu-referendum-a7100466.html), and is considering [vetoing Brexit entirely](http://www.independent.co.uk/news/uk/politics/nicola-sturgeon-veto-brexit-scottish-parliament-eu-referendum-scotland-latest-a7104046.html).
- Many in Northern Ireland are [calling for a United Ireland](http://www.independent.co.uk/news/uk/politics/brexit-northern-ireland-eu-referendum-result-latest-live-border-poll-united-martin-mcguinness-a7099276.html), rather than adopting the gruelling border controls that Brexit might bring.

It's a shambles.

Given that the Leave voters have had a few days to [try and understand what the EU is and does](https://twitter.com/googletrends?lang=en), and the wholesale abandonment of Leave campaign promises, surely many Leave supporters must be reconsidering their choice?

## A Change of Heart?
A [petition calling for the government to hold a second referendum](https://petition.parliament.uk/petitions/131215) has amassed over 3.6 million signatures, and certainly appears to suggest that many in Britain are not happy with the Brexit result. But who are the 3.6 million? Is this petition fuelled by the righteous rage of disenfranchised Remain voters, or the Bregretful U-turns of Leave voters? Does it reflect a real change in public opinion, or the vocal complaints of the 48%?

We can start to answer these questions by looking at how each Local Authority voted in the referendum, and comparing tht to what % of their electorates has since signed the petition:

<figure class="figcap" >
  <img src="/images/brexit/EUref-popn-20160627-0112.png" width="100%">
  <figcaption>Who's signing the petition? In each Local Authority there are clear relationships between the % of the electorate that voted Remain, and the % that have signed the petition. The petition is also much more popular in England (11.2% signed) than the rest of the UK (7.5% in wales and 5% in Scotland and Northern Ireland. </figcaption>
</figure>

The first clear trend in this data is the relationship between % Remain and % Signatures in each Local Authority. A higher % of the electorate have signed the petition in areas where there was also a high % Remain vote. This is a clear sign of the 'complaining 48%' - people are more likely to sign the petition if the referendum result was not what they wanted. No surprises there.

A second pattern here is that a much greater propotion of English and Welsh voters have signed the petition than Scottish or N Irish. Specifically, 10.6% of the English electorate, and 7.4% of the Welsh have signed the petition, as opposed to 5% in Scotland and N Ireland. Not only have a lower proportion of the Scottish and Welsh electorate signed the petition, but the % Signed is lower than you'd expect based on the general positive relationship between % Remain and % Signed seen in the English and Welsh Local Authorities. This is interesting, because the majority of the Leave vote came from England and Wales. Is this a sign of Bregret?

To examine this pattern more closely, we must better understand the trends in the data:

<figure class="figcap" >
  <img src="/images/brexit/EUref-popn-trends-20160627-0112.png" width="100%">
  <figcaption>The lines here are population-weighted least squares regressions through the data, with 99% shaded confidence intervals. These lines describe the underlying relationship between % Remain and % Signed in each region of the UK. Any point above the line represents a Local Authority where more people than expected are signing the petition, based on how that area voted in the referendum.</figcaption>
</figure>

A few things to note here:

1. The slope of the underlying relationship between % Remain vote and % Signatures is similar in all regions.
2. In Scotland, the data is fairly evenly distributed about the line of best fit.
3. In England, there are many more points *above* the line of best fit than below it, and the high points are much further from the line than the low points. This pattern is present in Wales, though less clearly.
4. Most of the points that fall above the line of best fit are low-pupulation constituencies (i.e. non-urban).

What does this mean? First, the similar slope in the underlying trend in the data in all regions suggests that % Remain votes do a fairly good job of predicting the % of the electorate that signed the petition. Therefore, if a Local Authorities falls above the line of best fit, it tells us that a higher proportion of people in that area are signing the petition, based on how the rest of the country is behaving. There are all sorts of reasons that could cause this pattern, one of which is a change of heart. The fact that this pattern is seen only in England and Wales, particularly in areas with a moajority Leave vote, may suggest that people in these regions are unhappy with the result they voted for.

However, there are numerous caveats to this simplistic analysis (see below).

## Back of the Envelope
To take the arm-waving to *dangerous* levels, we can use this simple description of the data to estimate how big a difference these regions might make in a second referendum.

If we assume that there are no confounding factors in the data (see caveats!), we can use the line of best fit to identify Local Authorities where people are signing the petition more than predicted by the %Remain vote in the area. For our purposes, we will consider all points above the 99% confidence interval of the line of best fit. To make our calculation a little more conservative, we will also only consider Local Authorities that voted Leave in the referendum.

<figure class="figcap" >
  <img src="/images/brexit/EUref-popn-trends-sub-20160627-0112.png" width="100%">
  <figcaption>The Local Authorities we're considering in our back-of-the-envelope arm-waving. All the points that fall above the 99% confidence interval of the line of best fit, and voted to Leave in the referendum.</figcaption>
</figure>

Using our line of best fit, we can predict the % Remain vote in each Local Authority in a second referendum based on the % of the electorate in each area that signed the petition.
Because we know the size of the electorate in each Local Authority, and the % Remain in the last referendum, we can calculate the number of votes that would shift towards Remain: (Predicted % Remain - Previous % Remain) / 100 * Electorate Size

This predicts a shift in favour of Remain of 1,355,874 votes. The winning margin for Leave was 1,269,501.

Far too close to call, particularly given the complexity of the data, and the simplicity of my model and assumptions.

## The Data
The datasets used here come from the [EU referendum ballot counts](http://www.electoralcommission.org.uk/find-information-by-subject/elections-and-referendums/upcoming-elections-and-referendums/eu-referendum/electorate-and-count-information), and [information on the signees of the 'do-over' petition](https://petition.parliament.uk/petitions/131215.json). The ballot data contain the size of the electorate, and the percentage that voted to leave in each of the 382 Local Authorities that reported the election (also the percentage of spoiled ballots, and some other interesting tidbits). The petition data contains the number of signees from each of the 650 Parliamentary Constituencies in the UK.

Python code for the entire analysis and links to the raw data sets are available [here]({% post_url 2016-06-27-bregret-analysis %}).

## Caveats
The data considered here is extremely complex, and is likely to be influenced by numerous socio-economic factors that are impossible to account for. In particular:

- People signing an online petition are likely to be politically active and computer literate. This is likely to bias sampling towards younger populations, which are known to favour a Remain vote.



