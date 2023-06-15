# Predicting Recipe Calories using Features from the Recipes Dataset

by Kenny Tran (kat009@ucsd.edu)


---

## Framing the Problem

The prediction problem will be predicting recipe **calories** using features from the **Recipes** Dataset used in Project 3 <a href="https://kennyyyyt.github.io/KT/">here</a> and a **linear regression** model. We are choosing the response variable to be calories because there are many reasons why it would be useful to predict it: health awareness, meal planning, tracking progress, etc. Our metric being used to evaluate our model will be **Root Mean Squared Error (RMSE)** because of our linear regression model, and it uses the same scale as the original Recipes dataset. The variables known before we make our baseline model are all of the same ones after the **Cleaning and EDA** section from <a href="https://kennyyyyt.github.io/KT/">Project 3</a> except for calories (located as the first item of each list in the variable **nutrition**).



---

## Baseline Model

To create our baseline model, the nominal categorical variables **review** and **tags** as well as the quantitative variable **n_ingredients** will be transformed into features to help train it. **review** is transformed by **FunctionTransformer()**, in which it contains a function that calculates the word lengths of each review for the recipes. **tags** is also transformed by **FunctionTransformer()**, in which it contains a function that calculates the length of each list in tags, or the number of tags for each recipe. **n_ingredients** is left as it is since we aren't looking to transform the numerical variables in the baseline model. These variables were pre-processed and all put in a **Pipeline()** containing **LinearRegression()**. After the pipeline was fit and predicted y-values that were used to calculate 50 different testing errors, the average **RMSE** of those errors was **597 calories**. This value for the metric isn't good considering 597 calories is a significant amount to be off by, so there's a lot of room to improve during the Final Model stage.

---

## Final Model

**Features:**
In the final model, another feature is made from **tags**, one from **nutrition**, and **n_ingredients** is transformed into a new feature. Note that the two features made from **tags** and **review** in the baseline model remains in the final model. The new feature from **tags** is from a custom binary function that is passed through 
**FunctionTransformer()**. This function returns 1 if the tags list for a specific recipe contains "desserts" and doesn't contain "dietary", else 0 is returned. This is meant to associate recipes that are seen as more "unhealthy" with a different value since this specific combination of tags would usually mean a recipe is unhealthy (note that it won't account for all unhealthy recipes though, just the non-dietary desserts). The new feature that is made from **nutrition** is also from a custom function passed through **FunctionTransformer()**. This function extracts the "total_fat" from each list in **nutrition**, which would help predict calories since total fat is very calorie-dense, making up a lot of calories in each recipe. **n_ingredients** is transformed by the StandardScalar() in order to make a standardized feature which makes it easier to compare to other features, thus helping the model's ability to predict.


**Model:**
The model is still **LinearRegression()** using the pipeline function. But hyperparameters need to be chosen for the model in order to improve it even further. The ones that best fit the model were **fit_intercept=True** and **copy_X=True**. They were selected by using **GridSearchCV()** with 5 folds. After creating a final model with these hyperparameters, our **RMSE** now comes out to be about **254 calories**! Even if the baseline model's metric was split in half, the final model would still have a lower RMSE!

---

## Fairness Analysis

**Groups:**
In the fairness analysis, one group is **recipes with more than 10 ingredients**, and the other group is **recipes with 10 or less ingredients**. 

**Evaluation Metric:**
RMSE

**Null Hypothesis:**
Our model is fair. Its RMSE for recipes with over and less than or equal to 10 ingredients are roughly the same, and any differences are due to random chance.

**Alternative Hypothesis:**
Our model is unfair. Its RMSE's for the group with recipes over 10 ingredients and the group with recipes less than or equal to 10 ingredients are different.

**Test Statistic:**
Absolute difference in RMSE

**Significance Level:**
0.05

**Conclusion:**
After performing a fairness analysis using a permutation test, the resulting p-value ended up being 0.73. At a 5% significance level, we fail to reject the null hypothesis. Meaning, our model is fair. Its RMSE for recipes with over 10 ingredients compared to less than or equal to 10 ingredients are roughly the same, and any differences are due to random chance.

---
