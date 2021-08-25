
A summary table was created to use for visualisation

    CREATE TABLE tripdata_summary AS
    (SELECT rideable_type, start_station_name, end_station_name, day_of_week,
    trip_duration_cat, month_started_at, rider_type, COUNT(*) 
    FROM tripdata
    GROUP BY 1, 2, 3, 4, 5, 6, 7)

Tableu public and spreadsheet were used for visualisation.