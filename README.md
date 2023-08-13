# Hospitality Revenue Insights using Power BI

This README.md document outlines the steps taken throughout the project to provide valuable insights to the Revenue Team in the Hospitality Domain using Power BI. The project's focus was on analyzing data related to hospitality challenges and presenting these insights through Power BI reports.

## Data Loading and Power Query Documentation
### Step 1: Data Preparation
* Create a folder on your Desktop to organize and store all the CSV files related to the hospitality challenge.
### Step 2: Data Loading
* Open Power BI and navigate to the "Get Data" option.
* Choose the "Folder" option and browse through the designated folder containing the CSV files.
### Step 3: Data Transformation
* After loading the data, go to the "Transform Data" option to initiate the data transformation process.
* Expand each dataset within the Power Query Editor.
### Step 4: Data Source Duplication and Expansion
* Duplicate the data source four times, creating four instances of the same source.
* For each duplicated source, utilize the "Binary" option to expand one dataset.
* Rename the tables according to the dataset they represent.

## Power Query Steps for Tables
### `dim_date` Table
* Remove the column 'day_type' as it conflicts with industry standards regarding weekends.
* This action was necessitated by feedback from a mock dashboard review, highlighting that Friday and Saturday are typically considered weekends, contrary to the dataset's interpretation.
* Re-create `day_type` using calculated columns.
  
**Calculated Column: day type**
```DAX
day type = 
VAR wkd = WEEKDAY(dim_date[date], 1)
RETURN IF(wkd > 5, "Weekend", "Weekday")
```

### `dim_rooms` Table
The specifics of column names are not detailed here. However, remember to select the "Use First Row as Headers" option during transformation.

## Calculated Columns

1. `week_number` (Week Number Calculation)

Calculate the week number from the corresponding date.
Formula: ```DAX 
         wn = WEEKNUM(dim_date[date])```
Table: `dim_date`

2. `day type` (Weekday Calculation based on Feedback)

Create a calculated column to differentiate weekdays and weekends based on stakeholder feedback.
Formula:

```DAX
Copy code
day type = 
VAR wkd = WEEKDAY(dim_date[date], 1)
RETURN IF(wkd > 5, "Weekend", "Weekday")
```
Table: `dim_date`
