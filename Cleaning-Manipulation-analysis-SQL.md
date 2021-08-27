### Cleaning

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
using DISTINCT function.

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

### Analysis

Average trip duration by rider type

    SELECT rider_type, AVG(trip_duration) AS average_trip_duration 
    FROM tripdata
    GROUP BY rider_type
    
    
"casual" :	"00:37:34.324499"
"member" :  "00:14:23.432426"


Average trip duration for each rider type by day of week
  
    SELECT rider_type, day_of_week, AVG(trip_duration) AS average_trip_duration
    FROM tripdata
    GROUP BY 2,1
    ORDER BY 3 DESC
    
"casual"	"Sunday"	"00:43:17.843691"
"casual"	"Saturday"	"00:40:18.437458"
"casual"	"Monday"	"00:36:51.44145"
"casual"	"Friday"	"00:35:42.857019"
"casual"	"Wednesday"	"00:33:22.887567"
"casual"	"Tuesday"	"00:33:21.673899"
"casual"	"Thursday"	"00:32:49.348675"
"member"	"Sunday"	"00:16:30.035743"
"member"	"Saturday"	"00:15:55.019743"
"member"	"Friday"	"00:14:01.603394"
"member"	"Monday"	"00:13:51.607175"
"member"	"Wednesday"	"00:13:39.838663"
"member"	"Tuesday"	"00:13:34.988858"
"member"	"Thursday"	"00:13:24.307766"


Maximum trip duration by rider type

    SELECT rider_type, Max(trip_duration) AS maximum_trip_duration 
    FROM tripdata
    GROUP BY rider_type
    
"casual" :	"38 days 20:24:09"
"member" :	"23 days 05:01:22"


Number of each rider type

    SELECT rider_type, COUNT(*)
    FROM tripdata
    GROUP BY rider_type
    
    
"casual" :	1826043
"member" :	2333089


Number of rideable types

    SELECT rideable_type, COUNT(*)
    FROM tripdata
    GROUP BY rideable_type
    
  "classic_bike" :	1780342
  "docked_bike" :	1547791
  "electric_bike" :	830999
  

Number of rides by rider_type and rideable_type

    SELECT rider_type, rideable_type,COUNT(*)
    FROM tripdata
    GROUP BY 1,2
    ORDER BY 3 DESC
    
"member"	"classic_bike"	1087973
"member"	"docked_bike"	790970
"casual"	"docked_bike"	756821
"casual"	"classic_bike"	692369
"member"	"electric_bike"	454146
"casual"	"electric_bike"	376853
  

A summary table was created for visualisation purpose

    CREATE TABLE tripdata_summary AS
    (SELECT rideable_type, start_station_name, end_station_name, day_of_week,
    trip_duration_cat, month_started_at, rider_type, COUNT(*) 
    FROM tripdata
    GROUP BY 1, 2, 3, 4, 5, 6, 7)

Tableu public and spreadsheet were used for visualisation.

