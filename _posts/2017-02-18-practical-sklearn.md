---
layout: post
category: Machine learning
title: Practical Scikit-learn
tagline: by Snail
tags: 
  - python
  - machine learning
published: true
---

# Model evaluation

## cross-validation

```python
from sklearn.model_selection import KFold
X = np.array([[1, 2], [3, 4], [1, 2], [3, 4]])
y = np.array([1, 2, 3, 4])
kf = KFold(4, n_folds=2)
for train_index, test_index in kf:
  X_train, X_test = [X[ii] for ii in train_index], [X[ii] for ii in test_index]
  y_train, y_test = [Y[ii] for ii in train_index], [Y[ii] for ii in test_index]
```

## k-fold cross-validation

## parameter grid search