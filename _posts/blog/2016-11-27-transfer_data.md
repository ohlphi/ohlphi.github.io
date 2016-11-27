---
layout: blog
title: Visualizing Football Transfer Data from Premier League - Summer 2016
meta: The Silly season of 2016 is over and in its backwash some interesting transfers came into place in the Premier League. These transfers have been visualized, using R, in the form of a transfer network, in- and outflow of players for each team and an interactive table of all transfers in Premier League. 
category: blog
date: November 27, 2016
---

The Silly season of 2016 is over and in its backwash some interesting transfers came into place in the Premier League. These transfers have been visualized, using R, in the form of a transfer network, in- and outflow of players for each team and an interactive table of all transfers that occurred in Premier League.
<br>
<br>
<h3>Interactive network</h3>

Below is an interactive (zoom, drag, move, select) network graph, showing the transfers of football players to and from Premier League. 

<div class="container2">
	<iframe src="{{site.baseurl}}/blogfigs/2016-11-27-transfer-data/transfer_network.html" scrolling='no' seamless frameborder="0" class="visNetwork"></iframe>
</div>

<style>div.container2{ position: relative; width: 100%; height: 0; padding-bottom: 80%;}</style>
<style>iframe.visNetwork{ position: absolute; top: 0; left: 0; width: 100%; height: 100%;}</style>
Some explanatory notes about the network: The size of a team's logo depends on how much each team has bought or sold players for (the bigger the logo, the more the team spent or received).<br>The color of the arrows depends on the type of transfer that has occured:<br><font color = "#FFCDD2">Pink -  Transfer</font>, <font color="#2196F3">Blue -  Return from Loan</font>, <font color="#4CAF50"> Green - Loan</font>, <font color="#FFC107">Orange - Free Transfer</font> and <font color="#9E9E9E">Gray - Retired</font>.<br>Hovering over an arrow will show the name of the player, to and from, and the type of transfer (and transfer sum, if applicable). Hovering over the logo of a team will show the total spend and sales of players.

<br>
<br>

<h3>Premier League Summer Transfer 2016: Players bought and sold per team (MEUR)</h3> 

<div class="container2">
	<iframe src="{{site.baseurl}}/blogfigs/2016-11-27-transfer-data/chart_teams.html" scrolling='no' seamless frameborder="0" class="highcharter"></iframe>
</div>

<style>div.container2{ position: relative; width: 100%; height: 0; padding-bottom: 80%;}</style>
<style>iframe.highcharter{ position: absolute; top: 0; left: 0; width: 100%; height: 100%;}</style>
Overall, the Premier League teams have been more eager to buy rather than to sell players. Also, although Manchester United bought the most expensive player in the world (P. Pogba), Manchester City was the Premier League team that spent the most during the summer window.
<br>
<br>

<h3>Interactive data table</h3>
The table below is showing all players to and from Premier League during the summer of 2016. In case you wish to download, copy or print the data set, then you're in luck, because you can do all those things by clicking at one of the buttons! You can also rearrange the columns by drag-and-drop them as well as search for whatever you want in data table by using the search field.

<br>
<div class="container2">
	<iframe src="{{site.baseurl}}/blogfigs/2016-11-27-transfer-data/data_transfer.html" scrolling='no' seamless frameborder="0" class="datatable"></iframe>
</div>

<style>div.container2{ position: relative; width: 100%; height: 0; padding-bottom: 80%;}</style>
<style>iframe.datatable{ position: absolute; top: 0; left: 0; width: 100%; height: 100%;}</style>

<br>
<br>
The interactive network, barchart and data table have all been made using R and the packages <a href="http://datastorm-open.github.io/visNetwork/">visNetwork</a>, <a href="http://jkunst.com/highcharter/">highcharter</a> and <a href="http://rstudio.github.io/DT/">DT</a>. These are all great packages for making interactive and stunning visualizations. The data used was collected from <a href="http://www.footballdatabase.eu/transfertstab.php?competition=1&lieu=Angleterre">FootballDatabase.eu</a>.
<br><br>
I hope you enjoyed reading and playing around with the transfer data. I will shortly put up a new post, explaining the steps for making the visualizations, as well as scraping the data. Also, this is only Premier League, we have plenty of other leagues to look into!
<br><br>Thanks for reading and kudos to Mike Duff (CM 01/02 legend, who retired this summer. If it wasn't for this post I wouldn't have known he retired!).