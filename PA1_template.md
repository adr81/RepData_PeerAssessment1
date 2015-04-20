# Reproducable Research Peer Assessment 1
Alan Rice  
April 19th 2015  

## Introduction ##

It is now possible to collect a large amount of data about personal movement using activity monitoring devices such as a [Fitbit](http://www.fitbit.com/), [Nike Fuelband](http://www.nike.com/us/en_us/c/nikeplus-fuelband), or [Jawbone Up](https://jawbone.com/up). These type of devices are part of the “quantified self” movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. But these data remain under-utilized both because the raw data are hard to obtain and there is a lack of statistical methods and software for processing and interpreting the data.

This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals throughout the day. The data consists of two months of data from an anonymous individual collected during the months of October and November 2012 and include the number of steps taken in 5 minute intervals each day.

## Data ##

The data for this assignment can be downloaded from the course web site:

- Dataset: [Activity monitoring data](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip) [56k]

The variables included in this dataset are:

- **steps**: Number of steps taking in a 5-minute interval (missing values are coded as `NA`).
- **date**: The date on which the measurement was taken in YYYY-MM-DD format.
- **interval**: Identifier for the 5-minute interval in which measurement was taken.

The dataset is stored in a comma-separated-value (CSV) file and there are a total of 17,568 observations in this dataset.

## Assignment ##

During this assignment I will carry out the steps below and answer the following questions:

1. Load and preprocess the data.
2. What is the mean total number of steps taken per day?
3. What is the average daily activity pattern?
4. Input missing values.
5. Are there any differences in activity patterns between weekdays and weekends?

## Loading and Preprocessing the data ##

The data is loaded into R and stored as `amd` *(Activity Monitoring Data)*.


```r
amd <- read.csv("./activity.csv", stringsAsFactors = FALSE)
summary(amd)
```

```
##      steps            date              interval     
##  Min.   :  0.00   Length:17568       Min.   :   0.0  
##  1st Qu.:  0.00   Class :character   1st Qu.: 588.8  
##  Median :  0.00   Mode  :character   Median :1177.5  
##  Mean   : 37.38                      Mean   :1177.5  
##  3rd Qu.: 12.00                      3rd Qu.:1766.2  
##  Max.   :806.00                      Max.   :2355.0  
##  NA's   :2304
```

This work requires the packages `dplyr`, `ggplot2` and `lubridate`.


```r
library(dplyr)
```

```
## Warning: package 'dplyr' was built under R version 3.1.2
```

```
## 
## Attaching package: 'dplyr'
## 
## The following object is masked from 'package:stats':
## 
##     filter
## 
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
library(ggplot2)
```

```
## Warning: package 'ggplot2' was built under R version 3.1.2
```

```r
library(lubridate)
```

```
## Warning: package 'lubridate' was built under R version 3.1.2
```

## What is the total number of steps taken per day? ##

In this section I will carry out the following:

1. Calculate the total number of steps taken per day.
2. Make a histogram of the total number of steps taken each day.
3. Calculate the mean and median of the total number of steps taken per day.

For this part of the assignment missing values are ignored.

**1. Calculate the total number of steps taken per day**

Group the data by date and summarise:


```r
daily <- summarise(group_by(amd, date), steps = sum(steps, na.rm = TRUE))
daily
```

```
## Source: local data frame [61 x 2]
## 
##          date steps
## 1  2012-10-01     0
## 2  2012-10-02   126
## 3  2012-10-03 11352
## 4  2012-10-04 12116
## 5  2012-10-05 13294
## 6  2012-10-06 15420
## 7  2012-10-07 11015
## 8  2012-10-08     0
## 9  2012-10-09 12811
## 10 2012-10-10  9900
## ..        ...   ...
```

**2. Make a histogram of the total number of steps taken each day**


```r
hist(daily$steps, xlab = "Steps", main = "Daily Steps", col = "steelblue")
```

![](./PA1_template_files/figure-html/unnamed-chunk-4-1.png) 

**3. Calculate the mean and median of the total number of steps taken per day**

The mean number of steps per day is:


```r
mean(daily$steps)
```

```
## [1] 9354.23
```

The median number of steps per day is:


```r
median(daily$steps)
```

```
## [1] 10395
```

## What is the average daily activity pattern? ##

In this section I will:

1. Make a time series plot of the 5 minute intervals and the average number of steps taken averaged across all days.
2. Identify which 5 minute interval, on average across all days, contains the maximum number of steps.

For this part of the assignment missing values are ignored.

**1. Make a time series plot of the 5 minute intervals and the average number of steps taken averaged across all days**

For the average number of steps I have taken the mean of each interval:


```r
intervals <- summarise(group_by(amd, interval), steps = mean(steps, na.rm = TRUE))
intervals
```

```
## Source: local data frame [288 x 2]
## 
##    interval     steps
## 1         0 1.7169811
## 2         5 0.3396226
## 3        10 0.1320755
## 4        15 0.1509434
## 5        20 0.0754717
## 6        25 2.0943396
## 7        30 0.5283019
## 8        35 0.8679245
## 9        40 0.0000000
## 10       45 1.4716981
## ..      ...       ...
```

Now plot the intervals against the mean number of steps:


```r
g <- qplot(interval, steps, data = intervals, geom = "line")
g + geom_line(color = "steelblue")
```

![](./PA1_template_files/figure-html/unnamed-chunk-8-1.png) 

**2. Identify which 5 minute interval, on average across all days, contains the maximum number of steps**


```r
filter(intervals, steps == max(intervals$steps))
```

```
## Source: local data frame [1 x 2]
## 
##   interval    steps
## 1      835 206.1698
```

## Input missing values ##

In this section I will:

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with `NA`s).
2. Devise a strategy for filling in all the missing values in the dataset.
3. Create a new dataset that is equal to the original dataset but with the missing data filled in.
4. Make a histogram of the total number of steps taken each day and calculate the mean and median steps taken per day.

**1. Calculate and report the total number of missing values in the dataset**


```r
sum(is.na(amd$steps))
```

```
## [1] 2304
```

**2. Devise a strategy for filling in all the missing values in the dataset**

I have decided that I will use the mean number of steps per 5 minute interval to replace the `NA`s, e.g. for the first observation in `amd` I will use the mean value for interval `0` e.g.:


```r
amd[1, ]
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
```

```r
filter(intervals, interval == 0)
```

```
## Source: local data frame [1 x 2]
## 
##   interval    steps
## 1        0 1.716981
```

**3. Create a new dataset that is equal to the original dataset but with the missing data filled in**

To do this firstly merge the `amd` and `intervals` dataframes to create a single dataframe containing both the number of steps and the average number for that interval:


```r
merged <- rename(merge(amd, intervals, by = "interval"), mean.steps = steps.y, steps = steps.x)
summary(merged)
```

```
##     interval          steps            date             mean.steps     
##  Min.   :   0.0   Min.   :  0.00   Length:17568       Min.   :  0.000  
##  1st Qu.: 588.8   1st Qu.:  0.00   Class :character   1st Qu.:  2.486  
##  Median :1177.5   Median :  0.00   Mode  :character   Median : 34.113  
##  Mean   :1177.5   Mean   : 37.38                      Mean   : 37.383  
##  3rd Qu.:1766.2   3rd Qu.: 12.00                      3rd Qu.: 52.835  
##  Max.   :2355.0   Max.   :806.00                      Max.   :206.170  
##                   NA's   :2304
```


Then separate the data into two dataframes, one without the `NA`s (`no.na`) and the other with only the `NA`s (`na.only`):


```r
no.na <- filter(merged, is.na(steps) == FALSE)
only.na <- filter(merged, is.na(steps) == TRUE)
summary(no.na)
```

```
##     interval          steps            date             mean.steps     
##  Min.   :   0.0   Min.   :  0.00   Length:15264       Min.   :  0.000  
##  1st Qu.: 588.8   1st Qu.:  0.00   Class :character   1st Qu.:  2.486  
##  Median :1177.5   Median :  0.00   Mode  :character   Median : 34.113  
##  Mean   :1177.5   Mean   : 37.38                      Mean   : 37.383  
##  3rd Qu.:1766.2   3rd Qu.: 12.00                      3rd Qu.: 52.835  
##  Max.   :2355.0   Max.   :806.00                      Max.   :206.170
```

```r
summary(only.na)
```

```
##     interval          steps          date             mean.steps     
##  Min.   :   0.0   Min.   : NA    Length:2304        Min.   :  0.000  
##  1st Qu.: 588.8   1st Qu.: NA    Class :character   1st Qu.:  2.486  
##  Median :1177.5   Median : NA    Mode  :character   Median : 34.113  
##  Mean   :1177.5   Mean   :NaN                       Mean   : 37.383  
##  3rd Qu.:1766.2   3rd Qu.: NA                       3rd Qu.: 52.835  
##  Max.   :2355.0   Max.   : NA                       Max.   :206.170  
##                   NA's   :2304
```

Now in the `only.na` dataframe replace the values in `steps` with those in `mean.steps`:


```r
only.na$steps <- only.na$mean.steps
summary(only.na)
```

```
##     interval          steps             date             mean.steps     
##  Min.   :   0.0   Min.   :  0.000   Length:2304        Min.   :  0.000  
##  1st Qu.: 588.8   1st Qu.:  2.486   Class :character   1st Qu.:  2.486  
##  Median :1177.5   Median : 34.113   Mode  :character   Median : 34.113  
##  Mean   :1177.5   Mean   : 37.383                      Mean   : 37.383  
##  3rd Qu.:1766.2   3rd Qu.: 52.835                      3rd Qu.: 52.835  
##  Max.   :2355.0   Max.   :206.170                      Max.   :206.170
```

Finally merge the two dataframes back together again:


```r
new.amd <- rbind(no.na, only.na)
summary(new.amd)
```

```
##     interval          steps            date             mean.steps     
##  Min.   :   0.0   Min.   :  0.00   Length:17568       Min.   :  0.000  
##  1st Qu.: 588.8   1st Qu.:  0.00   Class :character   1st Qu.:  2.486  
##  Median :1177.5   Median :  0.00   Mode  :character   Median : 34.113  
##  Mean   :1177.5   Mean   : 37.38                      Mean   : 37.383  
##  3rd Qu.:1766.2   3rd Qu.: 27.00                      3rd Qu.: 52.835  
##  Max.   :2355.0   Max.   :806.00                      Max.   :206.170
```

**4. Make a histogram of the total number of steps taken each day and calculate the mean and median steps taken per day**

As before group the data by date, summarise and then plot the histogram:


```r
new.daily <- summarise(group_by(new.amd, date), steps = sum(steps))
hist(new.daily$steps, xlab = "Steps", main = "Daily Steps", col = "steelblue")
```

![](./PA1_template_files/figure-html/unnamed-chunk-16-1.png) 

Then calculate the mean and median number of steps in the new data set:


```r
mean(new.daily$steps)
```

```
## [1] 10766.19
```

```r
median(new.daily$steps)
```

```
## [1] 10766.19
```

Now compare them to the old mean & median values:


```r
mean(daily$steps)
```

```
## [1] 9354.23
```

```r
median(daily$steps)
```

```
## [1] 10395
```

Replacing the `NA`s in the data with the mean for that interval has increased both the mean and median values:


```r
mean(new.daily$steps) - mean(daily$steps)
```

```
## [1] 1411.959
```

```r
median(new.daily$steps) - median(daily$step)
```

```
## [1] 371.1887
```

## Are there differences in activity patterns between weekdays and weekends? ##

In this section I will:

1. Create a new factor variable in the new dataset with two levels - *weekday* and *weekend* indicating whether a given date is on a weekday or weekend.
2. Make a panel plot containing a time series plot of the 5 minute interval and the average number of steps taken, averaged across all weekday & weekend days.

**1. Create a new factor variable in the new dataset with two levels - *weekday* and *weekend* indicating whether a given date is on a weekday or weekend**

The following code converts the `new.amd$date` variable into `POSIXct` format, determines what day of the week it is and then whether or not this is a *weekday* or *weekend* and adds this as a new factor variable:


```r
new.amd <- mutate(new.amd, day = as.factor(ifelse(weekdays(ymd(new.amd$date)) %in% c("Saturday", "Sunday"), "Weekend", "Weekday")))
summary(new.amd)
```

```
##     interval          steps            date             mean.steps     
##  Min.   :   0.0   Min.   :  0.00   Length:17568       Min.   :  0.000  
##  1st Qu.: 588.8   1st Qu.:  0.00   Class :character   1st Qu.:  2.486  
##  Median :1177.5   Median :  0.00   Mode  :character   Median : 34.113  
##  Mean   :1177.5   Mean   : 37.38                      Mean   : 37.383  
##  3rd Qu.:1766.2   3rd Qu.: 27.00                      3rd Qu.: 52.835  
##  Max.   :2355.0   Max.   :806.00                      Max.   :206.170  
##       day       
##  Weekday:12960  
##  Weekend: 4608  
##                 
##                 
##                 
## 
```

**2. Make a panel plot containing a time series plot of the 5 minute interval and the average number of steps taken, averaged across all weekday & weekend days**

As before summarise the data by `interval` & `day` then plot the data:


```r
new.intervals <- summarise(group_by(new.amd, interval, day), steps = mean(steps))
q <- qplot(interval, steps, data = new.intervals, facets = day ~ ., geom = "line")
q + geom_line(color = "steelblue")
```

![](./PA1_template_files/figure-html/unnamed-chunk-21-1.png) 
