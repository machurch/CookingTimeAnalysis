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



