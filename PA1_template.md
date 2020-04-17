Loading and preprocessing the data
----------------------------------

    activity<-read.csv("activity.csv")

What is mean total number of steps taken per day?
-------------------------------------------------

\*Calculate the total number of steps taken per day

    totalstep<-aggregate(steps~date,activity,FUN = sum,na.rm=T)

\*Make a histogram of the total number of steps taken each day

    hist(totalstep$steps,xlab="total steps")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-3-1.png)

\*Calculate and report the mean and median of the total number of steps
taken per day

    mean(totalstep$steps)

    ## [1] 10766.19

    median(totalstep$steps)

    ## [1] 10765

-   The **mean** total number of steps taken per day is 1.076618910^{4}
    steps.

-   The **median** total number of steps taken per day is 10765 steps.

What is the average daily activity pattern?
-------------------------------------------

\*Make a time series plot of the 5-minute interval (x-axis) and the
average number of steps taken, averaged across all days (y-axis)

    stepinterval<-aggregate(steps~interval,activity,FUN = mean,na.rm=T)

    plot(x=stepinterval$interval,y=stepinterval$steps,type = "l",xlab="interval",ylab="steps")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-5-1.png)

\*Which 5-minute interval, on average across all the days in the
dataset, contains the maximum number of steps?

    stepinterval$interval[which.max(stepinterval$steps)]

    ## [1] 835

-   The 5-minute interval, contains the maximum number of steps is
    number 835

Imputing missing values
-----------------------

\*Calculate and report the total number of missing values in the dataset

    sum(is.na(activity))

    ## [1] 2304

-   There is 2304 rows with NA in the dataset

\*Devise a strategy for filling in all of the missing values in the
dataset

We can use the mean for that 5-minute interval to fill in the missing
values

    get_mean_step<-function(interval){
      stepinterval[stepinterval$interval==interval,]$steps
    }

\*Create a new dataset that is equal to the original dataset but with
the missing data filled in.

    activity1<-activity
    for(i in 1:nrow(activity1)){}
    for(i in 1:nrow(activity1)){
    if(is.na(activity1[i,]$steps)){
    activity1[i,]$steps<-get_mean_step(activity1[i,]$interval)
    }
    }

\*Make a histogram of the total number of steps taken each day

    hist(aggregate(steps~date,activity1,FUN = sum)$steps,xlab = "date",ylab = "steps")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-10-1.png)

\*Calculate and report the mean and median total number of steps taken
per day

    mean(aggregate(steps~date,activity1,FUN = sum)$steps)

    ## [1] 10766.19

    median(aggregate(steps~date,activity1,FUN = sum)$steps)

    ## [1] 10766.19

-   The **mean** total number of steps taken per day is 1.076618910^{4}
    steps.

-   The **median** total number of steps taken per day is
    1.076618910^{4} steps.

\*Do these values differ from the estimates from the first part of the
assignment? What is the impact of imputing missing data on the estimates
of the total daily number of steps?

The **mean** value is the same because we put the mean value, which has
0 weight on the data. The **median** has changed a little bit and that
is normal

Are there differences in activity patterns between weekdays and weekends?
-------------------------------------------------------------------------

\*Create a new factor variable in the dataset with two levels –
“weekday” and “weekend”

    library(lubridate)

    ## 
    ## Attaching package: 'lubridate'

    ## The following objects are masked from 'package:base':
    ## 
    ##     date, intersect, setdiff, union

    activity1$weekday<-ifelse(wday(as.Date(activity1$date)) %in% c(1,7),"weekend","weekday")
    activity1$weekday<-factor(activity1$weekday,levels=c("weekend","weekday"))

    stepinterval1<-aggregate(steps~interval+weekday,activity1,mean)
    library(ggplot2)
    p<-ggplot(stepinterval1,aes(x=interval,y=steps))+geom_line()+facet_grid(weekday~.)
    print(p)

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-13-1.png)
