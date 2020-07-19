# Power-Outage-EDA


## Summary of Findings
### Introduction
The 'outage.xlsx' dataset represents inputs about major outages in different states of the United States from 2000 to 2016. The topics of the columns are: general information, regional climate information, outage events information, regional electricity consumption information, regional economic characteristics, and regional land-use characteristics. A more detailed description of the separate columns can be found at: https://www.sciencedirect.com/science/article/pii/S2352340918307182.

In this project, we perform exploratory data analysis in order to perform some sort of hypothesis test. In order to develop a hypothesis, we narrowed down what we were exploring to a few possible questions and inquiries. These include:

- Where and when do major power outages tend to occur?
- What characteristics are associated with each category of cause?
- How have characteristics of major power outages changed over time? Is there a clear trend?

Below we will be providing a summary of what we accomplished in the different sections of this notebook.

### Cleaning and EDA
First, we had to clean our given dataset. When we first imported the set, the columns were not the correct names and the rows did not correctly represent the data generating process (DGP). In order to fix this, we obtained the relevant header names (which were located on row four), and made the column names equal to them. We also dropped the irrelevant 'variables' column that originally showed up because it did not matter in our analysis. Lastly, we set the observations equal to the index in order to more quickly find data.

After reformatting the table to more correctly reflect the DGP, we combined the start date and time in order to create a summarized datetime column. We did the same for the end date and time in order to be uniform. We added these two columns to the dataframe in order to encompass a more precise dataset.

We also reformatted the dtypes of the columns that did not contain null values. This was in order to ensure that the DGP was accurate and so that future analysis would run smoother.

We then began our exploratory data analysis (EDA). In the EDA we focused on the above questions by creating graphs. More specifically, we performed univariate, bivariate, and aggregation analysis.

When performing univariate analysis, we mainly focused on finding where and when major power outages occur. We found that the main cause of power outages between 2000 and 2016 is severe weather followed by intentional attacks. We also found that most outages occur in states with higher populations. This may be due to the fact that states with a higher population tend to have more power plants, and therefore would be prone to having more outages. Going off of this fact, we found that the region that takes the most impact is the northeast region of the U.S. This would be fitting considering that many of these states (e.g. New York) have a high population density.

After visualizing this data, we knew we wanted to bring in the causes of the outages into our analysis. In our bivariate analysis we focused on how different outage causes affected rural versus urban populations. We created a spaghetti plot to see how the counts of different causes fluctuated by year. We noticed that severe weather was the main cause of outages up until 2011. Not only were there 269 overall outages this year, but intentional attacks took over as the biggest cause of outages. Upon doing research we can attribute the large amount of power outages to the 2011 Southwest Blackout Event. The outage was the result of 23 distinct events that occurred on 5 separate power grids in a span of 11 minutes. More information can be found here: https://www.nerc.com/pa/rrm/ea/Pages/September-2011-Southwest-Blackout-Event.aspx. This led us to wonder: how differently are urban versus rural areas affected by outages caused by intential attacks? We plotted a boxplot that conveyed how differently urban versus rural areas were affected by different causes. Although we found that intential attacks affected them at the same rate, severe weather did not. So this led us to our hypothesis testing question: Are rural areas more prone to severe weather outages than urban areas? More information is in the Hypothesis Test Question of this summary.

We also did some aggregation analysis be checking how many overall customers were affected by a specific cause. We did this in order to try to see why certain causes may affect rural areas more often.

We split up the graphs that relate to our newly found hypothesis test and other fun visualizations we did while investigating our questions. We won't go into much detail about the findings in those, however we plan on utilizing those graphs for further analysis in the future so you can go ahead and take a look at them yourself.

### Assessment of Missingness
Hypothesis for MAR Permutation Tests:

Null hypothesis: The missingness of "OUTAGE.DURATION" is not dependent on the compared column data.
Alt hypothesis: The missingness of "OUTAGE.DURATION" is dependent on the the compated column data.
For our analysis of the missingness of our dataset, we believe that our data is not NMAR (not missing at random). NMAR classification is given to data if the missingness of the missingness of a column can be credited to that is not given within the dataset. In order to determine whether or not our dataset contain NMAR data, we conducted permutation tests on all columns within the dataset that contained missing data. Every permutation tests on all non trivial missingness columns seems to have at leave one simulation that returned a p value less than 0.05 which allowed us to reject the null hypothesis.

The column with nontrivial missingness data that we chose to analyze was 'OUTAGE.DURATION'. This column has 1476 non-null values out of the 1534 possible data entries. In order to determine 'OUTAGE.DURATION''s dependency of missingness, we conducted a KS-Statistic permutation test. The KS test is used to identify whether the two distributions are from the same continuous distribution. Using the KS statistic test, we were able to create two samples of data (one which contains the distribution of a column where OUTAGE.DURATION is null and the other which contains the distribution of a column where OUTAGE.DURATION is not null). After generating multiple sample through the 1000 simulations, we compared to our observed statistic which was the ks_2samp of our initial two independent samples. The results of the permutation test, along with a significance level of 0.05, showed that 'TOTAL.SALES' was able to reject null hypothesis with a p value of 0.0 and 'CUSTOMERS.AFFECTED' failed to reject the null hypothesis with a p value of 0.28. Rejecting the null hypothesis indicates that the distribution are not similar. Failing to reject the null hypothesis indicates that the distribution is similar. This result does make sense as there are many factors other than outage duration that will affect the missingness of customers affected. However, outage duration can directly affects the total sales which indicates total electricity consumption in the U.S. state.

### Hypothesis Test
As stated above our question was: Are rural areas more prone to severe weather outages than urban areas? We came up with this questions because we observed that rural areas had a higher rate or outages related to weather compared to urban areas. This may be due to reasons such as rural areas having less facilities to protect their power plants from large weather disasters.

When testing this we maintained these hypotheses:

Null: There is no difference in the amount that severe weather affects rural vs urban populations.
Alternative: There is a difference in the amount that severe weather affects rural vs urban populations. (However, we don't know why though)
We used the difference in medians between the two populations.

We performed 10,000 trials and with a p-value of 0.0 and a significance level 0.05, we came to the conclusion that we can reject the null. In our data set we can conclude that there is a statistically significant difference in the amount that severe weather affects rural vs urban populations.
