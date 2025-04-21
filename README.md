# Intoduction
This project investigates what types of recipes tend to have the most calories. This is incredibly important because it can help individuals make more informed dietary choices and can aid in meal planning and general nutritional awarness. Learning about the relationships between nutritional information and calories can empower people to maintain a healthier and more balanced lifestyle.

The data used in this analysis consists of scrapped recipe reviews from the website food.com. There were 2 seperate datasets, one containing recipies and the other containing the reviews and ratings for the recipies. Once the two datasets were combined, the merged dataset contained 23,4429 rows and 17 columns, with each row containing a review for a particular recipe. The two columns that were most relavent include: 

- **`Nutrition`**: Consists of nutrient information such as number of calories and the Percentage of Daily Value (PDV) for total fat, sugar, sodium, protein, saturated fat, and carbohydrates
- **`Tags`**: The descriptor tags that are present for each recipe. The tags that I was particularly interested in include breakfast, lunch, dinner, brunch, main-dish

# Data Cleaning and Exploratory Data Analysis

**Data Cleaning Steps**
In order to begin exploring the data, I first began by preprocessing and cleaning the current dataset in the following way:

1. I started with two separate datasets, RAW_recipes.csv, which contains recipe information from food.com, and RAW_interactions.csv, which contains user ratings and reviews for these recipes. I merged these datasets using a left join on the recipe_id column to combine the relevant information.
2. After the dataset has been merged, I filled all the ratings values that had a rating of zero to np.nan, since a zero indicates that the user did not provide any rating (since the lowest rating is typically 1 star) so we would not want the zero's to affect our analysis
3. Then, I found the average rating for each recipe and included a new column to store these averages.
4. The nutrition column contained a string with multiple nutritional values. I split this string into individual columns for each nutritional value (such as calories, total fat, sugar, sodium, protein, saturated fat, and carbohydrates). I was able to use these columns for my baseline and final models.
5. Finally, I processed the tags column by exploding the list so that each tag has its own row, allowing for one tag per row per recipe. This allowed me to use these tags to observe different nutritional information for each tag type.

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
 
This histogram shows the distribution of calories, where the calories are grouped into 100 calorie increments. There is a clear right skew with most dishes being within the range of 0-600 calories, which makes sense as it aligns with the typical average calories that both snacks and meals have. Although this doesn't directly answer my inital exploration question, it does provide a useful visualization of the typical calorie ranges that are present for recipes in this dataset.

**Carbohydrates vs Calories Distribution**
 <iframe
 src="assets/carbs-calories-distribution.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>
This graph displays the relationship between the amount of carbohydrates in a dish and the amount of calories the dish has. There appears to be a positive linear relationship between these variables, which suggests that foods with higher amounts of carbohydrates generally contain more calories.

**Nutritional Information Per Meal Type**

| Tag        |   Calories |   Total Fat |   Sugar |   Carbohydrates |   Count |
|:-----------|-----------:|------------:|--------:|----------------:|--------:|
| main-dish  |    502.815 |     40.0705 | 30.5317 |        11.2432  |   71806 |
| lunch      |    401.056 |     31.7938 | 35.7984 |        11.2587  |   19582 |
| brunch     |    387.927 |     29.7146 | 63.9722 |        13.2426  |   18057 |
| breakfast  |    355.093 |     26.2486 | 56.2776 |        12.4942  |   16259 |
| appetizers |    337.755 |     32.6989 | 28.768  |         7.37155 |   17268 |

This pivot table shows the average nutritional content for various meal types including main-dish, breakfast, lunch, brunch and appetizers. It is interesting to note that the main dish has a significantly higher calorie count (about 100 more) and total fat count (appoximately 9% more) than lunch, despite the two tags having some overlap (as main-dish could refer to either dinner or lunch meals). This suggests that certain factors, such as fat content, contribute to the higher caloric amount. Another interesting observation is that brunch contains a significantly higher sugar content, sometimes showing a 2x difference compared to other meal types. Additionally, lunch, brunch, breakfast, and appetizers tend to have similar values for calories, total fat, and carbohydrates, with little variation across these meal types.

**Imputation**
After analyzing my dataset, I found that the two columns with the most significant number of missing values were rating (15,036 missing values) and avg_rating (2,777 missing values). These correspond to approximately 6.41% and 1.18% of the total dataset. Given that these missing percentages are relatively low compared to the size of the dataset, one option could be to drop the rows containing these missing values. However, since my analysis is focused on determining which factors influence calorie amount (which doesn't involve ratings or average ratings), I decided to keep the missing values since there is no significant impact.

# Framing a Prediction Problem
The problem that I am trying to predict is given nutritional facts about a recipe, can we predict the amount of calories that dish has. Since my response variable is calories (which is a continuous variable), this problem would be considered regression. During my exploratory analysis, there seemed to be correlations between various nutition facts and the calorie amount, which is why I chose calories as my prediction variable. The metric that I decided to use to evaluate my model is R^2 because this indicated how well a model fits the data and what percentage of the variability the model captures. This is more informative than other evaluation metrics such as Mean Squared Error (MSE) and Root Mean Squared Error (RMSE) because MSE and RMSE only provides information about the magnitude of the model's prediction error without offering insight into the overall fit of the model. 

# Baseline Model
The baseline model I chose was linear regression because it can effectively model the linear relationships between the input features (nutrition facts) and the target variable (calories). The features used for this model include Carbohydrates and Proteins, which both have a positive linear relationship with calories. Both of these features are quantitative and nominal and neither are ordinal. The R^2 performance on this model was 0.8116 for the training set and 0.8077 for the testing set. This indicates that the model captures 80.77% of the variance in the calorie values on unseen data. I believe that my current model is fairly good at capturing most of the variance but there is still room for improvement through feature engineering. 

# Final Model
In order to improve upon the baseline model's 

The first feature that I added was an energy density variable, which takes the PDV values and multiplies by the respective amounts. Ex: 4 * Protien, 4 * Carbs, 9 * Total Fat. This is because this is similar to the equation of calculating macronutrients expect I am weighting the percents accordingly. Another feature that I added was the protein to carb ratio, which gaves an idea of which meals have more/less calories. I also added total fats because it had a linear relationship with calories as well. 

The modeling algorithm that I chose was Ridge because it handles linear relationships as well as multicollinearity. 

The best hyperparameters were {'model__alpha': 10, 'model__fit_intercept': True}

The final model's R^2 value was 0.9959, which means that 99.59% of the variance in the data is captured by my model, which is a significant improvement from my baseline. 
