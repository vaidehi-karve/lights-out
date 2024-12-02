# When Will My Lights Turn Back On?

## Introduction 

Our [dataset](https://engineering.purdue.edu/LASCI/research-data/outages) contains major power outage data in the continental U.S. from January 2000 to July 2016. The focus is on understanding how power outage duration varies with specific factors, such as weather conditions, time of year, population density, location, and more. Contaning many variables across 1534 rows, the dataset provides key information to understanding the patterns that can help minimize the impact of future outages.

Question: What is the predicted outage duration given a variety of factors including the U.S. state in which the outage occurred, the cause of the outage, the number of customers affected by the outage, and the anomaly level of the outage?

Key columns relevant to our question include:

- OUTAGE.DURATION: Duration of outages in minutes.
- OUTAGE.START.DATE: Day of the year when outage began.
- OUTAGE.START.TIME: Time of day when outage began. 
- OUTAGE.RESTORATION.DATE: Day of the year when outage was restored.
- OUTAGE.RESTORATION.TIME: Time of day when outage was restored.
- MONTH: Month when outage occurred.
- CAUSE.CATEGORY: General causes (e.g., weather, equipment failure).
- CAUSE.CATEGORY.DETAIL: Detailed description of the event that caused the outage. 
- UTIL.CONTRI: Utility industry’s contribution to the total GSP in the state (in %). 
- CUSTOMERS.AFFECTED: Number of customers impacted.
- U.S._STATE: State in the continental U.S.
- ANOMALY.LEVEL: The oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season.

## Data Cleaning and Exploratory Data Analysis

To clean our dataset, we first dropped blank rows and columns, as well as set the index to the “OBS” (Observation #) column. Then, we combined the “OUTAGE.START.DATE” and “OUTAGE.START.TIME” columns into a new column that contains pd.Timestamp objects. The same was done to the “OUTAGE.RESTORATION.DATE” and “OUTAGE.RESTORATION.TIME” columns. Additionally, we dropped the original 'OUTAGE.START.DATE', 'OUTAGE.START.TIME', 'OUTAGE.RESTORATION.DATE', 'OUTAGE.RESTORATION.TIME' columns to prevent confusion. We also converted the “TOTAL.CUSTOMERS” column from strings to integers. All of this was done for later analysis. Here are the first few rows of our cleaned dataset:

|   OUTAGE.DURATION | OUTAGE.START        | OUTAGE.RESTORATION   |   MONTH | CAUSE.CATEGORY     | CAUSE.CATEGORY.DETAIL   |   UTIL.CONTRI |   CUSTOMERS.AFFECTED | U.S._STATE   |   ANOMALY.LEVEL |
|------------------:|:--------------------|:---------------------|--------:|:-------------------|:------------------------|--------------:|---------------------:|:-------------|----------------:|
|              3060 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |       7 | severe weather     | nan                     |       1.75139 |                70000 | Minnesota    |            -0.3 |
|                 1 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |       5 | intentional attack | vandalism               |       1.79    |                  nan | Minnesota    |            -0.1 |
|              3000 | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |      10 | severe weather     | heavy wind              |       1.70627 |                70000 | Minnesota    |            -1.5 |
|              2550 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |       6 | severe weather     | thunderstorm            |       1.93209 |                68200 | Minnesota    |            -0.1 |
|              1740 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |       7 | severe weather     | nan                     |       1.6687  |               250000 | Minnesota    |             1.2 |


### What Is Causing My Power Outage?

This histogram demonstrates the frequencies of power outage causes from the most frequent to the least. Severe weather is the most frequent with 763 instances, and islanding is the least frequent with 46 instances.

<iframe
  src="assets/univariate_plot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


### How Long Will My Lights Be Out? 
This heat map demonstrates the average power outage duration by U.S. state with darker colors indicating a longer outage duration and lighter colors indicating a shorter outage duration. Note that Alaska and Rhode Island are displayed as black because these states do not have any reported outages within the dataset.

<iframe
  src="assets/bivariate_plot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Embed at least one grouped table or pivot table in your website and explain its significance.

## Assessment of Missingness

## Hypothesis Testing

## Framing a Prediction Problem

## Baseline Model

## Final Model

## Fairness Analysis
