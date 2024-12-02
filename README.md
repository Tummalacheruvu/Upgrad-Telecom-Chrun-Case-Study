# Telecom Churn Case Study

## Problem Statement

## Business Problem Overview

In the telecom sector, consumers have the freedom to select from various service providers and can easily switch between them. This highly competitive environment results in an average annual churn rate of 15-25%. Considering that acquiring a new customer is 5-10 times more expensive than retaining an existing one, maintaining customer loyalty has become more crucial than the process of attracting new customers.

For many established operators, keeping their most profitable customers is the top priority.

To reduce customer churn, telecom companies need to predict which customers are at high risk of churn.

In this project, we will examine customer-level data from a leading telecom company, develop predictive models to pinpoint customers with a high likelihood of churn, and identify the key factors contributing to churn.

## Understanding and Defining Churn

The telecom industry primarily operates with two payment models: postpaid, where customers pay a monthly or annual bill after using the services, and prepaid, where customers pay or recharge a specified amount upfront before accessing the services.

In the postpaid model, when customers decide to switch to a different operator, they typically notify the current provider to cancel their services, making it a clear case of churn.

In the prepaid model, customers who wish to switch to another network can simply stop using the services without notifying the provider, making it difficult to determine if they have truly churned or if they are just temporarily inactive (for example, a customer might be traveling abroad for a month or two and plan to resume using the services later).

As a result, predicting churn is often more challenging and crucial for prepaid customers, and the definition of "churn" needs to be precise. Additionally, the prepaid model is more prevalent in India and Southeast Asia, while the postpaid model is more common in Europe and North America.

This project focuses on the Indian and Southeast Asian markets.

## Definitions of Churn 

Churn can be characterized in different ways, such as:

**Revenue-based churn:** Customers who have not used any revenue-generating services, such as mobile internet, outgoing calls, SMS, etc., within a specified period. Alternatively, one could define churn using aggregate metrics, such as "customers who have generated less than INR 4 per month in total, average, or median revenue."

A key limitation of this definition is that it overlooks customers who primarily receive calls or SMS messages from family members or others, without generating revenue themselves. For instance, many rural users only receive calls from their working relatives in urban areas, without generating any income from the service.

**Usage-based churn:** Customers who have not engaged in any usage, whether incoming or outgoing, such as calls, internet, etc., over a specified period.

A possible drawback of this definition is that if a customer has ceased using the services for an extended period, it may be too late to implement retention measures. For instance, if churn is defined by a "two-month zero usage" period, predicting churn might be ineffective, as the customer may have already switched to another provider by that point.

In this project, we will use the **usage-based** definition to define churn.

## High-value Churn

In the Indian and the southeast Asian market, approximately 80% of revenue comes from the top 20% customers (called high-value customers). Thus, if we can reduce churn of the high-value customers, we will be able to reduce significant revenue leakage.

In this project, we will define high-value customers based on a certain metric (mentioned later below) and predict churn only on high-value customers.

## Understanding the Business Objective and the Data

The dataset contains customer-level information for a span of four consecutive months - June, July, August and September. The months are encoded as 6, 7, 8 and 9, respectively. 

The business objective is to predict the churn in the last (i.e. the ninth) month using the data (features) from the first three months. To do this task well, understanding the typical customer behaviour during churn will be helpful.

## Understanding Customer Behaviour During Churn

Customers usually do not decide to switch to another competitor instantly, but rather over a period of time (this is especially applicable to high-value customers). In churn prediction, we assume that there are three phases of customer lifecycle :

1. **The ‘good’ phase:** In this phase, the customer is happy with the service and behaves as usual.

2. **The ‘action’ phase:** The customer experience starts to sore in this phase, for e.g. he/she gets a compelling offer from a  competitor, faces unjust charges, becomes unhappy with service quality etc. In this phase, the customer usually shows different behaviour than the ‘good’ months. Also, it is crucial to identify high-churn-risk customers in this phase, since some corrective actions can be taken at this point (such as matching the competitor’s offer/improving the service quality etc.)

3. **The ‘churn’ phase:** In this phase, the customer is said to have churned. We define churn based on this phase. Also, it is important to note that at the time of prediction (i.e. the action months), this data is not available to us for prediction. Thus, after tagging churn as 1/0 based on this phase, we discard all data corresponding to this phase.

In this case, since we are working over a four-month window, the first two months are the ‘good’ phase, the third month is the ‘action’ phase, while the fourth month is the ‘churn’ phase.

## Dataset and Data Dictionary

The dataset can be downloaded from [here](https://drive.google.com/file/d/1SWnADIda31mVFevFcfkGtcgBHTKKI94J/view).

Data dictionary is uploaded. The data dictionary contains meanings of abbreviations. Some frequent ones are loc (local), IC (incoming), OG (outgoing), T2T (telecom operator to telecom operator), T2O (telecom operator to another operator), RECH (recharge) etc.

The attributes containing 6, 7, 8, 9 as suffixes imply that those correspond to the months 6, 7, 8, 9 respectively.

## Data Preparation

The following data preparation steps are crucial for this problem:

1. **Derive new features**
This is one of the most important parts of data preparation since good features are often the differentiators between good and bad models. We will use our business understanding to derive features that we think could be important indicators of churn.

2. **Filter high-value customers**
As mentioned above, we need to predict churn only for the high-value customers. Define high-value customers as follows: Those who have recharged with an amount more than or equal to X, where X is the 70th percentile of the average recharge amount in the first two months (the good phase).

3. **Tag churners and remove attributes of the churn phase**
Now tag the churned customers (churn=1, else 0) based on the fourth month as follows: Those who have not made any calls (either incoming or outgoing) AND have not used mobile internet even once in the churn phase. The attributes we need to use to tag churners are:

- total_ic_mou_9
- total_og_mou_9
- vol_2g_mb_9
- vol_3g_mb_9

After tagging churners, we need to remove all the attributes corresponding to the churn phase (all attributes having ‘ _9’, etc. in their names).

## Modelling

Build models to predict churn. The predictive model that we are going to build will serve two purposes:

1. It will be used to predict whether a high-value customer will churn or not, in near future (i.e. churn phase). By knowing this, the company can take action steps such as providing special plans, discounts on recharge etc.

2. It will be used to identify important variables that are strong predictors of churn. These variables may also indicate why customers choose to switch to other networks.

In some cases, both of the above-stated goals can be achieved by a single machine learning model. But here, we have a large number of attributes, and thus we should try using a dimensionality reduction technique such as PCA and then build a predictive model. After PCA, we can use any classification model.

Also, since the rate of churn is typically low (about 5-10%, this is called class-imbalance) - we will try using techniques to handle class imbalance. 

We can take the following suggestive steps to build the model:

1. Preprocess data (convert columns to appropriate formats, handle missing values, etc.)
2. Conduct appropriate exploratory analysis to extract useful insights (whether directly useful for business or for eventual modelling/feature engineering).
3. Derive new features.
4. Reduce the number of variables using PCA.
5. Train a variety of models, tune model hyperparameters, etc. (handle class imbalance using appropriate techniques).
6. Evaluate the models using appropriate evaluation metrics. Note that it is more important to identify churners than the non-churners accurately - choose an appropriate evaluation metric which reflects this business goal.
7. Finally, choose a model based on some evaluation metric.

The above model will only be able to achieve one of the two goals - to predict customers who will churn. We can’t use the above model to identify the important features for churn. That’s because PCA usually creates components which are not easy to interpret.

Therefore, we will build another model with the main objective of identifying important predictor attributes which help the business understand indicators of churn. A good choice to identify important variables is a logistic regression model or a model from the tree family. In case of logistic regression, we will make sure to handle multi-collinearity.

After identifying important predictors, display them visually - we can use plots, summary tables etc. - whatever we think best conveys the importance of features.

Finally, recommend strategies to manage customer churn based on our observations.
