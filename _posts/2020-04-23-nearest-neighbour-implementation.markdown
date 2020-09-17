---
layout: post
title:  "Nearest Neighbour Implementation"
date:   2020-04-23 12:00:00 +0100
categories: projects
tags: projects python jupyter machine-learning
---

```python
import pandas as pd
import numpy as np

data_train = pd.read_csv('sonar_train.csv', header='infer')
data_test = pd.read_csv('sonar_test.csv', header='infer')
```

The following cell contains the two algorithms (Euclidean & Manhattan) as functions<br><br>
They both require:
* Two vectors to be evaluated
* The number of attributes to consider (it is 60 by default, declared in the main function)


```python
# Function to calculate (float) euclidean distance 
def euclidean_distance(vector_1, vector_2, attr_length):
    distance = 0.0

    # Go through attributes in attr_length (60 attributes) and add the squares up
    for x in range(attr_length):
        distance += (vector_1[x] - vector_2[x]) ** 2

    # Square the distance before returning
    return np.sqrt(distance)


# Function to calculate (float) manhattan distance
def manhattan_distance(vector_1, vector_2, attr_length):
    distance = 0.0

    # Go through attributes in attr_length (60 attributes) and add the absolute values up
    for x in range(attr_length):
        distance += np.absolute(vector_1[x] - vector_2[x])

    return distance
```

This next cell contains the main function that measures all distances of each Vector of the Training set with every single Vector from the Testing set.<br>

It takes as parameters:
* Training set - All data (2D Vector)
* Testing vector - One vector per function call
* Algorithm type (Euclidean or Manhattan) - String
* Attributes length to consider (By default 60)

It iterates through each Vector of the Training set and calculates the distances (Manhattan or Euclidean) to the Testing vector, storing each distance in a data structure. At the end of the function, the data structure is sorted, and the first element is returned, which is the closest Vector, its index and distance.


```python
# Function to return an array in this form [(nearest neighbour vector), distance, index of the vector] 
def get_nearest_neighbour(training_set, testing_vector, algorithm, attr_length):

    distances = []

    # Calculate the distance of every vector in the training set
    for i in range(len(training_set)):

        # What algorithm to use (euclidean or manhattan)
        if algorithm == 'euclidean':
            dist = euclidean_distance(testing_vector, training_set[i], attr_length)
        else:
            dist = manhattan_distance(testing_vector, training_set[i], attr_length)

        # Save the (vector, the distance and the index) in distances array  
        distances.append([training_set[i], dist, i])
        
    # Convert to NP vector and sort by index 1 (distance) to get the closest first
    distances = np.array(distances)
    dsorted = distances[np.argsort(distances[:, 1])]

    # Return the first element of the sorted array
    return dsorted[0]
```

The following is the main function of the program. It is used to get all the class predictions for the Testing data using the Training data. It also uses all the functions declared and explained above. <br>

It takes as parameters:
* Training set - All data (2D Vector)
* Testing set - All data (2D Vector)
* Algorithm type (Euclidean or Manhattan) - String
* Attributes length to consider (By default 60)

`myNN` will store each prediction given by `get_nearest_neighbour` for the whole Training set and then return a vector of classes of the same length.



```python
# Main function to return the vector of all predictions 
def myNN(trainSet, testSet, algorithm, attr_length):

    allPredictions = []

    # Go through each vector in the testing data
    for i in range(len(testSet)):

        # Find the nearest neighbour as [vector, distance (float), index]
        nearestNeighbour = get_nearest_neighbour(trainSet, testSet[i], algorithm, attr_length) 
        classfound = nearestNeighbour[0][-1]

        # Save each prediction in the data structure
        allPredictions.append(classfound)

    # Convert to NP array and return
    return np.array(allPredictions)
```

Lastly, `get_accuracy` function will find the accuracy percentage comparing the ground truth classes and the predictions given by the program.<br>

It requires:<br>
* The Testing set which contains the right classes 
* Vector of predictions obtained by the program


```python
# Find the accuracy in respect of the predictions and return its rate
def get_accuracy(testset, predictions):
    totalright = 0
    
    # Go through each record of the test data 
    for i in range(len(testset)):
        
        # Compare the class of the test data and the prediction for this vector
        if testset[i][-1] == predictions[i]:
            totalright += 1
        
    # Calculate and return the accuracy as a float 
    return (totalright/float(len(testset))) * 100
```

This last cell contains all the function calls and where the results are being retrieved and shown. There are calls to training functions, accuracies and display for both algorithms, Euclidean and Manhattan.


```python
# Convert the data to numpy arrays
nparrtrain = np.array(data_train)
nparrtest = np.array(data_test)

# Get the predictions (NP arrays) for each algorithm
euclidean_prediction = myNN(nparrtrain, nparrtest, 'euclidean', 60)
manhattan_prediction = myNN(nparrtrain, nparrtest, 'manhattan', 60)

# Evaluate the accuracy of each prediction by algorithm
euclidean_accuracy = get_accuracy(nparrtest, euclidean_prediction)
manhattan_accuracy = get_accuracy(nparrtest, manhattan_prediction)

# Print the accuracies with 3 decimal places
print("Accuracy for Euclidean algorithm: %.3f" % euclidean_accuracy)
print("Accuracy for Manhattan algorithm: %.3f" % manhattan_accuracy)

# Print the prediction vectors
print("\nClass predictions for Euclidean:\n", euclidean_prediction)
print("\nClass predictions for Manhattan:\n", manhattan_prediction)

# Print the ground truth
real_classes = np.array(data_test['Class'])
print("\nGround truth classes from Testing set:\n", real_classes)

```

    Accuracy for Euclidean algorithm: 89.855
    Accuracy for Manhattan algorithm: 88.406
    
    Class predictions for Euclidean:
     ['R' 'M' 'M' 'R' 'R' 'M' 'M' 'M' 'M' 'M' 'R' 'R' 'R' 'R' 'M' 'M' 'M' 'R'
     'M' 'M' 'M' 'R' 'R' 'R' 'R' 'M' 'R' 'R' 'M' 'M' 'M' 'M' 'M' 'R' 'M' 'M'
     'M' 'M' 'M' 'R' 'R' 'M' 'M' 'M' 'M' 'R' 'R' 'M' 'R' 'R' 'M' 'R' 'R' 'M'
     'M' 'R' 'M' 'R' 'M' 'M' 'R' 'M' 'M' 'R' 'M' 'M' 'M' 'M' 'M']
    
    Class predictions for Manhattan:
     ['R' 'M' 'M' 'R' 'R' 'M' 'M' 'M' 'M' 'M' 'R' 'R' 'R' 'R' 'M' 'M' 'M' 'R'
     'M' 'M' 'M' 'R' 'R' 'R' 'R' 'M' 'R' 'R' 'M' 'M' 'M' 'M' 'M' 'R' 'M' 'M'
     'M' 'M' 'M' 'R' 'R' 'M' 'M' 'M' 'R' 'R' 'R' 'M' 'R' 'R' 'M' 'R' 'M' 'M'
     'M' 'R' 'M' 'R' 'M' 'R' 'M' 'M' 'M' 'R' 'M' 'M' 'M' 'M' 'R']
    
    Ground truth classes from Testing set:
     ['R' 'M' 'M' 'R' 'R' 'R' 'M' 'M' 'M' 'R' 'R' 'R' 'R' 'R' 'R' 'M' 'M' 'M'
     'M' 'M' 'M' 'R' 'R' 'R' 'R' 'M' 'R' 'R' 'M' 'M' 'M' 'M' 'M' 'R' 'M' 'M'
     'M' 'M' 'M' 'R' 'R' 'M' 'M' 'M' 'R' 'R' 'R' 'M' 'R' 'R' 'M' 'R' 'R' 'M'
     'M' 'R' 'M' 'R' 'M' 'M' 'R' 'M' 'M' 'R' 'M' 'M' 'R' 'M' 'R']
    
