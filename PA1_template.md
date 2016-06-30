# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data


```r
dataURL <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
if(!file.exists("activity.zip")) 
  {
    download.file(dataURL,"activity.zip")
  }
unzip("activity.zip")
dataSet <- read.csv("activity.csv")
```

## What is mean total number of steps taken per day?

1. Calculate the total number of steps taken per day
2. If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day


```r
#stepsTable <- tapply(dataSet$steps, dataSet$date, sum) - this produces dates with 0 steps
stepsTable <- aggregate(steps ~ date, dataSet, sum)
#barplot(stepsTable$steps, names.arg=stepsTable$date, ylab="Steps")
hist(stepsTable$steps, main = "Frequency of days by total step count")
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

3. Calculate and report the mean and median of the total number of steps taken per day

```r
mean(stepsTable$steps, na.rm=TRUE)
```

```
## [1] 10766.19
```

```r
median(stepsTable$steps, na.rm=TRUE)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
stepsTable <- aggregate(steps ~ interval, dataSet, mean)
plot(stepsTable, type="l")
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
maxNum <- which.max(stepsTable$steps)
stepsTable$interval[maxNum]
```

```
## [1] 835
```

## Imputing missing values
1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
nrow(dataSet[!complete.cases(dataSet),])
```

```
## [1] 2304
```

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
3. Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
fixedSet <- subset(dataSet, TRUE) #create empty copy of the dataSet
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
stepsTableFixed <- aggregate(steps ~ date, fixedSet, sum)
hist(stepsTableFixed$steps, main = "Frequency of days by total step count")
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

```r
mean(stepsTableFixed$steps)
```

```
## [1] 10766.19
```

```r
median(stepsTableFixed$steps)
```

```
## [1] 10765
```

## Are there differences in activity patterns between weekdays and weekends?

1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
fixedSet$day <- ifelse(weekdays(as.Date(fixedSet$date)) %in%  c("Saturday","Sunday") ,"weekend","weekday")
```

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.



```r
par(mfrow = c(2, 1))
wknd <- aggregate(steps ~ interval, fixedSet, subset = fixedSet$day == "weekend", mean)
weekday <- aggregate(steps ~ interval, fixedSet, subset = fixedSet$day == "weekday", mean)
plot(wknd, type = "l", main = "weekend")
plot(weekday, type = "l", main = "weekday")
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png)<!-- -->
