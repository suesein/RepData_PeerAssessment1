Set working directory and loading the necessary packages
--------------------------------------------------------

    setwd('C:/Users/user/Desktop/Reproducible Research/')
    library(ggplot2)
    library(knitr)
    library(lattice)

Clearing the working environment before commencing the project
--------------------------------------------------------------

    rm(list=ls(all=TRUE))

Loading the data
----------------

    if(!file.exists('activity.csv')){unzip('activity.zip')}
    activitydata=read.csv("activity.csv",stringsAsFactors=FALSE)

Study the data
--------------

    head(activitydata)
    str(activitydata)

Mean and median total number of steps taken per day
---------------------------------------------------

    steps_by_day<-aggregate(steps~date,activitydata,sum)
    rmean<-mean(steps_by_day$steps)
    rmedian<-median(steps_by_day$steps)

The mean is 10766.19 The median is 10765

Including Plots
---------------

    steps_by_interval<-aggregate(steps~interval,activitydata,mean)

    plot( x=steps_by_interval[,1], 
          y=steps_by_interval[,2], 
          type="l",
          col="black",
          main="Average steps recorded per interval",
          ylab="Amount of steps", 
          xlab="Time interval")

![](Reproducible_Research_Assignment_files/figure-markdown_strict/plot-1.png)<!-- -->

    max_interval<-steps_by_interval[which.max(steps_by_interval$steps),1]

The 5-minute interval that, on average, contains the maximum number of
steps is 835

Imputing missing values and data cleaning
-----------------------------------------

    incomplete<-sum(!complete.cases(activitydata))

    Newdata<-transform(activitydata,steps=ifelse(is.na(activitydata$steps),steps_by_interval$steps[match(activitydata$interval,steps_by_interval$interval)],activitydata$steps))

    Newdata[as.character(Newdata$date)=="22-10-01",1]<-0

    steps_by_day2<-aggregate(steps~date,Newdata,sum)
    hist(steps_by_day2$steps,main=paste("Total steps taken each day"),col="green",xlab="Number of Steps")

![](Reproducible_Research_Assignment_files/figure-markdown_strict/plot%202-1.png)<!-- -->

Are there differences in activity patterns between weekdays and weekends
------------------------------------------------------------------------

    weekdays <- c("Monday", "Tuesday", "Wednesday","Thursday", "Friday")

    Newdata$dateofweek=as.factor(ifelse(is.element(weekdays(as.Date(Newdata$date)),weekdays),"Weekday","Weekend"))

    steps_by_interval_i<-aggregate(steps ~ interval + dateofweek, Newdata, mean)

    library(lattice)

    xyplot(steps_by_interval_i$steps ~ steps_by_interval_i$interval|steps_by_interval_i$dateofweek, main="Average Steps per Day by Interval",xlab="Interval", ylab="Steps",layout=c(1,2), type="l")

![](Reproducible_Research_Assignment_files/figure-markdown_strict/plot%203-1.png)<!-- -->
