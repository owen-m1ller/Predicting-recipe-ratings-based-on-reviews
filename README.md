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

Finally, I applied a standard scaled to the calories column.
In the next step I will standardize all the features so that some
aren't weighted arbitrarily heavily. If a decision tree based model 
ends up out-performing the linear regression model, this 
standardization may become redundant.

Evaluating our model based on RMSE, we find that on the training
set our model was off by .3768 on average and on the test set
our model was off by .735 on average. Predicting within the range of
one star seems somewhat reasonable to me, but the disparity between
our performance in the training and test set indicates we are
overfitting and I believe we can do better.


## Creating a final model

## Fairness analysis
