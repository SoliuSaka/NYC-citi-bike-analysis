# NYC Citi Bike Analysis

## Table of Contents
1. [Project Overview](#project-overview)
2. [Objectives](#objectives)
3. [Data Source](#data-source)
4. [Methodology](#methodology)
    - [Step 1: Data Collection](#step-1-data-collection)
    - [Step 2: Data Analysis](#step-2-data-analysis)
5. [Results](#results)
6. [Conclusions](#conclusions)
7. [Recommendations](#recommendations)

## Project Overview
This project analyzes Citi Bike usage in New York City to support the introduction of a bike-sharing system. The goal is to provide data-driven insights to city officials, highlighting the potential benefits for sustainability and urban transportation.

## Objectives
1. Gather data related to Citi Bike trip durations.
2. Analyze average trip durations by station.
3. Compare trip durations across different stations.
4. Identify the five stations with the longest mean trip durations.

## Data Source
- **Dataset**: New York Citi Bike
- **Table**: `citibike_trips`
- **BigQuery Link**: [BigQuery Public Dataset - New York Citi Bike](https://console.cloud.google.com/marketplace/product/bigquery-public-data/new_york_citibike)

## Methodology
### Step 1: Data Collection
- Accessed the Citi Bike trip data using SQL in BigQuery.
- Queried the `citibike_trips` table to extract relevant data.

### Step 2: Data Analysis
- **Average Trip Duration by Station**: Used SQL subqueries to calculate the average trip duration for each station.
``` SQL
SELECT
    subquery.start_station_id,
    subquery.avg_duration
FROM
    (
    SELECT
        start_station_id,
        AVG(tripduration) as avg_duration
FROM bigquery-public-data.new_york_citibike.citibike_trips
GROUP BY start_station_id) as subquery
ORDER BY avg_duration DESC;

```
  
- **Comparison of Trip Durations**: Compared trip durations across various stations using SQL.
``` SQL
SELECT
    starttime,
    start_station_id,
    tripduration,
    (
        SELECT ROUND(AVG(tripduration),2)
        FROM bigquery-public-data.new_york_citibike.citibike_trips
        WHERE start_station_id = outer_trips.start_station_id
    ) AS avg_duration_for_station,
    ROUND(tripduration - (
        SELECT AVG(tripduration)
        FROM bigquery-public-data.new_york_citibike.citibike_trips
        WHERE start_station_id = outer_trips.start_station_id), 2) AS difference_from_avg
FROM bigquery-public-data.new_york_citibike.citibike_trips AS outer_trips
ORDER BY difference_from_avg DESC
LIMIT 25;
```
- **Longest Mean Trip Durations**: Identified the five stations with the longest mean trip durations using SQL subqueries.
```
SELECT
    tripduration,
    start_station_id
FROM bigquery-public-data.new_york_citibike.citibike_trips
WHERE start_station_id IN
    (
        SELECT
            start_station_id
        FROM
        (
            SELECT
                start_station_id,
                AVG(tripduration) AS avg_duration
            FROM bigquery-public-data.new_york_citibike.citibike_trips
            GROUP BY start_station_id
        ) AS top_five
        ORDER BY avg_duration DESC
        LIMIT 5
    );

```

## Results
1. Average Trip Duration by Station
![Average Trip Duration by Station](https://github.com/SoliuSaka/NYC-citi-bike-analysis/blob/main/AVR%20trip%20duration%20by%20stations.jpg)
2. Comparison of Trip Durations
![Comparison of Trip Durations](https://github.com/SoliuSaka/NYC-citi-bike-analysis/blob/main/comparing%20trip%20duration%20by%20station.jpg)
3. Longest Mean Trip Durations
![Longest Mean Trip Durations](https://github.com/SoliuSaka/NYC-citi-bike-analysis/blob/main/Longest%20Mean%20trip%20duration.jpg)

## Conclusions
- The analysis of Citi Bike trip data in New York City reveals significant patterns in bike usage, highlighting the potential for a bike-sharing system to contribute to the city's sustainability goals. The average trip durations and the identification of key stations indicate a strong demand for cycling infrastructure and services.

## Recommendations
  1. Invest in Bike Infrastructure: Enhance and expand bike lanes, particularly around stations with high average trip durations, to improve safety and accessibility for cyclists.
  2. Implement Bike Sharing: Consider the introduction of a bike-sharing program in areas with high trip demand to encourage more residents to use bicycles for commuting and leisure.
  3. Promote Public Awareness: Launch campaigns to educate the public on the benefits of cycling and the availability of bike-sharing options, aiming to increase user adoption.
  4. Monitor and Evaluate: Ensure the ongoing of data collection and analysis to monitor bike usage trends, allowing for responsive adjustments to the bike-sharing program and infrastructure.
  5. Collaborate with Community Stakeholders: Engage with local communities, businesses, and environmental organizations to build support for cycling initiatives and ensure they meet the needs of residents.
