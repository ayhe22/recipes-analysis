Annie He || anniehe@umich.edu || EECS 398-003: Practical Data Science

## Introduction

In this project, I combined two datasets scraped from [food.com](food.com), a popular online collection of recipes submitted by home cooks and celebrity chefs alike, using a subset of all recipes from the website submitted between January 2008 and December 2018. The first dataset, focusing on the recipe itself, contains comprehensive information about the submitted recipes, such as detailed cooking instructions and nutritional values. The second dataset includes ratings and reviews of these recipes, providing information on how well they were received by the website's users. The datasets were merged using common recipe IDs, allowing me to examine recipe information and user interaction data in parallel.

As a college student, I'm always looking for recipes that are healthy and have large portions: I don't want to spend too much time working through complicated steps or shopping for obscure ingredients if I'm not getting much nutrition out of it. These nutritional and caloric concerns are undoubtedly a big factor for many others as well when it comes to trying new recipes, since maximizing nutrition in a cost-effective way is one of the biggest benefits of cooking at home. Thus, the central question for this project is: **What factors in a recipe are most relevant to its nutritional value?**

To investigate this, I conducted univariate, bivariate, and grouped analyses on specific columns within this dataset. I also created a predictive model for the number of calories in a given recipe, which would be helpful for users deciding on the types of recipes they want to peruse. An overview of the datasets are given below.

Dataset 1, `recipes`, has **83782 rows** representing 83782 unique recipes and their associated characteristics. The following columns were used in the analysis:

- `id`: Unique identifier for the recipe
- `minutes`: Number of minutes needed to prepare the recipe
- `tags`: Tags for the recipe from [food.com](food.com), such as the type of food in the recipe or the time required to make it
- `nutrition`: Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)], where PDV stands for “percentage of daily value”
- `n_steps`: Number of steps in the recipe
- `n_ingredients`: Number of ingredients required for the recipe

Dataset 2, `interactions`, has **731927 rows**, representing online interactions from 188834 unique users on 185913 different recipes. The following columns were used in the analysis:

- `recipe_id`: Unique identifier for the recipe
- `rating`: Rating given for the recipe

The two datasets were cleaned and merged for further analysis.

## Data Cleaning/Exploratory Data Analysis

### Data Cleaning

To aid in my analysis, I performed the following data cleaning steps on the two datasets:
- I first performed a "left merge" on the `recipes` and `interactions` datasets, using the `id` column for `recipes` and the `recipe_id` column for `interactions`. This step allowed me to consolidate the two datasets, dropping all user interactions that did not correspond to recipes in the `recipes` dataset.
- I then dropped all columns that wouldn't be used in my analysis, keeping only the `id`, `minutes`, `tags`, `nutrition`, `n_steps`, `n_ingredients`, and `rating` columns.
- In the `rating` column, I replaced all values of 0 with `np.nan`. Since ratings only range from 1 to 5, a value of 0 is not a valid submission and likely indicates that no ratings were actually submitted. Therefore, replacing any ratings of 0 ensures that they will not be included in any aggregate measures performed on the `ratings` column.
- I calculated the mean rating of each recipe, storing the information in a new column titled `avg_rating`. I also dropped the original `rating` column, since I would only be using the average rating in future analyses.
- My next step was to groupby the `id` column and take the "max" value within all columns. The merge step created one row for each interaction with a recipe in the `recipes` dataset, and since I wouldn't be looking at individual interactions in my analysis, I wanted to compress my dataframe so that each recipe is only represented in one row. The resulting dataframe once again consists 83782 rows, representing the 83782 unique recipes in the dataset.
- Since the `tags` and `nutrition` columns were both represented as one long string, I extracted the relevant information from the strings by splitting on the comma character, storing the result as a list of strings.
- Finally, I extracted the individual nutrition values from the list in the cleaned `nutrition` column, storing each value in its own column, then dropped the `nutrition` column (since all information was already stored elsewhere). These columns are as follows:
    * `num_calories`: Number of calories
    * `total_fat_pdv`: Fat (Percentage of Daily Value, or PDV)
    * `sugar_pdv`: Sugar (PDV)
    * `sodium_pdv`: Sodium (PDV)
    * `protein_pdv`: Protein (PDV)
    * `saturated_fat_pdv`: Saturated Fat (PDV)
    * `carbohydrates_pdv`: Carbohydrates (PDV)

After all steps were completed, the head of the cleaned dataframe appears as follows:

<div class="table-wrapper" markdown="block">

|     id |   minutes | tags                                                                                                                                                                                                                                                                                                                                    |   n_steps |   n_ingredients |   avg_rating |   num_calories |   total_fat_pdv |   sugar_pdv |   sodium_pdv |   protein_pdv |   saturated_fat_pdv |   carbohydrates_pdv |
|-------:|----------:|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------:|----------------:|-------------:|---------------:|----------------:|------------:|-------------:|--------------:|--------------------:|--------------------:|
| 275022 |        50 | ['60-minutes-or-less', 'time-to-make', ... 'elbow-macaroni']                                                                                                     |        11 |               7 |            3 |          386.1 |              34 |           7 |           24 |            41 |                  62 |                   8 |
| 275024 |        55 | ['60-minutes-or-less', 'time-to-make', ... 'dietary']                                                                                                                                                                                                             |         6 |               8 |            3 |          377.1 |              18 |         208 |           13 |            13 |                  30 |                  20 |
| 275026 |        45 | ['60-minutes-or-less', 'time-to-make', ... 'shellfish']                                                                                       |         7 |               9 |            3 |          326.6 |              30 |          12 |           27 |            37 |                  51 |                   5 |
| 275030 |        45 | ['60-minutes-or-less', 'time-to-make', ... 'sweet']                                                                                                                                                                                                   |        11 |               9 |            5 |          577.7 |              53 |         149 |           19 |            14 |                  67 |                  21 |
| 275032 |        25 | ['lactose', '30-minutes-or-less', ... 'sweet'] |         8 |               9 |            5 |          386.9 |               0 |         347 |            0 |             1 |                   0 |                  33 |

</div>

### Univariate Analysis

First, I performed univariate analysis on the number of calories in each recipe:
<iframe
  src="assets/calorie_dist.html"
  width="650"
  height="400"
  frameborder="0"
></iframe>
In this visualization, the distribution of calories is clearly right-skewed. In fact, in the raw dataset, the `calories` column had outliers all the way up to 45609 calories. Since this number seems incredibly unrealistic for a normal recipe (the average adult consumes about 2000-3000 calories per day), I decided to exclude all recipes with calorie values that were 5 or more standard deviations above the mean, and the resulting plot is shown above. All following analyses will be performed with this abbreviated dataset that excludes these dramatic outliers.

I also looked at the distribution of ingredient number in each recipe:
<iframe
  src="assets/ingredients_dist.html"
  width="650"
  height="400"
  frameborder="0"
></iframe>
The distribution of the number of ingredients required is closer to a normal distribution, but still remains skewed to the right. Most of the recipes seem to require 10 or less ingredients, which seem very reasonable for home-cooked recipes.

Finally, while not directly related to the central question of the analysis, I looked at the distribution of average ratings for each recipe:
<iframe
  src="assets/ratings_dist.html"
  width="650"
  height="400"
  frameborder="0"
></iframe>
The distribution of average ratings is significantly right-skewed, with the vast majority of recipes having an average rating of 4 stars or higher (out of 5 possible stars). Overall, it seems that [food.com](food.com) is a great source for new recipes, since the users seem to enjoy what they've made from the site.

### Bivariate Analysis

In addition to the univariate analyses, I also performed analyzed bivariate relationships between different columns in the dataset. First, I looked at the relationship between the number of steps in a recipe and the number of calories:
<iframe
  src="assets/steps_v_cals.html"
  width="650"
  height="400"
  frameborder="0"
></iframe>
There appears to be a slight negative correlation between steps and calories, with recipes that involve more steps often having lower caloric value. From an nutritional perspective, it seems that these complex recipes require a lot of effort but don't result in a lot of calories, so they might not be a great fit for someone looking for a more efficient meal. Overall, however, while there are some slight patterns in the data, there doesn't appear to be an overly strong relationship between steps and calories.

I also analyzed the relationship between recipe time and calories. To do so, I calculated the median time required for a recipe (35 minutes), then split the data based on whether or not a recipe required more than 35 minutes; the median was used because the data for recipe time was incredibly right-skewed, making it a better measure of centrality than the mean. The distributions of these two groups are visualized below:
<iframe
  src="assets/time_v_cals.html"
  width="650"
  height="400"
  frameborder="0"
></iframe>
Both groups show a visible right skew for number of calories, which makes sense given the univariate analysis of calories as a whole. It seems that on average, recipes that require more than 35 minutes have slightly higher average calories, as compared to recipes requiring at most 35 minutes.

### Grouped Analysis

The previous bivariate analysis (involving recipe time vs calories) was intriguing, so I decided to perform a similar grouped analysis on all columns in the dataset. Again, I split the data based on whether or not a recipe required more than 35 minutes, storing this information in a column titled `above_median_time`; this column took on the value "Yes" if a recipe's length was >35 minutes, and "No" if a recipe's length was ≤35 minutes. Next, I grouped by this new `above_median_time` column and used the mean function to aggregate the data:

<div class="table-wrapper" markdown="block">

| **above_median_time**   |   n_steps |   n_ingredients |   avg_rating |   num_calories |   total_fat_pdv |   sugar_pdv |   sodium_pdv |   protein_pdv |   saturated_fat_pdv |   carbohydrates_pdv |
|:--------------------|----------:|----------------:|-------------:|---------------:|----------------:|------------:|-------------:|--------------:|--------------------:|--------------------:|
| **Yes**                 |  12.2443  |        10.4308  |      4.60939 |        461.064 |         34.5834 |     66.6108 |      30.2973 |       38.4853 |             43.8126 |             14.5587 |
| **No**                  |   8.01075 |         8.03359 |      4.64085 |        339.714 |         25.9017 |     52.6939 |      24.5882 |       25.6901 |             30.8715 |             10.6982 |

</div>

Recipes that took more than 35 minutes tended to have more steps and ingredients than recipes that took at most 35 minutes, with both groups having similar average ratings. Interestingly, from a nutritional perspective, recipes that took more than 35 minutes had higher mean values for calories, fat, sugar, sodium, protein, saturated fat, and carbohydrates. 

### Imputation

In this dataset, the only missing values were found in the `avg_rating` column. As discussed in the data cleaning steps, these values make sense for the data: if a recipe was posted to the site but did not receive any ratings from users, then there would be no average rating value. Therefore, I decided not to imputate any values in this column.

## Framing a Prediction Problem

My model will try to predict **the number of calories in a recipe**. In my exploratory data analysis, I was mainly interested in exploring the distribution of calories for different recipes, as well as how calories relates to other variables in the dataset; this information is important in deciding if a recipe is nutritionally dense enough to consider making, especially for someone with limited ingredients or time. Since the number of calories is a continuous numeric variable, this is a regression problem. As such, the metric I'll be using to evaluate the model's effectiveness is the **Mean Squared Error (MSE)** on the test data (after performing a train-test split on the current dataset). MSE will allow me to evaluate how far my model's predictions are from the true values, while also avoiding a model that overfits to the training data.

At the time of prediction, it's likely that I'll know information about the recipe preparation/procedure itself, as well as any tags the recipe creator chooses to include on their website. However, if I don't know the number of calories yet (my response variable), then it's unlikely that I'll know information about any other nutritional values (fat, sodium, etc), so these variables will not be used in my prediction. Finally, because ratings are given on completed recipes that provide nutritional information, it's unlikely that I'll be able to use user ratings to predict the number of calories in the dataset. Therefore, the columns I'll be using in my prediction are `minutes`, `tags`, `n_steps`, and `n_ingredients`.

## Baseline Model

For the baseline model, I used a linear regression model to predict `calories` using the features `minutes`, `n_steps`, and `n_ingredients`. These are three of the four features known to us at the time of prediction; since the `tags` feature contains more complicated, categorical information about the recipes, I left it out in the baseline model to see how the other features alone could inform the model. All three features are quantitative, and I applied the `StandardScaler` Transformer to each feature so that they can be more easily compared with each other.

I performed a train-test split on the sample dataset to evaluate how the model performs on unseen data. In addition, because the `calories` feature has many high outliers (as discussed above), I again limited the data to include only recipes where `calories` were within 5 standard deviations of the mean. After fitting the model to the training data, I analyzed the performance on both testing and training data using MSE; I also used the coefficient of determination, R-squared, to analyze the quality of the linear fit. These values were calculated as follows:

- Training Data: MSE = 139586.498618, R-squared = 0.044421
- Testing Data: MSE = 149812.011900, R-squared = 0.032617

This model is... pretty bad! For the testing data, the R-squared coefficient was about 0.033, meaning that the model only explains about 3.3% of the variance in the data. Ideally, the model would be able to explain as close to 100% of the variance as possible, so it is not performing well at all. The MSE is also quite high on the testing data as well, again indicating that the model is performing poorly: in a well-performing model, MSE should be as close to 0 as possible on the testing data. One (very small) silver lining, however, is that the MSE and R-squared values appear to be similar in the training and testing data, so the model is not overfitting to noise in the training data while exclusively performing poorly on the unseen testing data.

The model's performance is disappointing, but it also isn't super surprising: from the exploratory data analysis earlier, there didn't seem to be clear relationships between calories, ingredients, and minutes for each recipe. Therefore, while the chosen features encompass most of the limited information known at the time of prediction, it makes sense that they are not entirely helpful in predicting the number of calories.

## Final Model

In the final model, I continued using the `minutes`, `n_steps`, and `n_ingredients` to inform my analysis, while also adding the `tags` feature (categorical) to improve the model's performance. The `tags` feature contains many keywords that users of the [food.com](food.com) website can utilize to filter recipes; therefore, it's possible that the tags for a recipe may provide further information to help improve the predictive model. 

Before utilizing any of the information in `tags` for the model, I conducted some additional exploratory data analysis on the column, discovering that there were 549 unique tags used across all recipes. I then looked through the top 30 tags to identify any that seemed like good predictors of `calories` while also being well-represented among all recipes in the dataset. From these, I selected 5 tags to add to my analysis: `dietary`, `low-in-something`, `healthy`, `low-calorie`, and `main-dish`. The first four tags all seem to have some relation to health and calorie count, while the last tag may be indicative of a more substantial meal, so they might provide further information for the predictive model.

Next, I added new columns to my sample dataset for all 5 tags to represent whether each recipe had the tag or not. After this step, the new sample dataframe looks like this,

<div class="table-wrapper" markdown="block">

|   minutes |   n_steps |   n_ingredients |   low_cal |   main_dish |   low_something |   healthy |   dietary |
|----------:|----------:|----------------:|----------:|------------:|----------------:|----------:|----------:|
|        50 |        11 |               7 |         0 |           1 |               0 |         0 |         1 |
|        55 |         6 |               8 |         0 |           0 |               0 |         1 |         1 |
|        45 |         7 |               9 |         1 |           1 |               1 |         0 |         1 |
|        45 |        11 |               9 |         0 |           0 |               0 |         0 |         0 |
|        25 |         8 |               9 |         0 |           0 |               0 |         0 |         1 |

</div>

which I used to create a new train-test split to fit my updated model.

I also wanted to see if there were any possible transformations of my numerical features from the baseline model that would increase this model's performance. Since there didn't seem to be any linear relationships between `minutes`, `n_steps`, `n_ingredients`, and calories, I wondered if a polynomial transformer might be more effective. Therefore, I conducted a hyperparameter search for the best degree for a `PolynomialFeatures` transformer on all three of the above numerical columns with `GridSearchCV`, testing all possible degrees from 1-20 using 5-fold cross-validation. The best polynomial degree for `minutes` and `n_ingredients` turned out to be 1, so no polynomial transformations were needed, but the best degree for `n_steps` was 3. Therefore, in my final model, I used `PolynomialFeatures` on the `n_steps` column with a degree of 3.

Finally, from my exploratory data analysis, I'd seen that the `minutes` column was skewed right, even more so than the other quantitative columns. Therefore, I applied a `QuantileTransformer` transformer to the `minutes` column in my final linear regression model. After these updates, I re-evaluated the final model's performance:

- Training Data: MSE = 134381.197452, R-squared = 0.090075
- Testing Data: MSE = 137722.648585, R-squared = 0.082164

This model is not the greatest either: even after the transformations and additional columns used in the analysis, the R-squared value for the test data was about 0.082, meaning that only 8.2% of the variance in the data is explained by the model. Despite this, the R-squared value is more than double that of the baseline model, and the MSE decreased significantly as well (especially for the training data). The model again performs similarly between the training and test data, again suggesting that it is not overfitting to the training data while sacrificing performance on the testing data. Therefore, while the final model ends up being a poor predictor of the number of calories, it is still a big improvement over the baseline model.