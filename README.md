# An Analysis of Recipe Nutrition
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

After all steps were completed, the cleaned dataframe appears as follows:

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

First, I performed univariate analysis on single variables in the dataset:
<iframe
  src="assets/calorie_dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
In this visualization, the distribution of calories is clearly right-skewed. In fact, in the raw dataset, the `calories` column had outliers all the way up to 45609 calories. Since this number seems incredibly unrealistic for a normal recipe (the average adult consumes about 2000-3000 calories per day), I decided to exclude all recipes with calorie values that were 5 or more standard deviations above the mean, and the resulting plot is shown above. All following analyses will be performed with this abbreviated dataset that excludes these dramatic outliers.

## Framing a Prediction Problem

## Baseline Model

## Final Model