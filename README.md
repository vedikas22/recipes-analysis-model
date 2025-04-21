# Cooking Up Predictions: A Machine Learning Model for Recipe Ratings

by Vedikas Sridharan (vedikas@umich.edu)

---

## Introduction

My project uses data from two sources :

RAW_recipes.csv – Contains information on 83,782 recipes, including ingredients, nutrition facts, cooking steps, and preparation time

RAW_interactions.csv – Includes 731,927 user interactions with those recipes, such as ratings, reviews, and timestamps

What makes a recipe well-rated? We want to understand how different characteristics of a recipe like the number of ingredients, total preparation time, and nutritional content—correlate with high user ratings.

This matters because:

It helps users find recipes they'll likely enjoy.
It can guide food bloggers or platform designers to improve recipe recommendations.
It reveals trends in what people consider “good” or “worth making” when it comes to food.
From RAW_recipes.csv:

id: Unique identifier for each recipe.

name: Title of the recipe.

minutes: Total preparation time.

n_steps: Number of steps in the instructions.

n_ingredients: Number of ingredients used.

calories, fat, sugar, protein, sodium: Nutritional info per serving.

From RAW_interactions.csv:

recipe_id: Matches with id in RAW_recipes.csv.

rating: User rating from 1 to 5.

---

## Data Cleaning and Exploratory Data Analysis

## Cleaning and EDA

<iframe src="assets/calories_distribution.html" width=800 height=600 frameBorder=0></iframe>