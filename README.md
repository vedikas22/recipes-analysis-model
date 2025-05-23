# 🍳 Cooking Up Predictions: A Machine Learning Model for Recipe Ratings

**Author**: Vedikas Sridharan  
**Email**: vedikas@umich.edu

---

## 📘 Introduction

This project uses data from two sources:

- `RAW_recipes.csv`: 83,782 recipes with ingredients, nutrition facts, cooking steps, and preparation time.
- `RAW_interactions.csv`: 731,927 user interactions with these recipes (ratings, reviews, timestamps).

Question : What makes a recipe well-rated? We want to understand how different characteristics of a recipe like the number of ingredients, total preparation time, and nutritional content correlate with high user ratings.

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

## 📋 Dataset Overview

### 📦 Number of Rows and Columns

- **Number of Rows**: 46,886  
- **Number of Columns**: 14 (relevant to our prediction task)

---

### 📌 Relevant Columns

Below are the columns used to frame and answer the central prediction question — *Can we predict whether a recipe will receive a high user rating based on its structure and nutritional content?*

| Column Name          | Description                                                                 |
|----------------------|-----------------------------------------------------------------------------|
| `rating`             | User rating for the recipe (1 to 5 stars). Used to create the target label. |
| `high_rating`        | Binary target variable: 1 if rating ≥ 4, else 0.                            |
| `minutes`            | Total time in minutes required to prepare the recipe.                       |
| `n_ingredients`      | Number of ingredients listed in the recipe.                                 |
| `n_steps`            | Number of steps in the cooking instructions.                                |
| `calories`           | Total calories per serving.                                                 |
| `total_fat_g`        | Total fat content per serving (in grams).                                   |
| `sugar_g`            | Sugar content per serving (in grams).                                       |
| `sodium_mg`          | Sodium content per serving (in milligrams).                                 |
| `protein_g`          | Protein content per serving (in grams).                                     |
| `saturated_fat_g`    | Saturated fat per serving (in grams).                                       |
| `carbohydrates_g`    | Carbohydrates per serving (in grams).                                       |
| `log_minutes`        | Log-transformed preparation time to reduce skewness.                        |
| `fat_to_protein_ratio` | Ratio of fat to protein, capturing nutritional richness.                  |

---

These features were selected because they are **available at the time of recipe posting** and are likely to influence **user satisfaction** and **ratings**. The `high_rating` column serves as the **binary response variable** for our classification task.

---- 
## 🧹 Data Cleaning and Exploratory Data Analysis

To prepare the data for analysis, I began by merging two separate datasets: one containing user interactions with recipes (such as ratings) and another containing detailed recipe information. This merge was essential because it brought together user behavior and recipe characteristics, allowing for meaningful analysis of how recipe attributes influence ratings.

One of the key variables in the recipe data was a nutrition field, which was originally stored as a stringified list of values. I converted this list into individual numeric columns—such as calories, sugar, fat, and protein to make the data usable in analysis. This step reflected a necessary unpacking of aggregated information that was likely produced automatically during the recipe submission process.

I also converted the date column from string format to a datetime object. This not only standardized the format but also made the data suitable for any potential time-based analyses in the future. In some cases, the conversion revealed invalid or malformed entries, which were handled by coercing them into null values.

Another important step was dealing with unrealistic preparation time values. Specifically, some recipes listed a prep time of zero minutes, which is clearly invalid. I replaced these with missing values to avoid skewing the results, especially important for scatter plots using a log-transformed time axis.

Finally, I checked for missing values in key columns such as rating, preparation time, number of ingredients, number of steps, and nutritional content. This helped me understand the completeness of the dataset and guided decisions about which rows could be included in various parts of the analysis.

Overall, these cleaning steps were closely aligned with how the data was likely generated—through user submissions, automated scraping, and imperfect manual inputs. Cleaning the data this way improved the quality and interpretability of the visualizations and ensured that the trends I observed reflected genuine patterns rather than noise or errors.

### 🧪 Head of the Data

| user_id | recipe_id | date       | rating | review                                                                                                                                       | name                                               | id     | minutes | contributor_id | submitted  | tags                                                                                                                                                                                                                       | n_steps | steps                                                                                                                                                                                                                                                                       | description                                                                                                           | ingredients                                                                                                      | n_ingredients | calories | total_fat_g | sugar_g | sodium_mg | protein_g | saturated_fat_g | carbohydrates_g |
|---------|-----------|------------|--------|----------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------|--------|---------|----------------|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|----------------|----------|--------------|---------|------------|------------|------------------|------------------|
| 483827  | 306785    | 2008-07-15 | 5      | Being a huge fan of America's Test Kitchen...                                                                                               | spicy nectarine and corn salsa                     | 306785 | 40      | 377499         | 2008-06-02 | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'occasion', 'low-protein'...] | 4        | ['in a medium bowl , toss together nectarines , corn , shallot , chile , 1 tablespoon lime juice , and salt', ...]                                    | from my heros at america's test kitchen (cooks illustrated, summer 2008)...                                          | ['nectarines', 'ear of corn', 'shallot', 'habanero pepper', 'lime juice', 'salt', 'sugar', 'fresh chives']      | 8              | 95.3     | 1            | 50      | 16         | 5          | 0                | 7                |
| 5060    | 310237    | 2010-05-07 | 5      | wow red and white sweetness! DH loved them...                                                                                               | french strawberry crepes                           | 310237 | 30      | 452576         | 2008-06-19 | ['30-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'cuisine', 'preparation', 'occasion'...]     | 9        | ['for the filling: toss the strawberries and brown sugar in a bowl and set aside', 'for the crepes:in a medium...'] | yummy and sweet french-style strawberry breakfast crepes!                                                           | ['strawberries', 'brown sugar', 'cool whip free', 'flour', 'egg', 'oil', 'vanilla extract', 'baking powder'...] | 10             | 143.5    | 5            | 25      | 3          | 10         | 3                | 7                |
| 935485  | 321038    | 2009-06-22 | 5      | Being a healthy bar this is very good...                                                                                                    | hg s grab  n go breakfast cookies   weight watchers   2 points | 321038 | 22      | 346383         | 2008-08-24 | ['30-minutes-or-less', 'time-to-make', 'course', 'preparation', 'occasion', 'drop-cookies', 'breakfast'...]     | 14       | ['preheat oven to 375 degrees', 'chop raisins and craisins into small pieces', 'set aside', ...]                   | i received a hungry girl email today and this was one of the recipes that were featured...                          | ['oats', 'whole wheat flour', 'fiber one cereal', 'splenda granular', 'peach baby food', 'canned pumpkin'...]   | 14             | 182.4    | 2            | 50      | 7          | 11         | 1                | 13               |
| 539686  | 321038    | 2011-03-28 | 5      | My very health conscious daughter and I tripled this recipe...                                                                               | hg s grab  n go breakfast cookies   weight watchers   2 points | 321038 | 22      | 346383         | 2008-08-24 | ['30-minutes-or-less', 'time-to-make', 'course', 'preparation', 'occasion', 'drop-cookies', 'breakfast'...]     | 14       | ['preheat oven to 375 degrees', 'chop raisins and craisins into small pieces', 'set aside', ...]                   | i received a hungry girl email today and this was one of the recipes that were featured...                          | ['oats', 'whole wheat flour', 'fiber one cereal', 'splenda granular', 'peach baby food', 'canned pumpkin'...]   | 14             | 182.4    | 2            | 50      | 7          | 11         | 1                | 13               |
| 22174   | 342209    | 2008-12-28 | 4      | I am changing my initial review because they got better overnight!...                                                                       | banana oatmeal cookies                              | 342209 | 40      | 361931         | 2008-12-08 | ['60-minutes-or-less', 'time-to-make', 'course', 'preparation', 'desserts', 'cookies-and-brownies']              | 7        | ['sift flour , soda , baking powder and salt together', 'cream together sugar and shortening', 'beat in...']        | a twist on an old favorite                                                                                          | ['sugar', 'butter', 'eggs', 'mashed banana', 'vanilla', 'lemon juice', 'rolled oats', 'flour', 'baking soda'...] | 12             | 658.2    | 45           | 151     | 35         | 24         | 72               | 29               |



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

Recipes with lower calorie content (Very Low and Low) have slightly higher average ratings than high-calorie dishes. This suggests a possible health-conscious trend among users, where lighter meals are more appreciated. However, the drop-off is gradual, indicating that users don’t necessarily dislike high-calorie recipes, just that lighter options are more broadly favored.

#### Ingredients Group
<iframe src="assets/ingredients_group.html" width="100%" height="450" frameborder="0" style="margin:0;padding:0;"></iframe>
The data suggests that recipes with around 3 to 8 ingredients tend to receive the highest average ratings. This may indicate a user preference for recipes that are neither too simple nor too complex—balancing ease of preparation with flavor and variety. Recipes with very few ingredients might be seen as too basic, while those with too many could feel overwhelming or impractical for everyday cooking.

#### Time Group
<iframe src="assets/time_group.html" width="100%" height="450" frameborder="0" style="margin:0;padding:0;"></iframe>
The trend is clear: shorter prep times receive higher average ratings. Recipes under 15 minutes are the most highly rated, and the average rating declines as the prep time increases. This supports the idea that users tend to favor quick, convenient recipes, especially for weeknight cooking, reinforcing that ease and efficiency are major drivers of satisfaction.

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
- **Recall (Class 1 - High Rating)**: 1.00  
- **F1-score (Class 1 - High Rating)**: 0.94  
- **Class 0 (Low Rating)**: Precision/Recall/F1 = 0.00

⚠️ **Note**: The model never predicts Class 0 (low ratings), which is a major issue.

---

## 🤔 Is This a Good Model?

While the accuracy appears high (**88.3%**), the model is **not good** in its current state due to **extreme class imbalance**:

- It fails to identify any low-rated recipes.
- High accuracy is misleading because the model predicts every recipe as high-rated, benefiting from the skewed distribution.

## 🔍 Final Model Rationale and Evaluation

### 🧩 Feature Engineering: What I Added and Why

To improve my model, I added two engineered features based on domain intuition and patterns observed during exploratory analysis:

- **`log_minutes`**  
  Prep times were highly skewed, with some recipes taking extremely long to prepare. Taking the natural logarithm of preparation time (`log1p`) helped normalize this feature and reduce the impact of extreme outliers. Log transformations are especially useful for tree-based models that may otherwise overweight rare, large values.

- **`fat_to_protein_ratio`**  
  Rather than considering `total_fat_g` and `protein_g` separately, I created a ratio feature to capture a recipe’s **nutritional richness vs. healthfulness**. This ratio provides insight into how indulgent or balanced a recipe might be, which is likely to influence user ratings. For example, protein-rich meals may be preferred for health-conscious users, while fatty recipes may appeal more to indulgence seekers.

These features were chosen **based on the data-generating process**, not just for boosting accuracy. They reflect reasonable assumptions about what might drive user satisfaction, such as ease of preparation (normalized time) and nutritional balance (fat vs. protein).

---

### 🤖 Model Selection: Random Forest Classifier

I selected the **Random Forest** classifier for the final model due to its ability to:

- Capture **non-linear relationships** between features and outcomes  
- Handle **interactions** between multiple predictors without explicit specification  
- Be robust to **outliers** and **correlated features**  
- Naturally support **class weighting** to mitigate imbalance

---

### 🔧 Hyperparameter Tuning

To optimize model performance, I used **`GridSearchCV` with 5-fold cross-validation**, tuning the following hyperparameters:

- `n_estimators`: Number of trees in the forest → [50, 100]  
- `max_depth`: Maximum tree depth → [5, 10, None]  
- `min_samples_split`: Minimum number of samples to split a node → [2, 5]

**Best-performing combination**:

```python
{
  'classifier__n_estimators': 50,
  'classifier__max_depth': None,
  'classifier__min_samples_split': 2
}
```

### 📈 Performance Comparison

| Metric                | Baseline Model (Logistic Regression) | Final Model (Random Forest) |
|-----------------------|--------------------------------------|------------------------------|
| **Accuracy**          | 88.3%                                | 77.9%                        |
| **F1 (Class 1 - High)** | 0.94                               | 0.87                         |
| **F1 (Class 0 - Low)**  | 0.00                               | 0.26                         |
| **Macro F1**          | 0.47                                 | **0.56**                     |
| **Weighted F1**       | 0.83                                 | **0.80**                     |

While the baseline model achieved higher **accuracy**, it failed to predict **any low-rated recipes**. In contrast, the final **Random Forest** model improves balance across both classes and yields a much more meaningful **F1-score** for low-rated recipes (Class 0). This makes it a significantly better model overall, especially for **imbalanced data**.

### 📊 Confusion Matrix Summary

![Confusion Matrix](assets/confusion_matrix.png)

|                      | Predicted Low (0) | Predicted High (1) | Total |
|----------------------|-------------------|---------------------|-------|
| **Actual Low (0)**   | **1792** (True Negatives) | **3681** (False Positives) | 5473 |
| **Actual High (1)**  | **6691** (False Negatives) | **34722** (True Positives) | 41413 |

---

### 🔍 Interpretation

- **True Positives (TP)**: 34,722  
  The model correctly predicted high ratings for these recipes.

- **True Negatives (TN)**: 1,792  
  The model correctly predicted low ratings.

- **False Positives (FP)**: 3,681  
  These recipes were actually low-rated but predicted as high-rated. This reduces trust in highly-rated suggestions.

- **False Negatives (FN)**: 6,691  
  These were actually high-rated recipes that the model predicted as low-rated — potentially good recipes that the model mistakenly devalues.

---

### ✅ Strengths

- **Strong at detecting highly rated recipes**:  
  With 34,722 true positives, the model performs well on the majority class (high ratings).

- **Substantial improvement in low-rating detection** compared to the baseline:  
  Unlike the baseline model that failed to detect any low-rated recipes, this model correctly identifies **1,792** of them.

---

### ⚠️ Weaknesses

- **Low precision and recall for Class 0 (Low Rating)**:  
  - Precision is limited due to **3,681 false positives**.
  - Recall for low ratings is approximately **32.7%**:  


- **High number of false negatives (6,691)**:  
  Many high-rated recipes are missed, which could negatively affect user trust in recipe recommendations.



