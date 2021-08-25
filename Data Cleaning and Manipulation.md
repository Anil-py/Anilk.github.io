## Data cleaning and manipulationlog

### cleaning

Past 12 month data was made available in separate csv file 
for each month. The data was combined to form a single csv 
file and imported to SQL (pgAdmin)
The data had many rows with null values and these were removed.
New table created by removing rows with null values 
        
    CREATE TABLE tripdata AS 
    SELECT * FROM tripdata_master WHERE tripdata IS NOT NULL;

There are rows where trip start time is greater than trip end time, 
and rows where they are same which seems to be irrelevant to analysis.

    SELECT COUNT (*) 
    FROM tripdata
    WHERE started_at > ended_at OR started_at = ended_at;

Since the count of such rows are very less as compared to total rows 
in data these rows have been removed from database and will not be 
considered in further analysis
    
    DELETE FROM tripdata 
    WHERE started_at > ended_at OR started_at = ended_at;
                    
Columns rideable_type, member_casual were checked for any typo errors 
using DISTICT function.

Total rows remaining after the cleaning process : 4159132

    SELECT COUNT(*)
    FROM tripdata


----------My initial plan was to do basic cleaning using spreadsheet but after 
doing this for one month data I realised it would be a time consuming activity 
as spreadsheet could not handle entire dataset in single sheet hence decided to 
combine all the data in single csv file and use SQL for cleaning----------------


### Manipulation

member_casual was renamed to rider_type

New column trip_duration, day_of_week, month_started_at were created

    ALTER TABLE tripdata
    ADD COLUMN trip_duration INTERVAL, 
    ADD COLUMN day_of_week VARCHHAR,
    ADD COLUMN month_started_at VARCHAR

    UPDATE tripdata
    SET trip_duration = ended_at - started_at

    UPDATE tripdata
    SET day_of_week = 
    EXTRACT(dow FROM tripdata.started_at)
    
    UPDATE tripdata
    SET month_started_at = 
    EXTRACT(MONTH FROM tripdata.started_at)


A new column trip_duration_cat was created to categorise trip_duration column in limited range
This was done so that it can be used in GROUP BY clause to summarise into limited
number of intervals.


    UPDATE tripdata
    SET trip_duration_cat =
    (CASE
    WHEN trip_duration <= '00:15:00' THEN  '<=15 min'
    WHEN trip_duration > '00:15:00' and trip_duration <= '00:30:00' THEN '15 - 30 min'
    WHEN trip_duration > '00:30:00' and trip_duration <= '00:45:00' THEN '30 - 45 min'
    WHEN trip_duration > '00:45:00' and trip_duration <= '01:00:00' THEN '45 - 60 min'
    WHEN trip_duration > '01:00:00' and trip_duration <= '01:30:00' THEN '60 - 90 min'
    WHEN trip_duration > '01:30:00' and trip_duration <= '02:00:00' THEN '90 - 120 min'
    WHEN trip_duration > '02:00:00' and trip_duration <= '24:00:00' THEN '02 - 24 hrs'
    WHEN trip_duration > '24:00:00' and trip_duration <= '2 days 00:00:00' THEN '1 - 2 day'
    WHEN trip_duration > '2 days 00:00:00' and trip_duration <= '5 days 00:00:00' THEN '2 - 5 day'
    WHEN trip_duration > '5 days 00:00:00' and trip_duration <= '7 days 00:00:00' THEN '1 week'
    WHEN trip_duration > '7 days 00:00:00' and trip_duration <= '14 days 00:00:00' THEN '2 weeks'
    WHEN trip_duration > '14 days 00:00:00' and trip_duration <= '21 days 00:00:00' THEN '3 weeks'
    WHEN trip_duration > '21 days 00:00:00' and trip_duration <= '30 days 00:00:00' THEN '4 weeks'
    WHEN trip_duration > '30 days 00:00:00' THEN 'more than a month' 
    END)


A summary table was created to use for visualisation

    CREATE TABLE tripdata_summary AS
    (SELECT rideable_type, start_station_name, end_station_name, day_of_week,
    trip_duration_cat, month_started_at, rider_type, COUNT(*) 
    FROM tripdata
    GROUP BY 1, 2, 3, 4, 5, 6, 7)

Tableu public and spreadsheet were used for visualisation.

