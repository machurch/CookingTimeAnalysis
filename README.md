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
effect the cooking time the most.** To do so, I am analyzing two datasets
that were posted on [food.com](https://www.food.com/) in 2008. The columns that are 

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
    - I did this so that I would be able to work on a dataframe that contained the data from `interactions` for each recipe that occured in both dataframes. 
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

I named this new datafram `'combo'` since it was a combination of the two. `'combo'` has 234,429 rows and 25 columns. Below is the head of `'combo'` with only the `'name'`, `'id'`, `'minutes'`, `'contributor_id'`, `'protein'`, '`saturated fat'`, '`carbohydrates'` and `'month'` shown for simplicity:

| name                                 |     id |   minutes |   contributor_id |   protein |   saturated fat |   carbohydrates |   month |
|:-------------------------------------|-------:|----------:|-----------------:|----------:|----------------:|----------------:|--------:|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 |         3 |              19 |               6 |      10 |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 |        13 |              51 |              26 |       4 |
| 412 broccoli casserole               | 306168 |        40 |            50969 |        22 |              36 |               3 |       5 |
| 412 broccoli casserole               | 306168 |        40 |            50969 |        22 |              36 |               3 |       5 |
| 412 broccoli casserole               | 306168 |        40 |            50969 |        22 |              36 |               3 |       5 |

## Univariate Analysis

One column that I figured would have a great effect on the cooking time of a recipe was the number of ingredients. To analyze this column, I made a histogram of the `'n_ingredients'` column, which contains the number of ingredients needed for all recipes in `'combo'`, here is the histogram:

<iframe
  src="assets/ingredient_count_hist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

What I learned from looking at this is that the distribution of the number of ingredients is centered around 8 and 9. The distribution is unimodal, and has a slight right skew. 

-------------------------------------------------------------------

Since my main goal is to be able to predict the time it takes to cook a recipe, I decided to create a box plot to analyze the `'minutes'` column.

<iframe
  src="assets/minutes_box.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Looking at this plot, I can see that there are some extreme outliers. The third quartile is 60 minutes, while the max is over 1 million. This is definetly something that I will have to consider moving forward in my analysis.

## Bivariate Analysis

Since I am interested in the relationship between the number of ingredients a recipe needs and the amount of time it takes to cook, I decided to create a scatter plot with `'n_ingredients'` on the x-axis and `'minutes'` on the y-axis. To create this plot I had to use a subset of `'combo'` where I only kept minutes that were in the third quartile or below because the outliers made the scatter plot unreadable when working with the whole dataset.

<iframe
  src="assets/ingredients_mins.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Looking at the scatter plot, it is hard to distinguish any relationship between the two variables. There could be a slight positive correlation, but it is very small. 