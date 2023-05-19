# Recipe Ratings: An Investigation

a project by Nic Colebank (ncoleban@ucsd.ed)

---

## Introduction

This project is investigating a recipes dataset from food.com. The data originates from two CSV files, one containing recipe data, and the other containing user interactions with the recipe (i.e. ratings and reviews). The dataset provides insight into why some recipes may get higher ratings than others, the most common kinds of recipes on food.com, and many other columns of interest. After merging the two datasets together and creating an avg_rating column, the dataset has 234,429 rows and 18 columns. 

I was specifically interested in how the number of calories related to the average rating of a recipe. It is often thought that the more calories, the better the food tastes (although definitely not always the case). As a result of this, I decided my research question would be: Do recipes with an average rating of 3+ tend to have more calories than recipes that do not?

The main columns of interest for this question are 'nutrition' and 'avg_rating' (which is derived from 'rating'). The nutrition column contains nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]. The rating column is the rating given for a recipe by a user, and avg_rating is the average of all ratings given for a given recipe.

---

## Cleaning and EDA

### Data Cleaning
To clean the dataset, I started by creating a separate, numeric column for calories so that I could perform operations on it. I extracted this value using string operations on the nutrition column. 

I then filtered out recipes that had more than 5000 calories, since these were mainly outliers made up of massive dishes like full cakes that would not generally be considered a meal. For the purposes of my question, I thought these kinds of recipes were not relevant due to it being difficult to determine what a single serving would be. Ideally, my question would be focusing on the relationship between average rating and the number of calories per serving, but since there is no column corresponding to number of servings, the next best way was to filter out recipes that clearly were much more than 1 serving. 


### Univariate Analysis

### Bivariate Analysis

### Interesting Aggregates
---

## Assessment of Missingness

### NMAR Analysis

### Missingness Dependency
---

## Hypothesis Testing
