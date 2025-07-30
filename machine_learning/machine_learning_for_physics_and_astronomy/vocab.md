
**Instance:** A single data point or example used in a machine learning model, one row in our data set. IE A single observation of a pulsar or FRB, one exoplanet

**Features:** the individual measurable properties or characteristics of an instance. They are the inputs the machine learning algorithm uses to make decisions or predictions.
> **Note** that it is tempting to just count the columns of a data set and assume this is the correct number of features, but that is not always the case


**Gini impurity**  $= 1 - \sum_i f(i)^2$  f(i) : is the fractional abundance of each class. In a two-class problem, the Gini impurity varies between 0 and 0.5, with 0.5 being the highest level of impurity, corresponding to a set with equal numbers of objects from each class. Used in decision trees. 
- Example data set contains 6 dots and 9 stars, for a total Gini impurity of
- = $1 - (6/15)^2 - (9/15)^2 = 0.48$ 
- You want to achieves a larger decrease of impurity fast (order of splits in a tree)

**Accuracy:** The percentage of correct classifications

**Standardizing:** Real normalizing some feature scale 
 
**Decrease of Impurity:**

**Information Gain:**