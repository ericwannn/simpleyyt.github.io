---
layout: post
category: Machine Learning
title: Why cross-validation
tagline: by Snail
tags: 
  - machine learning
  - scikit-learn
published: true
---
# TL; DR

* Cross-validation (CV), or k-fold CV is a model evaluation method, which is extensively used for fine-tuning parameters.
* Advantages: 
    1. Make full use of data; 
    2. Prevent overfitting;
    3. Estimation on variance of model when applied to real data;
* Disadvantages: 
    1. Computational Expensive;
* Only splitting the whole data set into training/test set causes overfitting.
* Implementation:
    1. Split the whole data set into one training set and one test set;
    2. Split the **training set** into *k* parts; 
    3. Repeat the experiments *k* times. Each time: 
        1. One different part of data are held out and used as validation set;
        2. Train the model with the rest *k-1* parts of data;
        3. Validate the model on validation set and got one result;
    4. (*optional*) Average the total *k* results. 

---

# Intuition & Motivation

It's simple to know how to perform a CV but it could be a bit confusing to machine learning beginners. 
That's because the intuition behind CV isn't that straight forward, and it evolves several times until what it looks like today.
To understand why we need CV even though it's expensive and what if we don't use CV, we can have some previous versions of validation methods and what's their problems.

## Training/Test split

It's intuitive to split the data into a training set and a test set when given a brunch of data. So we can train the model with training set and see how it goes on test set and then we can use the result to refine our models. Everything seems right! No. It's correct to split the data into training set and test set but it's wrong to fine-tune models with test set. This is the first common misunderstanding about validation: 

### Do not use test set to fine-tune models 

Even though data for training and test are isolated, the information about test set might leak to the model when the model is trained in this way repeatedly. You might keep optimizing your models until it yields good results on test set which might overfit your model to test set. What we want from test set is **an estimation on the generalizability of the model** or in other word **how the model would perform on real data that we never come across**. Test set is only used after the model is properly trained. But how can we modify our models without knowing how it behaves? This is why there is another set of data named **validation set**.  


### Validation set ≠ Test set

Some beginners are confused with these two concepts because they have the same purpose: evaluating model, like testing the performance. The difference is that they are used at different time. The whole data set is first split into training set and test set and then part of training data are held out as validation set. Test set, as mentioned above, is only used after you finish training the model. Validation set is used to fine-tune your models. Bad thing is the model might still overfit to validation set. But good thing is we will have scores on both validation set and test set. If they are significantly different, we have more evidence to tell if the model is overfitting or underfitting.

It's nearly perfect except that **too many data are wasted on pure evaluation** and **the model now is kinda sensitive to how we split the training set**. K-fold cross-validation solve these pain points to some degree by training and validating the models for *k* times. And here comes the third common misunderstanding.


### The entire CV is done on training set

You may wanna look back to the steps to perform CV in TL;DR. We perform CV only on training set to evaluate the model. In most cases, there are many choices for setting parameters and we will perform a CV for each of them (definitely time-consuming) and see which one gets highest score. Finally we can feed test data to the trained model. Because we get *k* results from CV some it also helps us to estimate how precise the model is (i.e. from standard deviation of these results).


# Best practice 

Below is the implementation of CV in `Scikit-learn`. The results are not perfect but it shows how CV is combined with 


```python
from __future__ import print_function
from __future__ import division
import numpy as np
from sklearn.tree import DecisionTreeRegressor
from sklearn.datasets import load_boston
from sklearn.model_selection import train_test_split, KFold, GridSearchCV
from sklearn.metrics import make_scorer, r2_score

# Load data

boston = load_boston()
X_train, X_test, y_train, y_test = train_test_split(boston.data, boston.target, test_size=0.2)


# Fit model with Grid search CV

def fit_model(X, y):

    cross_validator = KFold(4)    
    regressor = DecisionTreeRegressor(random_state=0)
    params = {"max_depth":range(3, 10)}
    scoring_func = make_scorer(r2_score, greater_is_better=True)
    grid = GridSearchCV(estimator=regressor,  param_grid=params, scoring=scoring_func, cv=cross_validator)

    grid = grid.fit(X, y)

    return grid.best_estimator_


optimal_reg = fit_model(X_train, y_train)

print("Parameter 'max_depth' is {} for the optimal model.".format(optimal_reg.get_params()['max_depth']))


# Test model 

y_predict = optimal_reg.predict(X_test)
r2 = r2_score(y_predict, y_test)
print("Optimal model has r2 score: {:,.2f} on test data".format(r2))

```