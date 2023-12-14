# Predicting-recipe-ratings-based-on-reviews

## Problem Identification

This project aims to predict the average rating of a recipe posted
online based on information such as its description, nutrition info,
and the time it takes to make the food. This will be a regression task
since we are estimating average rating, which is a continuous variable.

I chose to use average rating as the response variable because a model
predicting average rating could be very valuable. If the model was
effective, website oweners could use the model to predict which recipes
would be well-received and promote those recipes more than recipes
predicted to have a low rating so that viewers are more likely to find
good recipes on their site.

The evaluation metric I'll be using is RMSE (root mean squared error).
RMSE can be impacted by outliers which might be dangerous in our
dataset, but it will give us a straightforward understanding of how
well or badly our model performs on the test set.

All the columns that are explained during the next step will be
feautures we can realisticaslly train our model on because each feature
will be inputted by a user at the time they upload their recipe to the
website.

## Building a baseline model

For a first-go at a model, I decided to go with linear regression.
It's one of the most straightforward approach to many regression
problems so I assumed our performance on linear regression would give
us a good baseline on how suited our features are for a regression task.

For this baseline model I will apply two transformations: a
CountVectorizer and a StandardScaler. The CountVectorized is meant
to give us meaning from the categorical columns.

The features in our dataframe are as follows:

| Feature Name | Description |
| name | Name given to recipe by recipe creator |
| minutes | estimates minutes taken to make the recipe |
| tags | short descriptions of the recipe |
| n_steps | the number of steps to cook the recipe |
| steps | each step of the recipe described |
| description | overview of the recipe |
| ingredients | list of ingredients used in recipe |
| n_ingredients | the number of ingredients used |
| review | a compilation of reviews of the recipe |
| average_rating | the average user rating |
| calories | number of calories |
| total_fat_pdv | percent of daily recommended value of fat |
| sugar_pdv | percent of daily recommended value of sugar |
| sodium_pdv | percent of daily recommended value of sodium |
| protein_pdv | percent of daily recommended value of protein |
| saturated_fat_pdv | percent of daily recommended value of saturated fat |
| carbohydrates_pdv | percent of daily recommended value of carbohydrates |

Minutes, n_steps, n_ingredients, average_rating, calories,
total_fat_pdv, sugar_pdv, sodium_pdv, saturated_fat_pdv, and
carbohydrates_pdv are all quantitative columns. Name, tags, steps,
description, and review are all nominal columns which we must encode.
During this step I used a count vectorizer as a straightforward way
to numerically represent the text in these columns.

Finally, I applied a standard scaled to the total_fat_pdv column since
it was the most linearly correlated with the average rating.
In the next step I will standardize all the features so that some
aren't arbitrarily weighted more heavily. If a decision tree based model 
ends up out-performing the linear regression model, this 
standardization may become redundant.

Evaluating our model based on RMSE, we find that on the training
set our model was off by .3768 on average and on the test set
our model was off by .735 on average. Predicting within the range of
one star seems somewhat reasonable to me, but the disparity between
our performance in the training and test set indicates we are
overfitting and I believe we can do better. Considering that if we
were to predict the mean of the average_rating every time we would
have an RMSE of .638, our model appears to actually be doing 
poorly.


## Creating a final model

There are many complex rules in our dataset, especially because of
our encoding with language. One could imagine that the presence of
a word is significant in conjunction with otherwords but not
meaningful on its own. While a decision tree might pick up on some of
these patterns, a linear regression model would not.

A problem with decision trees, though, is their tendency to overfit.
By using a random forest we can minimize this effect and create
a model that generalizes more effectively.

For feature engineering, the first change I made was to replace
the CountVectorizer with a TfidfVectorizer. Tfidf vectors will
account for how unique each word is to a review or recipe, which
could make our model much more powerful.

Secondly, I added a binarizer to the n_steps column. I thought
that any recipe with under 8 steps is a rather short one while
a recipe with over 8 steps can feel much longer. A binarizer captures
this effect and could play a small role in reducing overfitting
(this effect is likely small since average_rating and n_steps don't
appear to be very correlated).

I also realized that some of our overfitting might have been caused
by the model learning to recognize unique words in each recipe
and fitting accordingly. The steps could tended to be very unique
to each recipe, so I decided to use a function transformer to 
replace this column with its length. Perhaps recipes with
longer steps turn out better when people make them because of their
detailed descriptions.

Finally, I standardized all the numerical columns although this
likely won't be so impactful in a model based off of decision trees.

I had to be very selective with the hyperparameters I wanted to explore.
Because of how large the vectorized representations of categorical
columns made my dataframe, training even a single random forest model
took tens of minutes on my laptop. To not spend too much time on the
training process I selected the two features I thought would be
both most impactful to learning and that would reduce overfitting
the most. These columns were max_depth and n_estimators.

max_depth is impactful because there is a lot of information
and complexity added through every level of a tree. Keeping max_depth
small would greatly reduce overfitting, but having it too small
would make our model inaccurate. 

n_estimators is the number of trees we train that each vote on a
response. More trees can make your model much more accurate without
overfitting the data.

The result of this training was a training RMSE of .5125 and
a testing RMSE of .55445. This means we aren't overfitting anymore,
and our model is now outperforming the mean! This time, however, since
I had to limit the size of the hyperparameters, we are likely
underfitting. This seems especially true considering that the best
parameters were a max_depth of 15 and a n_estimators of 15.
Since both of these are maxed out, it's likely that larger parameters
would've performed even better.

After training the random forest model with max_depth=15 and
n_estimators=15, our training RMSE is around .5 and our testing RMSE
is around .56.

## Fairness analysis

Finally, let's examine how this model performs on subgroups of recipes.
One interesting subgroup to examine is whether the model performs
as well on vegetarian recipes as non-vegetarian recipes. To find the
answer to this question, we'll conduct a permutation test.

The null hypothesis for our test will be that the model performs
worse on vegetarian recipes than it does for non-vegetarian recipes.
To test this hypothesis, our test statistic will be the 
difference in RMSE values for vegetarian and non-vegetarian recipes.
We'll choose a significance level of .01 since our dataframe is large.

After conducting the test, we obtain a p-value of 1, meaning we fail
to reject the null hypothesis. (Unfortunate due to runs times, I had to
limit my number of repetitions to 10). Still, this p-value suggests
that the model likely does just as well on vegetarian food as it does
on non-vegetarian food.
