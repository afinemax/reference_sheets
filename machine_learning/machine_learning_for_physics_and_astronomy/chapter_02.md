
# Neighbors and Trees


## **The golden rule** of data science is to know your data before starting out:
- Figure out the size (number of examples / number of features) of our data set.
- Check whether there are missing data, and decide how to handle them.
- Check whether all the features are in a similar numerical range (and if not, decide whether we need to normalize them), and determine whether there is something strikingly unusual about the distribution of their numerical values.
- In a classification problem, check whether the data set is very imbalanced 
- If possible, develop some intuition on how well we expect our model to work: Are these features meaningful? Do we have enough examples?

## The fundamental steps of building a *supervised* model are the following:

* Arrange the data into features and target arrays.
* Split them into a training set and a test set
* Select the ML algorithm you want to use, and its parameters
* Build the model by applying the selected algorithm to the training set, which will create a tentative (implicit) input-output relationship. If you are using **`sklearn`**, this corresponds to applying the **`.fit`** method.
* Apply the model to the features of the test data to predict the target property of the test data. If you are using **`sklearn`**, this corresponds to applying the **`.predict`** method.
* Estimate the performance of your model by using an appropriate evaluation metric to compare the predicted and true target property of the test data. Rejoice (unlikely), or figure out what is not working out and repeat (likely).
* **Warning:** different train/test splits might yield significantly different models, and expected test errors


## Decision Trees
- Work by asking a series of questions
	- we want to get to the answer using the smallest possible number of questions
- Decision trees are defined by splits (the criteria, or questions) and nodes (the groups of objects created by those criteria).
	- Splits are binary questions 
- The terminal nodes of the tree are called leaf nodes
- features are always considered one at a time
- **Warning:** very deep trees might have issues in generalizing, and decision trees can only make splits that are aligned with the original features, suggesting the need for feature engineering before the model is built.
- **Gini impurity**  $= 1 - \sum_i f(i)^2$  f(i) is the fractional abundance of each class. In a two-class problem, the Gini impurity varies between 0 and 0.5, with 0.5 being the highest level of impurity, corresponding to a set with equal numbers of objects from each class.
	- Example data set contains 6 dots and 9 stars, for a total Gini impurity of
	- = $1 - (6/15)^2 - (9/15)^2 = 0.48$ 
	- You want to optimize the decrease in impurity 
		- You want to achieves a larger decrease of impurity fast (order of splits in a tree)



## kNN: FINDING NEIGHBORS
- k Nearest Neighbors (kNN) 
- In a binary classification problem, ensuring that k is an odd number is a natural way to handle possible ties
- need to re-normalize aka standardize feature scales to be comparable 
	- somewhat normally distributed
	- different methods will be suitable to standardize different distributions, and sometimes it is good to experiment with more than one standardization procedure.
	-  more robust scaling technique that sets the median (instead of the mean) to zero and the difference between the 25th and 75th percentile (instead of the standard deviation) to one
- **Warning:**  redundant or useless features may harm kNN models, as distance is calculated over the full dimensionality of the feature space