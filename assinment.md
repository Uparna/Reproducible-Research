

---
title: "Reproducible research"
author: "Uparna"
date: "10/13/2020"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

#Reproducible Reseach assignment 1

## Loading and processing the data

First we need to load data from the file.

```{r}
library(readr)
activity <- read_csv("repdata_data_activity/activity.csv")
```



## what is mean number of steps taken per day?
 
Now we want a histogram of the total number of steps taken each day.

```{r}
stepSums <- aggregate(activity$steps, by=list(activity$date),sum, na.rm = TRUE)
colnames(stepSums) <- c("date", "sum")
hist(stepSums$sum, xlab = "Steps Taken Per Day", main = "Histogram of Taken Per Day")
```
Now we will calculate the mean and median of these sums per day:
```{r}
mean(stepSums$sum)
median(stepSums$sum)
```



## What is the average daily pattern?

First we want to plot the average number of steps taken across the days for the various time intervals.
```{r}
intervalMeans <- aggregate(activity$steps, by=list(activity$interval), mean, na.rm=TRUE)
colnames(intervalMeans) <- c("interval", "steps")
```
Now we plot the time series data:
```{r}
plot(intervalMeans$interval, intervalMeans$step, type = "l", 
     xlab = "5-Minute Interval", ylab = "Mean Steps Taken",
     main = "Steps during the day")
```
Now we calculate the at which interval on average had the maximum numbers of steps?
```{r}
intervalMeans$interval[which.max(intervalMeans$steps)]
```


## Imputing missing values
First calculate the missing data
```{r}
sum(!complete.cases(activity))
```
Now produce a histogram without missing data.
```{r}
library(dplyr)
activity2 <- activity
iMeans <- by(activity$steps, activity$interval, mean, na.rm=TRUE)
activity2 <- filter(activity2, steps != "NA")
stepSums2 <- aggregate(activity2$steps, by=list(activity2$date),sum)
colnames(stepSums2) <- c("date", "sum")
hist(stepSums2$sum, xlab = "Steps Taken Per Day", 
     main = "Histogram of Steps Taken Per Day(with imputed missing data)")
```
Now calculate the mean and madian of these sums of steps per day:
```{r}
mean(stepSums2$sum)
median(stepSums2$sum)
```


## Are there difference in activity patterns between weekdays and weekends?
We are going to add a facttor variable to our data set with the filled in missing values for the weekends and weekdays.
```{r}
 days <- weekdays(as.Date(activity2$date))
 weekends <- days =="Saturday" | days == "Sunday"
 days[weekends] <- "weekend"
 days[!weekends] <- "weekday"
 activity2$dayType <- factor(days)
 aggMeans <- aggregate(activity2$steps, by = list(activity2$interval, activity2$dayType),mean)
 colnames(aggMeans) <- c("interval", "dayType", "steps")
```
Now we plot this data.
```{r}
library(lattice)
 xyplot(aggMeans$steps ~ aggMeans$interval | aggMeans$dayType, aggMeans, type = "l", 
        layout = c(1, 2), xlab = "Interval", ylab = "Number of Steps")
```