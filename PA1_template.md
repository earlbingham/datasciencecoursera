
## Loading and preprocessing the data
Show any code that is needed to

1. Load the data (i.e. 𝚛𝚎𝚊𝚍.𝚌𝚜𝚟())
```
activity = read.csv("activity.csv")
```
2. Process/transform the data (if necessary) into a format suitable for your analysis
```
totalSteps <- aggregate(steps~date, data = activity, sum, na.rm = TRUE)
```


## What is mean total number of steps taken per day?
For this part of the assignment, you can ignore the missing values in the dataset.

1. Calculate the total number of steps taken per day
```
sum(is.na(activity$steps))
```
__The total number of steps taken per day is 2304__

2. If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day
```
hist(totalSteps$steps)
```
__The histogram is in the hist_total_steps_taken_per_day.png file__

3. Calculate and report the mean and median of the total number of steps taken per day
```
mean(totalSteps$steps)
median(totalSteps$steps)
```
__The mean total number of steps taken per day is: 10766.19__
__The median total number of steps taken per day is: 10765__


## What is the average daily activity pattern?
1. Make a time series plot (i.e. 𝚝𝚢𝚙𝚎 = "𝚕") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)
```
stepsInterval <- aggregate(steps~interval, data = activity, mean, na.rm = TRUE)
plot(steps~interval, data = stepsInterval, type = "l")
```
__The plot is saved as mean_steps_taken_per_day.png__

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?
```
stepsInterval[which.max(stepsInterval$steps),]$interval
```
__The 5-minute interval on average that is max number of steps is: 835__



## Imputing missing values
Note that there are a number of days/intervals where there are missing values (coded as 𝙽𝙰). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with 𝙽𝙰s)
```
sum(is.na(activity$steps))
```
__The number of missing values for steps is 835__

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
__Since each row always has a date and interview value, I decided to write a function that would have an interval as input to return the corresponding mean value for that interval. Using the average of the date would not provide good values to be used for missing values.__
```
meanStepsByInterval <- function(interval) {
    stepsInterval[stepsInterval$interval == interval, ]$steps
}
```

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.
```
activity2 <- activity  
for(i in 1:nrow(activity2)){
  if(is.na(activity2[i, ]$steps)) {
    activity2[i, ]$steps <- meanStepsByInterval(activity2[i, ]$interval)
  }
}
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?
```
totalSteps2 <- aggregate(steps~date, data=activity2, sum)
hist(totalSteps2$steps)
mean(totalSteps2$steps)
median(totalSteps2$steps)
```
__The mean value is 10766.19, which is the same as the earlier average value.__
__The median value is 10766.19, which isn't the same as earlier because of the empty fields were populated with average values. Making the median the same as the mean.__
__The impact of imputing missing data is that the median estimate is not realistic any longer, becoming the same as the average.__


## Are there differences in activity patterns between weekdays and weekends?
For this part the 𝚠𝚎𝚎𝚔𝚍𝚊𝚢𝚜() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.
```
activity$day = ifelse(grepl("S(atday|unday)", weekdays(as.Date(activity$date))), "weekend", "weekday")
```

2. Make a panel plot containing a time series plot (i.e. 𝚝𝚢𝚙𝚎 = "𝚕") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.
```
activity2MeanDayOfWeek = aggregate(steps~interval+day, activity, mean)
library(lattice)
xyplot(steps~interval|factor(day), data = activity2MeanDayOfWeek, aspect=1/2, type="l")
```
__Plot was saved to mean_steps_by_day_of_week.png file__

