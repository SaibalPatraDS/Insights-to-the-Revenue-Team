# Hospitality Revenue Insights using Power BI

Welcome to the documentation that outlines the entire process undertaken for the project aimed at providing insights to the Revenue Team in the Hospitality Domain using Power BI. The project's core objective was to analyze hospitality-related data and present these insights through well-designed Power BI reports.

## Data Loading and Power Query Documentation

### Step 1: Data Preparation

Start by creating a dedicated folder on your Desktop to systematically store all the CSV files associated with the hospitality challenge.

### Step 2: Data Loading

1. Launch Power BI and navigate to the "Get Data" option.
2. Choose "Folder" to facilitate data import and browse through the folder containing the designated CSV files.

### Step 3: Data Transformation

1. Once the data is loaded, proceed to the "Transform Data" option. This opens the Power Query Editor, where transformations can be applied.
2. Expand and format each dataset within the Power Query Editor.

### Step 4: Data Source Duplication and Expansion

1. Duplicate the data source four times, creating four distinct instances of the same source.
2. For each duplicated source, utilize the "Binary" option to expand one dataset.
3. Renaming tables is crucial for clear representation and interpretation.

## Power Query Steps for Tables

### `dim_date` Table

1. Omit the column 'day_type' to align with industry norms regarding weekends.
2. This action is prompted by feedback from a mock dashboard review, indicating that in the industry, Friday and Saturday are typically considered weekends. In our dataset, however, Saturday and Sunday were labeled as weekends. As a solution, 'day_type' is redefined using calculated columns.

#### Calculated Column: day type

```DAX
day type = 
VAR wkd = WEEKDAY(dim_date[date], 1)
RETURN IF(wkd > 5, "Weekend", "Weekday")
```

### `dim_rooms` Table

1. The specifics of column names aren't provided here. Remember to opt for the "Use First Row as Headers" option during transformation.

### Measures

1. **Revenue**: Calculate the total revenue_realized.
   ```DAX
   Revenue = SUM(fact_bookings[revenue_realized])
   ```
   Table: `fact_bookings`

2. **Total Bookings**: Calculate the total number of bookings.
   ```DAX
   Total Bookings = COUNT(fact_bookings[booking_id])
   ```
   Table: `fact_bookings`

3. **Total Capacity**: Determine the total capacity of rooms in hotels.
   ```DAX
   Total Capacity = SUM(fact_aggregated_bookings[capacity])
   ```
   Table: `fact_aggregated_bookings`

4. **Total Successful Bookings**: Sum up the total successful bookings for all hotels.
   ```DAX
   Total Successful Bookings = SUM(fact_aggregated_bookings[successful_bookings])
   ```
   Table: `fact_aggregated_bookings`

5. **Occupancy %**: Calculate the occupancy percentage.
   ```DAX
   Occupancy % = DIVIDE([Total Successful Bookings], [Total Capacity], 0)
   ```
   Table: `fact_aggregated_bookings`

6. **Average Rating**: Compute the average ratings given by customers.
   ```DAX
   Average Rating = AVERAGE(fact_bookings[ratings_given])
   ```
   Table: `fact_bookings`

7. **No of days**: Calculate the total number of days present in the data.
   ```DAX
   No of days = DATEDIFF(MIN(dim_date[date]), MAX(dim_date[date]), DAY) + 1
   ```
   Table: `dim_date`

8. **Total Cancelled Bookings**: Count the "Cancelled" bookings out of all total bookings.
   ```DAX
   Total Cancelled Bookings = CALCULATE([Total Bookings], fact_bookings[booking_status] = "Cancelled")
   ```
   Table: `fact_bookings`

9. **Cancellation %**: Calculate the cancellation percentage.
   ```DAX
   Cancellation % = DIVIDE([Total Cancelled Bookings], [Total Bookings])
   ```
   Table: `fact_bookings`

10. **Total Checked Out**: Calculate the number of successful 'Checked Out' bookings.
    ```DAX
    Total Checked Out = CALCULATE([Total Bookings], fact_bookings[booking_status] = "Checked Out")
    ```
    Table: `fact_bookings`

11. **Total No Show Bookings**: Calculate the "No Show" bookings.
    ```DAX
    Total No Show Bookings = CALCULATE([Total Bookings], fact_bookings[booking_status] = "No Show")
    ```
    Table: `fact_bookings`

12. **No Show Rate %**: Calculate the no show percentage.
    ```DAX
    No Show Rate % = DIVIDE([Total No Show Bookings], [Total Bookings])
    ```
    Table: `fact_bookings`

13. **Booking % by Platform**: Show the percentage contribution of each booking platform for hotel bookings.
    ```DAX
    Booking % by Platform = DIVIDE([Total Bookings], CALCULATE([Total Bookings], ALL(fact_bookings[booking_platform]))) * 100
    ```
    Table: `fact_bookings`

14. **Booking % by Room Class**: Show the percentage contribution of each room class over total rooms booked.
    ```DAX
    Booking % by Room Class = DIVIDE([Total Bookings], CALCULATE([Total Bookings], ALL(dim_rooms[room_class]))) * 100
    ```
    Tables: `fact_bookings`, `dim_rooms`

15. **ADR (Average Daily Rate)**: Calculate the average daily rate.
    ```DAX
    ADR = DIVIDE([Revenue], [Total Bookings], 0)
    ```
    Table: `fact_bookings`

16. **Realisation %**: Calculate the realization percentage.
    ```DAX
    Realisation % = 1 - ([Cancellation %] + [No Show Rate %])
    ```
    Table: `fact_bookings`

17. **RevPAR (Revenue Per Available Room)**: Calculate the revenue per available room.
    ```DAX
    RevPAR = DIVIDE([Revenue], [Total Capacity])
    ```
    Tables: `fact_bookings`, `fact_aggregated_bookings`

18. **DBRN (Daily Booked Room Nights)**: Calculate the average number of rooms booked per day.
    ```DAX
    DBRN = DIVIDE([Total Bookings], [No of days])
    ```
    Tables: `fact_bookings`, `dim_date`

19. **DSRN (Daily Sellable Room Nights)**: Calculate the average number of rooms available for sale per day.
    ```DAX
    DSRN = DIVIDE([Total Capacity], [No of days])
    ```
    Tables: `fact_aggregated_bookings`, `dim_date`

20. **DURN (Daily Utilized Room Nights)**: Calculate the average number of rooms utilized by customers per day.
    ```DAX
    DURN = DIVIDE([Total Checked Out], [No of days])
    ```
    Tables: `fact_bookings`, `dim_date`

21. **Revenue WoW Change %**: Calculate the revenue change percentage week over week.
    ```DAX
    Revenue WoW Change % = 
    VAR selv = IF(HASONEFILTER(dim_date[wn]), SELECTEDVALUE(dim_date[wn]), MAX(dim_date[wn]))
    VAR revcw = CALCULATE([Revenue], dim_date[wn] = selv)
    VAR revpw = CALCULATE([Revenue], FILTER(ALL(dim_date), dim_date[wn] = selv - 1))
    RETURN DIVIDE(revcw, revpw, 0) - 1
    ```
    Table: `dim_date`

22. **Occupancy WoW Change %**: Calculate the occupancy change percentage week over week.
    ```DAX
    Occupancy WoW Change % = 
    VAR selv = IF(HASONEFILTER(dim_date[wn]), SELECTEDVALUE(dim_date[wn]), MAX(dim_date[wn]))
    VAR revcw = CALCULATE([Occupancy %], dim_date[wn] = selv)
    VAR revpw = CALCULATE([Occupancy %], FILTER(ALL(dim_date), dim_date[wn] = selv - 1))
    RETURN DIVIDE(revcw, revpw, 0) - 1
    ```
    Table: `dim_date`

23. **ADR WoW Change %**: Calculate the ADR (Average Daily Rate) change percentage week over week.
    ```DAX
    ADR WoW Change % = 
    VAR selv = IF(HASONEFILTER(dim_date[wn]), SELECTEDVALUE(dim_date[wn]), MAX(dim_date[wn]))
    VAR revcw = CALCULATE([ADR], dim_date[wn] = selv)
    VAR revpw = CALCULATE([ADR], FILTER(ALL(dim_date), dim_date[wn] = selv - 1))
    RETURN DIVIDE(revcw, revpw, 0) - 1
    ```
    Table: `dim_date`

24. **RevPAR WoW Change %**: Calculate the RevPAR (Revenue Per Available Room) change percentage week over week.
    ```DAX
    RevPAR WoW Change % = 
    VAR selv = IF(HASONEFILTER(dim_date[wn]), SELECTEDVALUE(dim_date[wn]), MAX(dim_date[wn]))
    VAR revcw = CALCULATE([RevPAR], dim_date[wn] = selv)
    VAR revpw = CALCULATE([RevPAR], FILTER(ALL(dim_date), dim_date[wn] = selv - 1))
    RETURN DIVIDE(revcw, revpw, 0) - 1
    ```
    Table: `dim_date`

25. **Realisation WoW Change %**: Calculate the realization change percentage week over week.
    ```DAX
    Realisation WoW Change % = 
    VAR selv = IF(HASONEFILTER(dim_date[wn]), SELECTEDVALUE(dim_date[wn]), MAX(dim_date[wn]))
    VAR revcw = CALCULATE([Realisation %], dim_date[wn] = selv)
    VAR revpw = CALCULATE([Realisation %], FILTER(ALL(dim_date), dim_date[wn] = selv - 1))
    RETURN DIVIDE(revcw, revpw, 0) - 1
    ```
    Table: `dim_date`

26. **DSRN WoW Change %**: Calculate the DSRN (Daily Sellable Room Nights) change percentage week over week.
    ```DAX
    DSRN WoW Change % = 
    VAR selv = IF(HASONEFILTER(dim_date[wn]), SELECTEDVALUE(dim_date[wn]), MAX(dim_date[wn]))
    VAR revcw = CALCULATE([DSRN], dim_date[wn] = selv)
    VAR revpw = CALCULATE([DSRN], FILTER(ALL(dim_date), dim_date[wn] = selv - 1))
    RETURN DIVIDE(revcw, revpw, 0) - 1
    ```
    Table: `dim_date`

These measures collectively provide comprehensive insights into the hospitality industry's revenue performance. They allow you to analyze various aspects, including booking trends, occupancy rates, revenue, and changes over different time periods.

## Conclusion

This README.md offers a detailed account of the entire project process. From loading data to performing Power Query transformations and defining essential measures, each step contributes to producing valuable insights for the Revenue Team in the Hospitality Domain using Power BI. To explore the project further and access the actual Power BI project files, refer to the project documentation and accompanying files.

## Final Presentation - 

![image](https://github.com/SaibalPatraDS/Insights-to-the-Revenue-Team-/assets/102281722/a066c1cb-40b6-485f-9b4c-b241bd68bb82)

