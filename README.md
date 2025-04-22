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

To prepare the data for analysis, I began by merging two separate datasets: one containing user interactions with recipes (such as ratings) and another containing detailed recipe information. This merge was essential because it brought together user behavior and recipe characteristics, allowing for meaningful analysis of how recipe attributes influence ratings.

One of the key variables in the recipe data was a nutrition field, which was originally stored as a stringified list of values. I converted this list into individual numeric columns—such as calories, sugar, fat, and protein to make the data usable in analysis. This step reflected a necessary unpacking of aggregated information that was likely produced automatically during the recipe submission process.

I also converted the date column from string format to a datetime object. This not only standardized the format but also made the data suitable for any potential time-based analyses in the future. In some cases, the conversion revealed invalid or malformed entries, which were handled by coercing them into null values.

Another important step was dealing with unrealistic preparation time values. Specifically, some recipes listed a prep time of zero minutes, which is clearly invalid. I replaced these with missing values to avoid skewing the results, especially important for scatter plots using a log-transformed time axis.

Finally, I checked for missing values in key columns such as rating, preparation time, number of ingredients, number of steps, and nutritional content. This helped me understand the completeness of the dataset and guided decisions about which rows could be included in various parts of the analysis.

Overall, these cleaning steps were closely aligned with how the data was likely generated—through user submissions, automated scraping, and imperfect manual inputs. Cleaning the data this way improved the quality and interpretability of the visualizations and ensured that the trends I observed reflected genuine patterns rather than noise or errors.

Here is the head of the data :

 <iframe
 src="assets/table.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>



Here is a univariate analysis of distributions from the data : 

 <iframe
 src="assets/ingredients_distribution.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>

This plot shows the distribution of the number of ingredients used in recipes. The majority of recipes tend to include between 5 and 15 ingredients, with a sharp drop-off for longer ingredient lists. This suggests that simpler recipes are more common, potentially because they are easier for users to prepare — an insight that aligns with our goal of identifying characteristics of highly rated, accessible recipes.
