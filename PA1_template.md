---
title: "Reproducible Research: Peer-Review Assessment 1"
output: 
  html_document:
    keep_md: true
---

The following document specifies the submission for the first peer-review assignment for the course *Reproducible Research*. Several steps are defined below. 






## Loading the Data and Preprocessing the Data
The first part of the analysis consists out of *loading the data*. This is done by applying the chunck of code specified below.  


```r
unzip("activity.zip")
data_activity <- read.csv("activity.csv")
```

Three libaries (*ggplot2*, *knitr* and *dplyr*) are loaded through the chunck of code below. 


```r
library(dplyr)
library(ggplot2)
library(knitr)
```

The data consists out of three variables. These being the following: 

* **Steps**: The number of steps taken in a 5 - minute interval. (NA = A missing value) 
* **Date**: The date on which the measurements were taken. (YYYY - MM - DD)
* **Interval**: Identifiers for the interval in which a measurement was taken.

The code below specifies how this information was retrieved.


```r
str(data_activity)
```

```
## 'data.frame':	17568 obs. of  3 variables:
##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ date    : chr  "2012-10-01" "2012-10-01" "2012-10-01" "2012-10-01" ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
```






## The Mean of the Total Number of Steps taken per Day
Displaying the mean total number of steps taken per day is done by subsequently covering three distinct steps. These being: 

* **(1)** Calculating the total numbers of steps taken per day. 
* **(2)** Making a histogram of the steps taken per day.
* **(3)** Reporting the mean and median total number of steps taken per day.




#### 1. The Number of Steps per Day.
The number of steps taken per day is calculated through the folllowing code (Note that we are allowed to ignore the NA values): 

```r
StepsDay <- aggregate(data_activity$steps, list(data_activity$date), FUN = sum)
colnames(StepsDay) <- c("Date", "Steps")
StepsDay
```

```
##          Date Steps
## 1  2012-10-01    NA
## 2  2012-10-02   126
## 3  2012-10-03 11352
## 4  2012-10-04 12116
## 5  2012-10-05 13294
## 6  2012-10-06 15420
## 7  2012-10-07 11015
## 8  2012-10-08    NA
## 9  2012-10-09 12811
## 10 2012-10-10  9900
## 11 2012-10-11 10304
## 12 2012-10-12 17382
## 13 2012-10-13 12426
## 14 2012-10-14 15098
## 15 2012-10-15 10139
## 16 2012-10-16 15084
## 17 2012-10-17 13452
## 18 2012-10-18 10056
## 19 2012-10-19 11829
## 20 2012-10-20 10395
## 21 2012-10-21  8821
## 22 2012-10-22 13460
## 23 2012-10-23  8918
## 24 2012-10-24  8355
## 25 2012-10-25  2492
## 26 2012-10-26  6778
## 27 2012-10-27 10119
## 28 2012-10-28 11458
## 29 2012-10-29  5018
## 30 2012-10-30  9819
## 31 2012-10-31 15414
## 32 2012-11-01    NA
## 33 2012-11-02 10600
## 34 2012-11-03 10571
## 35 2012-11-04    NA
## 36 2012-11-05 10439
## 37 2012-11-06  8334
## 38 2012-11-07 12883
## 39 2012-11-08  3219
## 40 2012-11-09    NA
## 41 2012-11-10    NA
## 42 2012-11-11 12608
## 43 2012-11-12 10765
## 44 2012-11-13  7336
## 45 2012-11-14    NA
## 46 2012-11-15    41
## 47 2012-11-16  5441
## 48 2012-11-17 14339
## 49 2012-11-18 15110
## 50 2012-11-19  8841
## 51 2012-11-20  4472
## 52 2012-11-21 12787
## 53 2012-11-22 20427
## 54 2012-11-23 21194
## 55 2012-11-24 14478
## 56 2012-11-25 11834
## 57 2012-11-26 11162
## 58 2012-11-27 13646
## 59 2012-11-28 10183
## 60 2012-11-29  7047
## 61 2012-11-30    NA
```




#### 2. Histogram of the Steps per Day 
Drawing the histogram is done by using *ggplot2* The following chunck of code specifies this. 

```r
library(ggplot2)

histogramplot <- ggplot(StepsDay, aes(Steps))
final_histogram <- histogramplot + geom_histogram(boundary = 0, binwidth = 2500, col = "orangered4", fill = "orangered1") + ggtitle("Histogram of Steps per Day") + xlab("Steps") + ylab("Frequency")
final_histogram
```

```
## Warning: Removed 8 rows containing non-finite values (stat_bin).
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

In order to make the histogram downloadable, the following code can be executed:


```r
png("./figures/Plot1.png")
final_histogram
dev.off()
```


```r
library(knitr)
include_graphics("./figures/Plot1.png")
```

![](./figures/Plot1.png)<!-- -->




#### 3. Reporting the Mean and Median of the Total Number of Steps taken per Day
Calculating the mean and median of the total number of steps taken per day is done by applying the following chuck of code: 


```r
mean(StepsDay$Steps, na.rm = TRUE)
```

```
## [1] 10766.19
```

```r
median(StepsDay$Steps, na.rm = TRUE)
```

```
## [1] 10765
```

It can be concluded that the Mean and Median are respectively *10766.19* and *10765*.






## Daily Activity Pattern
Assessing the Daily Activity Pattern is done by means of two steps. Them being the following: 

* **(1)** Creating a time series plot of the 5-minute interval and the average number of steps taken, averaged across all days.
* **(2)** Looking at the 5-minute interval which contains the maximum number of steps. 




#### 1. Creating a Time Series Plot
The following chunk of code is applied in order to create a Time Series Plot. First of all, the steps per interval are selected before using *ggplot2* to create the Time Series Plot. 


```r
library(ggplot2)

StepsInterval <- aggregate(steps ~ interval, data = data_activity, FUN = mean, na.action = na.omit)
StepsInterval$time <- StepsInterval$interval/100

time_series <- ggplot(StepsInterval, aes(time, steps))
final_time_series <- time_series + geom_line(col = "purple") + ggtitle("Average Steps per Time Interval") + xlab("Time") + ylab("Steps")
final_time_series
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

In order to make the time series downloadable, the following code can be executed:


```r
png("./figures/Plot2.png")
final_time_series
dev.off()
```


```r
include_graphics("./figures/Plot2.png")
```

![](./figures/Plot2.png)<!-- -->




#### 2. 5-Minute Interval with Maximum Number of Steps
Assessing the 5-minute interval with the maximum number of steps is done by using *dpylr*. The following chunck of code is used. 


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
MAX <- tibble :: as_tibble(StepsInterval)
MAX %>% select(time, steps) %>% filter(steps == max(MAX$steps))
```

```
## # A tibble: 1 x 2
##    time steps
##   <dbl> <dbl>
## 1  8.35  206.
```






## Imputting Missing Values
The following chuncks of code are concerned with analysing the number of NAs in the dataset. The following steps will be taken: 

* **(1)** Calculate and Report the total number of missing values.
* **(2)** Devise a strategy for filling in all the missing values in the dataset.
* **(3)** Create a new dataset which includes the filled in NAs.
* **(4)** Make a histogram, reporting both the mean and median. 




#### 1. Calculate and Report the Missing Values
The number of NAs will, once again, be calculated by using *dyplr*


```r
library(dplyr)
NUMBER <- tibble :: as_tibble(data_activity)
NUMBER %>% filter(is.na(steps)) %>% summarize(missing_values = n())
```

```
## # A tibble: 1 x 1
##   missing_values
##            <int>
## 1           2304
```
The table shows that there is a total of 2304 Missing Values. 




#### 2. Replacing the Missing Values
The mean of the average steps per 5-minute interval is used in order to replace the NA values.


```r
data_activity$CompleteData <- ifelse(is.na(data_activity$steps), round(StepsInterval$steps[match(data_activity$interval, StepsInterval$interval)], 0), data_activity$steps)
```




#### 3. Create a New Dataset 
The new dataset is created y using the impot from the previous step. The function head() is used to display the first few lines of the new dataset. 


```r
data_activity_full <- data.frame(steps = data_activity$CompleteData, interval = data_activity$interval, date = data_activity$date)

head(data_activity_full, n = 20)
```

```
##    steps interval       date
## 1      2        0 2012-10-01
## 2      0        5 2012-10-01
## 3      0       10 2012-10-01
## 4      0       15 2012-10-01
## 5      0       20 2012-10-01
## 6      2       25 2012-10-01
## 7      1       30 2012-10-01
## 8      1       35 2012-10-01
## 9      0       40 2012-10-01
## 10     1       45 2012-10-01
## 11     0       50 2012-10-01
## 12     0       55 2012-10-01
## 13     0      100 2012-10-01
## 14     1      105 2012-10-01
## 15     0      110 2012-10-01
## 16     0      115 2012-10-01
## 17     0      120 2012-10-01
## 18     1      125 2012-10-01
## 19     2      130 2012-10-01
## 20     0      135 2012-10-01
```



#### 4. Create a Histogram
A histogram is created, which marks the mean and median. These descriptive measures will be compared with earlier calculations. The histogram is plotted using *ggplot2* 


```r
StepsDayFull <- aggregate(data_activity_full$steps, list(data_activity_full$date), FUN = sum)
colnames(StepsDayFull) <- c("Date", "Steps")

histogram_full <- ggplot(StepsDayFull, aes(Steps))
histogram_full_final <- histogram_full + geom_histogram(col = "slateblue4", fill = "slateblue") + ggtitle("Histogram of Steps per Day") + xlab("Steps") + ylab("Frequency")
histogram_full_final
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](PA1_template_files/figure-html/unnamed-chunk-16-1.png)<!-- -->

In order to make the histogram downloadable, the following code can be executed:


```r
png("./figures/Plot3.png")
histogram_full_final
dev.off()
```


```r
include_graphics("./figures/Plot3.png")
```

![](./figures/Plot3.png)<!-- -->

The mean and median are calculated through the folowwing chunck of code: 


```r
mean(StepsDayFull$Steps)
```

```
## [1] 10765.64
```

```r
median(StepsDayFull$Steps)
```

```
## [1] 10762
```

It can be concluded that the input of values instead of NAs has little to no impact on the mean and median. 






## Differences in Activity Patterns between Weekdays and Weekends
The last part of this assignment consists out of two distinct steps.

* **(1)** Creating a new factor variable in the dataset with two levels: "weekday" and "weekend"
* **(2)** Creating a panel plot, containing a time series plot and the average number of steps taken.




#### 1. Creating a New Factor Variable
The following function creates a new variable which evaluates whether a day is a "weekday" or a "weekend"


```r
data_activity_full$RealDate <- as.Date(data_activity_full$date, format = "%Y-%m-%d")
data_activity_full$Weekday <- weekdays(data_activity_full$RealDate)
data_activity_full$Type <- ifelse(data_activity_full$Weekday == 'zaterdag' | data_activity_full$Weekday == 'zondag', 'weekend', 'weekday')
head(data_activity_full, n = 20)
```

```
##    steps interval       date   RealDate Weekday    Type
## 1      2        0 2012-10-01 2012-10-01 maandag weekday
## 2      0        5 2012-10-01 2012-10-01 maandag weekday
## 3      0       10 2012-10-01 2012-10-01 maandag weekday
## 4      0       15 2012-10-01 2012-10-01 maandag weekday
## 5      0       20 2012-10-01 2012-10-01 maandag weekday
## 6      2       25 2012-10-01 2012-10-01 maandag weekday
## 7      1       30 2012-10-01 2012-10-01 maandag weekday
## 8      1       35 2012-10-01 2012-10-01 maandag weekday
## 9      0       40 2012-10-01 2012-10-01 maandag weekday
## 10     1       45 2012-10-01 2012-10-01 maandag weekday
## 11     0       50 2012-10-01 2012-10-01 maandag weekday
## 12     0       55 2012-10-01 2012-10-01 maandag weekday
## 13     0      100 2012-10-01 2012-10-01 maandag weekday
## 14     1      105 2012-10-01 2012-10-01 maandag weekday
## 15     0      110 2012-10-01 2012-10-01 maandag weekday
## 16     0      115 2012-10-01 2012-10-01 maandag weekday
## 17     0      120 2012-10-01 2012-10-01 maandag weekday
## 18     1      125 2012-10-01 2012-10-01 maandag weekday
## 19     2      130 2012-10-01 2012-10-01 maandag weekday
## 20     0      135 2012-10-01 2012-10-01 maandag weekday
```

Excuse the Dutch :) Maandag means Monday, Dinsdag is Tuesday, Woensdag is Wednesday, Donderdag is Thursday, Vrijdag is Friday, Zaterdag is Saturday and Zondag is Sunday. 




#### 2. Creating a Panel Plot
First of all, a table will be created with the steps per interval across weekdays or weekend days. 


```r
StepsIntervalType <- aggregate(steps ~ interval + Type, data = data_activity_full, FUN = mean, na.action = na.omit)
```

The variable *Time* is added.


```r
StepsIntervalType$time <- StepsIntervalType$interval/100
```

The plot is drawn using *ggplot2*


```r
library(ggplot2)
panel_plot <- ggplot(StepsIntervalType, aes(time, steps))
panel_plot_final <- panel_plot + geom_line(col = "purple") + ggtitle("Average Steps per Time Interval: Weekdays versus Weekends") + ylab("Steps") + xlab("Time") + facet_grid(Type ~ .)
panel_plot_final
```

![](PA1_template_files/figure-html/unnamed-chunk-23-1.png)<!-- -->

In order to make the panel plot downloadable, the following code can be executed:


```r
png("./figures/Plot4.png")
panel_plot_final
dev.off()
```


```r
include_graphics("./figures/Plot4.png")
```

![](./figures/Plot4.png)<!-- -->






