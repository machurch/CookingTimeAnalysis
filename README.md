# Analyzing Cooking Times

Author: Marguerite Church

## Overview

This is a project created for the DSC 80 class at UCSD. The goal is to explore the relationship between the cook time of a recipe and other variables such as the month it was cooked, or the amount of sugar.

# Introduction

## General Introduction
As a college student, it is important for me to find recipes that I will
be able to cook quickly to be able to spend more time studying. Many people with busy lifestyles have a hard time finding time to cook, so I wanted to
see if there was a way to predict how long it would take to cook a recipe
and what types of recipes have shorter cooking times to make it easier to
choose recipes that cook quickly. **I want to know what factors of a recipe
affect the cooking time the most.** To do so, I am analyzing two datasets
that were posted on [food.com](https://www.food.com/) in 2008. 

## Data Introduction

The first dataset is called `recipe` and it contains 83,782 unique recipes,
one per row of the data frame. `recipe` has 10 columns described below:


| Column             | Description                       |
| :----------------- |:--------------------------------- |
| `'name'`           | Recipe Name                       |
| `'id'`             | Recipe ID                         |
| `'minutes'`        | Minutes to prepare recipe         |
| `'contributor_id'` | User ID who submitted this recipe |
| `'submitted'`      | Date recipe was submitted         |
| `'tags'`           | Food.com tags for recipe          |
| `'nutrition'`      | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| `'n_steps'` | Number of steps in recipe |
|  `'steps'` | Text for recipe steps, in order |
| `'description'` | User-provided description |

The second data set is called `interactions` and contains 731,927 rows where each row represents a review that a user left on a recipe. Some of these recipes are from the `recipe` dataset, but others aren't. `interactions` consists of the following columns:

| Column | Description |
| :----- | :---------- |
| `'user_id'` | User ID |
| `'recipe_id'` | Recipe ID |
| `'date'` | Date of interaction |
| `'rating'` | Rating given |
| `'review'` | Review text |

# Data Cleaning and Exploratory Data Analysis

## Data Cleaning

To clean the data, I did the following:
1. Left merged `recipes` and `interactions`
    - I did this so that I would be able to work on a dataframe that contained the data from `interactions` for each recipe that occured in `recipes`. 
2. Filled all ratings of 0 with np.nan
    - If you rate a recipe, the lowest you can rate it is one star which means that recipes that received a rating of 0 weren't rated at all. It would be unfair to the recipe to keep the rating at 0 when performing calculations because it would unfairly lower the average.
3. Created a column with the average rating per recipe
    - This allowed me to compare ratings across recipes and not just reviews. I named this column `'avg_rating'`
4. Split the nutrition column into 7 new columns
    - The data in the nutrition column had originally been a string that looked like a list, so it was very hard to analyze. I pulled out each separate stat and put it into its own column as a float data type. The columns I created are `'calories'`, `'total fat'`, `'sugar'`, `'sodium'`, `'protein'`, `'saturated fat'`, and `'carbohydrates'`.
5. Converted the `'date'` and `'submitted'` columns to datetime
    - This allowed me to better analyze the dates because they would now be able to be correctly sorted.
6. Created a `'month'` column
    - Using the data from the `'submitted'` column, I created a new column called `'month'` which only contained the `'month'` that the recipe was submitted. This allowed me to compare cooking times across months.
7. Created a `'under_quarter'` column
    - Using the data from the `'minutes'` column, I created a new column called `'under_quarter'` which is 1 if it takes less than a quarter hour, 45 minutes, to cook the recipe, and 0 if it doesn't. I created this column so that I can use it to build a binary classification model.

I named this new dataframe `'combo'` since it was a combination of the two. `'combo'` has 234,429 rows and 25 columns. Below is the head of `'combo'` with only the `'name'`, `'id'`, `'minutes'`, `'contributor_id'`, `'saturated fat'`, '`carbohydrates'`, `'month'`, and `'under_quarter'` shown for simplicity:

| name                                 |     id |   minutes |   contributor_id |   saturated fat |   carbohydrates |   month | under_quarter   |
|:-------------------------------------|-------:|----------:|-----------------:|----------------:|----------------:|--------:|:------------|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 |              19 |               6 |      10 | 1       |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 |              51 |              26 |       4 | 1       |
| 412 broccoli casserole               | 306168 |        40 |            50969 |              36 |               3 |       5 | 1       |
| 412 broccoli casserole               | 306168 |        40 |            50969 |              36 |               3 |       5 | 1       |
| 412 broccoli casserole               | 306168 |        40 |            50969 |              36 |               3 |       5 | 1       |

## Univariate Analysis

One column that I figured would have a great effect on the cooking time of a recipe was the number of ingredients. To analyze this column, I made a histogram of the `'n_ingredients'` column, which contains the number of ingredients needed for all recipes in `'combo'`, here is the histogram:
<iframe
  src="assets/ingredient_count_hist.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>
What I learned from looking at this is that the distribution of the number of ingredients is centered around 8 and 9. The distribution is unimodal, and has a slight right skew. 

-------------------------------------------------------------------

Since my main goal is to be able to predict the time it takes to cook a recipe, I decided to create a box plot to analyze the `'minutes'` column.
<iframe
  src="assets/minutes_box.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>
Looking at this plot, I can see that there are some extreme outliers. The third quartile is 60 minutes, while the max is over 1 million. This is definetly something that I will have to consider moving forward in my analysis.

## Bivariate Analysis

Since I am interested in the relationship between the number of ingredients a recipe needs and the amount of time it takes to cook, I decided to create a scatter plot with `'n_ingredients'` on the x-axis and `'minutes'` on the y-axis. To create this plot I had to use a subset of `'combo'` where I only kept minutes that were in the third quartile or below because the outliers made the scatter plot unreadable when working with the whole dataset.
<iframe
  src="assets/ingredients_mins.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>
Looking at the scatter plot, it is hard to distinguish any relationship between the two variables. There could be a slight positive correlation, but it is very small. 

--------------------------------------------------------------------------------------------------
I also wanted to examine the relationship between the day a recipe was submitted and the amount of minutes it takes to cook. Here is a plot of the year 2008.
<iframe
  src="assets/calendar.html"
  width="800"
  height="200"
  frameborder="0"
></iframe>

## Interesting Aggregates

I wanted to see how much the month a recipe was submitted played a role in being able to predict cooking time, so I decided to analyze this by creating a pivot table. First, I had to create a new column in `'combo'` named `'ingredient_bins'`. `'ingredient_bins'` places each value into one of 5 equally sized bins. I set `'ingredient_bins'` as the index, `'month'` as the columns, and `'minutes'` as the value to be analyzed in terms of the mean amount of minutes for the given ingredient bin and month.

| ingredient_bins   |        1 |        2 |        3 |        4 |        9 |       10 |      11 |        12 |
|:------------------|---------:|---------:|---------:|---------:|---------:|---------:|--------:|----------:|
| (0.964, 8.2]      |  64.2838 | 268.483  |  95.5878 |  93.1892 | 122.742  |  60.8491 | 70.7752 |  425.288  |
| (8.2, 15.4]       |  86.3269 |  81.9981 |  69.6562 | 114.294  |  79.2826 |  87.2387 | 79.1323 |   90.0369 |
| (15.4, 22.6]      |  96.2355 | 162.476  | 102.952  | 114.946  |  91.42   | 135.544  | 99.0093 |  912.574  |
| (22.6, 29.8]      | 118.787  | 129.567  |  87.7541 | 102.692  |  99.4094 | 125.854  | 89.75   | 1050.3    |
| (29.8, 37.0]      | nan      | 171.875  |  50      |  97.3333 |  60.625  | 180      | 84      |  nan      |

The pivot table below shows months 1, 2, 3, 4, 9, 10, 11, and 12. It revealed that month 12, December, had very high mean minute counts compared to the other months shown. nan values mean that there were no recipes submitted that month with the number of recipes in the given bin.

# Assessment of Missingness

## NMAR Analysis

I believe that the `'review'` column of `'combo'` is likely NMAR because it is up to the person who made the recipe whether or not to leave a review. People who don't feel strongly about a recipe probably won't feel the need to go out of their way and review it. One characteristic of the user that could have a relationship with the `'review'` column is their age. Users who are retirement age may have more time on their hands to leave reviews, therefore making the `'review'` column MAR. Since I don't have access to ages, however, I will assume that the `'review'` column is NMAR.

## Missingness Dependency

I want to see if missingness in the `'ratings'` column is dependent on these two columns: `'n_steps'`, and `'minutes'`.

### Number of Ingredients

To see if the mean number of steps it takes to prepare a recipe is significantly different when the rating is missing I came up with the following hypotheses:
**Null Hypothesis:** The mean number of steps in a recipe is the same when the rating is missing vs. not missing.
**Alternate Hypothesis:** The mean number of steps in a recipe is different when the rating is missing vs. not missing. 
Here is a histogram of the generated mean number of steps when the rating is missing compared to the true mean number of steps when the rating is missing:
<iframe
  src="assets/steps_ratings.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>
As you can see from the plot, the observed mean is greater than all of the generated values leading to a p-value of 0.0 Since the p-value is 0, I reject the null hypothesis and conclude that whether a recipe is rated or not likely depends on the number of steps it takes.

### Number of Minutes

To see if the mean number of minutes it takes to prepare a recipe is significantly different when the rating is missing I came up with the following hypotheses:
**Null Hypothesis:** The mean number of minutes a recipe takes is the same when the rating is missing vs. not missing.
**Alternate Hypothesis:** The mean number of minutes a recipe takes is different when the rating is missing vs. not missing.
Here is a histogram of the generated mean number of minutes when rating is missing compared to the true mean number of minutes when the rating is missing:
<iframe
  src="assets/mins_ratings.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>
As you can see from the plot, the observed mean is above the average generated mean, but not significantly. The p-value that I generated is 0.109, since 0.136 is greater than 0.05, I fail to reject the null hypothesis and can't conclude that whether a recipe is rated or not depends on the number of minutes it takes.

# Hypothesis Testing
I will be testing whether recipes submitted in December have a longer average cooking time compared to all other recipes. I chose December because a lot of baking happens around the holidays and recipes that require baking take a decent amount of time.
**Null Hypothesis:** Recipes submitted in December have a similar average cooking time compared to all other recipes.
**Alternate Hypothesis:** Recipes submitted in December have a longer average cooking time compared to all other recipes.
**Test Statistic:** Difference in means, calculated using mean minutes in December - mean minutes in all other months.
**Significance Level:** 0.05
To calculate the p-value, I used a permutation test where I generated 10000 differences in means. The resulting histogram is shown below:
<iframe
  src="assets/dec_hist.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>
From the plot, it is evident that the p-value is 0.0, and that is also what I calculated. Since 0.0 is less than my designated significance level of 0.05, I can reject the null hypothesis and conclude that it is likely that recipes submitted in the month of December take longer to cook compared to all other recipes. 

# Framing a Prediction Problem

My model will try to predict whether or not a recipe will take under 45 minutes to prepare. This is a binary classification model since the recipe is either under an 45 minutes or over. 

The variable I am predicting is the column `'under_quarter'`, which is 1 if the recipe takes 45 minutes or less to prepare and 0 if it takes over. I chose this variable because people who are in a time crunch may not have more than 45 minutes to spend cooking.

The metric I am choosing to evaluate my model is it's precision because I don't want people to choose a recipe thinking that they have time to cook it and then end up being wrong. 

The information that I know now is all of the data in the `'combo'` dataframe.

# Baseline Model

My baseline model is a binary classifier that uses logarithmic regression and takes in the number of ingredients a recipe needs and the month it was submitted to predict whether or not it will take under 45 minutes to cook.

The features that I used are `'n_ingredients'` which is quantitative, and `'month'` which is nominal. I chose to use `'n_ingredients'` because it would make sense that recipes that require more ingredients would take a longer time to prepare. I chose `'month'` because when performing my hypothesis test I learned that recipes submitted in December take a significantly longer time to prepare.

To prepare `'month'` for my model, I transformed it using One Hot encoding. I left `'n_ingredients'` as-is because it is quantitative.

The precision of this model is 0.67 which is okay considering it only took in two features. This model is okay because in the training data, if a recipe was predicted to take under 45 minutes, there was a 0.67 probability that it actually took less than 45 minutes.

# Final Model

The features that I used from `combo` were, `'n_ingredients'`, `'month'`, and two that I created for the final model were `'under_30'` and `'over_60'`. I used a Logarithmic Regression classifier and my precision was 0.77. 

I added `'under_30'` by finding recipes with the tag "30-minutes-or-less". `'under_30'` is nominal because the value is 1 if the recipe contains the tag and 0 if it doesn't. I believed that this would be a good feature to add because if a recipe takes under 30 minutes then it definetly takes under 45. The other feature that I added was `'over_60'` and I did this also by looking at the tags and finding the ones that contained "60-minutes-or-less", using these values, I reversed them so that a recipe would have a value of 1 in `'over_60'` if it took 60 minutes or more to prepare. I thought that this would be a useful feature because anything over 60 minutes is also over 45. 

To transform `'under_30'` and `'over_60'`, I used One Hot Encoding because they were categorical variables.

This model is better than the baseline one because it has a higher precision, 0.77 vs. 0.67, which means that it is less likely that someone would choose a recipe that they thought would take under 45 minutes and find out that it takes more than 45 minutes. This means that people would be able to better allocate their time.

# Fairness Analysis

The groups that I chose to analyze for fairness were December recipes vs. other months. 

I decided on these groups because I learned from my hypothesis test that recipes submitted in December were longer than average than other recipes. I wondered if this would affect the model's precision when predicting if recipes submitted in December would take less than 45 minutes to cook.

My evaluation metric will still be precision because that's what I used when creating the model.

**Null Hypothesis:** There is no difference between the recall of December months and other months
**Alternative Huypothesis:** There is a difference between the recall of December months and other months
**Test Statistic:** The difference in precision, calculated by precision of December - precision of other months

To calculate the p-value, I used a permutation test with 10000 trials, I set my significance level to be 0.05 and got a p-value of 0.0. A plot of the distribution is below:
<iframe
  src="assets/fair_hist.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>
I noticed that the difference in precision was greater compared to the differences under the null which are mainly negative. This means that December actually had a higher precision than other months which was not what I had thought initially. Looking back, it makes sense because if most recipes in December take a while to make, then it would be easy to predict that they are over 45 minutes. 






