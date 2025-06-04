# Dessert or Not? A Recipe for Classification

by Minyoung Kim and Tanisha Kumar

---

## Overview

This data science project, conducted at UCSD, focuses on Holiday recipes and explores the relationship between the feature 'calories' and whether the recipe is a dessert or non-dessert. As we could say, this project was not a piece of cake! 🍰

---

## Introduction

Holidays are times of gathering, when families come together to celebrate, and they cannot be celebrated without food. Rich fesitve dishes, decadent desserts, and family recipes passed down through generations. But in an era where people are increasingly health-conscious, how much do our holiday favorites really differ in nutritional content, particularly when it comes to calories?

**In this project, we investigated the difference in calorie content between dessert and non-dessert holiday foods 🎂** To do so, we analyzed two datasets consisting of recipes and ratings posted since 2008 on [food.com](https://www.food.com/). The original purpose of the dataseets is for the recommender sustem research paper, [Generating Personalized Recipes from Historical User Preferences](https://cseweb.ucsd.edu/~jmcauley/pdfs/emnlp19c.pdf) by Majumder et al.

The first dataset, `recipe`, contains 83782 rows, indicating 83782 unique recipes, with 10 columns recording the following information:

| Column             | Description                                                                                                                                                                                       |
| :----------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `'name'`           | Recipe name                                                                                                                                                                                       |
| `'id'`             | Recipe ID                                                                                                                                                                                         |
| `'minutes'`        | Minutes to prepare recipe                                                                                                                                                                         |
| `'contributor_id'` | User ID who submitted this recipe                                                                                                                                                                 |
| `'submitted'`      | Date recipe was submitted                                                                                                                                                                         |
| `'tags'`           | Food.com tags for recipe                                                                                                                                                                          |
| `'nutrition'`      | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| `'n_steps'`        | Number of steps in recipe                                                                                                                                                                         |
| `'steps'`          | Text for recipe steps, in order                                                                                                                                                                   |
| `'description'`    | User-provided description                                                                                                                                                                         |
| `'ingredients'`    | Text for recipe ingredients                                                                                                                                                                       |
| `'n_ingredients'`  | Number of ingredients in recipe                                                                                                                                                                   |

The second dataset, `interactions`, contains 731927 rows and each row contains a review from the user on a specific recipe. The columns it includes are:

| Column        | Description         |
| :------------ | :------------------ |
| `'user_id'`   | User ID             |
| `'recipe_id'` | Recipe ID           |
| `'date'`      | Date of interaction |
| `'rating'`    | Rating given        |
| `'review'`    | Review text         |

**Given the datasets, we are investigating the difference in calorie content between dessert and non-dessert holiday foods.**

---

## Data Cleaning and Exploratory Data Analysis

To make our analysis of the dataset more efficient and convenient, we conducted the following data cleaning steps.

1. Left merge the recipes and interactions datasets on id and recipe_id.

   - This step helps match the unique recipes with their rating and review.

1. Fill all ratings of 0 with np.nan.

   - Rating is generally on a scale from 1 to 5, 1 meaning the lowest rating while 5 means the highest rating. With that being said, a rating of 0 indicates missing values in rating. Thus, to avoid bias in the ratings, we filled the value 0 with np.nan.

1. Add column `'avg_rating'` containing average rating per recipe.

   - Since a recipe can have numerous ratings from different users, we take an average of all the ratings to get a more comprehensive understanding of the rating of a given recipe.

1. Create a copy of the merge dataframe and only keep the following columns: `'name', 'id', 'tags', 'nutrition', 'avg_rating'`

   - This step removes features of the data that we do not need for the timebeing. We may bring them back later for our final model.

1. Retrieve rows of data that only include the tag `'holiday-event'` in the `tags` feature.

   - This step filters out and creates a dataframe that only includes recipes from [food.com](https://www.food.com/) thtat include the tag `'holiday-event'`

1. Add `'calories'` to the dataframe

    - `'calories'` is a float column with the calorie amount for each recipe. This step separates the calories from the list of nutrition information inside `'nutrition'` by splitting the list with `', '` and get the index 0 to get the `calories (#)`. This provides us an easier way to access the `'calories'` feature rather than finding it everytime inside the `'nutrition'` feature. 

1. Add `'dessert'` to the dataframe

   - `'dessert'` is a boolean column checking if the tags of recipes contain 'dessert' since desserts typically have a higher amount of sugar, which could potentially mean more calories. This step separates the recipes into two groups, ones that are dessert and ones that are not. This is the column that we will be predicting later in the model.

1. Remove outliers in the data for `'calories'` feature

   - This step prevents disorting our analysis and leading to misleading conclusions. This step is also important for when we use a t-test in bivariate analysis, where a single extreme value can dramatically shift results.

#### Result
Here are all the columns of the cleaned holiday_df_clean.


| Column                  | Description    |
| :---------------------- | :------------- |
| `'name'`                | object         |
| `'id'`                  | int64          |
| `'tags'`                | object         |
| `'nutrition'`           | object         |
| `'avg_rating'`          | object         |
| `'calories'`            | float64        |
| `'dessert'`             | bool           |


Our cleaned dataframe ended up with 8684 rows and 5 columns. Here are the first  rows of ~ unique recipes of our own cleaned dataframe for illustration.

### Univariate Analysis

For this analysis, we examined the distribution of the proportion of desserts by calorie group. For this analysis, we categorized every recipe that had the median or high number of calories into the `'High'` group and the rest, the `'Low'` group. As the plot shows, the percentage of desserts is higher for the low calorie group than the high calorie group, although the difference is not that significant.

### Bivariate Analysis

For this analysis, we compared the calorie distribvution of holiday desserts and non-desserts. Although both box plots look very similar from far away, when we hover over the box plot the values of each quartile, every quartile was higher for non-desserts except for the minimum value. Also looking at the spread of data points next to each box plot, there definitely seems to be more data points that are outliiers for non-desserts than desserts.

### Interesting Aggregates

---

## Assessment of Missingness


---

## Hypothesis Testing


---

## Framing a Prediction Problem


---

## Baseline Model


---

## Final Model


---

## Fairness and Analysis


---
