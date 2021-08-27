### Load required library
    library(tidyverse) 
    library(lubridate)  
    library(ggplot2) 

### set working directory
    getwd()
    setwd("/Users/anil/Documents/")

### load dataset
    tripdata <- read_csv('tripdata_master.csv')

### Inspect the new table that has been created
    head(tripdata)
    colnames(tripdata)
    nrow(tripdata)
    dim(tripdata)
    str(tripdata)
    summary(tripdata)

### check for observations in column for any mistakes
    table(tripdata$rider_type)
    table(tripdata$rideable_type)

### Add columns that list the date, month, day, and year of each ride
    tripdata$date <- as.Date(tripdata$started_at)
    tripdata$month <- format(as.Date(tripdata$date),'%m')
    tripdata$day <- format(as.Date(tripdata$date),'%d')
    tripdata$year <- format(as.Date(tripdata$date),'%Y')
    tripdata$day_of_week <- format(as.Date(tripdata$date),'%A')

### Add a "ride_length" calculation
    tripdata$ride_length <- difftime(tripdata$ended_at,tripdata$started_at)

### Convert "ride_length" from Factor to numeric so we can run calculations on the data
    is.factor(tripdata$ride_length)
    is.numeric(tripdata$ride_length)
    is.character(tripdata$ride_length)

    tripdata$ride_length <- as.numeric(as.character(tripdata$ride_length))
    
    is.numeric(tripdata$ride_length)

### Remove ride length where it is negative or zero
    tripdata <- tripdata[!(tripdata$ride_length <= 0),]

    tripdata <- tripdata %>% 
                    select(-c(start_lat, start_lng, end_lat, end_lng))

### drop rows having null
    tripdata <- tripdata %>% drop_na()

### Descriptive analysis on ride_length
    summary(tripdata$ride_length)

### Compare members and casual users
    aggregate(tripdata$ride_length ~ tripdata$rider_type, FUN = mean)
    aggregate(tripdata$ride_length ~ tripdata$rider_type, FUN = median)
    aggregate(tripdata$ride_length ~ tripdata$rider_type, FUN = max)
    aggregate(tripdata$ride_length ~ tripdata$rider_type, FUN = min)

### See the average ride time by each day for members vs casual users
    aggregate(tripdata$ride_length ~ tripdata$rider_type + 
                tripdata$day_of_week, FUN = mean)

### Order by day
    tripdata$day_of_week <- ordered(tripdata$day_of_week, levels = c('Sunday',
                            'Monday','Tuesday','Wednesday','Thursday','Friday','Saturday'))

### run the aggregate again
    aggregate(tripdata$ride_length ~ tripdata$rider_type + 
                tripdata$day_of_week, FUN = mean)

### analyze ridership data by type and weekday
    tripdata %>% mutate(weekday = wday(started_at, label = T)) %>% 
    group_by(rider_type,weekday) %>% 
    summarise(number_of_rides = n(), average_duration = mean(ride_length)) %>% 
    arrange(rider_type, weekday)

### Plot
    tripdata %>% mutate(weekday = wday(started_at, label = T)) %>% 
    group_by(rider_type,weekday) %>% 
    summarise(number_of_rides = n(), average_duration = mean(ride_length)) %>% 
    arrange(rider_type, weekday) %>% 
    ggplot(aes(x = weekday, y = number_of_rides, fill = rider_type)) +
                      geom_col(position = 'dodge')


tripdata %>% mutate(weekday = wday(started_at, label = T)) %>% 
    group_by(rider_type,weekday) %>% 
    summarise(number_of_rides = n(), average_duration = mean(ride_length)) %>% 
    arrange(rider_type, weekday) %>% 
    ggplot(aes(x = weekday, y = average_duration, fill = rider_type)) +
    geom_col(position = 'dodge')



























