# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data
First you have to set your working directory to where you cloned the git depository to first. Then you load the data into R:

```r
data<-read.csv(unz("activity.zip","activity.csv"))
```

## What is mean total number of steps taken per day?
To make a histogram of the total number of steps taken each day:

```r
sum<-aggregate(data$steps,list(date=data$date),sum)
hist(sum$x)
```

![plot of chunk unnamed-chunk-2](./PA1_template_files/figure-html/unnamed-chunk-2.png) 

The mean total number of steps taken per day is

```r
mean(sum$x,na.rm=TRUE)
```

```
## [1] 10766
```

The median total number of steps taken per day is

```r
median(sum$x,na.rm=TRUE)
```

```
## [1] 10765
```
## What is the average daily activity pattern?

```r
avg<-aggregate(data$steps,list(Interval=data$interval),mean,na.rm=TRUE)

plot(avg$Interval,avg$x,main="Average Daily Activity Pattern",xlab="5-minute Intervals",ylab="Avg num of steps",type="l")
```

![plot of chunk unnamed-chunk-5](./PA1_template_files/figure-html/unnamed-chunk-5.png) 

The inverval that contains the maximum number of steps is

```r
avg[which.max(avg$x),1]
```

```
## [1] 835
```

## Imputing missing values
The total number of missinv values in the dataset is

```r
sum(is.na(data$steps))
```

```
## [1] 2304
```

Create a new dataset which replaces missing values with the mean for the corresponding interval across all dates.

```r
newData<-data

for (i in 1:length(newData$steps)){
    if (is.na(newData[i,1])){
        search<-newData[i,3]
        newData[i,1]<-avg[grep(paste("^",search,"$",sep=""),avg$Interval),2]
    }
}
```

Now let's look at the histogram of the new dataset:

```r
newSum<-aggregate(newData$steps,list(date=newData$date),sum)
hist(newSum$x)
```

![plot of chunk unnamed-chunk-9](./PA1_template_files/figure-html/unnamed-chunk-9.png) 

The new mean total number of steps taken per day is

```r
mean(newSum$x,na.rm=TRUE)
```

```
## [1] 10766
```

The new median total number of steps taken per day is

```r
median(newSum$x,na.rm=TRUE)
```

```
## [1] 10766
```

Under this strategy of replacing missing values with the mean for the corresponding interval across all dates, the new mean and median total number of steps taken per day do not differ from the estimates. However, from the new historgram we can observe that the impact of the missing data is that it reduces the frequency of the 10k-15k bracket. 

## Are there differences in activity patterns between weekdays and weekends?

Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
wd<-weekdays(as.POSIXlt(newData$date))
wd<-gsub("Monday|Tuesday|Wednesday|Thursday|Friday","weekday",wd)
wd<-gsub("Saturday|Sunday","weekend",wd)
newData["weekday"]<-factor(wd)
```

Calculate the new average daily pattern according to weekdays/weekend

```r
avgwk<-aggregate(newData$steps,list(Interval=newData$interval,Week=newData$weekday),mean,na.rm=TRUE)
```

Plot the activity patterns between weekdays and weekends

```r
library(lattice)
    xyplot(avgwk$x~avgwk$Interval|avgwk$Week,data=avgwk,layout=c(1,2),type="l")
```

![plot of chunk unnamed-chunk-14](./PA1_template_files/figure-html/unnamed-chunk-14.png) 
