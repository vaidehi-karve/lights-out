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


<div style="overflow-x:auto;">
|   OUTAGE.DURATION | OUTAGE.START        | OUTAGE.RESTORATION   |   MONTH | CAUSE.CATEGORY     | CAUSE.CATEGORY.DETAIL   |   UTIL.CONTRI |   CUSTOMERS.AFFECTED | U.S._STATE   |   ANOMALY.LEVEL |
|------------------:|:--------------------|:---------------------|--------:|:-------------------|:------------------------|--------------:|---------------------:|:-------------|----------------:|
|              3060 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |       7 | severe weather     | nan                     |       1.75139 |                70000 | Minnesota    |            -0.3 |
|                 1 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |       5 | intentional attack | vandalism               |       1.79    |                  nan | Minnesota    |            -0.1 |
|              3000 | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |      10 | severe weather     | heavy wind              |       1.70627 |                70000 | Minnesota    |            -1.5 |
|              2550 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |       6 | severe weather     | thunderstorm            |       1.93209 |                68200 | Minnesota    |            -0.1 |
|              1740 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |       7 | severe weather     | nan                     |       1.6687  |               250000 | Minnesota    |             1.2 |
</div>


### What Is Causing My Power Outage?

This histogram demonstrates the frequencies of power outage causes from the most frequent to the least. Severe weather is the most frequent with 763 instances, and islanding is the least frequent with 46 instances.

<iframe
  src="assets/uni_plot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


### How Long Will My Lights Be Out? 
This heat map demonstrates the average power outage duration by U.S. state with darker colors indicating a longer outage duration and lighter colors indicating a shorter outage duration. Western states tend to have a lower power outage duration, whereas eastern states have longer outage durations.

<iframe
  src="assets/bi_plot.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>


To see if the months of the year had any effect on outage duration, this pivot table conveys the average outage duration by month. The table shows that April and November were the months with the shortest average outage durations, whereas October and September had the longest average outages durations. 

|   MONTH |   OUTAGE.DURATION |
|--------:|------------------:|
|       4 |           1493.86 |
|      11 |           1728.16 |
|       6 |           1948.4  |
|       5 |           2077.29 |
|       7 |           2218.99 |
|       8 |           2428.48 |
|       2 |           2497.14 |
|       3 |           3265.89 |
|      12 |           3293.79 |
|       1 |           3387.95 |
|      10 |           3600.94 |
|       9 |           4294.52 |

## Assessment of Missingness

CUSTOMERS.AFFECTED is likely NMAR (Not Missing At Random) because the missingness of this column depends on the values themselves. For example, a large number of customers being affected may be omitted for political or business reasons. In order for the missingness to be MAR, data on current state policies or utility providers should be obtained. 

To determine the missingness of CAUSE.CATEGORY.DETAIL, we ran a permutation test on UTIL.CONTRI. The test yielded a p-value of 0.013, which is greater than the significance level of 0.01. Therefore, we fail to reject the null hypothesis that CAUSE.CATEGORY.DETAIL is not dependent on UTIL.CONTRI. The following shows the empirical distribution of the K-S statistics that were created from the samples in the permutation test, as well as the observed statistic of ~0.0853. 

<iframe
  src="assets/MCAR_plot.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>


## Hypothesis Testing

Because our goal is to understand the factors that affect power outages across the country, it is crucial to understand the impact of U.S. states on power outage durations. 

**Null Hypothesis:** The distribution of average power outage duration is the same for U.S. states.

**Alternative Hypothesis:** The distribution of average power outage duration is different for U.S. states. 

We chose to use TVD (Total Variation Distance) as our test statistic and a significance level of 0.01. Since the p-value of 0.004 is less than the significance level of 0.01, we reject the null hypothesis that the state has no effect on the average power outage duration.

## Framing a Prediction Problem

**Prediction Problem:** How can we use the following features to predict outage duration using linear regression?

- CUSTOMERS.AFFECTED
- CAUSE.CATEGORY
- U.S._STATE
- ANOMALY.LEVEL

At the time of prediction, the number of customers that are affected, the cause of the outage, the state the outage occurs in, and the anomaly level would all be known. 

We evaluated the following models based on the R^2^ statistic because we utilized a linear regression model over classification methods. 

## Baseline Model



## Final Model

## Fairness Analysis
