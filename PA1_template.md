# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data



```r
unzip("activity.zip")
data <- read.csv("activity.csv")
```

## What is mean total number of steps taken per day?

```r
stepsByDate <- aggregate(steps ~ date, data = data, FUN = sum)
barplot(height = stepsByDate$steps, names.arg = stepsByDate$date, xlab="Date", ylab="Steps")
```

![](figure/unnamed-chunk-3-1.png) 

```r
mean(stepsByDate$steps)
```

```
## [1] 10766.19
```

```r
median(stepsByDate$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

```r
stepsByInterval <- aggregate(steps ~ interval, data = data, FUN = mean)
plot(stepsByInterval, type="l")
```

![](figure/unnamed-chunk-4-1.png) 

```r
stepsByInterval$interval[which.max(stepsByInterval$steps)]
```

```
## [1] 835
```

## Imputing missing values

```r
sum(is.na(data))
```

```
## [1] 2304
```

```r
dataBis <- merge(x = data, y=stepsByInterval, by="interval", suffixes=c("",".y"))
nas <- is.na(dataBis$steps)
dataBis$steps[nas] <- dataBis$steps.y[nas]
dataBis <- dataBis[,c(1:3)]

stepsByDateBis <- aggregate(steps ~ date, data = dataBis, FUN = sum)
barplot(height = stepsByDateBis$steps, names.arg = stepsByDateBis$date, xlab="Date", ylab="Steps")
```

![](figure/unnamed-chunk-5-1.png) 

```r
mean(stepsByDateBis$steps)
median(stepsByDateBis$steps)
```

## Are there differences in activity patterns between weekdays and weekends?

```r
daytype <- function(date) {
  if (weekdays(as.Date(date)) %in% c("Samedi", "Dimanche")) {
    "weekend"
  } else {
    "weekday"
  }
}

dataBis$daytype <- as.factor(sapply(dataBis$date, daytype))

stepsByIntervalWeekday <- aggregate(steps ~ interval, data=dataBis, subset = dataBis$daytype == "weekday", FUN = mean)
stepsByIntervalWeekend <- aggregate(steps ~ interval, data=dataBis, subset = dataBis$daytype == "weekend", FUN = mean)

par(mfrow = c(2, 1))
plot(stepsByIntervalWeekday, type="l",main="Weekday")
plot(stepsByIntervalWeekend, type="l", main="Weekend")
```

![](figure/unnamed-chunk-6-1.png) 
