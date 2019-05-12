---
title: "Reproducible Research: Peer Assessment 1"
author: "Jessica Roland"
date: "11 May 2019"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data
Read in the data

```r
dataset <- read.csv("activity.csv")
```

Change date string to date format  

```r
dataset$date <-(as.Date(dataset$date))
```

```
## Warning in strptime(xx, f <- "%Y-%m-%d", tz = "GMT"): unknown timezone
## 'zone/tz/2019a.1.0/zoneinfo/America/Los_Angeles'
```

Get ggplot2 library

```r
library(ggplot2)
```
## What is mean total number of steps taken per day?

Get sum of steps per day

```r
dailySteps <- tapply(dataset$steps, dataset$date, FUN=sum, na.rm=TRUE)
```

Make a histogram of steps per day

```r
hist(dailySteps,col="blue")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

Calculate the mean steps per day

```r
dailyStepsMean <-mean(dailySteps, na.rm=TRUE)
dailyStepsMean
```

```
## [1] 9354.23
```

Calculate the median steps per day

```r
dailyStepsMedian <-median(dailySteps, na.rm=TRUE)
dailyStepsMedian
```

```
## [1] 10395
```
##What is the average daily activity pattern?

calculate mean of steps per interval

```r
average_steps <- aggregate(x=list(steps=dataset$steps), by=list(interval=dataset$interval),FUN=mean, na.rm=TRUE)
```

plot it

```r
ggplot(data=average_steps, aes(x=interval, y=steps, colour="#000099")) + geom_line() + ggtitle("Time Series: average number of steps") + xlab("5-minute interval") + ylab("average number of steps taken")
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

identify interval with the highest average max # of steps

```r
average_steps[which.max(average_steps$steps),]
```

```
##     interval    steps
## 104      835 206.1698
```

##Imputing missing values

count the number of NAs (missing data)

```r
sum(is.na(dataset$steps))
```

```
## [1] 2304
```

replace NA values with the mean of the steps

```r
dataset$steps[is.na(dataset$steps)]<-mean(dataset$steps,na.rm=TRUE)
```

Get sum of steps per day with the NA values replaced

```r
replacedNA_dailySteps <- tapply(dataset$steps, dataset$date, FUN=sum, na.rm=TRUE)
```
Make a histogram of steps per day with the NA values replaced

```r
hist(replacedNA_dailySteps,col="blue")
```

![](PA1_template_files/figure-html/unnamed-chunk-14-1.png)<!-- -->

Calculate the new "replacedNA" mean steps per day

```r
replacedNA_dailyStepsMean <-mean(replacedNA_dailySteps, na.rm=TRUE)

replacedNA_dailyStepsMean
```

```
## [1] 10766.19
```
Calculate the new "replacedNA" median steps per day

```r
replacedNA_dailyStepsMedian <-median(replacedNA_dailySteps, na.rm=TRUE)

replacedNA_dailyStepsMedian
```

```
## [1] 10766.19
```

##Are there differences in activity patterns between weekdays and weekends?

change date format to weekdays

```r
dataset$days=weekdays(dataset$date)
```

change day names to weekend or weekday

```r
dataset$days_type<-ifelse(dataset$days=="Saturday"|dataset$days=="Sunday","weekend","weekday")
```

calculate mean steps taken per interval, using the weekday/weekend categorization

```r
average_steps_byDaysType<-aggregate(dataset$steps,by=list(dataset$interval,dataset$days_type),FUN=mean,na.rm=TRUE)
```

simplify the column names in preparation for plotting

```r
colnames(average_steps_byDaysType)<-c("interval","day_type","steps")
```

plot the graphs of weekend versus weekday steps by interval

```r
ggplot(aes(x=interval,y=steps),data=average_steps_byDaysType)+geom_line()+facet_wrap(~average_steps_byDaysType$day_type)
```

![](PA1_template_files/figure-html/unnamed-chunk-21-1.png)<!-- -->












