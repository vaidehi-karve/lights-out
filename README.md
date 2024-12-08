# When Will My Lights Turn Back On? 🔋

## Introduction 💡

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

## Data Cleaning and Exploratory Data Analysis 💡

To clean our dataset, we first dropped blank rows and columns, as well as set the index to the “OBS” (Observation #) column. Then, we combined the “OUTAGE.START.DATE” and “OUTAGE.START.TIME” columns into a new column that contains pd.Timestamp objects. The same was done to the “OUTAGE.RESTORATION.DATE” and “OUTAGE.RESTORATION.TIME” columns. Additionally, we dropped the original 'OUTAGE.START.DATE', 'OUTAGE.START.TIME', 'OUTAGE.RESTORATION.DATE', 'OUTAGE.RESTORATION.TIME' columns to prevent confusion. We also converted the “TOTAL.CUSTOMERS” column from strings to integers. All of this was done for later analysis. Here are the first few rows of our cleaned dataset:

|   OUTAGE.DURATION | OUTAGE.START        | OUTAGE.RESTORATION   |   MONTH | CAUSE.CATEGORY     |
|------------------:|:--------------------|:---------------------|--------:|:-------------------|
|              3060 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |       7 | severe weather     |
|                 1 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |       5 | intentional attack |
|              3000 | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |      10 | severe weather     |
|              2550 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |       6 | severe weather     |
|              1740 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |       7 | severe weather     |

| CAUSE.CATEGORY.DETAIL   |   UTIL.CONTRI |   CUSTOMERS.AFFECTED | U.S._STATE   |   ANOMALY.LEVEL |
|:------------------------|--------------:|---------------------:|:-------------|----------------:|
| nan                     |       1.75139 |                70000 | Minnesota    |            -0.3 |
| vandalism               |       1.79    |                  nan | Minnesota    |            -0.1 |
| heavy wind              |       1.70627 |                70000 | Minnesota    |            -1.5 |
| thunderstorm            |       1.93209 |                68200 | Minnesota    |            -0.1 |
| nan                     |       1.6687  |               250000 | Minnesota    |             1.2 |

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

## Assessment of Missingness 💡

CUSTOMERS.AFFECTED is likely NMAR (Not Missing At Random) because the missingness of this column depends on the values themselves. For example, a large number of customers being affected may be omitted for political or business reasons. In order for the missingness to be MAR, data on current state policies or utility providers should be obtained. 

To determine the missingness of CAUSE.CATEGORY.DETAIL, we ran a permutation test on UTIL.CONTRI. The test yielded a p-value of 0.013, which is greater than the significance level of 0.01. Therefore, we fail to reject the null hypothesis that CAUSE.CATEGORY.DETAIL is not dependent on UTIL.CONTRI. The following shows the empirical distribution of the K-S statistics that were created from the samples in the permutation test, as well as the observed statistic of ~0.0853. 

<iframe
  src="assets/MCAR_plot.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

## Hypothesis Testing 💡

Because our goal is to understand the factors that affect power outages across the country, it is crucial to understand the impact of U.S. states on power outage durations. 

**Null Hypothesis:** The distribution of average power outage duration is the same for U.S. states.

**Alternative Hypothesis:** The distribution of average power outage duration is different for U.S. states. 

We chose to use TVD (Total Variation Distance) as our test statistic and a significance level of 0.01. Since the p-value of 0.004 is less than the significance level of 0.01, we reject the null hypothesis that the state has no effect on the average power outage duration.

## Framing a Prediction Problem 💡

**Prediction Problem:** How can we use the following features to predict outage duration using linear regression?

- CUSTOMERS.AFFECTED
- CAUSE.CATEGORY
- U.S._STATE
- ANOMALY.LEVEL

At the time of prediction, the number of customers that are affected, the cause of the outage, the state the outage occurs in, and the anomaly level would all be known. 

We evaluated the following models based on the R<sup>2</sup> statistic because we utilized a linear regression model over classification methods.

## Baseline Model 💡

At first, we decided to predict outage duration using 1 quantitative variable, CUSTOMERS.AFFECTED and 1 categorical nominal variable, CAUSE.CATEGORY. We standardized CUSTOMERS.AFFECTED and one-hot encoded CAUSE.CATEGORY, ensuring to drop a column to avoid multicollinearity. 

We initially utilized CUSTOMERS.AFFECTED because we thought the number of customers in the area may have a correlation with the outage  duration. In other words, we first thought that if there were many people in the area, there may be a prolonged outage duration time. We also used CAUSE.CATEGORY because we thought outages caused by natural disasters would take longer to restore compared to man-made problems. 

However, the performance of the baseline model on the training data achieved a R<sup>2</sup> score of ~0.181 and ~0.098 on the test data. We had expected a higher score, yet the output scores suggested otherwise. We believe this might be due to the model not using "good" features. For example, if there are many customers affected because of a single power outage, there may be more urgency to restore power. As a result, we decided to fine-tune and improve our model. 

## Final Model 💡

In order to fine-tune our model, we added two new features -- U.S._STATE (categorical nominal) and ANOMALY.LEVEL (quantitative). We believed that U.S._STATE would have a correlation with outage duration because there are vast geographic, political, and socioeconomic differences between the 50 states. At the same time, ANOMALY.LEVEL indicates the climate influence in the area of an outage using the ONI index. Due to the increasing severity of climate change, natural disasters may have even stronger effects on outages. 

We one-hot encoded U.S._STATE and binarized ANOMALY.LEVEL with a threshold of 0. If the anomaly level was greater than 0, the transformed value would be 1 and all other anomaly levels would be 0. A transformed value of 1 indicates El Nino, or warmer oceanic temperatures, whereas a transformed value of 0 indicates La Nina, or cooler oceanic temperatures. We considered that when ocean temperatures were warm, hurricanes and other natural disasters are more prone to occur which could ultimately impact outage durations. 

When choosing hyperparameters, we utilized GridSearchCV. We tuned the fit_intercept and positive hyperparameters of the LinearRegression() model, which both resulted in True values. In the end, the performance of the final model on the training data achieved a R<sup>2</sup> score of ~0.253 and ~0.102 on the test data. This is an improvement because the scores of the final model are higher than those of the baseline model, which were ~0.181 and ~0.098 on the training and test data, respectively.

## Fairness Analysis 💡

To check the fairness of our final model, we decided to check if there is a difference in RMSE for outages due to natural causes or outages that are caused by other factors. 

- **Group X:** Cause of outage is a natural cause (e.g., severe weather)
- **Group Y:** Cause of outage is not a natural cause(e.g., intentional attack, equipment failure)
- **Evaluation Metric:** RMSE (Root Mean Squared Error) 
- **Null:** Our model is fair. There is no difference in RMSE for natural causes and non-natural causes, and any differences are due to random chance.
- **Alternative:** Our model is unfair. There is a difference in RMSE for natural causes and non-natural causes.
- **Test Statistic:** Difference in RMSE 
- **Significance Level:** α = 0.01
- **P-Value**: 0.0 

Since our p-value of 0.0 is less than the significance level of 0.01, we reject the null hypothesis, and there is not statistically significant evidence to support that our model is fair. 

<iframe
  src="assets/RMSE_plot.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

# Contributers 

- Vaidehi Karve: vkarve@ucsd.edu 
- Snigdha Podugu: spodugu@ucsd.edu




