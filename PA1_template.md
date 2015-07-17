# Reproducible Research: Peer Assessment 1

## Install and Load Libraries

```r
# Auto Install Packages
list.of.packages <- c("dplyr", "ggplot2", "knitr")
new.packages <- list.of.packages[!(list.of.packages %in% installed.packages()[,"Package"])]

if(length(new.packages)) install.packages(new.packages)

## Bring in the libs.
library(dplyr)
library(ggplot2)
library(knitr)
```

## Loading and preprocessing the data

```r
    # Load the data from CSV
    activity <- read.csv((unz("activity.zip", "activity.csv")))
    
    # Turn the date column into actual dates.    
    activity <- activity %>%
        mutate(date = as.Date(date))
```



## What is mean total number of steps taken per day?
For this part of the assignment, you can ignore the missing values in the dataset.

1. Calculate the total number of steps taken per day
2. If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day
3. Calculate and report the mean and median of the total number of steps taken per day


```r
    daily_activity <- activity %>%
        filter(!is.na(steps)) %>%
        group_by(date) %>%
        summarise(sumSteps = sum(steps, na.rm = TRUE))

    g <- ggplot(data = daily_activity, aes(sumSteps)) + 
        labs(title="Total Number of Steps Taken Per Day, Histogram") + 
        geom_histogram()

    print(g)
```

![](PA1_template_files/figure-html/sumsteps-1.png) 

```r
## Todo - come back for axis labels.
```

#### Mean Steps Taken Per Day: 

```r
    meanSteps <- mean(daily_activity$sumSteps, na.rm = TRUE)
    # Make it a bit easier to read than the super long number.
    round(meanSteps, 3)
```

[1] 10766.19

##### Median Steps Per Day

```r
    medianSteps <- median(daily_activity$sumSteps, na.rm = TRUE)
    medianSteps
```

[1] 10765




## What is the average daily activity pattern?

##### 1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
    ## Once again ignoring NAs
    clean_activity <- filter(activity, !is.na(steps))
    
    intervalCount <- count(clean_activity, interval)
   
    activity_pattern <- activity %>%
        group_by(interval) %>%
        summarise(sum_steps = sum(steps, na.rm = TRUE)) %>%
        inner_join(intervalCount, c("interval")) %>%
        mutate(avg_steps_per_interval = sum_steps / n)

    ## Using ggplot2, since assignment allows using any plotting system.
    g <- ggplot(
            data = activity_pattern, 
            aes(x = interval, y = avg_steps_per_interval)
        ) + 
        labs(
            title = "Average Daily Activity Pattern",
            x = "Interval",
            y = "Average Steps Per Interval"
        ) + 
        geom_line()
    print(g)
```

![](PA1_template_files/figure-html/activity_pattern-1.png) 

```r
## TODO -- come back for x axis labels for 5 minute intervals.

#plot(activity_pattern$interval, activity_pattern$avg_steps_per_interval, type="l")
```

##### 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
    ap_ordered <- arrange(activity_pattern, desc(avg_steps_per_interval))
    ap_high_interval <- ap_ordered[1, 1]
    ap_high_interval_steps <- ap_ordered[1, 4]
```

The 5 minute interval 835 has the highest number of average steps per day, with 206.17 steps per interval. 

## Imputing missing values



## Are there differences in activity patterns between weekdays and weekends?
