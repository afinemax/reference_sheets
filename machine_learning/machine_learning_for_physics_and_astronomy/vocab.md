
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

---
## CH3 Supervised Classification: Evaluation and Diagnostics

**Imputing:** Instead of discarding data rows with missing or incomplete values, replace it with a plausible estimate 

**Accuracy Paradox:** In an imbalanced data set, a very high accuracy is not necessarily indicative of an informative model. IE a data set of exoplanets and predicting if it is habitable or not.  Just saying everything is uninhabitable is a **highly accurate** model, and its useless. 

**TP:** True positive

**TN:** True Negative 

**FP:** False Positive

**FN:**   False Negative

**Accuracy:** = $\frac{TP + FN}{TN + TP + FP + FN}$ 

**Precision:** The fraction of correct positive out of all positive labeled samples. Precision $= TP/(TP+FP)$ . IE when we say something is positive, how  likely is it that it really is positive?

**Recall:** The fraction of correctly identified positive samples out of all intrinsically positive samples. Recall $=TP/(TP +FN)$. IE how many do we miss? AKA TPR

**FPR:** False positive rate,  $FPR =FP/(FP + TN)$ 

**TPR** True positive rate, $TP/(TP + FN)$ aka Recall 

**Confusion Matrix:** 2x2 matrix showing predicted labels (col) vs ground truth (row). Accuracy is the sum of the main diagonal elemets over the total, precision is lower right over the RHS, and recall is the lower right over the bottom half (for a binary 2x2)

**Receiver Operating Characteristic (ROC):**  A commonly used metric to evaluate model performance.  It is known as the ROC curve. Instead of treating the output as a binary, we use the predicted probability scores.  The ROC curve sees how good the model is as a function of  the  classification threshold. Varying the threshold adjusts the trade off between the two types of errors FP, and FN.  The ROC curve is a plot of   FPR vs TPR. The threshold decreases from left to right (plot w/ a color bar). The **area under the curve (AUC)** is a way of telling how good the model is, and compare against a range of threshold (I think higher AUC is better).

**Custom Metric:** Some function that is a combination of the above. 

**Cross Validation (CV):** Instead of doing a single split/train, do an ensemble then you are able to quote the typical metric $\pm$ $\sigma$ on that metric. 

**k-fold Cross Validation:** The most common practical strategy for cross validation. In this, the training set is divided into $k$ parts. Use 1 as a "test" set, and the remaining $k-1$ as the training sets, and cycle over them.  Almost the same thing as choosing k random realizations of train/test splits.  Need to **shuffle** data otherwise we will notice the inprinted ordering effects.  The predictive model is built $k$ times, the final model will be build using the entire training data set. 

The “right” $k$ for a given problem comes from establishing a trade-off between repeating the procedure many times and keeping computational times reasonable. Typical k values are between 5 and 10; it is not advisable to go below 3 if at all possible (loose meaning on $\sigma$).

**Other CV techniques:** Most other CV methods are a variation of the **k-fold** CV.  

**Stratification**the process of choosing the folds while maintaining approximately the same fractional class membership of the entire learning set.

**Learning Curve:** Plot of the performance metrics vs the training data set size. Metrics should improve and then plateau. `sci-kit-learn` has a way to plot this from the `cv` method.  

**Hyperparameter tuning:** Change the hyper params of the model. Used to effectively increase or decrease the model complexity. If facing high bias, you want to add complexity. If over-fit, with high variance, you want to reduce complexity.  A hyper param describes the model architecture. 

**Feature Engineering:** The process of building new features for models IE taking the star mass, and orbital period, and (exoplanet radius) and making a surface temp param. Expected to help high bias cases. 

**Feature Selection:** Only train, and use the model on a sub-sect of data features that matter more. 

**Resampling Data:** Force balance a data set, so sample with replacement 

**Micro vs Macro Averaging:** For multi-class classification, you can write the precision as $P = \frac{1}{n} \sum P$  aka the average precision for each class, or you can break it further as $P  = \sum \frac{TP_i}{TP_i + FP_i}$. This second method is called **micro averaging**, and it weighs good performance in a high membership class. IE we weigh each class by the number of ground truth in that class. Or we can weigh them all evenly this is the **Macro**.  


---
## CH4 Supervised Learning Models Optimization

**nested cross validation (CV):**

**Support Vector Machines (SVMs):**   ML algo suitable for classification or regression.  The algorithm works by searching for the hyperplane that provides optimal separation between the two classes; in a multiclass problem, this will be done using a one-vs-all approach. The idea is to maximize the width of the margin between the two classes.  The data points from each class that determine the decision boundary are called the **support vectors**. The classifier’s decision is only influenced by the **support vectors**, as opposed to by the entire training set. This makes it computationally cheap. 

Math wise it looks like:
$$
f(\mathbf{x}) = \mathbf{w}^T \mathbf{x} + b
$$
Where $\mathbf{w}$ is the weight vector, and $b$ is the bias. $\mathbf{w}^T \mathbf{x}$ is the dot product between the weight vector and the input vector $\mathbf{x}$.  The **decision boundary of the classifier is:**
$$  \mathbf{w}^T \mathbf{x} +b =0 $$

AKA where the function vanishes.  The hyperplane defined by the function splits the space into two, based on the sign of the function $f(\mathbf{x})$. We can define the labels based on if $f(\mathbf{x})$ is positive or negative.   

The width of the margin is defined in terms of the weight vector.  The function for the closest examples from each class. 
$$ f(\mathbf(x)_+) = \mathbf{w}^T \mathbf{x}_+ + b =a > 0$$
$$ f(\mathbf(x)_-) = \mathbf{w}^T \mathbf{x}_- + b =-a < 0$$
  $a$ is the distance from the decision boundary, which is assumed to be equal distant from $\mathbf{x}_-$, and $\mathbf{x}_+$ .  We are free to resale   $\mathbf{w}, b$ by $1/a$ .  Which makes the new $a =1$, and the decision function is simply then if $f(\mathbf{x}) > 1$ or $f(\mathbf{x}) < -1$.  The width of the margin is $d = 2 / || \mathbf{w}||$ aka the width of the margin is inversely proportional to the norm of the weight vector. 

Finding the optimal classier is then  a constrained optimization problem. We want to maximize the width of the margin, given that the margin should be empty.  This is saying that we want to minimize $1/d$, subject to $y^i(\mathbf{w}^T \mathbf{x}_i) \ge 1$$, and $y^i = -1$ for negative examples, and $y^i = +1$ for positive examples.  

It turns out that the weight vector can be expressed as a linear combo of the training data:
$$ \mathbf{w} = \sum_{i=1}^n y_i\alpha_i \mathbf{x}_i$$

For the data points for which $\alpha_i \ne 0$ are the **support vectors**. The fraction of the data points that are support vectors  is an upper bound on the error rate of the classifier.  

Non-linear **SVMs**






on page 86 on the pdf 













