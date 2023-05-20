# Do Higher Calories Result in Higher Ratings?

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

To make the assessment of missingness a smoother process, I added a rating_missing column, which was true when the row was missing a value in the rating column.

The columns 'steps', 'description', 'tags', 'ingredients', and 'review' are all text columns that weren't relevant to my analysis, so I dropped them.

I created a second DataFrame called unique_recipes that drops all rows with a duplicate recipe_id. This is to ensure that my hypothesis test is not artificially skewed towards recipes with multiple reviews. 

The last step in my data cleaning process was to create a categorical variable from the average_rating column. This would be used to group the data into recipes that have an average rating between 1.0-2.9 and a group with average ratings between 3.0-5.0. 

**Head of cleaned recipes DataFrame**

| name                                 |     id |   minutes |   contributor_id | submitted   | nutrition                                    |   n_steps |   n_ingredients |          user_id |   recipe_id | date       |   rating_x |   avg_rating |   calories | ratings_missing   |
|:-------------------------------------|-------:|----------:|-----------------:|:------------|:---------------------------------------------|----------:|----------------:|-----------------:|------------:|:-----------|-----------:|-------------:|-----------:|:------------------|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]     |        10 |               9 | 386585           |      333281 | 2008-11-19 |          4 |            4 |      138.4 | False             |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11  | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0] |        12 |              11 | 424680           |      453467 | 2012-01-26 |          5 |            5 |      595.1 | False             |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]    |         6 |               9 |  29782           |      306168 | 2008-12-31 |          5 |            5 |      194.8 | False             |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]    |         6 |               9 |      1.19628e+06 |      306168 | 2009-04-13 |          5 |            5 |      194.8 | False             |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]    |         6 |               9 | 768828           |      306168 | 2013-08-02 |          5 |            5 |      194.8 | False             |

**Head of unique_recipes**

| name                                 |     id |   minutes |   contributor_id | submitted   | nutrition                                     |   n_steps |   n_ingredients |          user_id |   recipe_id | date       |   rating_x |   avg_rating |   calories | ratings_missing   | avg_rating_categorized   |
|:-------------------------------------|-------:|----------:|-----------------:|:------------|:----------------------------------------------|----------:|----------------:|-----------------:|------------:|:-----------|-----------:|-------------:|-----------:|:------------------|:-------------------------|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]      |        10 |               9 | 386585           |      333281 | 2008-11-19 |          4 |            4 |      138.4 | False             | 3.0-5.0                  |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11  | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0]  |        12 |              11 | 424680           |      453467 | 2012-01-26 |          5 |            5 |      595.1 | False             | 3.0-5.0                  |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]     |         6 |               9 |  29782           |      306168 | 2008-12-31 |          5 |            5 |      194.8 | False             | 3.0-5.0                  |
| millionaire pound cake               | 286009 |       120 |           461724 | 2008-02-12  | [878.3, 63.0, 326.0, 13.0, 20.0, 123.0, 39.0] |         7 |               7 | 813055           |      286009 | 2008-04-09 |          5 |            5 |      878.3 | False             | 3.0-5.0                  |
| 2000 meatloaf                        | 475785 |        90 |          2202916 | 2012-03-06  | [267.0, 30.0, 12.0, 12.0, 29.0, 48.0, 2.0]    |        17 |              13 |      2.20436e+06 |      475785 | 2012-03-07 |          5 |            5 |      267   | False             | 3.0-5.0                  |
### Univariate Analysis
<iframe src="assets/calories-dist.html" width=1000 height=600 frameBorder=0></iframe>

This plot shows how the calories in recipes are distributed across the dataset. The data is skewed right, with the vast majority of recipes being under 1000 calories.
### Bivariate Analysis
<iframe src="assets/calories-ratings-boxplot.html" width=1000 height=600 frameBorder=0></iframe>

This plot shows the distribution of calories by rating group, visualized as boxplots. The two boxplots are fairly similar, although the center of the 3.0-5.0 rating boxplot is slightly higher.


### Interesting Aggregates

| avg_rating_categorized   |     id |   minutes |   contributor_id |   n_steps |   n_ingredients |     user_id |   recipe_id |   rating_x |   avg_rating |   calories |   ratings_missing |
|:-------------------------|-------:|----------:|-----------------:|----------:|----------------:|------------:|------------:|-----------:|-------------:|-----------:|------------------:|
| 1.0-2.9                  | 389379 |   96.9893 |      2.91427e+07 |   10.5306 |         9.11309 | 3.33548e+08 |      389379 |    1.68134 |      1.64404 |    424.777 |         0.0469417 |
| 3.0-5.0                  | 380570 |  111.442  |      1.28254e+07 |   10.0361 |         9.20381 | 6.73688e+07 |      380570 |    4.70419 |      4.67806 |    407.263 |         0.0164895 | 
This DataFrame is the result of grouping recipes by rating category and taking the mean of the numeric columns. The main columns of interest in this resulting table are 'calories' and 'ratings_missing'. The 'calories' column shows the center of each rating group, with the 3.0-5.0 group having about 18 more average calories. The 'ratings_missing' column shows the percentage of each group missing a rating.

---

## Assessment of Missingness

In the original merged DataFrame, there are three columns with missing values: 'description', 'rating', and 'review'. 

### NMAR Analysis
When looking if any of the columns with missing values are NMAR, none of the columns, except 'review', qualify as so. The value of the 'description' column cannot be determined by looking at other columns, but there is no reason for the missingness to depend on the actual description values. It ultimately depends on the user deciding whether or not to create a description, which is not tracked by other columns in the dataset. 

For the 'rating' column, it would make sense for more neutral ratings to be missing, since people tend to review things they have a strong opinion about. However, there are many reviews without a rating, which goes against this idea. 

The 'review' column most likely NMAR, as more neutral reviews would be more likely to be missing. Recording the satisfaction level of users that tried the recipe would be able to explain the missingness in this scenario.


### Missingness Dependency

After concluding that the 'rating' column was not NMAR, I decided to see what columns its missingness depended on. From running permutation tests that used the absolute difference of means as the test statistic, I found that the missingness does likely depend on 'calories' and does not depend on 'minutes'. 

The permutation test for missingness on minutes had a p-value of 0.116, meaning that 'ratings' likely does not depend on 'minutes'. Below is the empirical distribution of the absolute difference of means for means from 1000 permutations. 

<iframe src="assets/diffs-plot.html" width=1000 height=600 frameBorder=0></iframe>

The permutation test for missingness of 'ratings' on 'calories' had a p-value of 0.0, meaning that 'rating' like does depend on 'calories'. This poses a problem for my research question, as I am looking at the relationship between 'calories' and a column derived from 'rating'. Probabilistic imputation would ideally be the best solution to this, but since 'calories' is not a categorical column, it cannot be done. Instead, rows with a missing value for 'avg_rating' will not be factored into the hypothesis testing, which will put an asterik on the results of the testing.

---

## Hypothesis Testing

My hypotheses are as follows:

Null Hypothesis - The median number of calories of recipes with an average rating of 3+ is the same as ther median number of calories of recipes with an average rating of 1.0-2.9.
Alternative Hypothesis - The median number of calories of recipes with an average rating of 3+ is greater than the median number of calories of all recipes.

Since my alternative hypothesis is looking to see if the median number is greater, I will be using the signed difference in medians between the two rating groups as my test statistic.

After generating 1000 test statistics from doing permutations, the resulting p-value is 0.139. With a 5% significance level, this p-value is not enough to reject the null hypothesis, suggesting that the median calories is generally the same between recipes with an average rating of 1.0-2.9 and recipes with an average rating of 3.0-5.0.
