# An Analysis of Recipe Nutrition
Annie He || anniehe@umich.edu || EECS 398-003: Practical Data Science

## Introduction

In this project, I combined two datasets scraped from [food.com](food.com), a popular online collection of recipes submitted by home cooks and celebrity chefs alike, using a subset of all recipes from the website submitted between January 2008 and December 2018. The first dataset, focusing on the recipe itself, contains comprehensive information about the submitted recipes, such as detailed cooking instructions and nutritional values. The second dataset includes ratings and reviews of these recipes, providing information on how well they were received by the website's users. The datasets were merged using common recipe IDs, allowing me to examine recipe information and user interaction data in parallel.

As a college student, I'm always looking for recipes that are healthy and have large portions: I don't want to spend too much time working through complicated steps or shopping for obscure ingredients if I'm not getting much nutrition out of it. These nutritional and caloric concerns are undoubtedly a big factor for many others as well when it comes to trying new recipes, since maximizing nutrition in a cost-effective way is one of the biggest benefits of cooking at home. Thus, the central question for this project is: **What factors in a recipe are most relevant to its nutritional value?**

To investigate this, I conducted univariate, bivariate, and grouped analyses on specific columns within this dataset. I also created a predictive model for the number of calories in a given recipe, which would be helpful for users deciding on the types of recipes they want to peruse. An overview of the datasets are given below.

Dataset 1, which contains information about the recipes themselves, has **83782 rows** representing 83782 unique recipes. The following columns were used in the analysis:

- `id`: Unique identifier for the recipe
- `minutes`: Number of minutes needed to prepare the recipe
- `tags`: Tags for the recipe from [food.com](food.com), such as the type of food in the recipe or the time required to make it
- `nutrition`: Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)], where PDV stands for “percentage of daily value”
- `n_steps`: Number of steps in the recipe
- `n_ingredients`: Number of ingredients required for the recipe

Dataset 2, which contains user interaction data, has **731927 rows**, representing interactions from 188834 unique users on 185913 different recipes. The following columns were used in the analysis:

- `recipe_id`: Unique identifier for the recipe
- `rating`: Rating given for the recipe

The two datasets were cleaned and merged for further analysis.

## Data Cleaning & Exploratory Data Analysis



## Framing a Prediction Problem

## Baseline Model

## Final Model