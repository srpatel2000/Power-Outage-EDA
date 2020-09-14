# Power-Outage-EDA

## Introduction
The 'outage.xlsx' dataset represents inputs about major outages in different states of the United States from 2000 to 2016. The topics of the columns are: general information, regional climate information, outage events information, regional electricity consumption information, regional economic characteristics, and regional land-use characteristics. A more detailed description of the separate columns can be found at: https://www.sciencedirect.com/science/article/pii/S2352340918307182.

We divided the project into two sections: Hypothesis Test and Prediction Model

Below we will be providing a summary of what we accomplished in the different sections of this notebook.

### EDA
In this project, we performed exploratory data analysis in order to develop a hypothesis to test and decide on the metrics to train our prediction model.

We narrowed down what we were exploring to a few possible questions and inquiries. These include:

- Where and when do major power outages tend to occur?
- What characteristics are associated with each category of cause?
- How have characteristics of major power outages changed over time? Is there a clear trend?

Guided by the question above, we focused on analyzing the relationship between specific features in our dataset. More specifically, we performed univariate, bivariate, and aggregation analysis.

#### Univariate 
When performing univariate analysis, we focused on finding where and when major power outages occur. 

* Bar graph: Distribution of Outage Causes
* Cloropeth: Distribution of Outages Over States (Postal Codes)
* Pie Chart: Distribution of Outages Over Climate Regions

#### Bivariate 
After visualizing this data, we wanted to analyze the relationship between the causes of outages and the other specific features. In our bivariate analysis we focused on how different outage causes affected rural versus urban populations. 

* Bar graph: Distribution of Power Outages Over Time
* Spaghetti plot: Change of number of outages per cause over time 
* Box Plot: Distribution of Causes in Rural and Urbal Areas

#### Aggregation
We also utilized aggregation analysis by observing how many overall customers were affected by a specific cause. We did this in order to see why certain causes may affect rural areas more often.

* Bar graph: Distribution of Number of Customers Affected by Specific Cause Category Detail

More fun visualizations we generated while investigating our questions are included in the notebook.

## Section 1: Hypothesis Test

### Assessment of Missingness

In order to further understand our dataset and practice more data science skills, we decided to conduct an assessment of missingness. For this section, we decided to choose a particular non-trival column (column with a significant number of missing values) and run a Missing at Random Permutation Test. MAR data incidates that there is a relationship between the missingness of the data and another observed data.

* Null hypothesis: The missingness of "OUTAGE.DURATION" is not dependent on the compared column data.
* Alt hypothesis: The missingness of "OUTAGE.DURATION" is dependent on the the compared column data.

The column with nontrivial missingness data that we chose to analyze was 'OUTAGE.DURATION'. This column has 1476 non-null values out of the 1534 possible data entries. In order to determine 'OUTAGE.DURATION''s dependency of missingness, we conducted a KS-Statistic permutation test. The KS test is used to identify whether the two distributions are from the same continuous distribution. Using the KS statistic test, we were able to create two samples of data (one which contains the distribution of a column where OUTAGE.DURATION is null and the other which contains the distribution of a column where OUTAGE.DURATION is not null). After generating multiple sample through the 1000 simulations, we compared to our observed statistic which was the ks_2samp of our initial two independent samples. 

We used this permutation test, at a significance level of 0.05, to identify two columns within our dataset: one that OUTAGE.DURATION's missigness was dependent on (MAR) and one that OUTAGE.DURATION's missingness was not dependent on. The results of the permutation test showed that 'TOTAL.SALES' was able to reject null hypothesis with a p value of 0.0 and 'CUSTOMERS.AFFECTED' failed to reject the null hypothesis with a p value of 0.28. Rejecting the null hypothesis indicates that the distribution are not similar. Failing to reject the null hypothesis indicates that the distribution is similar. This result does make sense as there are many factors other than outage duration that will affect the missingness of customers affected. However, outage duration can directly affects the total sales which indicates total electricity consumption in the U.S. state.

### Hypothesis Test
From our EDA, we recognized that rural areas had a higher rate or outages related to weather, compared to urban areas. This may be due to reasons such as rural areas having less facilities to protect their power plants from large weather disasters. 

Our question: Are rural areas more prone to severe weather outages than urban areas? 

* Null Hypothesis: There is no difference in the amount that severe weather affects rural vs urban populations.
* Alternative Hypothesis: There is a difference in the amount that severe weather affects rural vs urban populations. 
* Test statistic: Difference in median outages for both urban and rural population density

Results: We performed 10,000 trials at a significance level 0.05. After receiving a p-value of 0.0, we concluded that we can reject the null hypothesis. In our dataset, we can conclude that there is a statistically significant difference in the amount that severe weather affects rural vs urban populations; however, we are unable to determine the specific factors that resulted in this difference.

## Section 2: Prediction Model
For this section, we chose to build a prediction model that can predict the cause of the major outages, using the discoveries from both the EDA and the hypothesis test.
* Target Variable: CAUSE.CATEGORY 

The performance of all classification models we designed are measured and compared using the accuracy evalution metric.

### Baseline Model
For the baseline model, we chose ['YEAR', 'POSTAL.CODE', 'CUSTOMERS.AFFECTED', 'POPPCT_URBAN'] as our initial columns. Through the analysis we conducted in the EDA, we deemed these columns the most revelant to predict the cause of outages.  

Overview of our Selected Dataset:
* Year (ordinal): Certain causes are more prominent in certain years. For example, particularly in the year 2011, many of the outages can be attributed to the Southwest Blackout Event (more information the EDA description).
* Postal Code (nominal): As seen in the univariate analysis, there is a strong relationship between particular states and number of outages. This suggests that causes could be predicted based on the most common cause of outages of a particular location.
* Customers Affected (quantitative)
* Poppct Urban or Population Percentage Urban (quantitative): As we observed from the hypothesis test, outages caused by severe weather are more prominent in rural communities as opposed to urban communities. Through this observation, we decided to include this data to train our model.

Baseline Pipeline:
- Preprocessed The Data: Simple Imputer and One Hot Encoder
- Classifer: Decision Tree Classifier.

Evalution Metrics:
- Test Accuracy: 0.729
- Validation Accuracy: 0.721

### Engineer New Features
Through the analysis we conducted in the EDA, we choose to derive and engineer six more features to further improve the accuracy of the model in predicting the cause category of a given outage. In addition, the following features focus on providing further details regarding the duration or timing of the outage. Its importance can be seen through the observations from the bivariate analysis. 

* Day of the week the outage occured (START.DAY.OF.WEEK)
* Day of the week the outage Was restored (RESTORATION.DAY.OF.WEEK)
* Hour an outage occured (START.HOUR)
* Hour the outage was restored (RESTORATION.HOUR)
* Number of Days Outage Occured (TOTAL.DAYS)
* State's population is more rural or urban dominated (IS.URBAN)

In addition to adding the new features to our dataset, we incoporated the Principal Component Analysis (PCA) to our preprocessing pipeline in order to handle highly correlated columns.

### Model and Parameter Testing
After engineering the features, we decided to compare various classifiers which will optimize our prediction model. We also optimized our parameters using Grid Search CV.

- Decision Tree Classifier 
  - Test Accuracy: 0.718
  - Validation Accuracy: 0.7399
- Decision Tree Classifier with Best Parameters
  - Test Accuracy: 0.748
  - Validation Accuracy: 0.746
- Random Forest Classifier
  - Test Accuracy: 0.77
  - Validation Accuracy: 0.750
- Random Forest Classfier with Best Parameters
  - Test Accuracy: 0.783
  - Validation Accuracy: 0.748
- KNN Classifier
  - Test Accuracy: 0.752
  - Validation Accuracy: 0.7399

From the given evaluation metrics, the Random Forest Classifier seems to be performing slightly better than the previous Decision Tree Classifier.

### Final Model
Our final model is trained on the data from ['YEAR', 'POSTAL.CODE', 'CUSTOMERS.AFFECTED', 'POPPCT_URBAN'] columns within the original dataset, along with our six engineered features stated above. 

Within the preprocessing pipeline, we used a simple imputer to account for the missing data within the columns, a one hot encoder to quantify the categorical data, and a principal component analysis to handle highly correlated columns. Finally, we used the random forest classifier as our classification model.

After optimizing the parameters for the random forest classifier through Grid Search, the accuracy metrics are as follows:
- test_accuracy: 0.787
- validation_accuracy: 0.753

The accuracy increased 6 percent and the validation accuracy has increased by 3 percent  from our initial baseline model.

### Fairness Evaluation
For the fairness evaluation, we chose to evaluate whether the model performed better when a larger group of customers was affected. We chose to do an accuracy parity evaluation since we had a multiclass classification model and found that accuracy was one of the easier ways to evaluate model fairness.

In order to perform this evaluation we performed a permutation test, at a significance level of 0.05, with these hypotheses:
* Null hypothesis: The classifications of major power outages are "the same" when a small and large number of people are affected.
* Alternative hypothesis: The classifications of major power outages are NOT "the same" when a small and large number of people are affected.

Since we got a p-value of 1.0, we can strongly fail to reject the null hypothesis. Therefore, the classifications of major power outages are "the same" when both small and large number of people are affected.
