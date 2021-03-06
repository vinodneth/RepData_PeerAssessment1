# Reproducible Research: Peer Assessment 1
*This Markdown document uses the template provided as part of the assignment and completes the sections included there*

## Introduction
This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.

## Loading and preprocessing the data
Load the dplyr, lubridate, Hmisc and ggplot libraries. 

Reading the data file and finding total

```r
activities <- read.csv("activity.csv")
activities$date <- ymd(activities$date)

activities_tot<-tapply(activities$steps, activities$date, FUN=sum, na.rm=TRUE)
```

Histogram of the total number of steps taken each day

```r
items <- tapply(activities$steps, activities$date, FUN=sum)
barplot(items, las=2, cex.names = 0.5, main = "Steps taken each day")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

## What is mean total number of steps taken per day?

```r
meanstepsbyday <- mean(activities_tot)
medianstepsbyday <-median(activities_tot)
```

Mean: 9354.2295082  
Median: 10395


## What is the average daily activity pattern?
Average daily pattern is presented as a time series plot of the interval as below

```r
avgactivitybyinterval<-aggregate(.~interval,activities,mean)
plot(avgactivitybyinterval$interval,avgactivitybyinterval$steps,type="l",ylab = "Steps", xlab = "Day", las=2, main = "Time series plot of average steps taken")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

The 5-minute interval that, on average, contains the maximum number of steps is 835

## Imputing missing values

```r
numMissingValues <- length(which(is.na(activities$steps)))
activitiesfit<-activities
activitiesfit$steps<-impute(activities$steps, fun=mean)
```

The number of missing data is 2304  

Histogram with the missing data filled

```r
itemsfit <- tapply(activitiesfit$steps, activitiesfit$date, FUN=sum)
barplot(itemsfit, las=2, cex.names = 0.5,main = "Steps taken each day - Missing data filled")
```

![](PA1_template_files/figure-html/unnamed-chunk-7-1.png)<!-- -->


## Are there differences in activity patterns between weekdays and weekends?
Include if weekend or weekday to a new variable dateType. Find average by interval and dateType


```r
activities$dateType <-  ifelse(as.POSIXlt(activities$date)$wday %in% c(0,6), 'weekend', 'weekday')
avgactivitybydatetype <- aggregate(steps ~ interval + dateType, data=activities, mean)
```

Plot the average step trend by interval for weekday and weekend

```r
ggplot(avgactivitybydatetype, aes(interval, steps)) + 
  geom_line() + 
  facet_grid(dateType ~ .) +
  xlab("5-minute interval") + 
  ylab("average number of steps") +
  ggtitle(label="Plots of average steps by interval for weekday and weekends")
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png)<!-- -->
 
