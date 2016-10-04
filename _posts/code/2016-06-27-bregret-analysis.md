---
layout: article
title: "Bregret: The Code"
date: 2016-06-20
modified:
excerpt:
categories: code
tags: code
image:
  feature:
  teaser: /brexit/brexit-teaser.png
  thumb: /brexit/brexit-thumb.png
ads: false
comments: true
published: false
---



{% highlight python linenos %}
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

import json
import urllib
import time

from scipy.optimize import curve_fit

%matplotlib inline

def intervals(x, y, f, p, xn=None, interval_type='confidence', conflevel=0.95):
    """
    General function to calculate the confidence or
    prediction interval for a fitted dataset using
    equations laid out here:
    http://www.jerrydallal.com/LHSP/slr.htm

    Parameters:
        x, y: array-like
            raw data arrays
        f: function
            the fit function
        p: array-like
            the fit parameters
        xn: array-like
            the x-range over which to return the interval
            if None,
            returns a sequence over the original x range,
            with n=100.
        conflevel: float
            confidence level of prediction (default = 0.95)
        interval_type: string
            'confidence' returns confidence interval
            'prediction' returns prediction interval

    Returns:
        (xn, y, confidence_upper_bound, confidence_lower_bound)
    """
    import numpy as np
    from scipy.stats import t

    # set up calculated parameters
    alpha = 1. - conflevel  # significance level
    n = x.size  # data sample size

    if xn is None:
        xn = np.linspace(x.min(), x.max(), 100)

    # calculate predicted values
    yp = f(x, *p)  # on original x-scale
    ynp = f(xn, *p)  # on new x-scale

    # calculate standard error of estimate
    # (http://onlinestatbook.com/2/regression/accuracy.html)
    Se = (np.sum((y - yp)**2) / (n - 2))**.5

    # calculate quantile of Student's t distribution
    # for p = 1 - alpha/2 (UNSURE WHY!)
    q = t.ppf(1. - alpha / 2, n - 2)

    # distance from data centre, for formula see
    # see http://www.jerrydallal.com/LHSP/slr.htm
    dx = (xn - x.mean())**2 / np.sum((xn - xn.mean())**2)

    # calculate distance from prediction line
    if interval_type is 'confidence':
        dy = q * Se * np.sqrt(1 / n + dx)
    if interval_type is 'prediction':
        dy = q * Se * np.sqrt(1 + 1 / n + dx)

    return xn, ynp, ynp + dy, ynp - dy
{% endhighlight %}

#### Load Petition & Vote Data

{% highlight python linenos %}
# get petition json data
petition_url = 'https://petition.parliament.uk/petitions/131215.json'
petition_json = urllib.request.urlopen(petition_url).read().decode('utf-8')

# load petition data from json file
petition = json.loads(petition_json)
sigs = pd.DataFrame(petition['data']['attributes']['signatures_by_constituency'])

# determine time data fetched
gettime = petition['data']['attributes']['updated_at'][:-1].replace('.','')
gettime = time.strptime(gettime, '%Y-%m-%dT%H:%M:%f')

# get total number of signatures
nsig = petition['data']['attributes']['signature_count']

# load vote data
votes_url = 'http://www.electoralcommission.org.uk/__data/assets/file/0014/212135/EU-referendum-result-data.csv'
votes = pd.read_csv(votes_url)
{% endhighlight %}

### Load Constituency -> Local Authority Lookup Table

Note: the votes are reported per Local Authority (LA), while the signatures are reported per Parliamentary Constituency (PC). Many LAs include multiple PCs. Therefore, we need to find out which LA each PC is in, and sum all those signatures to find out how many signatures come from each LA. We can do this using a handy lookup table provided by the Office of National Statistics, available [here](https://data.gov.uk/dataset/wards-2015-to-westminster-parliamentary-constituencies-2015-to-local-authority-districts-2015-u).

You must download and unzip the file into your working directory before running the next line.


{% highlight python linenos %}
# Wards (2015) to Westminster Parliamentary Constituencies (2015) to Local Authority Districts (2015) UK lookup
# https://data.gov.uk/dataset/wards-2015-to-westminster-parliamentary-constituencies-2015-to-local-authority-districts-2015-u
com = pd.read_csv('Wards_(2015)_to_Westminster_Parliamentary_Constituencies_(2015)_to_Local_Authority_Districts_(2015)_UK_lookup/WD15_PCON15_LAD15_UK_LU.csv')
{% endhighlight %}

### Count signatures for each Local Authority


{% highlight python linenos %}
# create an new 'combined' dataframe to compile the data
comb = votes.copy()
# create an empty signature_count column
comb['signature_count'] = 0

for n,s in comb.iterrows():
    # identify which constituencies are present within each LA
    v = com.loc[com.LAD15CD == s.Area_Code]
    # add the signatures for each PC to the 'signature_count' column of the combined dataset
    for c in v.PCON15CD.unique():
        comb.loc[comb.Area_Code == s.Area_Code,
                 'signature_count'] += sigs.loc[sigs.ons_code == c,
                                                'signature_count'].values[0]
{% endhighlight %}

### Identify N Ireland Counts


{% highlight python linenos %}
# N Ireland didn't assign correctly - do it manually.
comb.loc[[a[0] == 'N' for a in comb.Area_Code], 'signature_count'] = sigs.loc[[a[0] == 'N' for a in sigs.ons_code], 'signature_count'].sum()
{% endhighlight %}

### Identify Regions, Assign Colours, Calculate % Signed


{% highlight python linenos %}
# Identify which region each LA is in.
regions = {'E': 'England', 'S': 'Scotland', 'W': 'Wales', 'N': 'Northern Ireland'}
comb['Region'] = [regions[c[0]] for c in comb.Region_Code]

# Assign each region a colour
cols = {'E': 'red', 'S': 'orange', 'W': 'blue', 'N': 'purple'}
comb['cols'] = [cols[c[0]] for c in comb.Region_Code]

# Calculate the % of the electorate that has signed the petition
comb['Pct_Signed'] = 100 * comb.signature_count / comb.Electorate
{% endhighlight %}

### Examine Regions with high % signed.


{% highlight python linenos %}
# are there any regions with > 100% of electorate signatures?
comb.loc[comb.Pct_Signed > 100, ('Area', 'Electorate', 'signature_count', 'Pct_Signed')]
{% endhighlight %}




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Area</th>
      <th>Electorate</th>
      <th>signature_count</th>
      <th>Pct_Signed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>87</th>
      <td>City of London</td>
      <td>5987</td>
      <td>17475</td>
      <td>291.882412</td>
    </tr>
    <tr>
      <th>281</th>
      <td>Isles of Scilly</td>
      <td>1799</td>
      <td>5160</td>
      <td>286.826014</td>
    </tr>
  </tbody>
</table>
</div>



More people have signed the petition than are registered to vote in both the City of London and the Isles of Scilly. This could either be an artefact of how the petition assigns the location of British citizens living outside the UK, or be a sign of fraudulent activity, which is [already being investigated](http://www.bbc.com/news/uk-politics-eu-referendum-36634407). Either way, we will exclude these from analysis.


{% highlight python linenos %}
# exclude > 100%
comb = comb.loc[comb.Pct_Signed <= 100]

comb.loc[comb.Pct_Signed > 30, ('Area', 'Electorate', 'signature_count', 'Pct_Signed')]
{% endhighlight %}




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Area</th>
      <th>Electorate</th>
      <th>signature_count</th>
      <th>Pct_Signed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>6</th>
      <td>Cambridge</td>
      <td>80108</td>
      <td>29002</td>
      <td>36.203625</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Three Rivers</td>
      <td>67380</td>
      <td>28943</td>
      <td>42.954883</td>
    </tr>
    <tr>
      <th>106</th>
      <td>Kensington and Chelsea</td>
      <td>83042</td>
      <td>32532</td>
      <td>39.175357</td>
    </tr>
    <tr>
      <th>210</th>
      <td>Wokingham</td>
      <td>121891</td>
      <td>38153</td>
      <td>31.300916</td>
    </tr>
    <tr>
      <th>255</th>
      <td>Guildford</td>
      <td>102209</td>
      <td>36057</td>
      <td>35.277715</td>
    </tr>
  </tbody>
</table>
</div>



### Calculate Linear Regressions for each Region


{% highlight python linenos %}
def linfn(x, m, c):
    return x * m + c

eind = comb.Region == 'England'
ef, ecov = curve_fit(linfn, comb.Pct_Remain[eind], comb.Pct_Signed[eind], sigma=1/comb.Electorate[eind])

wind = comb.Region == 'Wales'
wf, wcov = curve_fit(linfn, comb.Pct_Remain[wind], comb.Pct_Signed[wind], sigma=1/comb.Electorate[wind])

sind = comb.Region == 'Scotland'
sf, scov = curve_fit(linfn, comb.Pct_Remain[sind], comb.Pct_Signed[sind], sigma=1/comb.Electorate[sind])

# not possible for N Ireland, because only one LA.

efit = intervals(comb.Pct_Remain[eind], comb.Pct_Signed[eind], linfn, ef, conflevel=.99)
wfit = intervals(comb.Pct_Remain[wind], comb.Pct_Signed[wind], linfn, wf, conflevel=.99)
sfit = intervals(comb.Pct_Remain[sind], comb.Pct_Signed[sind], linfn, sf, conflevel=.99)
{% endhighlight %}

### Per-Region Means


{% highlight python linenos %}
# calculate the total counts and electorate for each region
agg = comb.groupby('Region').aggregate(sum)

# recalculate the % Signed from the sums
agg['Pct_Signed'] = 100 * agg['signature_count'] / agg['Electorate']

agg.loc[:,('Electorate', 'Pct_Leave', 'Pct_Remain', 'Pct_Signed')]
{% endhighlight %}




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Electorate</th>
      <th>Pct_Leave</th>
      <th>Pct_Remain</th>
      <th>Pct_Signed</th>
    </tr>
    <tr>
      <th>Region</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>England</th>
      <td>38973876</td>
      <td>53.338256</td>
      <td>46.581484</td>
      <td>11.422474</td>
    </tr>
    <tr>
      <th>Northern Ireland</th>
      <td>1260955</td>
      <td>44.203900</td>
      <td>55.748789</td>
      <td>5.303441</td>
    </tr>
    <tr>
      <th>Scotland</th>
      <td>3987112</td>
      <td>37.980381</td>
      <td>61.957482</td>
      <td>5.375445</td>
    </tr>
    <tr>
      <th>Wales</th>
      <td>2270272</td>
      <td>52.489719</td>
      <td>47.439276</td>
      <td>7.936098</td>
    </tr>
  </tbody>
</table>
</div>


### Make Plots


{% highlight python linenos %}
# plot params
plt.rcParams['font.size'] = 14
size = 50

# determin the size 'scaling factor'
sfactor = 6E3
p = plt.hist(comb.Electorate / sfactor, 30)
{% endhighlight %}

<img src="/images/brexit/referendum_vs_petition_31_0.png" width="60%">



{% highlight python linenos %}
# signature_count vs Pct_Remain sized by electorate

fig, ax = plt.subplots(1,1,figsize=[8,6])

ax.scatter(comb.Pct_Remain, comb.Pct_Signed,
            c=comb.cols, s=comb.Electorate / sfactor, alpha=0.7)
ax.set_xlabel('% Vote Remain')

ylab = '% Signed (at ' + time.strftime('%Y/%m/%d %H:%M', gettime) + ')'
ax.set_ylabel(ylab)

# # # confidence intervals
# ax.plot(efit[0], efit[1], c=cols['E'], zorder=-1)
# ax.fill_between(efit[0], efit[2], efit[3], color=cols['E'], lw=0.5, alpha=0.2, zorder=-1)
# ax.plot(wfit[0], wfit[1], c=cols['W'], zorder=-1)
# ax.fill_between(wfit[0], wfit[2], wfit[3], color=cols['W'], lw=0.5, alpha=0.2, zorder=-1)
# ax.plot(sfit[0], sfit[1], c=cols['S'], zorder=-1)
# ax.fill_between(sfit[0], sfit[2], sfit[3], color=cols['S'], lw=0.5, alpha=0.2, zorder=-1)

# legend
e = ax.scatter([],[],label='England', c=cols['E'], alpha=0.7, s=size)
s = ax.scatter([],[],label='Scotland', c=cols['S'], alpha=0.7, s=size)
w = ax.scatter([],[],label='Wales', c=cols['W'], alpha=0.7, s=size)
n = ax.scatter([],[],label='N Ireland', c=cols['N'], alpha=0.7, s=size)

s100000 = ax.scatter([],[],label='100,000', c=(0,0,0,0), alpha=0.7, s=100000/sfactor)
s500000 = ax.scatter([],[],label='500,000', c=(0,0,0,0), alpha=0.7, s=500000/sfactor)
s1000000 = ax.scatter([],[],label='1,000,000', c=(0,0,0,0), alpha=0.7, s=1000000/sfactor)

ax.legend(loc='upper left', handles=[e,s,w,n], scatterpoints=1, title='Region')

ax2 = ax.twinx()
ax2.legend(loc='center left', handles=[s100000, s500000, s1000000], scatterpoints=1, title='Electorate')



ax.set_xlim(20,80)
ax.set_ylim(0,50)

# ax.set_title('Second Referendum Petition: a change of heart?', size=18)

ax.fill_between([20,50],0,50, color='blue', alpha=0.1, zorder=-2)
ax.fill_between([50,80],0,50, color='yellow', alpha=0.1, zorder=-2)

ax.text(.98, .98, 'Total Signatures: {:,}'.format(nsig), transform=ax.transAxes, ha='right', va='top')

# fig.savefig('EUref-popn' + time.strftime('-%Y%m%d-%H%M', time.localtime()) + '.pdf')
fig.savefig('EUref-popn' + time.strftime('-%Y%m%d-%H%M', time.localtime()) + '.png', dpi=450)
{% endhighlight %}

<img src="/images/brexit/referendum_vs_petition_32_0.png" width="60%">


{% highlight python linenos %}
# signature_count vs Pct_Remain sized by electorate

fig, ax = plt.subplots(1,1,figsize=[8,6])

ax.scatter(comb.Pct_Remain, comb.Pct_Signed,
            c=comb.cols, s=comb.Electorate / sfactor, alpha=0.7)
ax.set_xlabel('% Vote Remain')

ylab = '% Signed (at ' + time.strftime('%Y/%m/%d %H:%M', gettime) + ')'
ax.set_ylabel(ylab)

# confidence intervals
ax.plot(efit[0], efit[1], c=cols['E'], zorder=-1, alpha=0.8)
ax.fill_between(efit[0], efit[2], efit[3], color=cols['E'], lw=0.5, alpha=0.2, zorder=-1)
ax.plot(wfit[0], wfit[1], c=cols['W'], zorder=-1, alpha=0.8)
ax.fill_between(wfit[0], wfit[2], wfit[3], color=cols['W'], lw=0.5, alpha=0.2, zorder=-1)
ax.plot(sfit[0], sfit[1], c=cols['S'], zorder=-1, alpha=0.8)
ax.fill_between(sfit[0], sfit[2], sfit[3], color=cols['S'], lw=0.5, alpha=0.2, zorder=-1)

# legend
e = ax.scatter([],[],label='England', c=cols['E'], alpha=0.7, s=size)
s = ax.scatter([],[],label='Scotland', c=cols['S'], alpha=0.7, s=size)
w = ax.scatter([],[],label='Wales', c=cols['W'], alpha=0.7, s=size)
n = ax.scatter([],[],label='N Ireland', c=cols['N'], alpha=0.7, s=size)

s100000 = ax.scatter([],[],label='100,000', c=(0,0,0,0), alpha=0.7, s=100000/sfactor)
s500000 = ax.scatter([],[],label='500,000', c=(0,0,0,0), alpha=0.7, s=500000/sfactor)
s1000000 = ax.scatter([],[],label='1,000,000', c=(0,0,0,0), alpha=0.7, s=1000000/sfactor)

ax.legend(loc='upper left', handles=[e,s,w,n], scatterpoints=1, title='Region')

ax2 = ax.twinx()
ax2.legend(loc='center left', handles=[s100000, s500000, s1000000], scatterpoints=1, title='Electorate')



ax.set_xlim(20,80)
ax.set_ylim(0,50)

# ax.set_title('Second Referendum Petition: a change of heart?', size=18)

ax.fill_between([20,50],0,50, color='blue', alpha=0.1, zorder=-2)
ax.fill_between([50,80],0,50, color='yellow', alpha=0.1, zorder=-2)

ax.text(.98, .98, 'Total Signatures: {:,}'.format(nsig), transform=ax.transAxes, ha='right', va='top')

# fig.savefig('EUref-popn-trends' + time.strftime('-%Y%m%d-%H%M', time.localtime()) + '.pdf')
fig.savefig('EUref-popn-trends' + time.strftime('-%Y%m%d-%H%M', time.localtime()) + '.png', dpi=450)
{% endhighlight %}


<img src="/images/brexit/referendum_vs_petition_33_0.png" width="60%">


### Identify constituencies (above 99% CI) that want a re-do.

{% highlight python linenos %}
pfit = intervals(comb.Pct_Remain[eind], comb.Pct_Signed[eind], linfn, ef, xn=comb.Pct_Remain, conflevel=.99)

redo = comb.loc[(comb.Pct_Signed > pfit[2]) & (comb.Pct_Remain < 50)]
redo2 = comb.loc[(comb.Pct_Signed > pfit[2])]

# signature_count vs Pct_Remain sized by electorate with weighted trends, highlighting high outliers in Leave camp

fig, ax = plt.subplots(1,1,figsize=[8,6])

ax.scatter(comb.Pct_Remain, comb.Pct_Signed,
            c=comb.cols, s=comb.Electorate / sfactor, alpha=0.2)
ax.scatter(redo.Pct_Remain, redo.Pct_Signed,
           c=redo.cols, s=redo.Electorate / sfactor)

ax.set_xlabel('% Vote Remain')

ylab = '% Signed (at ' + time.strftime('%Y/%m/%d %H:%M', gettime) + ')'
ax.set_ylabel(ylab)

# confidence intervals
ax.plot(efit[0], efit[1], c=cols['E'], zorder=-1, alpha=0.5)
ax.fill_between(efit[0], efit[2], efit[3], color=cols['E'], lw=0.5, alpha=0.1, zorder=-1)
ax.plot(wfit[0], wfit[1], c=cols['W'], zorder=-1, alpha=0.5)
ax.fill_between(wfit[0], wfit[2], wfit[3], color=cols['W'], lw=0.5, alpha=0.1, zorder=-1)
ax.plot(sfit[0], sfit[1], c=cols['S'], zorder=-1, alpha=0.5)
ax.fill_between(sfit[0], sfit[2], sfit[3], color=cols['S'], lw=0.5, alpha=0.1, zorder=-1)

# legend
e = ax.scatter([],[],label='England', c=cols['E'], alpha=0.7, s=size)
s = ax.scatter([],[],label='Scotland', c=cols['S'], alpha=0.7, s=size)
w = ax.scatter([],[],label='Wales', c=cols['W'], alpha=0.7, s=size)
n = ax.scatter([],[],label='N Ireland', c=cols['N'], alpha=0.7, s=size)

s100000 = ax.scatter([],[],label='100,000', c=(0,0,0,0), alpha=0.7, s=100000/sfactor)
s500000 = ax.scatter([],[],label='500,000', c=(0,0,0,0), alpha=0.7, s=500000/sfactor)
s1000000 = ax.scatter([],[],label='1,000,000', c=(0,0,0,0), alpha=0.7, s=1000000/sfactor)

ax.legend(loc='upper left', handles=[e,s,w,n], scatterpoints=1, title='Region')

ax2 = ax.twinx()
ax2.legend(loc='center left', handles=[s100000, s500000, s1000000], scatterpoints=1, title='Electorate')

ax.set_xlim(20,80)
ax.set_ylim(0,50)

# ax.set_title('Second Referendum Petition: a change of heart?', size=18)

ax.fill_between([20,50],0,50, color='blue', alpha=0.1, zorder=-2)
ax.fill_between([50,80],0,50, color='yellow', alpha=0.1, zorder=-2)

ax.text(.98, .98, 'Total Signatures: {:,}'.format(nsig), transform=ax.transAxes, ha='right', va='top')

# fig.savefig('EUref-popn-trends-sub' + time.strftime('-%Y%m%d-%H%M', time.localtime()) + '.pdf')
fig.savefig('EUref-popn-trends-sub' + time.strftime('-%Y%m%d-%H%M', time.localtime()) + '.png', dpi=450)
{% endhighlight %}


<img src="/images/brexit/referendum_vs_petition_37_0.png" width="60%">


### Use underlying trend to predict vote change in 'Leave' LAs?


{% highlight python linenos %}
p, cov = curve_fit(linfn, comb.Pct_Remain[eind], comb.Pct_Signed[eind], sigma=1/comb.Electorate[eind])

def ilinfn(y, m, c):
    return (y - c) / m

redo['Pct_Remain_Pred'] = ilinfn(redo.loc[:,'Pct_Signed'].values, *ef)
redo2['Pct_Remain_Pred'] = ilinfn(redo2.loc[:,'Pct_Signed'].values, *ef)

redo['Extra_Votes'] = (redo.loc[:,'Pct_Remain_Pred'] - redo.loc[:,'Pct_Remain']) * redo.loc[:,'Electorate'] / 100
redo2['Extra_Votes'] = (redo2.loc[:,'Pct_Remain_Pred'] - redo2.loc[:,'Pct_Remain']) * redo2.loc[:,'Electorate'] / 100

# Number of votes predicted to move to Remain based on LAs with higher
# than expected signature counts in majority Leave LAs.
int(redo.Extra_Votes.sum())
{% endhighlight %}




    1345976




{% highlight python linenos %}
# Number of votes predicted to move to Remain based on LAs with higher
# than expected signature counts in all LAs.
int(redo2.Extra_Votes.sum())
{% endhighlight %}




    2132798



