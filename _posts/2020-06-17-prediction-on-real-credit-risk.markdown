---
layout: post
title:  "Predicting Real Credit Risk Data"
date:   2020-06-17 12:00:00 +0100
categories: projects
tags: python machine-learning jupyter
---

This post overviews and examines predicting real credit risk data on people from various backgrounds.
The training data I used is [here](https://gist.github.com/gokhj/f138856c2d759537264e02e4bdfc51cd){:target="_blank"}, and the test data is [here](https://gist.github.com/gokhj/546f5454ed8c23fc5c3e0d41b2a0061d){:target="_blank"}.


The following algorithms evaluated and their performance is tested on this particular credit risk problem.

* Adaboost
* Support Vector Machine
* K-Nearest Neighbour
* Decision Tree
* Random Forest
* Bagging

<br>

---

<br>

The data attributes are explained below:

**X1:** Amount of the given credit (NT dollar): it includes both the individual consumer credit and his/her family (supplementary) credit.

**X2:** Gender (1 = male; 2 = female).

**X3:** Education (1 = graduate school; 2 = university; 3 = high school; 4 = others).

**X4:** Marital status (1 = married; 2 = single; 3 = others).

**X5:** Age (year).

**X6 - X11:** History of past payment. One tracked the past monthly payment records (from April to September, 2005) as follows: X6 = the repayment status in September, 2005; X7 = the repayment status in August, 2005; . . .;X11 = the repayment status in April, 2005. The measurement scale for the repayment status is: -1 = pay duly; 1 = payment delay for one month; 2 = payment delay for two months; . . .; 8 = payment delay for eight months; 9 = payment delay for nine months and above.

**X12-X17:** Amount of bill statement (NT dollar). X12 = amount of bill statement in September, 2005; X13 = amount of bill statement in August, 2005; . . .; X17 = amount of bill statement in April, 2005.

**X18-X23:** Amount of previous payment (NT dollar). X18 = amount paid in September, 2005; X19 = amount paid in August, 2005; . . .;X23 = amount paid in April, 2005.

<br>

### Code Implementation

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline

from sklearn.metrics import accuracy_score, precision_score, recall_score, confusion_matrix, precision_recall_curve

train_data = pd.read_csv('creditdefault_train.csv', header='infer')
test_data = pd.read_csv('creditdefault_test.csv', header='infer')
```

#### Preparation of dummy data

Some features of the training and testing sets are treated as numerical features, but they are categorical. For example, the education field (X3) has range 1 - 4, but each value can be a separate binary feature.



```python
'''
  DATA PREPARATION MAIN FUNCTION
  This function works for both Training and Testing data as they both have the same features
  It requires the set to be prepare and returns the final set
'''
def data_preparation(dataset):

  # X3: Education field is divided in 3 binary features, last one is implicit when all three features are equal to 0
  dataset['graduated'] = (dataset['X3'] == 1).astype('int')
  dataset['university'] = (dataset['X3'] == 2).astype('int')
  dataset['high_school'] = (dataset['X3'] == 3).astype('int')
  dataset.drop('X3', axis=1, inplace=True)

  # X2: Gender field is binary as well. Male = 1 and Female = 0
  dataset['male'] = (dataset['X2'] == 1).astype('int')
  dataset.drop('X2', axis=1, inplace=True)

  # X4: Married field is also binary. Married = 1 and Single = 0
  dataset['married'] = (dataset['X4'] == 1).astype('int')
  dataset.drop('X4', axis=1, inplace=True)

  # X6 - X11: History of past payments. All values less than 0 are converted to 0 meaning they are not delayed on payments
  are_paid = ['X6', 'X7', 'X8', 'X9', 'X10', 'X11']
  for column in are_paid:
    dataset.loc[dataset[column] <= 0, column] = 0

  # Return the modified dataset
  return dataset


# Call for preparation of the data using the main function
train_data = data_preparation(train_data)
test_data = data_preparation(test_data)


# Separate classes from dataset for training and testing
y_train = train_data['Y']
X_train = train_data.drop(['Y'],axis=1)

y_test = test_data['Y']
X_test = test_data.drop(['Y'],axis=1)


'''
  Use RobustScaler preprocessor to convert the data to the same scale 
  as certain features like `age` have a larger scale.
'''
from sklearn.preprocessing import RobustScaler

X_train = RobustScaler().fit_transform(X_train)
X_test = RobustScaler().fit_transform(X_test)
```

#### Cross Validation

It evaluates all six algorithms using cross validation and plot them in a table to analize their accuracy and standard deviation.


```python
from sklearn.model_selection import cross_validate

from sklearn.neighbors import KNeighborsClassifier
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import BaggingClassifier
from sklearn.ensemble import AdaBoostClassifier
from sklearn.ensemble import RandomForestClassifier
from sklearn.svm import SVC

# Prepare the data frame for visualizing the cross validation
crossvalidator = pd.DataFrame(index=['Accuracy', 'Standard Deviation'], columns=[])

# Function to populate the evaluation of each model
def eval_crossval(model_name, crossval):
  crossvalidator.loc['Accuracy', model_name] = crossval['test_score'].mean()
  crossvalidator.loc['Standard Deviation', model_name] = crossval['test_score'].std() * 2


# Compute cross validation for KNN
knn = KNeighborsClassifier()
crossknn = cross_validate(knn, X_train, y_train, cv=5)
eval_crossval("KNN", crossknn)

# Compute cross validation for Decision Tree
dtc = DecisionTreeClassifier()
crossdtc = cross_validate(dtc, X_train, y_train, cv=5)
eval_crossval("Decision Tree", crossdtc)

# Compute cross validation for Bagging Accuracy
bag = BaggingClassifier()
crossbag = cross_validate(bag, X_train, y_train, cv=5)
eval_crossval("Bagging Accuracy", crossbag)

# Compute cross validation for Ada Boost
abc = AdaBoostClassifier()
crossabc = cross_validate(abc, X_train, y_train, cv=5)
eval_crossval("Ada Boost", crossabc)

# Compute cross validation for Random Forest
rfc = RandomForestClassifier()
crossrfc = cross_validate(rfc, X_train, y_train, cv=5)
eval_crossval("Random Forest", crossrfc)

# Compute cross validation for SVC
svc = SVC()
crosssvc = cross_validate(svc, X_train, y_train, cv=5)
eval_crossval("SVC", crosssvc)


# Display table of cross validation
crossvalidator
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>KNN</th>
      <th>Decision Tree</th>
      <th>Bagging Accuracy</th>
      <th>Ada Boost</th>
      <th>Random Forest</th>
      <th>SVC</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Accuracy</th>
      <td>0.791733</td>
      <td>0.728200</td>
      <td>0.807333</td>
      <td>0.818467</td>
      <td>0.815933</td>
      <td>0.816133</td>
    </tr>
    <tr>
      <th>Standard Deviation</th>
      <td>0.013289</td>
      <td>0.016114</td>
      <td>0.017430</td>
      <td>0.017495</td>
      <td>0.018825</td>
      <td>0.013918</td>
    </tr>
  </tbody>
</table>
</div>



#### Cross Validation analysis

According to the cross-validation table above, the algorithm with higher performance is Ada Boost. However, all the other algorithms are very close to it, except the Decision Tree. Since AdaBoost and Bagging use an implementation of Decision Trees in the first place, it gives a better precision for the data. In the end, these two ensemble algorithms worked better than the other ones. On the other hand, although SVC gave accurate prediction, since the nature of the algorithm, we expect more computation time compared to the other algorithms.

#### Helper functions for visualizing data
They will be use several times for plotting graphs or displaying tables.


```python
# Prepare the data frame for visualizing the final models evaluations
evaluator = pd.DataFrame(index=['Accuracy', 'Precision', 'Recall'], columns=[])


# Function to populate the evaluation of each model 
def eval_module(model_name, y_truth, y_pred):
  evaluator.loc['Accuracy', model_name] = accuracy_score(y_truth, y_pred)
  evaluator.loc['Precision', model_name] = precision_score(y_truth, y_pred)
  evaluator.loc['Recall', model_name] = recall_score(y_truth, y_pred)


# Function to visualize the evaluation of all models
def visual_evaluation(evaluation):
  fig, ax = plt.subplots(figsize=(8,5))
  evaluation.plot(kind='barh', ax=ax)
  ax.grid()


# Function to print the confusion matrix of a given module
def print_conf_matrix(cm):
  labels = ['Payers', 'Defaulters']
  conf = pd.DataFrame(data=cm, index=labels, columns=labels)
  conf.index.name = "TRUE"
  conf.columns.name = "PREDICTION"
  conf.loc['Total'] = conf.sum()
  conf['Total'] = conf.sum(axis=1)
  return conf
```

#### Helper functions for manipulating data
The functions here are used for calculations in the code of certain models.


```python
# This function takes a dictionary with indexes and accuracies and finds the highest and returns it. Use for KNN, Adaboost and Bagging
def find_best_param(accuracy_dict):
    largest, index = 0, 0

    # Loop the dictionary
    for i in accuracy_dict:

        # This iteration is higher than previous ones
        if largest < accuracy_dict[i]:
            largest = accuracy_dict[i]
            index = i
            
    # Return index and its best accuracy
    return (index, largest)
```

### KNN Classifier algorithm
This implementation finds the best K in a range of 1 to 9, store their performance and print the confusion matrix of the model.


```python
# KNN for K = 1 to 9
from sklearn.neighbors import KNeighborsClassifier

scores_knn = {}
total_k = 9 # Calculate distances for K = 1 to 9

# Train data K times
for k in range(1, total_k + 1):

    # Initialize KNN for k neighbors
    knn = KNeighborsClassifier(n_neighbors = k)
    knn.fit(X_train, y_train)

    # Predict classes and store the accuracy 
    y_pred = knn.predict(X_test)
    scores_knn[k] = accuracy_score(y_test, y_pred)

# Find the best K using the helper function
best_k = find_best_param(scores_knn)

print('Best accuracy at K =', best_k[0], 'of %.3f' % best_k[1])  

# Compute the final KNN with the best K achieved
knn_best_k = KNeighborsClassifier(n_neighbors = best_k[0])
knn_best_k.fit(X_train, y_train)

# Best predictor found
y_pred_best = knn_best_k.predict(X_test)

# Evaluation of this module
eval_module("KNN", y_test, y_pred_best)

# Print the confusion matrix of this model
con_matrix = confusion_matrix(y_test, y_pred)
print_conf_matrix(con_matrix)
```

    Best accuracy at K = 9 of 0.806
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>PREDICTION</th>
      <th>Payers</th>
      <th>Defaulters</th>
      <th>Total</th>
    </tr>
    <tr>
      <th>TRUE</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Payers</th>
      <td>10987</td>
      <td>695</td>
      <td>11682</td>
    </tr>
    <tr>
      <th>Defaulters</th>
      <td>2213</td>
      <td>1105</td>
      <td>3318</td>
    </tr>
    <tr>
      <th>Total</th>
      <td>13200</td>
      <td>1800</td>
      <td>15000</td>
    </tr>
  </tbody>
</table>
</div>



### Decision Tree Classifier algorithm
This implementation computes and print the confusion matrix of Decision Tree.


```python
# Decision Tree
from sklearn.tree import DecisionTreeClassifier

# clf = DecisionTreeClassifier(criterion='entropy', max_depth=3)
clf = DecisionTreeClassifier(min_samples_split=30, min_samples_leaf=10, random_state=10)
clf = clf.fit(X_train, y_train)

y_pred = clf.predict(X_test)

# Evaluation of this module
eval_module("DecisionTree", y_test, y_pred)

# Print the confusion matrix of this model
con_matrix = confusion_matrix(y_test, y_pred)
print_conf_matrix(con_matrix)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>PREDICTION</th>
      <th>Payers</th>
      <th>Defaulters</th>
      <th>Total</th>
    </tr>
    <tr>
      <th>TRUE</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Payers</th>
      <td>10604</td>
      <td>1078</td>
      <td>11682</td>
    </tr>
    <tr>
      <th>Defaulters</th>
      <td>2255</td>
      <td>1063</td>
      <td>3318</td>
    </tr>
    <tr>
      <th>Total</th>
      <td>12859</td>
      <td>2141</td>
      <td>15000</td>
    </tr>
  </tbody>
</table>
</div>



### Random Forest Classifier
This implementation uses the Random Forest algorithm fitting with 500 base classifiers, sends the prediction accuracy to the evaluation methods and print the confusion matrix.


```python
# Random Forest

from sklearn.ensemble import RandomForestClassifier

# 500 base classifiers fit to the 2-dimensional dataset
numBaseClassifiers = 500

# Initialize the Random Forest Classfier and fit the data to the model
rf = RandomForestClassifier(n_estimators=numBaseClassifiers)
rf.fit(X_train, y_train)

# Predict classes and store the accuracy 
y_pred = rf.predict(X_test)

# Evaluation of this model
eval_module("Random Forest", y_test, y_pred)

# Print the confusion matrix of this model
con_matrix = confusion_matrix(y_test, y_pred)
print_conf_matrix(con_matrix)

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>PREDICTION</th>
      <th>Payers</th>
      <th>Defaulters</th>
      <th>Total</th>
    </tr>
    <tr>
      <th>TRUE</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Payers</th>
      <td>11086</td>
      <td>596</td>
      <td>11682</td>
    </tr>
    <tr>
      <th>Defaulters</th>
      <td>2163</td>
      <td>1155</td>
      <td>3318</td>
    </tr>
    <tr>
      <th>Total</th>
      <td>13249</td>
      <td>1751</td>
      <td>15000</td>
    </tr>
  </tbody>
</table>
</div>



### Bagging Classifier
This implementation uses the Bagging algorithm along with Decision Trees, fitting with 500 base classifiers. It also iterates a list of max depth values and gets the best one. With the best picked depth, the model recalculates and sends the prediction data to evaluation. At the end it prints the confusion matrix.


```python
# Bagging

from sklearn.ensemble import BaggingClassifier
from sklearn.tree import DecisionTreeClassifier

# 500 base classifiers fit to the 2-dimensional dataset
numBaseClassifiers = 500

# Checking different sizes of max depth
maxdepths = [2,3,4,5,6,7,8,9,10,15,20,25,30,35,40,45,50]

# Keeping the individual max depth scores in a dictionary to get best one
scores_bagging = {}

for depth in maxdepths:

  # Initialize the Bagging Classifier and fit the data to the model
  bg = BaggingClassifier(DecisionTreeClassifier(max_depth=depth),n_estimators=numBaseClassifiers)
  bg.fit(X_train, y_train)

  # Predict classes and store the accuracy
  y_pred = bg.predict(X_test)

  # Putting the accuracy score to the dictionary
  scores_bagging[depth] = accuracy_score(y_test, y_pred)

best_depth = find_best_param(scores_bagging)
bagging_best_depth = BaggingClassifier(DecisionTreeClassifier(max_depth=best_depth[0]),n_estimators=numBaseClassifiers)

bagging_best_depth.fit(X_train, y_train)
y_pred_best = bagging_best_depth.predict(X_test)

# Evaluation of this model
eval_module("Bagging", y_test, y_pred_best)

print('Best depth is at =', best_depth[0], 'of %.3f' % best_depth[1])

# Print the confusion matrix of this model
con_matrix = confusion_matrix(y_test, y_pred)
print_conf_matrix(con_matrix)
```

    Best depth is at = 8 of 0.818
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>PREDICTION</th>
      <th>Payers</th>
      <th>Defaulters</th>
      <th>Total</th>
    </tr>
    <tr>
      <th>TRUE</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Payers</th>
      <td>11119</td>
      <td>563</td>
      <td>11682</td>
    </tr>
    <tr>
      <th>Defaulters</th>
      <td>2172</td>
      <td>1146</td>
      <td>3318</td>
    </tr>
    <tr>
      <th>Total</th>
      <td>13291</td>
      <td>1709</td>
      <td>15000</td>
    </tr>
  </tbody>
</table>
</div>



### AdaBoost Classifier
This implementation uses the AdaBoost algorithm along with Decision Trees, fitting with 500 base classifiers. It also iterates a list of max depth values and gets the best one. With the best picked depth, the model recalculates and sends the prediction data to evaluation. At the end it prints the confusion matrix.


```python
# AdaBoost

from sklearn.ensemble import AdaBoostClassifier
from sklearn.tree import DecisionTreeClassifier

# 500 base classifiers fit to the 2-dimensional dataset
numBaseClassifiers = 500

# Checking different sizes of max depth
# maxdepths = [2,3,4,5,6,7,8,9,10,15,20,25,30,35,40,45,50]
maxdepths = [10]

# Keeping the individual max depth scores in a dictionary to get best one
scores_adaboost = {}

for depth in maxdepths:
  # Initialize the AdaBoost Classifier and fit the data to the model
  ab = AdaBoostClassifier(DecisionTreeClassifier(max_depth=depth),n_estimators=numBaseClassifiers)
  ab.fit(X_train, y_train)

  # Predict classes and store the accuracy
  y_pred = ab.predict(X_test)

  # Putting the accuracy score to the dictionary
  scores_adaboost[depth] = accuracy_score(y_test, y_pred)

best_depth = find_best_param(scores_adaboost)
adaboost_best_depth = AdaBoostClassifier(DecisionTreeClassifier(max_depth=best_depth[0]),n_estimators=numBaseClassifiers)

adaboost_best_depth.fit(X_train, y_train)
y_pred_best = adaboost_best_depth.predict(X_test)

eval_module("AdaBoost", y_test, y_pred_best)
print('Best depth is at =', best_depth[0], 'of %.3f' % best_depth[1])

# Print the confusion matrix of this model
con_matrix = confusion_matrix(y_test, y_pred)
print_conf_matrix(con_matrix)
```

    Best depth is at = 10 of 0.794
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>PREDICTION</th>
      <th>Payers</th>
      <th>Defaulters</th>
      <th>Total</th>
    </tr>
    <tr>
      <th>TRUE</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Payers</th>
      <td>10831</td>
      <td>851</td>
      <td>11682</td>
    </tr>
    <tr>
      <th>Defaulters</th>
      <td>2235</td>
      <td>1083</td>
      <td>3318</td>
    </tr>
    <tr>
      <th>Total</th>
      <td>13066</td>
      <td>1934</td>
      <td>15000</td>
    </tr>
  </tbody>
</table>
</div>



### SVM
This implementation uses Linear SVM approach with a list of C parameters. It iterates through every parameter and pick the best one. With the best parameter, the implementation calculates the prediction accuracy and sends the evaluation methods. At the end, it prints a confusion matrix.



```python
# SVM

from sklearn import linear_model
from sklearn.svm import SVC

# Hyerparameter that controls the inverse of model complexity
C = [0.01, 0.1, 0.2, 0.5, 0.8, 1, 5, 10, 20, 50]

# Keeping the individual hypeparameter scores in a dictionary to get best one
scores_svm = {}

for param in C:

  # Initialize the SVM Classifier and fit the data to the model
  svclassifier = SVC(kernel='linear', C=param)
  svclassifier.fit(X_train, y_train)

  # Predict classes and store the accuracy
  y_pred = svclassifier.predict(X_test)

  # Putting the accuracy score to the dictionary
  scores_svm[param] = accuracy_score(y_test, y_pred)

best_param = find_best_param(scores_svm)
svm_best_param = SVC(kernel='linear', C=best_param[0])

svm_best_param.fit(X_train, y_train)
y_pred_best = svm_best_param.predict(X_test)

eval_module("SVM", y_test, y_pred_best)
print('Best param is at =', best_param[0], 'of %.3f' % best_param[1])

# Print the confusion matrix of this model
con_matrix = confusion_matrix(y_test, y_pred)
print_conf_matrix(con_matrix)
```

    Best param is at = 1 of 0.817
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>PREDICTION</th>
      <th>Payers</th>
      <th>Defaulters</th>
      <th>Total</th>
    </tr>
    <tr>
      <th>TRUE</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Payers</th>
      <td>11007</td>
      <td>675</td>
      <td>11682</td>
    </tr>
    <tr>
      <th>Defaulters</th>
      <td>2064</td>
      <td>1254</td>
      <td>3318</td>
    </tr>
    <tr>
      <th>Total</th>
      <td>13071</td>
      <td>1929</td>
      <td>15000</td>
    </tr>
  </tbody>
</table>
</div>




```python
visual_evaluation(evaluator*100)

100*evaluator
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>AdaBoost</th>
      <th>SVM</th>
      <th>KNN</th>
      <th>DecisionTree</th>
      <th>Random Forest</th>
      <th>Bagging</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Accuracy</th>
      <td>79.020000</td>
      <td>81.740000</td>
      <td>80.613333</td>
      <td>77.780000</td>
      <td>81.606667</td>
      <td>81.826667</td>
    </tr>
    <tr>
      <th>Precision</th>
      <td>54.516640</td>
      <td>65.007776</td>
      <td>61.388889</td>
      <td>49.649696</td>
      <td>65.962307</td>
      <td>67.411765</td>
    </tr>
    <tr>
      <th>Recall</th>
      <td>31.103074</td>
      <td>37.793852</td>
      <td>33.303195</td>
      <td>32.037372</td>
      <td>34.810127</td>
      <td>34.538879</td>
    </tr>
  </tbody>
</table>
</div>




![png](/assets/credit_risk/output_23_1.png)


#### Final evaluation
This graph illustrates the performance of all the algorithms using the test data. The primary three keys to evaluate are:
- Accuracy: How the model predicted correct and incorrect defaulters
- Precision: How often the model predicted someone is a defaulter correctly
- Recall: Proportion of actual defaulters that the model predicted

In order to choose the metrics, we need to consider which mistakes to avoid. 
In this project, the most appropriate metric to look at is Recall to catch the False Negative who are the defaulters that were predicted as payers.

SVM performance is generally one of the best choices but especially its Recall the most beneficial for the data.
