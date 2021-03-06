Setting global option to turn warnings off

    knitr::opts_chunk$set(warning=FALSE)

Read load the data
==================

    data<-read.table(unzip(".\\repdata%2Fdata%2Factivity.zip"),header=TRUE,sep=",",fill=TRUE,na.strings = "NA")
    names(data)

    ## [1] "steps"    "date"     "interval"

    dim(data)

    ## [1] 17568     3

    str(data)

    ## 'data.frame':    17568 obs. of  3 variables:
    ##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
    ##  $ date    : Factor w/ 61 levels "2012-10-01","2012-10-02",..: 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...

    summary(data)

    ##      steps                date          interval     
    ##  Min.   :  0.00   2012-10-01:  288   Min.   :   0.0  
    ##  1st Qu.:  0.00   2012-10-02:  288   1st Qu.: 588.8  
    ##  Median :  0.00   2012-10-03:  288   Median :1177.5  
    ##  Mean   : 37.38   2012-10-04:  288   Mean   :1177.5  
    ##  3rd Qu.: 12.00   2012-10-05:  288   3rd Qu.:1766.2  
    ##  Max.   :806.00   2012-10-06:  288   Max.   :2355.0  
    ##  NA's   :2304     (Other)   :15840

    head(data)

    ##   steps       date interval
    ## 1    NA 2012-10-01        0
    ## 2    NA 2012-10-01        5
    ## 3    NA 2012-10-01       10
    ## 4    NA 2012-10-01       15
    ## 5    NA 2012-10-01       20
    ## 6    NA 2012-10-01       25

    pairs(data)

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-2-1.png)

What is mean total number of steps taken per day?
=================================================

1.  Calculate the total number of steps taken per day

<!-- -->

    Stepsday<- aggregate(steps ~ date, data, sum, na.rm=TRUE)

2.If you do not understand the difference between a histogram and a
barplot, research the difference between them. Make a histogram of the
total number of steps taken each day

    hist(Stepsday$steps,main="Histogram of  total number of steps taken each day")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-4-1.png)
3.mean and median of the total number of steps taken per day mean total
number of steps taken each day is

    mean(Stepsday$steps)  

    ## [1] 10766.19

The median total number of steps taken each day is

    median(Stepsday$steps)

    ## [1] 10765

What is the average daily activity pattern?
===========================================

1.  Make a time series plot (i.e. type = "l") of the 5-minute
    interval (x-axis) and the average number of steps taken, averaged
    across all days (y-axis)

<!-- -->

    Stepsinterval<-aggregate(steps~interval, data=data, mean, na.rm=TRUE)
    plot(steps~interval, data=Stepsinterval, type="l",main="Average number of steps taken of the 5-minute interval ")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-7-1.png)

2.calculate Which 5-minute interval, on average across all the days in
the dataset, contains the maximum number of steps

    Stepsinterval[which.max(Stepsinterval$steps),]$interval

    ## [1] 835

Imputing missing values
=======================

1.Calculate and report the total number of missing values in the dataset

    sum(!complete.cases(data))

    ## [1] 2304

2.Devise a strategy for filling in all of the missing values in the
dataset. The strategy does not need to be sophisticated. For example,
you could use the mean/median for that day, or the mean for that
5-minute interval, etc.

use the mean for that 5-minute interval to fill the the missing valuse

3.Create a new dataset that is equal to the original dataset but with
the missing data filled in.

    datafillna<-data
    for(i in 1:nrow(datafillna))
    { if (is.na(datafillna$steps[i]))
      datafillna$steps[i]<-Stepsinterval$steps[i] 
    }

4.Make a histogram of the total number of steps taken each day and
Calculate and report the mean and median total number of steps taken per
day. Do these values differ from the estimates from the first part of
the assignment? What is the impact of imputing missing data on the
estimates of the total daily number of steps?

    datafillna_sum<-aggregate(steps ~ date, data=datafillna, FUN = "sum")
    hist(datafillna_sum$steps,main="Histogram of  total number of steps taken each day with na filled by mean of the 5-minute interval")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-11-1.png)
Here is the mean of the total number of steps taken per day:

    mean(datafillna_sum$steps) 

    ## [1] 10766.19

Here is the median of the total number of steps taken per day:

    median(datafillna_sum$steps)

    ## [1] 10765.59

The mean didn’t change after the replacements of NAs, the median
changed.

Are there differences in activity patterns between weekdays and weekends?
=========================================================================

1.Create a new factor variable in the dataset with two levels –
“weekday” and “weekend” indicating whether a given date is a weekday or
weekend day.

    Sys.setlocale("LC_TIME", "English")

    ## [1] "English_United States.1252"

    data$day<-weekdays(as.Date(data$date))
    data$day<-as.factor(data$day)

    levels(data$day)[levels(data$day) %in% c("Monday","Tuesday","Wednesday","Thursday","Friday" )] <- "weekday"
    levels(data$day)[levels(data$day) %in% c("Saturday","Sunday")] <- "weekend"

2.Make a panel plot containing a time series plot (i.e. type = "l") of
the 5-minute interval (x-axis) and the average number of steps taken,
averaged across all weekday days or weekend days (y-axis).

    datadayinterval <- aggregate(steps ~ interval+day, data, mean,na.rm=TRUE)
    library("lattice")
    xyplot(steps~interval|day,data=datadayinterval,type="l",layout=c(1,2),xlab="interval",ylab="Number of steps",main="Average number of steps taken, averaged across all weekday days or weekend days")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-15-1.png)
The average steps over the weekends show higher pattern than that of the
weekdays
