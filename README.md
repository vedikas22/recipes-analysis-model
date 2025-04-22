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

 <div style="margin: 0 auto; padding: 0; max-width: 800px;">
  <iframe src="assets/table.html" width="100%" height="450" frameborder="0" style="margin: 0; padding: 0;"></iframe>
</div>


Here is a univariate analysis of distributions from the data : 

<div style="margin: 0 auto; padding: 0; max-width: 800px;">
  <iframe src="assets/ingredients_distribution.html" width="100%" height="450" frameborder="0" style="margin: 0; padding: 0;"></iframe>
</div>

This plot shows the distribution of the number of ingredients used in recipes. The majority of recipes tend to include between 5 and 15 ingredients, with a sharp drop-off for longer ingredient lists. This suggests that simpler recipes are more common, potentially because they are easier for users to prepare. This is an insight that aligns with our goal of identifying characteristics of highly rated, accessible recipes.

 <div style="margin: 0 auto; padding: 0; max-width: 800px;">
  <iframe src="assets/calories_distribution.html" width="100%" height="450" frameborder="0" style="margin: 0; padding: 0;"></iframe>
</div>

The calorie distribution is right-skewed, with most recipes containing fewer than 1000 calories. There are a few extreme outliers with very high caloric values, likely from dense or multi-serving dishes. This plot helps illustrate the diversity in recipe health profiles and can guide users looking for lighter or heavier meals.

<div style="margin: 0 auto; padding: 0; max-width: 800px;">
  <iframe src="assets/rating_distribution.html" width="100%" height="450" frameborder="0" style="margin: 0; padding: 0;"></iframe>
</div>

The rating distribution is highly skewed toward the maximum rating of 5, indicating that users generally rate recipes very favorably. While this suggests strong satisfaction, it also limits the ability to differentiate between moderately and highly liked recipes, which is something to consider in modeling user preferences.

Here is a bivariate analysis of distributions from the data : 

<div style="margin: 0 auto; padding: 0; max-width: 800px;">
  <iframe src="assets/calories_vs_rating.html" width="100%" height="450" frameborder="0" style="margin: 0; padding: 0;"></iframe>
</div>

This scatter plot explores the relationship between calories and user ratings. There is no strong correlation, but we observe that high-calorie recipes are rated just as favorably as low-calorie ones, suggesting that calorie content does not significantly affect user satisfaction.

<div style="margin: 0 auto; padding: 0; max-width: 800px;">
  <iframe src="assets/ingredients_vs_rating.html" width="100%" height="450" frameborder="0" style="margin: 0; padding: 0;"></iframe>
</div>

This box plot shows how the number of ingredients relates to user ratings. While most recipes are highly rated regardless of complexity, those with very few or very many ingredients show slightly more variability, hinting that balance in ingredient count may appeal more consistently to users.

<div style="margin: 0 auto; padding: 0; max-width: 800px;">
  <iframe src="assets/prep_time_vs_rating.html" width="100%" height="450" frameborder="0" style="margin: 0; padding: 0;"></iframe>
</div>

This scatter plot, with preparation time shown on a logarithmic scale, reveals that most highly rated recipes cluster between 10 and 100 minutes of prep time. Recipes with extremely short or long prep times show more mixed ratings, suggesting that users prefer moderate effort in cooking.

<div style="margin: 0 auto; padding: 0; max-width: 800px;">
  <iframe src="assets/sugar_by_rating.html" width="100%" height="450" frameborder="0" style="margin: 0; padding: 0;"></iframe>
</div>

This box plot displays the distribution of sugar content across different rating levels. There is no clear pattern, indicating that sugar level alone is not a strong driver of user satisfaction. However, outliers suggest that some exceptionally sweet or non-sweet recipes still achieve high ratings.

Here are the Interesting Aggregates discovered :

<div style="margin: 0 auto; padding: 0; max-width: 800px;">
  <iframe src="assets/calorie_group.html" width="100%" height="450" frameborder="0" style="margin: 0; padding: 0;"></iframe>
</div>

The data suggests that recipes with around 3 to 8 ingredients tend to receive the highest average ratings. This may indicate a user preference for recipes that are neither too simple nor too complex—balancing ease of preparation with flavor and variety. Recipes with very few ingredients might be seen as too basic, while those with too many could feel overwhelming or impractical for everyday cooking.

<div style="margin: 0 auto; padding: 0; max-width: 800px;">
  <iframe src="assets/ingredients_group.html" width="100%" height="450" frameborder="0" style="margin: 0; padding: 0;"></iframe>
</div>

The trend is clear: shorter prep times receive higher average ratings. Recipes under 15 minutes are the most highly rated, and the average rating declines as the prep time increases. This supports the idea that users tend to favor quick, convenient recipes, especially for weeknight cooking, reinforcing that ease and efficiency are major drivers of satisfaction.

<div style="margin: 0 auto; padding: 0; max-width: 800px;">
  <iframe src="assets/time_group.html" width="100%" height="450" frameborder="0" style="margin: 0; padding: 0;"></iframe>
</div>

Recipes with lower calorie content (Very Low and Low) have slightly higher average ratings than high-calorie dishes. This suggests a possible health-conscious trend among users, where lighter meals are more appreciated. However, the drop-off is gradual, indicating that users don’t necessarily dislike high-calorie recipes,just that lighter options are more broadly favored.