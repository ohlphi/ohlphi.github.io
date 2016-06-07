---
layout: blog
title: Using UN refugee data to make interactive charts and networks in R
meta: United Nations High Commissioner for Refugees provide information about asylum applications for various countries. In this post, some of the data will be used to make interactive charts with the R packages 'highcharter' and 'visNetwork'. 
category: blog
date: June 5, 2016
---

United Nations High Commissioner for Refugees provide information about asylum applications for various countries. In this post, some of the data will be used to make interactive charts and networks with the R packages 'highcharter' and 'visNetwork'.
<br>
UNHCR provide plenty of population data, so if you are interested in what other information they have, I recommend you to check out their website <a href="http://popstats.unhcr.org">here</a>. The data used in the examples below are from <a href="http://popstats.unhcr.org/en/asylum_seekers_monthly">monthly asylum seekers</a>, which is providing information about asylum applications lodged in 38 European and 6 non-European countries, broken down by month and origin. 

The data used in the examples are stretching from January 1999 until May 2016, taking into consideration all asylum countries and origin in the data set (total size of approximately 12MB in CSV). 
<br>
<br>


<h3><b>The refugee and asylum countries - yearly top 7</b></h3>
The charts below are created with <a href="http://jkunst.com/highcharter/">'highcharter'</a>, which is a very nice R package for creating beautiful interactive charts.
<br>
The first chart shows the yearly top seven countries, in terms of the highest number of refugees fleeing their countries that year. If you hover over you can see the number of refugees for the specific country and if you wish to go into more detail you can also select, by clicking on the country below, which countries you want to view and/or exclude.  
<div class="container2">
	<iframe src="{{site.baseurl}}/blogfigs/2016-06-05-interactive-charts-refugee-data/refugee_highcharter.html" scrolling='no' seamless frameborder="0" class="rChart datamaps"></iframe>
</div>

<style>div.container2{ position: relative; width: 100%; height: 0; padding-bottom: 80%;}</style>
<style>iframe.rChart{ position: absolute; top: 0; left: 0; width: 100%; height: 100%;}</style>

<br>
<br>
The second chart shows the yearly top seven asylum countries, in terms of number of refugees seeking asylum for that specific country.


<div class="container2">
	<iframe src="{{site.baseurl}}/blogfigs/2016-06-05-interactive-charts-refugee-data/asylum_highcharter.html" scrolling='no' seamless frameborder="0" class="rChart datamaps"></iframe>
</div>

<style>div.container2{ position: relative; width: 100%; height: 0; padding-bottom: 80%;}</style>
<style>iframe.rChart{ position: absolute; top: 0; left: 0; width: 100%; height: 100%;}</style>


<h3><b>Graph network of refugees</b></h3>
The two graph networks below were made using the R package <a href="http://datastorm-open.github.io/visNetwork/">'visNetwork'</a>, which is a very handy package for illustrating the complexity of where refugees are coming from and where they seek asylum. 
<br>
The first graph is made by summarizing how many refugees left their countries in 2015 and to which country they seeked asylum. In the upper left corner of the graph network you can check out a specific country (whether it is a refugee or asylum country) and its network. In the group selection you can see which are the asylum countries (where the refugees are seeking asylum), refugee countries, dual flow countries (countries which have asylum seekers, but also people leaving their own country), mainly asylum countries (having a large proportion of asylum seekers instead of own citizens seeking refuge elsewhere) and mainly refugee countries (mainly a refugee country, but might have some refugees seeking asylum in their country).
<br>
You can also zoom in on the network and if you hover over the edges you can see from which country the refugees came from and how many they were to seek asylum in another country. Lastly, when hovering over a node, you can see information about the total number of refugees and/or asylum seekers for the specific country in 2015. 


<div class="container2">
	<iframe src="{{site.baseurl}}/blogfigs/2016-06-05-interactive-charts-refugee-data/refugees_network.html" scrolling='no' seamless frameborder="0" class="rChart datamaps"></iframe>
</div>

<style>div.container2{ position: relative; width: 100%; height: 0; padding-bottom: 80%;}</style>
<style>iframe.rChart{ position: absolute; top: 0; left: 0; width: 100%; height: 100%;}</style>

<br>
<br>
The graph below is showing the same as the one above, but it has been filtered to only look to countries which had more than 500 refugees leaving its country in 2015. The reason for this is just to make it a bit easier to read out the graph network (even with a nifty circular layout it might be tricky to read out all the information). 


<div class="container2">
	<iframe src="{{site.baseurl}}/blogfigs/2016-06-05-interactive-charts-refugee-data/refugees_network_500.html" scrolling='no' seamless frameborder="0" class="rChart datamaps"></iframe>
</div>

<style>div.container2{ position: relative; width: 100%; height: 0; padding-bottom: 80%;}</style>
<style>iframe.rChart{ position: absolute; top: 0; left: 0; width: 100%; height: 100%;}</style>
<br>
<br>
For those interested in wanting to recreate the charts and networks above, you can download or fork the script and data from <a href="https://github.com/ohlphi/refugee_data_UNHCR">here</a>.
<br>
For any questions, please feel free to <a href="{{site.baseurl}}/contact/">contact me</a> or leave a comment.
<br>
Thanks for reading!