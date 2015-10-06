---
layout: blog
title: Cleaning of and making an interactive choropleth map from weather data
meta: I will be using some weather data from NOAA, which I will clean up in order to check the human (fatalities & injuries) and economic (crop & property damage) impact due to weather events for each state, stretching from 1950-2011. The first part will be cleaning the data and the second part will be making interactive choropleth maps of our observations.
category: blog
date: October 6, 2015
---

<iframe src='{{site.baseurl}}/blogfigs/2015-10-06-weather-impact/injuries.html' scrolling='no' seamless
class='rChart datamaps '
id=iframe-chart_1 
frameBorder = "0"
></iframe>

<style>iframe.rChart{ width: 100%; height: 430px;}</style>

In this post I will show how it is possible in R to use data from the U.S. National Oceanic and Atmospheric Administration's (NOAA) database to make an interactive choropleth map that shows the impact on public health (injuries & fatalities) and economy (crop & property damage) for each states, ranging from 1950-2011, like the one I made above (showing the number of people injured due to weather events). The data I will be using can be downloaded from <a href="https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2">here</a> (be aware, it is almost 50MB).

<h3><strong>Loading the necessary packages</strong></h3>

Before starting let us load all the necessary packages required for cleaning the data:

{% highlight r %}
library(dplyr)
library(lubridate)
{% endhighlight %}

`dplyr` and `lubridate` will be used for cleaning the data. We will use some additional packages for the making of the choropleth maps, but this will require `plyr`, which might interfere with the `dplyr`. Therefore, we will load these later, once we are done cleaning the data.

<h3><strong>Processing the data</strong></h3>

Let us start with downloading the data and save it in `data`:


{% highlight r %}
if (!file.exists("stormdata.csv.bz2" )){
        fileUrl = "http://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2"
        download.file(fileUrl, destfile="./stormdata.csv.bz2", method = "curl")
}

# Read the file:
data<-read.csv(bzfile("./stormdata.csv.bz2"), sep=",")
{% endhighlight %}

Let's check what the data looks like:

{% highlight r %}
str(data)
{% endhighlight %}



{% highlight text %}
## 'data.frame':	902297 obs. of  37 variables:
##  $ STATE__   : num  1 1 1 1 1 1 1 1 1 1 ...
##  $ BGN_DATE  : Factor w/ 16335 levels "1/1/1966 0:00:00",..: 6523 6523 4242 11116 2224 2224 2260 383 3980 3980 ...
##  $ BGN_TIME  : Factor w/ 3608 levels "00:00:00 AM",..: 272 287 2705 1683 2584 3186 242 1683 3186 3186 ...
##  $ TIME_ZONE : Factor w/ 22 levels "ADT","AKS","AST",..: 7 7 7 7 7 7 7 7 7 7 ...
##  $ COUNTY    : num  97 3 57 89 43 77 9 123 125 57 ...
##  $ COUNTYNAME: Factor w/ 29601 levels "","5NM E OF MACKINAC BRIDGE TO PRESQUE ISLE LT MI",..: 13513 1873 4598 10592 4372 10094 1973 23873 24418 4598 ...
##  $ STATE     : Factor w/ 72 levels "AK","AL","AM",..: 2 2 2 2 2 2 2 2 2 2 ...
##  $ EVTYPE    : Factor w/ 985 levels "   HIGH SURF ADVISORY",..: 834 834 834 834 834 834 834 834 834 834 ...
##  $ BGN_RANGE : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ BGN_AZI   : Factor w/ 35 levels "","  N"," NW",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ BGN_LOCATI: Factor w/ 54429 levels ""," Christiansburg",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ END_DATE  : Factor w/ 6663 levels "","1/1/1993 0:00:00",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ END_TIME  : Factor w/ 3647 levels ""," 0900CST",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ COUNTY_END: num  0 0 0 0 0 0 0 0 0 0 ...
##  $ COUNTYENDN: logi  NA NA NA NA NA NA ...
##  $ END_RANGE : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ END_AZI   : Factor w/ 24 levels "","E","ENE","ESE",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ END_LOCATI: Factor w/ 34506 levels ""," CANTON"," TULIA",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ LENGTH    : num  14 2 0.1 0 0 1.5 1.5 0 3.3 2.3 ...
##  $ WIDTH     : num  100 150 123 100 150 177 33 33 100 100 ...
##  $ F         : int  3 2 2 2 2 2 2 1 3 3 ...
##  $ MAG       : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ FATALITIES: num  0 0 0 0 0 0 0 0 1 0 ...
##  $ INJURIES  : num  15 0 2 2 2 6 1 0 14 0 ...
##  $ PROPDMG   : num  25 2.5 25 2.5 2.5 2.5 2.5 2.5 25 25 ...
##  $ PROPDMGEXP: Factor w/ 19 levels "","-","?","+",..: 17 17 17 17 17 17 17 17 17 17 ...
##  $ CROPDMG   : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ CROPDMGEXP: Factor w/ 9 levels "","?","0","2",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ WFO       : Factor w/ 542 levels ""," CI","%SD",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ STATEOFFIC: Factor w/ 250 levels "","ALABAMA, Central",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ ZONENAMES : Factor w/ 25112 levels "","                                                                                                                               "| __truncated__,..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ LATITUDE  : num  3040 3042 3340 3458 3412 ...
##  $ LONGITUDE : num  8812 8755 8742 8626 8642 ...
##  $ LATITUDE_E: num  3051 0 0 0 0 ...
##  $ LONGITUDE_: num  8806 0 0 0 0 ...
##  $ REMARKS   : Factor w/ 436781 levels "","\t","\t\t",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ REFNUM    : num  1 2 3 4 5 6 7 8 9 10 ...
{% endhighlight %}



{% highlight r %}
head(data)
{% endhighlight %}



{% highlight text %}
##   STATE__           BGN_DATE BGN_TIME TIME_ZONE COUNTY COUNTYNAME STATE
## 1       1  4/18/1950 0:00:00     0130       CST     97     MOBILE    AL
## 2       1  4/18/1950 0:00:00     0145       CST      3    BALDWIN    AL
## 3       1  2/20/1951 0:00:00     1600       CST     57    FAYETTE    AL
## 4       1   6/8/1951 0:00:00     0900       CST     89    MADISON    AL
## 5       1 11/15/1951 0:00:00     1500       CST     43    CULLMAN    AL
## 6       1 11/15/1951 0:00:00     2000       CST     77 LAUDERDALE    AL
##    EVTYPE BGN_RANGE BGN_AZI BGN_LOCATI END_DATE END_TIME COUNTY_END
## 1 TORNADO         0                                               0
## 2 TORNADO         0                                               0
## 3 TORNADO         0                                               0
## 4 TORNADO         0                                               0
## 5 TORNADO         0                                               0
## 6 TORNADO         0                                               0
##   COUNTYENDN END_RANGE END_AZI END_LOCATI LENGTH WIDTH F MAG FATALITIES
## 1         NA         0                      14.0   100 3   0          0
## 2         NA         0                       2.0   150 2   0          0
## 3         NA         0                       0.1   123 2   0          0
## 4         NA         0                       0.0   100 2   0          0
## 5         NA         0                       0.0   150 2   0          0
## 6         NA         0                       1.5   177 2   0          0
##   INJURIES PROPDMG PROPDMGEXP CROPDMG CROPDMGEXP WFO STATEOFFIC ZONENAMES
## 1       15    25.0          K       0                                    
## 2        0     2.5          K       0                                    
## 3        2    25.0          K       0                                    
## 4        2     2.5          K       0                                    
## 5        2     2.5          K       0                                    
## 6        6     2.5          K       0                                    
##   LATITUDE LONGITUDE LATITUDE_E LONGITUDE_ REMARKS REFNUM
## 1     3040      8812       3051       8806              1
## 2     3042      8755          0          0              2
## 3     3340      8742          0          0              3
## 4     3458      8626          0          0              4
## 5     3412      8642          0          0              5
## 6     3450      8748          0          0              6
{% endhighlight %}

It is quite a hefty file (>900k rows and 37 different variables), however we are really only interested in the following variables: 

- `BGN_DATE`: Date of the weather event,
 
- `STATE`,
 
- `FATALITIES`, 

- `INJURIES`, 

- `PROPDMG`: Property damage, 

- `PROPDMGEXP`: Exponential value of the property damage,

- `CROPDMG`: Crop damage & 

- `CROPDMGEXP`: Exponential value of the property damage.


Select the variables above and create a new variable in `data` and call it `Year`, by extracting the year from the column `BGN_DATE`. Afterwards, we take out `BGN_DATE`, as we are not interested in the specific date, but only the year of the weather event:


{% highlight r %}
data <- select(data, BGN_DATE, STATE, FATALITIES, INJURIES, PROPDMG, PROPDMGEXP,
               CROPDMG, CROPDMGEXP)
data$Year <- as.numeric(format(mdy_hms(data$BGN_DATE), "%Y"))
data <- select(data, -BGN_DATE)
{% endhighlight %}

In order to check the economic impact of severe weather, both property and crop damage values involve expontential values: `PROPDMGEXP` and `CROPDMGEXP`. Let us check the variety and amount of the different exponential values in these two variables:

{% highlight r %}
unique(data$PROPDMGEXP)
{% endhighlight %}



{% highlight text %}
##  [1] K M   B m + 0 5 6 ? 4 2 3 h 7 H - 1 8
## Levels:  - ? + 0 1 2 3 4 5 6 7 8 B h H K m M
{% endhighlight %}



{% highlight r %}
summary(data$PROPDMGEXP)
{% endhighlight %}



{% highlight text %}
##             -      ?      +      0      1      2      3      4      5 
## 465934      1      8      5    216     25     13      4      4     28 
##      6      7      8      B      h      H      K      m      M 
##      4      5      1     40      1      6 424665      7  11330
{% endhighlight %}



{% highlight r %}
unique(data$CROPDMGEXP)
{% endhighlight %}



{% highlight text %}
## [1]   M K m B ? 0 k 2
## Levels:  ? 0 2 B k K m M
{% endhighlight %}



{% highlight r %}
summary(data$CROPDMGEXP)
{% endhighlight %}



{% highlight text %}
##             ?      0      2      B      k      K      m      M 
## 618413      7     19      1      9     21 281832      1   1994
{% endhighlight %}
We can see that there are several types of exponential values, however, they are pretty difficult to interpret. The only ones that can be interpreted are `Kk` (=1,000), `Mm` (=1,000,000) and `B` (=1,000,000,000). Since the other exponential values are relatively few and we have no further information about the interpretation of them, we set these to be 0. 
We replace the exponential values with numerical values using `gsub`: 

{% highlight r %}
data$PROPDMGEXP <- as.character(data$PROPDMGEXP)
data$PROPDMGEXP <- gsub("\\-|\\+|\\?|H|h|0", "0", data$PROPDMGEXP)
data$PROPDMGEXP <- gsub("K|k", "1000", data$PROPDMGEXP)
data$PROPDMGEXP <- gsub("M|m", "1000000", data$PROPDMGEXP)
data$PROPDMGEXP <- gsub("B|b", "1000000000", data$PROPDMGEXP)
data$PROPDMGEXP <- as.numeric(data$PROPDMGEXP)
data$PROPDMGEXP[is.na(data$PROPDMGEXP)] <- 0

data$CROPDMGEXP <- as.character(data$CROPDMGEXP)
data$CROPDMGEXP <- gsub("\\-|\\+|\\?|H|h|0", "0", data$CROPDMGEXP)
data$CROPDMGEXP <- gsub("K|k", "1000", data$CROPDMGEXP)
data$CROPDMGEXP <- gsub("M|m", "1000000", data$CROPDMGEXP)
data$CROPDMGEXP <- gsub("B|b", "1000000000", data$CROPDMGEXP)
data$CROPDMGEXP <- as.numeric(data$CROPDMGEXP)
data$CROPDMGEXP[is.na(data$CROPDMGEXP)] <- 0
{% endhighlight %}

Multiply `CROPDMG` with `CROPDMGEXP` and `PROPDMG` with `PROPDMG` and save them into two new variables called `Crop` and `Property`, then take out `CROPDMG`, `CROPDMGEXP`, `PROPDMG` and `PROPDMG` from `data`:

{% highlight r %}
data <- mutate(data, Crop = CROPDMG*CROPDMGEXP, Property = PROPDMG*PROPDMGEXP)
data <- select(data, -CROPDMG, -CROPDMGEXP, -PROPDMG, -PROPDMGEXP)
{% endhighlight %}

Before summarizing the observations (based on state and year), let us clean up the column names, so that they all follow the same principle of uppercase on the first letter: 

{% highlight r %}
colnames(data)[1:3] <- c("State", "Fatalities", "Injuries")
{% endhighlight %}

Okidoki, now we are ready to summarize the `data`, based on 'Year' and 'State':

{% highlight r %}
data <- data %>%
  group_by(State, Year) %>%
  summarize(Fatalities = sum(Fatalities), Injuries = sum(Injuries), Crop = sum(Crop), Property = sum(Property))
{% endhighlight %}

Now that the `data` has been summarized based on state and year, let us check what it looks like: 

{% highlight r %}
head(data)
{% endhighlight %}



{% highlight text %}
## Source: local data frame [6 x 6]
## Groups: State
## 
##   State Year Fatalities Injuries  Crop Property
## 1    AK 1993          5        3     0  2437500
## 2    AK 1994          0        0     0    31500
## 3    AK 1995          7        5 10000 17535000
## 4    AK 1996          2       29     0 11538000
## 5    AK 1997          0        0     0  4092500
## 6    AK 1998          0        0     0  2791000
{% endhighlight %}



{% highlight r %}
str(data)
{% endhighlight %}



{% highlight text %}
## Classes 'grouped_df', 'tbl_df', 'tbl' and 'data.frame':	3176 obs. of  6 variables:
##  $ State     : Factor w/ 72 levels "AK","AL","AM",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ Year      : num  1993 1994 1995 1996 1997 ...
##  $ Fatalities: num  5 0 7 2 0 0 18 13 9 7 ...
##  $ Injuries  : num  3 0 5 29 0 0 14 18 13 4 ...
##  $ Crop      : num  0 0 10000 0 0 0 10000 5000 0 0 ...
##  $ Property  : num  2437500 31500 17535000 11538000 4092500 ...
##  - attr(*, "vars")=List of 1
##   ..$ : symbol State
##  - attr(*, "drop")= logi TRUE
{% endhighlight %}

When taking a first glimpse we can see that there are 72 different states instead of 50. Since we do not know what these other states are we will exclude them from the data, using `state.abb` from the `datasets` package:

{% highlight r %}
data <- subset(data, State %in% c(state.abb))
{% endhighlight %}

We can now see that we have 2,934 rows of observations. However, considering that we are looking into 50 states over a 62 year period, we would expect the number of rows to be 3,100. So, there seem to be observations missing. We will add these to the `data` using `expand.grid` and `merge`:

{% highlight r %}
vals <- expand.grid(Year = unique(data$Year),
                    State = unique(data$State))
data <- merge(vals, data, all = TRUE)
head(data)
{% endhighlight %}



{% highlight text %}
##   Year State Fatalities Injuries Crop Property
## 1 1950    AK         NA       NA   NA       NA
## 2 1950    AL          0       15    0    27500
## 3 1950    AR          2       49    0   860030
## 4 1950    AZ         NA       NA   NA       NA
## 5 1950    CA         NA       NA   NA       NA
## 6 1950    CO          0        1    0    50000
{% endhighlight %}

We are now done with cleaning up the data. We have our observations based on the four events (Crop damage, Fatalities, Injuries and Property damage) and we have input for each state per year ranging from 1950-2011. In the cases where we do not have any data we have given them the value `NA` instead of 0. The reason behind it is that it is uncertain whether the NA's are due to no reported observations or that information is simply missing. Some states have reported values despite no human nor economic impact (all values are 0). Therefore, we will make a distinction between the observed rows and the missing ones.

That is about it for cleaning up the data. Next up, making a choropleth map!

<h3><strong>Making the interactive map</strong></h3>

Now the fun stuff: Making the interactive map! 
We still need to arrange the data in order to be able to read it into our choropleth map. We will add all the events into one column. Instead of having 3,100 rows with 6 variables, we will arrange it into 12,400 rows with 4 variables: State, Year, Event and value. We will use `melt` from the `reshape2` package and we will also load in the packages for making the choropleth maps:


{% highlight r %}
library(reshape2)
library(rMaps)
library(plyr)
library(dplyr)
library(rCharts)
library(RColorBrewer)
data <- melt(data, id = c("State", "Year"))
colnames(data)[3:4] <- c("Event", "Value")
{% endhighlight %}

We can choose to save this data into a csv file, so that we do not need to run through all the steps above again: 

{% highlight r %}
write.csv(data, file = "stormdata.csv")
{% endhighlight %}

In order for the choropleth map to work I had to do some changes in the `ichoropleth` function in the `rMaps` package, namely the `fillKey`. `fillKey` takes the `Value` from the event and applies a color to it based on its value (namely, which range the value falls within), which will be visible in the choropleth map. In the `ichoropleth` function this value is based on the quantile value, but this creates, for the data we are using, an error because quantile values in our data are the same for some levels and cannot cannot be used as break values.
 
In order to get around this I created another function, called `ichoropleth2` (very intuitive...), where I have changed the `fillKey` range to be a variable in our data set (which we will add later to our `data`), where we can decide what color range based on the `Value`. Also, we add a color for the NA's. Once done I save all the `ichoropleth2` in a separate R-file, which I will use to call later by using the command `source("the-name-of-your-choropleth-function.R")`. In my example i simply named it `helper.R`:



{% highlight r %}
ichoropleth2 <- function(x, data, pal = "Blues", ncuts = 6, animate = NULL, play = F, map = 'usa', legend = TRUE, labels = TRUE, ...){
  d <- Datamaps$new()
  fml = lattice::latticeParseFormula(x, data = data)
  data = transform(data, 
                   fillKey = data$fillKey #<-- This is where we did the change!
  )
  fillColors = brewer.pal(ncuts-1, pal) #<-- Modified because of the NA value
  NAcolor <- "#BDBDBD" #<-- Color we will use for the NA values
  fillColors = c(fillColors, NAcolor) #<- since the NA is the last color in the color palette, we need to order NA last as well in our order of fillKey
  d$set(
    scope = map, 
    fills = as.list(setNames(fillColors, levels(data$fillKey))), 
    legend = legend,
    labels = labels,
    ...
  )
  if (!is.null(animate)){
    range_ = summary(data[[animate]])
    data = dlply(data, animate, function(x){
      y = toJSONArray2(x, json = F)
      names(y) = lapply(y, '[[', fml$right.name)
      return(y)
    })
    d$set(
      bodyattrs = "ng-app ng-controller='rChartsCtrl'"  
    )
    d$addAssets(
      jshead = "http://cdnjs.cloudflare.com/ajax/libs/angular.js/1.2.1/angular.min.js"
    )
    if (play == T){
      d$setTemplate(chartDiv = sprintf("
                                       <div class='container'>
                                       <button ng-click='animateMap()'>Play</button>
                                       <span ng-bind='year'></span>
                                       <div id='{{chartId}}' class='rChart datamaps'></div>
                                       </div>
                                       <script>
                                       function rChartsCtrl($scope, $timeout){
                                       $scope.year = %s;
                                       $scope.animateMap = function(){
                                       if ($scope.year > %s){
                                       return;
                                       }
                                       map{{chartId}}.updateChoropleth(chartParams.newData[$scope.year]);
                                       $scope.year += 1
                                       $timeout($scope.animateMap, 1000)
                                       }
                                       }
                                       </script>", range_[1], range_[6])
      )
      
    } else {
      d$setTemplate(chartDiv = sprintf("
                                       <div class='container'>
                                       <input id='slider' type='range' min=%s max=%s ng-model='year' width=200>
                                       <span ng-bind='year'></span>
                                       <div id='{{chartId}}' class='rChart datamaps'></div>          
                                       </div>
                                       <script>
                                       function rChartsCtrl($scope){
                                       $scope.year = %s;
                                       $scope.$watch('year', function(newYear){
                                       map{{chartId}}.updateChoropleth(chartParams.newData[newYear]);
                                       })
                                       }
                                       </script>", range_[1], range_[6], range_[1])
      )
    }
    d$set(newData = data, data = data[[1]])
    
  } else {
    d$set(data = dlply(data, fml$right.name))
  }
  return(d)
}
{% endhighlight %}


<h3><strong>First map - Fatalities</strong></h3>

Let us start making the map for Fatalities. We will only need the `State`, `Year` and `Value` for making the interactive map from `data`. We will also need to create an additional variable, called `fillKey`, which will be determining what color each value will have on the map when we run `ichoropleth2`. 


{% highlight r %}
fatalities <- data[data$Event == "Fatalities",]
fatalities <- select(fatalities, State, Year, Value)
fatalities$State <- as.character(fatalities$State)
fatalities <- mutate(fatalities, fillKey = ifelse(is.na(Value) == TRUE, "No info",
                                           ifelse(Value <=  10, "0-10",
                                           ifelse(Value <= 50, "11-50",
                                           ifelse(Value <= 100, "51-100",
                                           ifelse(Value <= 500, "101-500", ">500"))))))

fatalities$fillKey <- ordered(fatalities$fillKey, levels = c("0-10",
                                                           "11-50",
                                                           "51-100",
                                                           "101-500",
                                                           ">500",
                                                           "No info"))
{% endhighlight %}

Ok, now we can run our `ichoropleth2` function and create the map:


{% highlight r %}
source("helper.R")
i1 <- ichoropleth2(Value ~ State, data = fatalities, pal="PuRd", animate= "Year")
i1
{% endhighlight %}

Our map should look something like this for fatalities: 
	<iframe src='{{site.baseurl}}/blogfigs/2015-10-06-weather-impact/fatalities2.html' scrolling='no' seamless
	class='rChart datamaps '
	id=iframe-chart_2 
	frameBorder = "0"
	></iframe>

<style>iframe.rChart{ width: 100%; height: 430px;}</style>


<h3><strong>Second map - Property Damage</strong></h3>

Actually, I will only do another map, which will be the Property damage (the other maps can be done by simply replacing the event):


{% highlight r %}
property <- data[data$Event == "Property",]
property <- select(property, State, Year, Value)
property$State <- as.character(property$State)
property <- mutate(property, fillKey = ifelse(is.na(Value) == TRUE, "No info",
                                       ifelse(Value <= 1000000, "0-1 M$",
                                       ifelse(Value <= 10000000, "1-10 M$",
                                       ifelse(Value <= 100000000, "10-100 M$",
                                       ifelse(Value <= 1000000000, "100-1,000 M$", ">1,000 M$"))))))
property$fillKey <- ordered(property$fillKey, levels = c("0-1 M$",
                                                         "1-10 M$",
                                                         "10-100 M$",
                                                         "100-1,000 M$",
                                                         ">1,000 M$",
                                                         "No info"))

i2 <- ichoropleth2(Value ~ State, data = property, pal="PuRd", animate= "Year")
i2
{% endhighlight %}

And there we have our second map of property damage: 
	<iframe src='{{site.baseurl}}/blogfigs/2015-10-06-weather-impact/property2.html' scrolling='no' seamless
	class='rChart datamaps '
	id=iframe-chart_3 
	frameBorder = "0"
	></iframe>

<style>iframe.rChart{ width: 100%; height: 430px;}</style>


There you have it: Two interactive choropleth map of the US, stretching from 1950-2011, showing us the number of fatalities and the total property damage due to weather events. The next steps will be some finetuning before we save our maps into html. We could stop here, but I would like to some finetuning.
First, I would like to add some more info when we hover over a state, for instance the property damage in USD. 
Second, in order to make the values a bit easier to read, I will introduce commas to separate groups of three digits.

Let us start with the second part first and update our choropleth maps for fatalities and property damage: 


{% highlight r %}
fatalities$Value <- prettyNum(fatalities$Value, big.mark = ",")
i1 <- ichoropleth2(Value ~ State, data = fatalities, pal = "PuRd", animate = "Year")
property$Value <- prettyNum(property$Value, big.mark = ",")
i2 <- ichoropleth2(Value ~ State, data = property, pal="PuRd", animate= "Year")
{% endhighlight %}

In order to provide info when hovering over the state, there seem to be some different ways to do this. However, I was not able to solve it in any of those ways and decided to do it my way. Firstly, I set the parameters for the size of the maps. Secondly, I save them to .html. Thirdly, I add `geographyConfig` to our created TopoJSON (which is created in the `ichoropleth` function).


{% highlight r %}
i1$addParams(height = 350, width = 510)
i1$save('fatalities.html', cdn = TRUE)
i2$addParams(height = 350, width = 510)
i2$save('property.html', cdn = TRUE)
{% endhighlight %}


Open up your preferred text editor, open your maps you created earlier, scroll down to almost the very bottom of your TopoJSON section, and add the following piece of code on top of "id" for fatalities.html and injuries.html (or whatever you named them): 

{% highlight javascript %}
"geographyConfig": {
  "popupTemplate":  function(geography, data){
    return '<div class=hoverinfo><strong>' + geography.properties.name + 
      ': ' + data.Value + '</strong></div>';
  }  
},
{% endhighlight %}

Do the same thing for crop and property damage, the only difference is the dollar sign:

{% highlight javascript %}
"geographyConfig": {
  "popupTemplate":  function(geography, data){
    return '<div class=hoverinfo><strong>' + geography.properties.name + 
      ': ' + '$ ' + data.Value + '</strong></div>';
  }  
},
{% endhighlight %}

The place where the code should be added is right after the `WY` and just before `id` (almost at the very end):

{% highlight javascript %}
"WY": {
 "State": "WY",
"Year": 1950,
"Value": "0",
"fillKey": "0-10" 
} 
},
"geographyConfig": {
  "popupTemplate":  function(geography, data){
    return '<div class=hoverinfo><strong>' + geography.properties.name + 
      ': ' + data.Value + '</strong></div>';
  }  
},
"id": "chartf7d8646b5a6f" 
}
  chartParams.element = document.getElementById('chartf7d8646b5a6f')
  
  
  var mapchartf7d8646b5a6f = new Datamap(chartParams);
  
{% endhighlight %}

If you open up your maps in your browser, you will now see that if you hover over a state, you will see the numbers behind the human or economic impact and that there is a comma separate grouping three digits, and that whenever a state has missing values, the state will be in a gray color:

<h3><strong>Fatalities</strong></h3>
<iframe src='{{site.baseurl}}/blogfigs/2015-10-06-weather-impact/fatalities.html' scrolling='no' seamless
class='rChart datamaps '
id=iframe-chart_4 
frameBorder = "0"
></iframe>

<style>iframe.rChart{ width: 100%; height: 430px;}</style>


<h3><strong>Property damage</strong></h3>
<iframe src='{{site.baseurl}}/blogfigs/2015-10-06-weather-impact/property.html' scrolling='no' seamless
class='rChart datamaps '
id=iframe-chart_5 
frameBorder = "0"
></iframe>
	
<style>iframe.rChart{ width: 100%; height: 430px;}</style>
 

And that is it, we are done! Now you can show off your new interactive choropleth maps to your friends, colleagues and neighbours, or you can play with them in your R environment or embed them and share them on your blogs, like I did :D 

I hope you enjoyed it, more posts will be coming shortly. 
For any further questions, please feel free to <a href="{{site.baseurl}}/contact/">contact me</a> or leave a comment. You can also see the code for the maps I made <a href="https://github.com/ohlphi/weather-choropleth-maps">here</a>. 
