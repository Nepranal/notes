# Decision Trees

Say we have the following

![alt text](<sources/Decision Trees and Ensemble Methods/Screenshot 2026-04-14 at 10.02.29 PM.png>)

such a situation would warrant a non-linear classifier, like SVM (through higher dimensions), as you won't be able to correctly draw a single line under such data. Here we will be introducing another kind of non-linear classifier called the decision tree

The decision tree will actually treat the problem recursively by splitting the map into regions. At first the algorithm will split the region into 2 and then 3, then 4, and so on until we're satisfied. In each region, the majority of the class will be used to label all data that fall under that region.

How do we split the region? We simply ask question. You can think of it as a binary tree where each node is based on the parameter of our data

![alt text](<sources/Decision Trees and Ensemble Methods/Screenshot 2026-04-14 at 10.07.21 PM.png>)

Formally we say the split region $S_p$ as

$$
S_p(j,t) = (\set{x|x_j<t, x\isin R_p}, \set{x|x_j\geq t, x\isin R_p})
$$

where $x_j$ is the jth feature, $t$ is the treshold, and $R_p$ is the parent region.

# Choosing A Split

The decision tree chooses a tree in a greedy fashion. If in a parent region $R_p$ there are $C$ classes and say $p_c$ is the proportion of the data with $c$ as it's class, then we can define a loss function for a region $R$ as such

$$
L(R) = 1 - p_c
$$

essentially just getting the propotion of the missclassified sample. The decision tree then will just find a feature and a treshold that will minimize the error. Particularly, if we split $R_p$ into $R_a$ and $R_b$, the minimization problem the decision tree is concerned with will be

$$
\max_{j,t} L(R_p) - \frac{L(R_a) + L(R_b)}{2}
$$

Assuming $|R_a| = |R_b|$. Here we will consider the average loss of the children.

> There is a typo from the lecture. For the minimization objective, please check the lecture notes instead

## Issues With $L(R)$

There is an issue with our current $L(R)$ as we split the region especially when the number of missclassifications stays the same. Consider

![alt text](<sources/Decision Trees and Ensemble Methods/Screenshot 2026-04-14 at 11.24.34 PM.png>)

The 2nd one on the right can be argued to be better because your split is a little nicer (again, **arguably**) because you can isolate a bigger region in the split. Regardless of what we think, the 2nd split will be considered the same as the first one under our current definition of $L(R)$. Furthermore, based on our metric, these 2 splits are the same as the parent.

We would like to have something more sensitive. So, instead of the missclassification loss, we'll use something called the cross entropy loss

$$
L(R) = -\sum_cp_c\log(p_c)
$$

The cross entropy loss measures the average number of bits required to represent the outcome event. Finding a split that minimizes this quantity then means that we're finding 2 regions that have the smallest set of possible answers

> I don't really know if this is right, it's just my interpretation

## Cross Entropy Vs Misclassification Loss

We can visualize the sensitivity of the two loss functions. Let's consider a simple case where we only have 2 classes: positive and negative. The loss function then simplifies to

$$
L_{m} = 1 - \max_cp_c\\
L_{cross} = -p_+\log(p_+)-(1-p_+)log(1-p_+)
$$

Putting it this way, we can graph both loss as a function of $p_+$

![alt text](<sources/Decision Trees and Ensemble Methods/Screenshot 2026-04-16 at 6.21.31 AM.png>)

Our original issue with the miscalssifcation loss can be interpreted as that if both split of the region falls into the same halves (majority of parent is majority of children), then their average error will be the same as the parent.

You can also show this mathematically. Assuming equal split and majority is $p_c$

$$
\frac{L(R_1) + L(R_2)}{2} = \frac{1 - p_{c_1} + 1 - p_{c_2}}{2} = 1 - \frac{Np_{c_1} + Np_{c_2}}{2N}\\ = 1-p_c
$$

where $N$ is the size of the children.

> Also true in the non-equal split case

The cross entropy loss on the other hand is a concave function. This means that the average loss of the children will always be lower than the loss of the parent so long as the proportion of the children are not the same. Everytime we lower the loss, we would have 'gained information'.

# Regression Trees

We can also use decision trees for regression. It does so by predicting averages for a given region. Take our previous example but now we would like to predict the depth of snow given a latitude and time of the year

![alt text](<sources/Decision Trees and Ensemble Methods/Screenshot 2026-04-15 at 7.07.41 AM.png>)

What you can do now instead of taking majority is to take averages and the loss function would be the least mean square.

# Categorical Variables

We can also consider categorical data. For instance, instead of having latitudes as a real number line, we can instead split it into Northen hemisphere, Southern hemisphere, and the Equator. We can then proceed with the set of questions as we do with decision trees.

One thing to consider however is that you can't have too big of a category since that won't scale as we search for questions to use for our decision as we'll have to consider the power set of the categories.

# Ensembling Decision Trees

This is where we take multiple decision trees and use them together. Decision trees are models of high variance. With this being the case they usually come with regularization. You can think of things like limiting the depth, limiting size of leaf (children's cardinality), or limiting the maximum number of nodes (children region).

One more fact to show variance of a tree is with additive structure. When say the data are linear, with decision trees, you'll have to ask a lot of question just for it to be able to approximate the linearity of the data.

Doing ensembling allows us to deal with this aspect of linearity a little better. Ensembling works on the basis of let's say we hav $N$ i.i.d random variable $X$ where $Var(X) = \sigma$. Then

$$
Var(\bar{X}) = Var\left(\frac{1}{N}\sum_i X_i\right) = \frac{\sigma^2}{N}
$$

As you see here, if we take a bunch of decision trees and average them, the resulting model may have a smaller variance. Assuming of course, the decision trees are independent. If we drop the independence,

$$
Var(\bar{X}) = p\sigma^2 + \frac{1-p}{N}\sigma^2
$$

where $p$ is the correlation between the random variables.

Just to note, ensembling does not necessarily mean you need to use the same model. You could ensemble the decision tree with say SVM and logictic regression.

# Bagging - Bootstrap Aggregation

What we can do is to take multiple models and train them on a bootstrap sample $Z$ which is samples from the training set $S that is assumed to be the true population. From each of the trained model, we can then combine them into a meta model by taking their average.

As per our analysis before, you'll find a similar set of equation describing the variance of the meta model. Bootstrapping allows for the correlation between models to go down since different model gets to see different parts of the data.

> I think, I'm not entirely sure

You can then techinically add as many models as you want though doing that will slightly increase the bias of your model because each subsample doesnt see all of the data. In general, for bagging, the variance decrease more than increase of the bias.

## Random Forest: Decision Trees + Bagging

Randomly pick a feature of the decision tree. One issue is that if there is a good feature, then all of your trees will use that first increasing the correlation between models. A simple solution then is to force the trees to look only for a particular set of features first randomly of the other trees.

## Boosting

Boosting is concerned with training weak learners. In the context of decision tree, we would only allow them to have a depth of 1 - a decision stump. We then ensemble these sets of stumps

The training process is also modified a bit.

![alt text](<sources/Decision Trees and Ensemble Methods/Screenshot 2026-04-16 at 7.32.09 AM.png>)

Say we start with the first model and we draw a line. The wrong data will then be rescaled before the next model trains on it and it will make decision on the reweighted dataset. The result is you'll get different model trained on a weighted dataset.

Ensembling the model together can be modified as well. Instead of taking average, we can take a weigted sum where each weight inidicates how many samples the model got right

$$
G(x) = \sum_i\alpha_ig_i
$$

Boosting allows for increasing bias but low variance

# References

1. https://www.youtube.com/watch?v=wr9gUr-eWdA&list=PLoROMvodv4rMiGQp3WXShtMGgzqpfVfbU&index=10
