# Optimizing an ML Pipeline in Azure

## Overview
This project is part of the Udacity Azure ML Nanodegree.
In this project, we build and optimize an Azure ML pipeline using the Python SDK and a provided Scikit-learn model.
This model is then compared to an Azure AutoML run.

## Architectural Diagram

![image](https://user-images.githubusercontent.com/68206315/115319967-d6fd3d00-a178-11eb-9c95-5e6bf5eca35e.png)

## Summary

The [Bank Marketing dataset](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) contains data relating to a marketing campaign based on phone calls to clients. In this project, we aim to predict if a client will subscribe (yes/no) to the bank product - term deposit, which is the target variable y.

The best performing model for the classification task was a model with an optimal primary metrics of accuracy 0.91492 that converges at 100 maximum iterations and a 0.08703 regularization strength. These are the logistic regression algorithm's parameters that were auto-tuned to achieve the best result.

## Scikit-learn Pipeline

Scikit pipeline constitute a train.py script which has the training data that was downloaded using the url link, an unregistered tabular dataset was created using the from_delimited_files() method, and then data preprocessing was also performed. The training algorithm used is the Logistic Regression algorithm with parameters C and max_iter, and the goal is to maximize primary metric "accuracy" by applying automatic hyperparameter tuning using Hyperdrive.

In order to define the search space for hyperparameter tuning, a choice among these discrete values for max_iter (25, 50, 100, 200) was made and C has a uniform distribution value ranging from a minimum value (0.08) and maximum vaule (0.1). Then a random sampling method randomly selects one of these hyperparameter values be it choice or uniform. For hyperparameter policy, a bandit early termination policy was chosen to avoid premature termination of first 5 runs and then subsequently the policy was applied every 2 iterations to terminate runs whose primary metric fell outside of the top 10%. Finally, HyperDriveConfig was created using the SKLearn estimator, hyperparameter sampler, and policy. This produced a trained model with hyperparameters optimized by HyperDrive

**Benefits of the parameter sampler chosen**

Random sampler has the advantage of supporting early termination of low performance runs for both discrete and continuous hyperparameters.

**Benefits of the early stopping policy chosen**

A key benefit of Bandit termination policy is that it terminates runs whose primary metrics are not within the specified slack amount when compared to the best perfroming run, thereby improving computational efficiency.

## AutoML

In AutoML experiment which was ran on a local compute with the number of cross validation 5, the automation tries various algorithms and hyperparameter selections, thereby leading to VotingEnsemble algorithm that uses weighted averages to generate the best model with an accuracy of 0.91671 & ensembled_iterations: (1, 0, 22, 28, 19, 15, 21). The regularization strengths and maximum number iterations are the parameters of all the top performing algorithms.

## Pipeline comparison

The AutoML model with an accuracy of 0.91671 outperformed the HyperDrive optimized logistic regression of accuracy 0.91492. There is also a slight contrast between their training processes. The parameter search space and algorithm for HyperDrive have to be defined before automation while for AutoML, the algorithm and hyperparameters selection are automated. I believe the difference is as a result of the principle of "the strenght is in the number". In a sense with AutoML, we are having a large number of trained models and combining their outputs to produce a final output, is a better approach to getting a higher accuracy.

## Future work

1. Some key areas that can bring about a higher quality prediction is trying different combinations of hyperparameters, increasing the maximum total runs for the HyperDrive, etc. 
2. For AutoML, training the model on a remote compute where the full set of features can be used will result in better model aacuracy.
