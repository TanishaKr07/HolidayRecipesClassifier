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

For this section, we investigated the relationship between recipe type (homemade or not), calorie group, and two key variables: average calories and average rating.
We grouped the dataset by the homemade status (True or False) and cal_group (Low, Medium, High, Very High), followed by creating a summary table that displays the mean calorie count and mean user rating for each subgroup.

| homemade | cal_group | calories | avg_rating |
|----------|-----------|----------|------------|
| False | Low | 84.729656 | 4.671719 |
| False | Medium | 203.754273 | 4.66179 |
| False | High | 351.186114 | 4.662766 |
| False | Very High | 650.514378 | 4.646736 |
| True | Low | 62.638462 | 4.734283 |
| True | Medium | 214.808333 | 4.699973 |
| True | High | 356.716667 | 4.713766 |
| True | Very High | 618.090909 | 4.680204 |

The resulting table shows some interesting patterns:

1. As expected, calories increase steadily from the low to very high group for both homemade and non-homemade recipes.

2. Homemade recipes consistently have slightly lower average calories across each calorie group, suggesting simpler or less dense ingredient profiles.

3. Homemade recipes also tend to have slightly higher average ratings than non-homemade recipes across all calorie groups, which may reflect personal preference or bias toward familiar/home-cooked meals.

This grouped summary allowed us to examine how nutritional content (calories) and user preferences (average ratings) vary across different calorie groups and homemade status, providing insight into potential patterns in recipe composition and user favorability.

## Assessment of Missingness
The "review" column in our holiday recipes dataframe has a significant amount of missing values, so we decided to assess
its missingness in relation to the other columns in the 
dataframe.

# NMAR Analysis
We believe the missingness in the 'review' column is NMAR. This is because individuals who feel indifferent about a recipe are less likely to leave a review—they may feel there’s nothing noteworthy to share. In contrast, users with stronger emotional responses, whether positive or negative, are more motivated to take the time to leave a review. For instance, someone who thoroughly enjoyed a recipe is more likely to go through the effort of writing a positive review.

# Missingness Dependency
We examined the missingness of the 'review' column in our holiday
recipes dataframe by testing the dependency of its missingness. We
are investigating whether the missingness in the 'review' column
depends on the 'minutes' column, which represents the preparation
time of the recipe in minutes, and the "n_steps" column, which
represents the number of steps in the recipe.

**Reviews & Minutes**

*Null Hypothesis:* The missingness of reviews does not depend on
the minutes required to prepare the recipe.

*Alternative Hypothesis:* The missingness of reviews does depend
on the minutes required to prepare the recipe.

*Test Statistic:* The absolute difference in mean in the minutes
of the distribution of the group with missing reviews and the
distribution of the group without missing reviews.

*Significance Level:* 0.05

**PLOTLY PLOT REQUIRED!!**

We ran a permutation test by shuffling the missingness of reviews
for 1000 times to collect 1000 simulating mean differences in the
two distributions as described in the test statistic.

The observed difference in minutes for the recipes with missing
reviews v/s the recipes without missing reviews was 94.419. The
resultant p-value of 0.034 was < 0.05 (our significance level).
Therefore, we reject the null hypothesis, and conclude that the
missingness of reviews does depend on the minutes required to
prepare the recipe.

One plausible explanation is that people may prefer quicker recipes, especially during the busy holiday season, and are more likely to leave reviews for recipes that are convenient and time-efficient.

**Reviews & Number of Steps**

*Null Hypothesis:* The missingness of reviews does not depend on
the number of steps in the recipe.

*Alternative Hypothesis:* The missingness of reviews does depend
on the number of steps in the recipe.

*Test Statistic:* The absolute difference in mean in the number
of steps of the distribution of the group with missing reviews and the distribution of the group without missing reviews.

*Significance Level:* 0.05

We ran another permutation test by shuffling the missingness of review for 1000 times to collect 1000 simulating mean differences in the two distributions as described in the test statistic.

The observed difference in the number of steps for the recipes 
with missing reviews v/s the recipes without missing reviews was
-0.389. The resultant p-value of 0.413 was > 0.05 (our significance level). Therefore, we fail to reject the null hypothesis, and conclude that the missingness of reviews does not depend on the number of steps in the recipe.


---

## Hypothesis Testing

As mentioned in the introduction, we are curious about whether there is a statistically significant difference in the calorie content of dessert holiday recipes and non-dessert holiday recipes.

By "dessert" holiday recipes, we are talking about the holiday recipes that explicitely had the keyword "dessert" as one of the tags in their "tags" column. For the purposes of our study, any holiday recipe that did not have the "dessert" tag was presumed to be "non-dessert", and classified as such.

To investigate the question, we ran a permutation test with the following hypotheses, test statistic, and significance level.

*Null Hypothesis:* Among holiday recipes, there is no significant difference in the calorie content between
desserts and non-desserts.

*Alternative Hypothesis:* Among holiday recipes, desserts and non-desserts have significantly different calorie content.

*Test Statisic:* The absolute difference in mean calories between the dessert and non-dessert holiday recipes.

*Significance Level:* 0.05

We chose to run a permutation test because we do not have access to the underlying population distribution, and we wanted to assess whether the two observed distributions—calorie content in desserts versus non-desserts—could plausibly come from the same population.

We anticipated that there would be a significant difference in calorie content between holiday dessert and non-dessert recipes, but the direction of this difference was not known in advance. On one hand, desserts are often high in sugar, which could contribute to higher calorie values. On the other hand, non-dessert holiday recipes—depending on ingredients like fats or starches—might contain more overall calories than desserts. Given this uncertainty, a two-sided permutation test was appropriate to determine whether the observed difference in means was statistically significant.

For our test statistic, we chose the absolute difference in mean calorie content between dessert and non-dessert holiday recipes, rather than the raw difference. This is because our hypothesis is non-directional and we are not assuming which group has higher calories, only that a difference exists. Using the absolute difference allows us to properly evaluate this two-sided hypothesis.

To run the test, we first split the data into two groups: desserts, which include recipes tagged as "dessert," and non-desserts, which include all other recipes. The observed difference in mean calorie content (dessert − non-dessert) was −19.59. 
Note, while the observed difference is negative — indicating that, on average, dessert recipes have slightly fewer calories than non-desserts — this direction does not affect the conclusion of our permutation test, since we use the absolute value of the difference as our test statistic. This ensures our test remains two-sided, detecting any significant difference in mean calories regardless of which group is higher.

Then we shuffled the 'dessert' and 'non-dessert' labels for 1000 times to collect 1000 simulating absolute mean differences in the two distributions as described in the test statistic. We got a p-value of 0.0.

**Conclusion of Permutation Test**
Since the p-value that we found (0.0) is less than the significance level of 0.05, we reject the null hypothesis. There is indeed a significant difference in the calorie content of dessert and non-dessert holiday recipes, with the observed statistic suggesting that non-desserts have a higher calorie content, on average. 
One plausible explanation for this finding could be that non-dessert holiday dishes, such as main courses or rich sides—often include calorie-dense ingredients like oils, meats, and cheeses, which can outweigh the sugar content typically found in desserts.


---

## Framing a Prediction Problem

We plan to predict the status of a holiday recipe as either "dessert" or "non-dessert" based on its overall calorie content and other relevant features. This constitutes a binary classification problem, as the model will be trained to predict one of two possible outcomes - dessert or non-dessert.

In our earlier analysis, we identified a significant association between a recipe’s dessert status and its calorie content, suggesting that calorie-related features may be predictive of whether a recipe is a dessert or not. Thus, we aim to leverage this relationship to build a binary classifier.

To evaluate our model, we use the F1 score rather than accuracy, due to the imbalance in our dataset, with there being significantly more non-dessert recipes than desserts. Relying solely on accuracy could result in misleadingly high performance if the model simply predicts the majority class. The F1 score, which balances precision and recall, is a more informative metric for imbalanced classification problems.

The features available to us at prediction time include 'calories', 'avg_rating', 'n_steps', 'cal_group', and 'homemade'. All of these were either present in the original interactions or raw_recipes datasets or were derived from existing columns. Since these features describe the recipe itself and do not rely on user-generated data like reviews, they are practical for predicting dessert status even for new or unrated recipes.

## Baseline Model

For our baseline model, we are  utilizing a logistic regression classifier to predictt whehter a recipe is a dessert based on the calorie. The two features used were:

1. `'calories'` 

   - A quantitative numerical features representing the total calorie count for each recipe

1. `'cal_group'` 

   - An ordinal featrure thatt bins calories values into "Low", "Medium", "High", and "Very High" categories.

We one-hot encoded `'cal_group'` using an `OrdinalEncoder` to preserve the calorie ranges. The dessert column served as the binary target label.

After training the model, we observed thatt the model was heavily biased towards non-desserts. The F1 score for predicting non-desserts was 0.81, while the F1 score for predicting desserts was only 0.02. This is because the model predicted almost every recipe as a non-dessert, which can be proved by 99% showing up as False while only 1% for True. The reason for this could be that there are more recipes that are non-desserts than desserts. The overal accuracy was 0.68, but the macro-average F1 score was 0.41, which indicates poor performance on identifying desserts. This shows the limitation of using logistic regression with only calorie-based features as a baseline model, so for the final model we decided to implement a more comple model with additional features to improve performance. 
---

## Final Model

For the final model, we used a random forest classifier using the following features: `'avg_rating', 'n_steps', 'calories', 'cal_group', 'homemade'`

`'avg_rating'`
This column represents the average rating a recipe has received. Based on our exploratory data analysis, we observed that there were a lot of 4 & 5 ratings. Because there could be a correlation to the average ratings with the amount of calories or sugar (which makes the recipe more likely to be a dessert), we included this feature. We standardized it using `StandardScaler` to normalize the scale and prevent it from overpowering other features.

`'n_steps'`
This column indicates the number of steps involved in making the recipe. _______________ We transformed this features using `QuantileTransformer`, which maps the values to a uniform distribution, which minimized thhe impact of etreme values while preserving the order and variation.

`'calories'`
The column provides the total calories for each recipe. From our bivariate comparisons, we noticed that desserts generally have lower calories content. We included this feature because it is our main determinant in the prediction model, like we have used it in our baseline model. We used `StandardScaler` to bring the values to a comparable scale across recipes.. SO IM NOT SURE IF WE NEED THIS BECAUSE WE ALREADY MOVED THE OUTLIERS...????

`'cal_group'`
This is an ordinal feature created by binning the `'calories'` column into four quantiles labeled as "Low", "Medium", "High", and "Very High". We one-hot encoded this feature similar to what we did in our baseline model.

`'homemade'`
This boolean feature indicates whether the recipe is tagged as `'homemade'` in the `'tags'` column. We assumed that homemade recipes may have distinctive characteristis (example: more steps, fewer processed ingredients) that are mmore common in desserts. Also, people visit this site to cook homemade meals. So although this feature may not be very useful, we wanted to add another feature that the model could test with. We encoded this feature using `OneHotEncoder`.

We used `RandomForestClassifier` as our modeling algorithm and used `GridSearchCV` to tune two key hyuperparameters `max_depth` and `n_estimators`. These hyperparameters control the depth of each tree and the number of trees in the forest which help reduced overfitting and manage variance. The best combination for those parameters were 10 `max_depth` and 100 for `n_estimators`.

The F1 Score for the final model was 0.58 (macro-average), which is a substantial improvement from tthe baseline model's F1 score of 0.41. We saw a significant improvement in the F1 score for predicting desserts (True), which increased from 0.02 to 0.46, and the recal rose from 0.01 to 0.52. The model is now significantly better at identifying desserts while aintaing the performance on non-desserts. These resulut validate the impact of our feature engineering and hyperparameter tuning.

---

## Fairness and Analysis


---
