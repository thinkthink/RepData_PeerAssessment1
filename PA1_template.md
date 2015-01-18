---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---
## Loading and preprocessing the data
   1 .Load the data
  ```{R}
    Data <- read.csv("./activity.csv",header=TRUE,na.string="NA")
  ```
   2 .Process/transform the data (if necessary) into a format suitable for analysis
  ```{R}
    SumData <- aggregate(data=Data,steps~date,FUN=sum,na.rm=TRUE)
  ```

## What is mean total number of steps taken per day?
1 .A histogram of the total number of steps taken each day
  ```{R results="asis"}
  hist(SumData$steps)
  ```
2 .The mean and median total number of steps taken per day
  ```{R}
  mean<-mean(SumData$steps)
  median<-median(SumData$steps)
  ```
  The mean total number of steps taken per day is 
  
  The median total number of steps taken per day is

## What is the average daily activity pattern?
  ```{R}
  AveData <- aggregate(data=Data,steps~interval,FUN=mean,na.rm=TRUE)
  plot(AveData$steps~AveData$interval,type="l")
  AveData[which.max(AveData$steps),]$interval
  ```

## Imputing missing values
  ```{R}
  NewData <- Data
  rows <- nrow(NewData)
  for(i in 1:rows)
  {
    if (is.na(NewData[i,]$steps))
    {
     n_interval<-NewData[1,]$interval
     NewData[i,]$steps <- mean(Data[Data$interval==n_interval,"steps"],na.rm=TRUE)
    }    
  }
  SumNewData <- aggregate(data=NewData,steps~date,FUN=sum,na.rm=TRUE)
  par(mfrow=c(2,1))
  hist(SumData$steps)
  hist(SumNewData$steps)
  Newmean<-mean(SumNewData$steps)
  Newmedian<-median(SumNewData$steps)
  ```
## Are there differences in activity patterns between weekdays and weekends?
```{R}
Sys.setlocale("LC_TIME","eng") 
NewData$type<-"weekday"  
rows <- nrow(NewData)
for(i in 1:rows)
  {
    if (weekdays(as.Date(NewData[i,]$date))=="Sunday"|weekdays(as.Date(NewData[i,]$date))=="Saturday")
    {
     NewData[i,]$type <- "weekend"
   }
 }

AveWeekData <- aggregate(steps~interval,subset(NewData,type=="weekday"),FUN=mean,na.rm=TRUE)
AveWeekendData <- aggregate(steps~interval,subset(NewData,type=="weekend"),FUN=mean,na.rm=TRUE)      
par(mfrow=c(2,1))
plot(AveWeekendData$steps~AveWeekendData$interval,type="l",xlab="interval",ylab="Number of steps",main="weekend")
plot(AveWeekData$steps~AveWeekData$interval,type="l",xlab="interval",ylab="Number of steps",main="weekday")
```

```{R}
install.packages("knitr")
library(knitr)
knit2html("PA1_template.md")
```
