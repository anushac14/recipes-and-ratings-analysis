# Behind the Bite: What Drives Calories in Recipes?
By: **Anusha Chinthamaduka**

---
## Introduction
This project investigates what types of recipes tend to have the most calories. This is incredibly important because it can help individuals make more informed dietary choices and can aid in meal planning and general nutritional awareness. By examining the relationship between nutritional information and calorie content, this analysis aims to empower people to maintain a healthier and more balanced lifestyle.

The data used in this analysis consists of scrapped recipe reviews from the website food.com. There were 2 separate datasets, one containing recipes and the other containing the reviews and ratings for the recipes. Once the two datasets were combined, the merged dataset contained 23,4429 rows and 17 columns, with each row containing a review for a particular recipe. The two columns that were most relevant include: 

- **`Nutrition`**: Consists of nutrient information such as number of calories and the Percentage of Daily Value (PDV) for total fat, sugar, sodium, protein, saturated fat, and carbohydrates
- **`Tags`**: The descriptor tags that are present for each recipe. The tags that I was particularly interested in include breakfast, lunch, dinner, brunch, main-dish

---

## Data Cleaning and Exploratory Data Analysis

**Data Cleaning Steps**

In order to begin exploring the data, I first began by preprocessing and cleaning the current dataset in the following way:

1. I started with two separate datasets, RAW_recipes.csv, which contains recipe information from food.com, and RAW_interactions.csv, which contains user ratings and reviews for these recipes. I merged these datasets using a left join on the recipe_id column to combine the relevant information.
2. After merging the dataset, I replaced all the rating values of zero with np.nan, as a rating of zero typically indicates that the user did not provide a rating (since the lowest valid rating is 1 star). By treating these values as missing rather than as zeros, it helps prevent them from skewing the analysis.
3. Then, I found the average rating for each recipe and included a new column to store these averages.
4. Each row in the nutrition column contained a string with multiple nutritional values. I split this string into individual columns for each nutritional value (such as calories, total fat, sugar, sodium, protein, saturated fat, and carbohydrates). By extracting these values into individual columns, I was able to use them to train both my baseline and final models.

Here is the first few rows of resulting cleaned dataframe:

|     id  | name                                              | tags                  |   avg_rating |   Calories |   Total Fat |   Sugar |   Sodium |   Protein |   Saturated Fat |   Carbohydrates |
|--------:|:--------------------------------------------------|:----------------------|-------------:|-----------:|------------:|--------:|---------:|----------:|----------------:|----------------:|
| 324496  | chocolate spritz cookies                          | desserts              |            5 |       47.1 |           4 |      10 |        0 |         1 |               8 |               1 |
| 287392  | banana raspberry slush                            | 5-ingredients-or-less |            5 |      229.9 |           2 |     165 |        1 |         8 |               2 |              18 |
| 414099  | scallops with garlic bread crumbs weight watchers | 15-minutes-or-less    |            5 |      152.6 |           6 |       1 |       38 |        36 |              10 |               3 |
| 476734  | peppery sweet oven roasted salmon                 | taste-mood            |            5 |      123.8 |           5 |      16 |        2 |        34 |               3 |               1 |
| 285338  | healthier chicken marsala                         | 60-minutes-or-less    |            5 |      360.1 |          15 |       9 |       18 |        56 |              15 |               4 |

**Calorie Distribution**
 
 <iframe
 src="assets/calorie-distribution.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>
 
This histogram shows the distribution of calories, where the calories are grouped into 100 calorie increments. There is a clear right skew with most dishes being within the range of 0-600 calories, which makes sense as it aligns with typical calorie ranges for snacks and meals. Although this doesn't directly answer my initial exploration question, it does provide a useful visualization of the average calorie ranges that are present for recipes in this dataset (Note: I decided to use a histogram over a box plot because it provided a more intuitive and detailed view of the data distribution).

**Calories vs Nutrients Correlation**

<iframe
 src="assets/calories-nutrients-correlation.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>

This heatmap visualizes the correlation between calories and different nutrient information. The nutrients that have the strongest positive correlation with calories include Total Fat, Saturated Fat, and Carbohydrates, which suggests that these nutrients are key contributors to determining the caloric content of a recipe. 

**Nutritional Information Per Meal Type**

| tags       |   Calories |   Total Fat |   Sugar |   Carbohydrates |   Count |
|:-----------|-----------:|------------:|--------:|----------------:|--------:|
| appetizers |    337.755 |     32.6989 | 28.768  |         7.37155 |   17268 |
| breakfast  |    355.093 |     26.2486 | 56.2776 |        12.4942  |   16259 |
| brunch     |    387.927 |     29.7146 | 63.9722 |        13.2426  |   18057 |
| lunch      |    401.056 |     31.7938 | 35.7984 |        11.2587  |   19582 |
| main-dish  |    502.815 |     40.0705 | 30.5317 |        11.2432  |   71806 |

This pivot table shows the average nutritional content for various meal types including main-dish, breakfast, lunch, brunch and appetizers. It is interesting to note that the main dish has a significantly higher calorie count (about 100 more) and total fat count (approximately 9% more) than lunch, despite the two tags having some overlap (as main-dish could refer to either dinner or lunch meals). This suggests that certain factors, such as fat content, contribute to the higher caloric count. Another interesting observation is that brunch contains a significantly higher sugar content, sometimes showing a 2x difference compared to other meal types. Additionally, lunch, brunch, breakfast, and appetizers tend to have similar values for calories, total fat, and carbohydrates, with little variation across these meal types.

**Imputation**

After analyzing my dataset, I found that the two columns with the most significant number of missing values were rating (15,036 missing values) and avg_rating (2,777 missing values). These correspond to approximately 6.41% and 1.18% of the total dataset. Given that these missing percentages are relatively low compared to the size of the dataset, one option could be to drop the rows containing these missing values. However, since my analysis is focused on determining which factors influence calorie amount (which doesn't involve ratings or average ratings), I decided to keep the missing values since there is no significant impact.

---

## Framing a Prediction Problem
The problem that I am trying to predict is given the nutritional facts about a recipe, can we predict the amount of calories that a dish has. Since my response variable is calories (which is a continuous variable), this would be considered a regression problem. During my exploratory analysis, there seemed to be correlations between various nutrition facts and the calorie amount, which is why I chose calories as my prediction variable. The metric that I decided to use to evaluate my model is R^2 because this indicated how well a model fits the data and what percentage of the variability the model captures. This is more informative than other evaluation metrics such as Mean Squared Error (MSE) and Root Mean Squared Error (RMSE) because MSE and RMSE only provide information about the magnitude of the model's prediction error without offering insight into the overall fit of the model. 

---

## Baseline Model
The baseline model that I chose was linear regression because it can effectively model the linear relationships between the input features (nutrition facts) and the target variable (calories). The features used for this model include Carbohydrates and Proteins, which both have a positive linear relationship with calories. Both of these features are quantitative and both are also nominal. Neither of the features are ordinal. The R^2 performance on this model was 0.8116 for the training set and 0.8077 for the testing set. This indicates that the model captures 80.77% of the variance in the calorie values on unseen data. I believe that my current model is fairly good at capturing most of the variance but there is still room for improvement through feature engineering. 

---

## Final Model
In order to improve upon the baseline model's performance, I decided to implement some feature engineering. Here are the 2 features that I included in the final model (in addition to the baseline model's protein and carbohydrates features):

- **`sugar_to_fat_ratio`** This feature is the ratio of sugar : total fat. Since foods that have higher amounts of sugar and fat tend to be more calorically dense, this feature helped improve my model's prediction accuracy by incorporating the balance between these two energy dense components.
- **`sodium_satfat_interaction`** This feature is the interaction of sodium * saturated fat. This helped improve my model because foods that tend to be higher in sodium and saturated fats (such as junk food or fast food) tend to also have higher calories so capturing this interaction allows the model to better account for these high-calorie food patterns.

The modeling algorithm that I chose was Random Forest Regression because it can capture the complex, non linear patterns in the data. Since I included the interaction term between sodium and saturated fat, this model would be the best to capture this relationship. The method that I used to select the hyperparameters was GridSearchCV, which gave me these optimial hyperparameters:

- **`min_samples_split`**: 2
- **`n_estimators`**: 200

After running my model on these four features and the optimal hyperparameters, the performance significantly increased from my baseline model. My R^2 values for the training set was 0.9876 and 0.9531 for the test set. This means that my model was able to capture 95.31% of the calorie variance on unseen data, which is approximately 15 percentage points greater than my baseline model. Since the R^2 value is close to 1, it indicates that the model demonstrates strong predictive performance for calorie estimation using the features protein, carbohydrates, sugar to fat ratio, and the interaction between sodium and saturated fat.
