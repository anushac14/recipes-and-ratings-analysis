# Intoduction
This project investigates what types of recipes tend to have the most calories. This is incredibly important because it can help individuals make more informed dietary choices and can aid in meal planning and general nutritional awarness. Learning about the relationships between nutritional information and calories can empower people to maintain a healthier and more balanced lifestyle.

The data used in this analysis consists of scrapped recipe reviews from the website food.com. It contains 23,4429 rows and 17 columns, with each row containing a review for a particular recipe. The two columns that were most relavent include: 

- **`Nutrition`**: Consists of nutrient information such as number of calories and the Percentage of Daily Value (PDV) for total fat, sugar, sodium, protein, saturated fat, and carbohydrates
- **`Tags`**: The descriptor tags that are present for each recipe. The tags that I was particularly interested in include breakfast, lunch, dinner, brunch, main-dish

# Data Cleaning and Exploratory Data Analysis

|     id | name                                              | tags                  |   avg_rating |   Calories |   Total Fat |   Sugar |   Sodium |   Protein |   Saturated Fat |   Carbohydrates |
|-------:|:--------------------------------------------------|:----------------------|-------------:|-----------:|------------:|--------:|---------:|----------:|----------------:|----------------:|
| 324496 | chocolate spritz cookies                          | desserts              |            5 |       47.1 |           4 |      10 |        0 |         1 |               8 |               1 |
| 287392 | banana raspberry slush                            | 5-ingredients-or-less |            5 |      229.9 |           2 |     165 |        1 |         8 |               2 |              18 |
| 414099 | scallops with garlic bread crumbs weight watchers | 15-minutes-or-less    |            5 |      152.6 |           6 |       1 |       38 |        36 |              10 |               3 |
| 476734 | peppery sweet oven roasted salmon                 | taste-mood            |            5 |      123.8 |           5 |      16 |        2 |        34 |               3 |               1 |
| 285338 | healthier chicken marsala                         | 60-minutes-or-less    |            5 |      360.1 |          15 |       9 |       18 |        56 |              15 |               4 |

This histogram displays the distribution of calories and specifically shows the count of the number of recipes per 100 calorie increment. Based on the graph, it is clear that there is a right skew, with many of the dishes being within the range of 0-600 calories, which makes sense as the average amount of calories that a meal fits within that range. This doesn't directly answer the question however it does give us an idea of what the typically amount of calories a dish has in this dataset.

This graph plots the relationship between the amount of carbohydrates in a dish to the amount of calories the dish has. It is clear that there is a linear relationship between these variables, as can be seen by the trend line, which indicates that foods with higher carbohydrates tend to also have more calories.

This pivot table shows the average amount of calories, fat, sugar, and carbohydrates from various types of meals such as a main meal (like dinner), breakfast, lunch, dinner and appetizers. It is interesting to note that the main dish has a significantly higher calorie count (about 100 more) than lunch even though those meals tend to be around the same. It also has a higher total fat amount. This indicates that there are a few distinguishing factors that make certain meals more caloric than others.

I did not choose to impute any missing values because the amount that was missing was relatively marginal that those rows could simply be dropped

# Framing a Prediction Problem
Using nutritional facts, can we predict the amount of calories a dish has?

# Baseline Model
Since I am predicting the number of calories, my problem is a regression one. I chose calories as my response variable because there are many factors (namely nutrion facts) that have a strong correlation with calories and can help predict the calorie amount more accurately. The metric that I chose to evaluate my model is R^2 because it is a good indicator of how well the model fits the data and how much of the variability the model captures. This is more informative than MSE or RMSE because those only provide information about the average error of the data and not the overall fit

The model that I chose was linear regression. The features that I included in my model are Carbohydrates and Proteins. Both are quantitative and nominal and neither are ordinal. The performance of my model was 0.7921, which means the model captured 79.21% of the variance in the data. This model is relatively pretty good as it captures more of the variance but there is still room for improvement

# Final Model
The first feature that I added was an energy density variable, which takes the PDV values and multiplies by the respective amounts. Ex: 4 * Protien, 4 * Carbs, 9 * Total Fat. This is because this is similar to the equation of calculating macronutrients expect I am weighting the percents accordingly. Another feature that I added was the protein to carb ratio, which gaves an idea of which meals have more/less calories. I also added total fats because it had a linear relationship with calories as well. 

The modeling algorithm that I chose was Ridge because it handles linear relationships as well as multicollinearity. 

The best hyperparameters were {'model__alpha': 10, 'model__fit_intercept': True}

The final model's R^2 value was 0.9959, which means that 99.59% of the variance in the data is captured by my model, which is a significant improvement from my baseline. 

