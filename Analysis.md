
Average trip duration by rider type

    SELECT rider_type, AVG(trip_duration) AS average_trip_duration 
    FROM tripdata
    GROUP BY rider_type
    
    
"casual" :	"00:37:34.324499"
"member" :  "00:14:23.432426"


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

