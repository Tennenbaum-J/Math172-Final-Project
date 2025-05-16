# Phase 1:

Research Question Proposal:
"How does the weather impact the transportation in NYC?"

We seek to understand how different weather conditions impact ridership across various methods of transportation in NYC. The effects of rain, snow, and temperature will be the primary focus.

The datasets we will use are: 
1) MTA Daily Ridership Data: 2020 - 2025  
This dataset covers daily ridership across all modes of MTA transportation (Subways, Buses, LIRR, Metro-North, Access-A-Ride, Bridges and Tunnels, Staten Island Railway).

2) Taxi data  
This dataset covers daily ridership in taxicabs.

3) National Weather Service  
This dataset tracks many weather-related metrics across many weather stations daily.

4) Citi Bike
This dataset tracks Citi Bike usage.


Potential Linkage Keys  
Date/time.

# Phase 2:

**Initial strategy for joining or cross-referencing datasets**
The datasets will be left joined by date. We will join everything onto the weather dataset.
We are using a left join in order to ensure that we maintain all the dates in the weather dataset even if one of the observations in one of our other datasets is null for one day (although this is unexpected).
Initially, we will join important weather attributes along with counts for taxi rides per day, subway riders per day, citibike rides, busgoers per day, and bridges and tunnels taken per day.
In our further analysis, we will look at specific weather attributes in relation to our methods of transportation (taxi, subway, citibike, bus, bridges and tunnels). 
We will merge our transportation datasets with that specific weather attribute. 

**Intermediate merged table with grain definition and justification**
SQL code for merging the tables:
-- merging tables on date
select w.date, w.avg_avg_temp, w.avg_max_temp, w.avg_min_temp, w.avg_precip, w.avg_snow, w.avg_snow_depth, w.avg_wind_speed, m.subways, m.buses, m.bridges_and_tunnels, t.count as taxi_count, c.count as citibike_count 
from weather w
left join taxi t on t.date = w.date
left join mta m on m.date = w.date
left join citibike c on c.date = w.date
order by w.date;

Each row in the merged dataset will respresent one day. Our data ranges from March 1, 2020 to January 9, 2025.
Using each row to represent a day means we will be able to see how specific weather attributes can affect ridership and use of different modes of transportation.

**EDA Through Data Visualization**
Using the merged table, separate SQL queries were written to extract data related to average precipitation, snowfall, and temperature. These subsets were exported and visualized individually in Tableau. Multiple charts were created and combined into an interactive dashboard.

Included are the charts focusing on the relationship between a specific weather attribute and ridership, as well as s seasonal ridership chart.  The seasonal chart showed that extreme weather seasons (particularly winter) had noticeably lower ridership compared to fall and spring.

The dashboard can be viewed here: https://public.tableau.com/app/profile/esther.yee/viz/EDA_Visual/Dashboard1#1

# Notes on the data:
1) MTA Daily Ridership Data
This dataset was clean from the get-go. All we did was import it as a ```VARCHAR``` to ensure landing, and then copied it to a cleaned table with the correct types. One thing to note with this table is that this is the limit when it comes to the dates. The other data sets went further back in time, but this one only went to 2020.

2) Taxi data  
This dataset was in the form of many parquet files. To import the data we used a python script to retreave it and ontother one to convert it to CSV format. Then, using a final script, we landed the raw data in a ```VARCHAR``` table to ensure everything landed. At this point, each row of the data represented a single ride, but we were interested in daily data. To solve this, we aggregated the data into a clean table, grouping the relevant information by date.

3) National Weather Service  
To get this data from the National Weather Service, we had to go to their website and request it; however, due to data limitations, we had to ship it in two separate parts. A consequence of this is that the schema has changed between the two tables. We solved this by normalising the schema of the CSVs using a Python script. We could the copy the data into a ```VARCHAR``` table to ensure eveything landed. At this point, each row of the data represented the reading of a single weather station per day, but we were interested in the average daily data. To solve for this, we aggregated the data into a clean table, grouping the relevant information by date. One column of note was a categorical variable, so special code was required to get the mode of the feature.

6) Citi Bike
To import this data, we used a Python script and then unzipped the files using the terminal. Once the files were unzipped, we used a Python script to copy them into a ```VARCHAR``` table to ensure everything landed. At this point, each row of the data represented a single ride, but we were interested in daily data. To solve this, we aggregated the data into a clean table, grouping the relevant information by date.
