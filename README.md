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

In the EDA, guided the above questions, we focused on analyzing the relationship between specific features in our dataset. More specifically, we performed univariate, bivariate, and aggregation analysis.

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
Hypothesis for MAR Permutation Tests:

* Null hypothesis: The missingness of "OUTAGE.DURATION" is not dependent on the compared column data.
* Alt hypothesis: The missingness of "OUTAGE.DURATION" is dependent on the the compated column data.

After conducting permutation tests on all columns within the dataset, we believe that our data is not NMAR (not missing at random) because all non trivial missingness columns have at least one simulation that returned a p value less than 0.05, thus rejecting the null hypothesis.

### Hypothesis Test
From our EDA, we recognized that rural areas had a higher rate or outages related to weather compared to urban areas. This may be due to reasons such as rural areas having less facilities to protect their power plants from large weather disasters. 

Our question: Are rural areas more prone to severe weather outages than urban areas? 

* Null Hypothesis: There is no difference in the amount that severe weather affects rural vs urban populations.
* Alternative Hypothesis: There is a difference in the amount that severe weather affects rural vs urban populations. 
Test statistic: Difference in median outages for both urban and rural population density

Results: We performed 10,000 trials and with a p-value of 0.0 and a significance level 0.05, we concluded that we can reject the null hypothesis. In our dataset, we can conclude that there is a statistically significant difference in the amount that severe weather affects rural vs urban populations; however, we are unable to determine the specific factors that resulted in this difference.

## Section 2: Prediction Model
For this section, we chose to predict the cause of the major outages as our prediction question. 
* Target Variable: CAUSE.CATEGORY 
    
We measured and compared the performance of our classifcation models using the accuracy evalution metric.

### Baseline Model
For the baseline model, we chose ['YEAR', 'POSTAL.CODE', 'CUSTOMERS.AFFECTED', 'POPPCT_URBAN'] as our initial columns. Through the analysis we conducted in the EDA, we deemed these columns the most revelant to predict the cause of outages.  

Overview of our Selected Dataset:
* Year (ordinal)
* Postal Code (nominal)
* Customers Affected (quantitative)
* Poppct Urban or Population Percentage Urban (quantitative)

Baseline Pipeline:
- Preprocessed The Data: Simple Imputer and One Hot Encoder
- Classifer: Decision Tree Classifier.

Evalution Metrics:
- Test Accuracy: 0.729
- Validation Accuracy: 0.721

### Engineer New Features
We decided to engineer six more features to improve our model.Through the analysis we conducted in the EDA, we choose to derive and engineer these features to further improve the accuracy of the model in predicting the cause category of a given outage.


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
Null hypothesis: The classifications of major power outages are "the same" when a small and large number of people are affected.
Alternative hypothesis: The classifications of major power outages are NOT "the same" when a small and large number of people are affected.

Since we got a p-value of 1.0, we can strongly fail to reject the null hypothesis. Therefore, the classifications of major power outages are "the same" when both small and large number of people are affected.
