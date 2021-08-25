
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
  


A summary table was created for visualisation purpose

    CREATE TABLE tripdata_summary AS
    (SELECT rideable_type, start_station_name, end_station_name, day_of_week,
    trip_duration_cat, month_started_at, rider_type, COUNT(*) 
    FROM tripdata
    GROUP BY 1, 2, 3, 4, 5, 6, 7)

Tableu public and spreadsheet were used for visualisation.

