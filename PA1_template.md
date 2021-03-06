# Reproducible Research: Peer Assessment 1
echo = TRUE

## Loading and preprocessing the data
unzip("activity.zip")

activity <- read.csv("activity.csv")

activity$date <- as.Date(activity$date, "%Y-%m-%d")

## What is mean total number of steps taken per day?
StepsPerDay <- tapply(activity$steps, activity$date, sum)

hist(StepsPerDay$steps, main = "Total steps taken per day", xlab = "Day")

mean(StepsPerDay$steps)

median(StepsPerDay$steps)

## What is the average daily activity pattern?
AverageActivity <- tapply(activity$steps, activity$interval, mean, na.rm = TRUE)

plot(row.names(AverageActivity), AverageActivity, type = "l", xlab = "Time intervals (5-minute)", ylab = "Average steps across all days", main = "Average steps taken at 5 minute intervals")

max_interval <- which.max(AverageActivity)

names(max_interval)

## Imputing missing values
NA_count <- sum(is.na(activity))

NA_count

NA_indicies <- which(is.na(activity))

fillValue <- AverageActivity[as.character(activity[NA_indicies, 3])]

for (i in NA_indicies) { 
  activity$steps[i] = fillValue[as.character(i)]
}

totalSteps <- tapply(activity$steps, activity$date, sum)

hist(totalSteps, xlab = "Day", ylab = "Frequency", main = "Total Steps taken per day")

## Are there differences in activity patterns between weekdays and weekends?
day <- weekdays(activity$date)

daylevel <- vector() 

for (i in 1:nrow(activity)) { 
  if (day[i] == "Saturday") { 
    daylevel[i] <- "Weekend" 
  } 
  else if (day[i] == "Sunday") { 
    daylevel[i] <- "Weekend" 
  }
  else { daylevel[i] <- "Weekday" 
  } 
}

activity$daylevel <- daylevel 

activity$daylevel <- factor(activity$daylevel)

stepsByDay <- aggregate(steps ~ interval + daylevel, data = activity, mean) 

names(stepsByDay) <- c("interval", "daylevel", "steps")

xyplot(steps ~ interval | daylevel, stepsByDay, type = "l", layout = c(1, 2), xlab = "Interval", ylab = "Number of steps")
