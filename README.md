# 🍳 Cooking Up Predictions: A Machine Learning Model for Recipe Ratings

**Author**: Vedikas Sridharan  
**Email**: vedikas@umich.edu

---

## 📘 Introduction

This project uses data from two sources:

- `RAW_recipes.csv`: 83,782 recipes with ingredients, nutrition facts, cooking steps, and preparation time.
- `RAW_interactions.csv`: 731,927 user interactions with these recipes (ratings, reviews, timestamps).

**Why this matters**:

- It helps users find recipes they'll likely enjoy.  
- It can guide food bloggers or platform designers to improve recipe recommendations.  
- It reveals trends in what people consider “good” or “worth making” when it comes to food.

**Key Columns**:

- From `RAW_recipes.csv`:  
  `id`, `name`, `minutes`, `n_steps`, `n_ingredients`, `calories`, `fat`, `sugar`, `protein`, `sodium`

- From `RAW_interactions.csv`:  
  `recipe_id`, `rating` (1–5 scale)

---

## 🧹 Data Cleaning and Exploratory Data Analysis

To prepare the data for analysis, I began by merging two separate datasets: one containing user interactions with recipes (such as ratings) and another containing detailed recipe information. This merge was essential because it brought together user behavior and recipe characteristics, allowing for meaningful analysis of how recipe attributes influence ratings.

One of the key variables in the recipe data was a nutrition field, which was originally stored as a stringified list of values. I converted this list into individual numeric columns—such as calories, sugar, fat, and protein to make the data usable in analysis. This step reflected a necessary unpacking of aggregated information that was likely produced automatically during the recipe submission process.

I also converted the date column from string format to a datetime object. This not only standardized the format but also made the data suitable for any potential time-based analyses in the future. In some cases, the conversion revealed invalid or malformed entries, which were handled by coercing them into null values.

Another important step was dealing with unrealistic preparation time values. Specifically, some recipes listed a prep time of zero minutes, which is clearly invalid. I replaced these with missing values to avoid skewing the results, especially important for scatter plots using a log-transformed time axis.

Finally, I checked for missing values in key columns such as rating, preparation time, number of ingredients, number of steps, and nutritional content. This helped me understand the completeness of the dataset and guided decisions about which rows could be included in various parts of the analysis.

Overall, these cleaning steps were closely aligned with how the data was likely generated—through user submissions, automated scraping, and imperfect manual inputs. Cleaning the data this way improved the quality and interpretability of the visualizations and ensured that the trends I observed reflected genuine patterns rather than noise or errors.

### 🧪 Head of the Data

 <div style="margin: 0 auto; padding: 0; max-width: 800px;">
  <iframe src="assets/table.html" width="100%" height="450" frameborder="0" style="margin: 0; padding: 0;"></iframe>
</div>


### 📈 Univariate Distributions

#### Ingredients
<div style="margin: 0 auto; padding: 0; max-width: 800px;">
  <iframe src="assets/ingredients_distribution.html" width="100%" height="450" frameborder="0" style="margin: 0; padding: 0;"></iframe>
</div>

This plot shows the distribution of the number of ingredients used in recipes. The majority of recipes tend to include between 5 and 15 ingredients, with a sharp drop-off for longer ingredient lists. This suggests that simpler recipes are more common, potentially because they are easier for users to prepare. This is an insight that aligns with our goal of identifying characteristics of highly rated, accessible recipes.

#### Calories
 <div style="margin: 0 auto; padding: 0; max-width: 800px;">
  <iframe src="assets/calories_distribution.html" width="100%" height="450" frameborder="0" style="margin: 0; padding: 0;"></iframe>
</div>

The calorie distribution is right-skewed, with most recipes containing fewer than 1000 calories. There are a few extreme outliers with very high caloric values, likely from dense or multi-serving dishes. This plot helps illustrate the diversity in recipe health profiles and can guide users looking for lighter or heavier meals.

#### Ratings
<div style="margin: 0 auto; padding: 0; max-width: 800px;">
  <iframe src="assets/rating_distribution.html" width="100%" height="450" frameborder="0" style="margin: 0; padding: 0;"></iframe>
</div>

The rating distribution is highly skewed toward the maximum rating of 5, indicating that users generally rate recipes very favorably. While this suggests strong satisfaction, it also limits the ability to differentiate between moderately and highly liked recipes, which is something to consider in modeling user preferences.

### 📉 Bivariate Analyses

#### Calories vs Rating
<div style="margin: 0 auto; padding: 0; max-width: 800px;">
  <iframe src="assets/calories_vs_rating.html" width="100%" height="450" frameborder="0" style="margin: 0; padding: 0;"></iframe>
</div>

This scatter plot explores the relationship between calories and user ratings. There is no strong correlation, but we observe that high-calorie recipes are rated just as favorably as low-calorie ones, suggesting that calorie content does not significantly affect user satisfaction.

#### Ingredients vs Rating
<div style="margin: 0 auto; padding: 0; max-width: 800px;">
  <iframe src="assets/ingredients_vs_rating.html" width="100%" height="450" frameborder="0" style="margin: 0; padding: 0;"></iframe>
</div>

This box plot shows how the number of ingredients relates to user ratings. While most recipes are highly rated regardless of complexity, those with very few or very many ingredients show slightly more variability, hinting that balance in ingredient count may appeal more consistently to users.

#### Prep Time vs Rating
<div style="margin: 0 auto; padding: 0; max-width: 800px;">
  <iframe src="assets/prep_time_vs_rating.html" width="100%" height="450" frameborder="0" style="margin: 0; padding: 0;"></iframe>
</div>

This scatter plot, with preparation time shown on a logarithmic scale, reveals that most highly rated recipes cluster between 10 and 100 minutes of prep time. Recipes with extremely short or long prep times show more mixed ratings, suggesting that users prefer moderate effort in cooking.

#### Sugar by Rating
<div style="margin: 0 auto; padding: 0; max-width: 800px;">
  <iframe src="assets/sugar_by_rating.html" width="100%" height="450" frameborder="0" style="margin: 0; padding: 0;"></iframe>
</div>

This box plot displays the distribution of sugar content across different rating levels. There is no clear pattern, indicating that sugar level alone is not a strong driver of user satisfaction. However, outliers suggest that some exceptionally sweet or non-sweet recipes still achieve high ratings.

### 📊 Aggregate Trends

#### Rating by Calorie Level

| calorie_level | avg_rating | num_ratings |
|---------------|------------|-------------|
| Very Low      | 4.41       | 72671       |
| Low           | 4.41       | 78912       |
| Medium        | 4.36       | 42074       |
| High          | 4.35       | 19018       |
| Very High     | 4.27       | 17958       |

The data suggests that recipes with around 3 to 8 ingredients tend to receive the highest average ratings. This may indicate a user preference for recipes that are neither too simple nor too complex—balancing ease of preparation with flavor and variety. Recipes with very few ingredients might be seen as too basic, while those with too many could feel overwhelming or impractical for everyday cooking.

#### Ingredients Group
<iframe src="assets/ingredients_group.html" width="100%" height="450" frameborder="0" style="margin:0;padding:0;"></iframe>
The trend is clear: shorter prep times receive higher average ratings. Recipes under 15 minutes are the most highly rated, and the average rating declines as the prep time increases. This supports the idea that users tend to favor quick, convenient recipes, especially for weeknight cooking, reinforcing that ease and efficiency are major drivers of satisfaction.

#### Time Group
<iframe src="assets/time_group.html" width="100%" height="450" frameborder="0" style="margin:0;padding:0;"></iframe>
Recipes with lower calorie content (Very Low and Low) have slightly higher average ratings than high-calorie dishes. This suggests a possible health-conscious trend among users, where lighter meals are more appreciated. However, the drop-off is gradual, indicating that users don’t necessarily dislike high-calorie recipes, just that lighter options are more broadly favored.

## 🛠️ Handling Missing Values

To handle missing values in key numerical columns such as `minutes`, `calories`, and `sugar_g`, I used **median imputation** via `SimpleImputer` from scikit-learn. The median is a robust measure of central tendency, making it ideal for skewed distributions and resistant to outliers—especially relevant in recipe data where a few entries may have unusually high prep times or calorie counts.

After imputation, I confirmed that all missing values were resolved. This step ensures that subsequent analyses and visualizations are not affected by gaps or NaNs in the data.

The interactive chart below compares the **distributions before and after imputation** for three key columns. You can observe that the shapes of the distributions remain mostly unchanged, with a minor spike at the median values—validating that median imputation preserved the original data structure while filling in missing values.

<div style="margin: 0 auto; padding: 0; max-width: 800px;">
  <iframe src="assets/imputation_comparison.html" width="100%" height="450" frameborder="0" style="margin: 0; padding: 0;"></iframe>
</div>

---

## 🤖 Framing a Prediction Problem

**Can we predict whether a recipe will receive a high user rating (4 or above) based on its structure and nutritional content?**

This is a **binary classification** problem. Each recipe is classified as either:

- **1 (High Rating)**: if the average user rating is **≥ 4**  
- **0 (Low Rating)**: if the average user rating is **< 4**

---

### 🎯 Response Variable

- **`high_rating`** — a binary indicator derived from the average user rating  
- **Why this threshold?** Ratings range from 0 to 5. A threshold of 4 is commonly used to indicate that something is “well-received” (e.g., 4+ star reviews are considered good on most platforms).

---

### 🧩 Features Used for Prediction

All features are available **at the time of recipe posting**, before any user interaction.

#### **Structural Features**
- `minutes` – total preparation time  
- `n_ingredients` – number of ingredients  
- `n_steps` – number of steps in the recipe  

#### **Nutritional Features**
- `calories`  
- `total_fat_g`  
- `sugar_g`  
- `sodium_mg`  
- `protein_g`  
- `saturated_fat_g`  
- `carbohydrates_g`

These features are static and not dependent on future data like reviews or ratings.

---

### ⚙️ Prediction Type

- **Type**: Classification  
- **Sub-type**: Binary Classification  

---

### 📊 Evaluation Metric: F1-Score

We chose **F1-score** as the primary evaluation metric.

**Why not accuracy?**
- The dataset is **imbalanced** — most recipes receive high ratings.
- A model that always predicts "high rating" could achieve high accuracy but offer **no real insight**.

**Why F1?**
- F1-score balances **precision and recall**
- It provides a better measure of performance for the **minority class** (low-rated recipes), ensuring we don’t ignore false negatives.

---


## Baseline Model 📊 

Our baseline model is a **logistic regression classifier** built using a scikit-learn pipeline. It predicts whether a recipe will receive a high user rating (defined as ≥4) based on two features available at the time of posting: total preparation time and number of ingredients.

---

## 🔧 Features Used in the Model

We used the following **2 features**:

| Feature         | Type         | Description                                      |
|-----------------|--------------|--------------------------------------------------|
| `minutes`       | Quantitative | Total time (in minutes) to prepare the recipe    |
| `n_ingredients` | Quantitative | Number of ingredients in the recipe              |

- **Quantitative features**: 2  
- **Ordinal features**: 0  
- **Nominal features**: 0  

➡️ No categorical or ordinal variables were included in this baseline model, so no encoding was necessary.

---

## ⚙️ Preprocessing Steps

These were performed as part of the pipeline:

1. **Imputation**: Missing values were imputed using the **median** strategy (via `SimpleImputer`).
2. **Scaling**: Features were scaled using **standardization** (`StandardScaler`) to normalize the input values.
3. **Classification**: A **logistic regression** classifier was trained with `max_iter=1000` to ensure convergence.

---

## 🧪 Model Performance

Performance metrics on the test set:

- **Accuracy**: 88.3%
- **Precision (Class 1 - High Rating)**: 0.88  
- **Recall (Class 1 - High Rating)**:

