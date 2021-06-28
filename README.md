# Optimizing an ML Pipeline in Azure

## Overview
This project is part of the Udacity Azure ML Nanodegree.
In this project, we build and optimize an Azure ML pipeline using the Python SDK and a provided Scikit-learn model.
This model is then compared to an Azure AutoML run.

## Summary
**In 1-2 sentences, explain the problem statement: e.g "This dataset contains data about... we seek to predict..."**  
The dataset contains data about marketing campaigns based on phone calls. There are information on the clients (age, job, marital status, etc) and some attributes on the campaigns. The goal is to predict whether a client will subscribe to a term deposit or not. It is a classification problem.

**In 1-2 sentences, explain the solution: e.g. "The best performing model was a ..."**  
The best performing model is a logistic regression with an inverse of regularization strength *C* equals *84.35* . The accuracy is 0.909.  
It is worth noting that the accuracy was between 0.905 and 0.909 for all the runs which indicates that the hyperparameter *C* doesn't impact much the performance of the model in this example.

## Scikit-learn Pipeline
**Explain the pipeline architecture, including data, hyperparameter tuning, and classification algorithm.**  
The dataset has 32950 rows and 21 columns. For the data processing, the categorical variables are one-hot encoded. For the months and the weekdays variables, an ordinal encoding is applied where January becomes 1 and December 12 for the months. Monday is 1 and Sunday is encoded as 7 for the weekdays. *month* and *day_of_week* are thus treated as numerical features.

We chose the logistic regression with a L2 regularization as the classification algorithm. The model is tuned by optimizing the hyperparameter *C* which is the inverse of regularization strength. We didn't include the maximum number of iterations as an hyperparameter because in our example here the solver converges in less than 100 iterations.  

**What are the benefits of the parameter sampler you chose?**  
We chose a random sampling to explore the hyperparameter space. First random sampling supports continuous hyperparameters which is the case of *C*. Most importantly, random sampling usually permits to achieve better results than a grid sampling in less time.

**What are the benefits of the early stopping policy you chose?**
A bandit policy is applied for the early stopping. This policy terminates a run if its best metric is less than a certain value derived from the current best performance. It permits to stop non promising runs and save costs.

## AutoML
**In 1-2 sentences, describe the model and hyperparameters generated by AutoML.**
AutoML's best model is a voting ensemble comprised of 6 models: 
* 4 *SparseNormalizer + XGBoostClassifier* with different hyperparameters for each XGBoost model.
*  *MaxAbsScaler + XGBoostClassifier*
* *MaxAbsScaler + LightGBM*
The accuracy of the model is 0.916.

## Pipeline comparison
**Compare the two models and their performance. What are the differences in accuracy? In architecture? If there was a difference, why do you think there was one?**
AutoML and the logistic regression has similar performance: 0.916 for the first and 0.909 for the second. 

In terms of architecture, the processing of the categorical variables is different in AutoML where a label encoding and a count vectorizer are both applied. Moreover, a *MaxAbsScaler* or *SparseNormalizer* is done on all the features. Furthermore, AutoML uses a combination of 6 models instead of one. Overall, AutoML architecture is more complex.

## Future work
**What are some areas of improvement for future experiments? Why might these improvements help the model?**  
The target variable has imbalanced classes. There are around 10 times more negative observations (y=0) than the positive ones (y=1). We could try different sampling techniques to create a balanced dataset such as upsample the minority class or downsample the majority one. It is also possible to give more weights to the positive observations by using algorithms that can take into account a weight parameter.

![AutoML infrastructure](./automl_archi.PNG)

**Image of cluster marked for deletion**
![Cluster deletion](./automl_archi.PNG)

