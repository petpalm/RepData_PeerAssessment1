---
title: "Report Writing Project 1"
author: "Peter Palmer."
date: "4/23/2020"
output:
  html_document: 
    keep_md: yes
  pdf_document: default
---



## Introduction

It is now possible to collect a large amount of data about personal movement using activity monitoring devices such as [Fitbit](https://www.fitbit.com/us/home), [Nike Fuelband](https://www.nike.com/help/a/why-cant-i-sync), or [Jawbone Up](https://www.jawbone.com/). These type of devices are part of the “quantified self” movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. But these data remain under-utilized both because the raw data are hard to obtain and there is a lack of statistical methods and software for processing and interpreting the data.

## Data 

This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.

The data for this assignment can be downloaded from the course web site:

- Dataset: [Activity monitoring data](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip) [52K]

The variables included in this dataset are:

- **steps**: Number of steps taking in a 5-minute interval (missing values are coded as NA)  
- **date**: The date on which the measurement was taken in YYYY-MM-DD format  
- **interval**: Identifier for the 5-minute interval in which measurement was taken  

The dataset is stored in a comma-separated-value (CSV) file and there are a total of 17,568 observations in this dataset.

## Loading and preprocessing the data


Assign the url for the data set to a handle; download the file in the working directory; check the download date; and 
unzip the file in the folder reportwriting.


```r
fileUrl <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
download.file(fileUrl, destfile = "./repdata%2Fdata%2Factivity.zip", method ="curl")
downloadDate <- date()
downloadDate
```

```
## [1] "Thu Apr 23 12:44:37 2020"
```

Load the data in R using read.csv() and check the first few rows.


```r
activity <- read.csv("./reportwriting/activity.csv", header=TRUE)
head(activity, n=5)
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
## 2    NA 2012-10-01        5
## 3    NA 2012-10-01       10
## 4    NA 2012-10-01       15
## 5    NA 2012-10-01       20
```

### What is mean total number of steps taken per day? 

1. Calculate the total number of steps taken per day


```r
stepsperday <- aggregate(steps~date, data = activity, FUN = sum, na.rm = TRUE)
head(stepsperday)
```

```
##         date steps
## 1 2012-10-02   126
## 2 2012-10-03 11352
## 3 2012-10-04 12116
## 4 2012-10-05 13294
## 5 2012-10-06 15420
## 6 2012-10-07 11015
```


2. Make a histogram of the total number of steps taken each day. Using the base plotting system, 
we create a histogram; and change the format of the given dates. 


```r
activity$date <- as.Date(activity$date, "%Y-%m-%d")

hist(stepsperday$steps, 
    main="Histogram of Total Steps per Day", 
    xlab="Steps per Day",
    col="blue",
    breaks=100 )
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

3. Calculate and report the mean and median of the total number of steps taken per day. The mean and median
can be computed separtely using the coressponding functions or we can use the summary function to get those values together. 


```r
meansteps <- mean(stepsperday$steps)
meansteps
```

```
## [1] 10766.19
```


```r
mediansteps <- median(stepsperday$steps)
mediansteps
```

```
## [1] 10765
```


```r
summary(stepsperday$steps)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##      41    8841   10765   10766   13294   21194
```
## What is the average daily activity pattern?

1. Make a time series plot (i.e. 𝚝𝚢𝚙𝚎 = "𝚕") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis). Use the base plotting system to plot a line graph. 


```r
stepinterval <- aggregate(steps ~ interval, data = activity, FUN = mean, na.rm = TRUE)      
head(stepinterval, n=3)  
```

```
##   interval     steps
## 1        0 1.7169811
## 2        5 0.3396226
## 3       10 0.1320755
```


```r
plot(x = stepinterval$interval, 
     y = stepinterval$steps, 
     type = "l", 
     col = "red",
     xlab = "Interval",
     ylab = "Average Number of Steps",
     main="Time Series Plot"    
    )
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png)<!-- -->


2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
maxnumbstep <- stepinterval$interval[which.max(stepinterval$steps)]
maxnumbstep
```

```
## [1] 835
```

## Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
numbnas <- sum(is.na(activity$steps))
numbnas
```

```
## [1] 2304
```
    
    
2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc. Therefore, use the median number of steps per day fill in missing values.  
    
3. Create a new dataset that is equal to the original dataset but with the missing data filled in. Replace the NAs in the steps coulmn of the activity data frame with the the median number of steps per day. Check to see that there are no NAs in the data frame. Look at the first three rows of the data frame to see that NAs have been replaced.  


```r
activity$steps[is.na(activity$steps)] <- median(stepsperday$steps)
numbnasNew <- sum(is.na(activity$steps))
numbnasNew
```

```
## [1] 0
```


```r
head(activity, n=3)
```

```
##   steps       date interval
## 1 10765 2012-10-01        0
## 2 10765 2012-10-01        5
## 3 10765 2012-10-01       10
```


4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

Create a new data frame with the data from the activity data frame. and look at the first few rows. 


```r
activityNew <- activity
head(activityNew)
```

```
##   steps       date interval
## 1 10765 2012-10-01        0
## 2 10765 2012-10-01        5
## 3 10765 2012-10-01       10
## 4 10765 2012-10-01       15
## 5 10765 2012-10-01       20
## 6 10765 2012-10-01       25
```

For the new data frame, determine the total number of steps per day, and form a data frame with this new result.


```r
stepsperdayNew <- aggregate(steps~date, data = activityNew, FUN = sum)
head(stepsperdayNew)
```

```
##         date   steps
## 1 2012-10-01 3100320
## 2 2012-10-02     126
## 3 2012-10-03   11352
## 4 2012-10-04   12116
## 5 2012-10-05   13294
## 6 2012-10-06   15420
```

Plot a histogram but use the log function to control the extreme values. Therefore, more bars can be viewed in the plot. 


```r
hist(log(stepsperdayNew$steps), 
    main="Histogram of Total Steps per Day",   
    xlab="Steps per Day",  
    col="red",  
    breaks=100 )
```

![](PA1_template_files/figure-html/unnamed-chunk-16-1.png)<!-- -->


```r
summary(stepsperdayNew$steps)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##      41    9819   11458  415954   15084 3100320
```
The median and mean have incresed when the NAs are replaced with the median number of steps. 


## Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.
1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.


```r
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
activityNew <- activityNew %>% 
        mutate(typeofday= ifelse(weekdays(activityNew$date)=="Saturday" | weekdays(activityNew$date)=="Sunday", "Weekend", "Weekday"))
head(activityNew)
```

```
##   steps       date interval typeofday
## 1 10765 2012-10-01        0   Weekday
## 2 10765 2012-10-01        5   Weekday
## 3 10765 2012-10-01       10   Weekday
## 4 10765 2012-10-01       15   Weekday
## 5 10765 2012-10-01       20   Weekday
## 6 10765 2012-10-01       25   Weekday
```


2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
fivemininterval<- aggregate(steps ~ interval, data = activityNew, FUN = mean )
head(fivemininterval)
```

```
##   interval    steps
## 1        0 1413.295
## 2        5 1412.098
## 3       10 1411.918
## 4       15 1411.934
## 5       20 1411.869
## 6       25 1413.623
```




```r
library(ggplot2)
ggplot(activityNew, aes(x =interval , y=steps, color=typeofday)) +
       geom_line() +
       labs(x = "Interval", y = "Total Number of Steps", title = "Ave Daily Steps") +
       facet_wrap(~typeofday, ncol = 1, nrow=2)
```

![](PA1_template_files/figure-html/unnamed-chunk-20-1.png)<!-- -->
