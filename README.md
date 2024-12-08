# Recipes and Ratings

Megha Sharma (meghash@umich.edu) and Akanksha Rai (raiaka@umich.edu)

## Introduction

Our analysis uses two connected datasets: interactions and recipes which, together, provide insights into user engagement with recipes. The interactions dataset captures feedback on recipes including through ratings and reviews by people with a timestamp (date column) to mark when the interaction occured. Each row demonstrates the interaction between a user and a recipe. The recipes dataset includes information on the recipes themselves, including the name, preparation time in minutes, nutrional content, steps, ingredients, and even tags. To explore these datasets further, we merged them on recipe id, creating 83,193 rows of what we deemed observable data capturing user engagement and characteristics of recipes.

### Key Question

**How do recipe characteristics, like preparation time and ingredient composition relate to the healthiness of a recipe, and can we predict the number of calories in a recipe using these attributes?**

### Why Should You Care?

Food has a crucial role in navigating your health and the choices we make can lead to a healthy lifestyle if done so informatively. Understanding relationships between recipe characteristics and their true healthiness can help people determine what healthier cooking habits are, reducing the risk of disease (for example, diabetes). Knowing how ingredient composition of healthy recipes impact caloric content can be especially helpful for those who want to maximize the nutritional value of the calories they eat daily. Our insights are data-driven so that you can use them to make informed decisions on cooking habits and promote your well-being.

## Data Cleaning and Exploratory Data Analysis

The first step in our analyzation process was cleaning and interpreting the data. First, we took a a look at column types to determine if any conversions were necessary. Most columns were consistent with their type - columns such as id, minutes, contributor id, n_steps, n_ingredients - were all of integer data type in recipes, and in interactions, user is, recipe id, and the rating were also. In our second step, we took a look at the number of NaN values in each dataset. Because the number of NaN values for the entire dataset and the mean of NaN values was low for each column, we deemed this as unimportant to attend to.

Our next step included us binning minutes in the recipes dataset. On average, recipes take under 30 minutes to cook. However, we accounted for the fact that some recipes take longer to prepare than others. For example, in many bread recipes, it is important to prepare ingredients overnight. We set our limit to 1440 minutes, or 1 day, in preparation time. We chose to bin values a bit arbitrarily, using real-world scenarios. Our first bin was recipes that take up to 30 minutes in preparation time, as we deemed this to be the most common preparation time in our day-to-day lives and a quick recipe. The second bin was for 30-60 minutes, for moderately longer recipes. The bin edges grow as the number of minutes increases, as the amount of recipes with longer preparation times start to diminish. It accounts for a natural "slowdown" in extreme values, as recipes that take longer are not as common as recipes that require a shorter preparation time. We then removed recipes that did not fit into any of these bins, including recipes that took 0 minutes or recipes that took longer than 24 hours.

Our next step was creating new columns out of the given 'nutrition' column into the percent daily values (PDV %) of different nutrients, including calories, total fat, sugar, sodium, protein, saturated fat, and carbohydrates. We created a list of all the different nutrients, from calories to carbohydrates. Because 'nutrition' was of object type initially, we split the percentages on all commas, then used the values in the list as a key to set all the values. Finally, we changed the type for each new column to a float.

Through research, we determined the PDV percentages for each nutrient that was necessary to be considered a healthy recipe, and created a new column called 'healthy' which was set to one if the recipes fit the criteria as defined, otherwise the 'healthy' column was set to 0.

After cleaning our data, we merged the dataset, and created the final DataFrame for our analysis. Our total number of NaN values remained low. Because the context is uncertain, for example it would be nonsensical to impute 'tags' and 'description' using other values in the dataset, and similarly with other values such as the different type of nutrients, we once again decided to not impute most NaN values. However, we did impute the 'ratings' that were 0, as it is not within the scale of a rating, and represents missing data. By imputing it as NaN, we differentiated between valid ratings and missing data allowing us to maintain consistency.

This DataFrame yields 6177 healthy recipes!

| name                                             | min_bins   |   n_steps |   n_ingredients |   calories |   total fat (PDV%) |   sugar (PDV%) |   sodium (PDV%) |   protein (PDV%) |   saturated fat (PDV%) |   carbohydrates (PDV%) |
|:-------------------------------------------------|:-----------|----------:|----------------:|-----------:|-------------------:|---------------:|----------------:|-----------------:|-----------------------:|-----------------------:|
| creamy   vegan potato leek soup                  | (30, 60]   |        10 |               8 |      183   |                  3 |             10 |               0 |               13 |                      1 |                     11 |
| quick biscuit bread                              | (0-30]     |        11 |               5 |      124   |                 10 |              8 |              13 |                6 |                      9 |                      4 |
| russian  dressing  ww                            | (120, 180] |         7 |              11 |       56.5 |                  5 |             14 |               7 |                2 |                      2 |                      1 |
| skordy  new potatoes w rosemary lemon   olive oi | (30, 60]   |        14 |               6 |      209.7 |                 10 |              7 |               0 |                8 |                      4 |                     11 |
| creamy  mushroom soup                            | (30, 60]   |        13 |              10 |       99.3 |                  1 |              8 |               1 |               12 |                      0 |                      6 |



## Analysis Models
How does the relationship between ingredients and healthy recipes vary based on preparation time?

Through our exploratory analysis we looked at the relationship between ingredients and healthy recipes based on preparation time. 

Out of mere curiosity, we first decided to look at the most popular ingredients in healthy recipes. We thought that seeing this may help us determine if there may be a correlation between healthy recipes and certain ingredients.

<iframe
    src="assets/top_20_common.html"
    width="800"
    height="600"
    frameborder="0"
></iframe>

For healthy recipes, sugar tends to be a less popular ingredient, with only approximately ~13% of healthy recipes using it as an ingredient, going to show that healthy recipes do not avoid sugar entirely, but that it does align with the common perception of healthiness (specifically that sugar is not a healthy ingredient).

<iframe
    src="assets/nutritional-content-cooking-time.html"
    width="800"
    height="600"
    frameborder="0"
></iframe>

Here, we can see that healthy recipes that take less preparation time & have less variance between the percentage of total fat, sodium, protein, saturated fat, and carbohydrates. Total fat, sodium and carbohydrates having almost no variance when preparation time is equivalent to ~90 minutes, however, as preparation time increases, so does the variance between the nutrients' percentage of daily values.

### Interesting Aggregates

We were also curious about the change in nutritional content depending on the preparation time.

| min_bins   |   calories |
|:-----------|-----------:|
| (0-30]     |    347.861 |
| (30, 60]   |    445.806 |
| (60, 120]  |    558.003 |
| (120, 180] |    565.975 |
| (180, 360] |    573.442 |

If the preparation time of the ingredients is low, so is the amount of calories, which could be due to the fact that these foods are not being cooked or fried as much. Recipes that take between 180 and 360 minutes (3 to 6 hours) to prepare tend to have the most amount of calories, which could be because they require more ingredients compared to recipes with shorter and longer preparation times. However, when recipes take longer than 360 minutes to prepare, the average calorie content drops again (see below). 

| min_bins   |   n_ingredients |
|:-----------|----------------:|
| (0-30]     |         7.80665 |
| (30, 60]   |        10.0906  |
| (60, 120]  |        10.9732  |
| (120, 180] |        10.6002  |
| (180, 360] |        10.2368  |


Referencing the *Nutritional Content by Cooking Time Bin for Healthy Recipes* graph, we can see that the PDV percentage is decreasing for all nutrients (except for sugar). This may also indicate why slow-cooked dishes are less calorie-dense.

## Framing a Prediction Problem

So far, we can see that there is a correlation between ingredients and recipes based on recipe preparation time. The higher the number of ingredients, the higher the number of calories. This leads us to our prediction question... *can we predict the number of calories for healthy recipes?*

With calories being continuous variable, and the response variable (as we are aiming to predict calories), we know that we have a regression problem. Predicting the number of calories in a recipe requires the use of numerical features, such as the number of ingredients ('n_ingredients'), and nutrients (the PDV percentages, in particular) because we are producing a continuous output. 

The metric we chose to evaluate our model was MSE (Mean Squared Error). We chose MSE because it penalizes errors and aims to minimize large errors in our predictions. Specifically, it measures the average squared error between predictions and actual values of our response variable, calories.

## Baseline Model

For our baseline model, we used features from our **healthy_recipes** DataFrame: total fat (PDV%), sugar (PDV%), protein (PDV%), sodium (PDV%), and min_bins to predict *y*, our response variable ('calories'). The 4 nutrients were all quantitative values, whereas min_bins was the only categorical variable, specficially ordinal. We split the data into training and testing sets, using approximately 80% of the data for training and 20% of the data for testing as a starting point. We standardized all the numerical features to allow for a mean of 0 and a standard deviation of 1 to make the features comparable. Because min_bins is categorical, we one-hot encoded it, dropping the first column to avoid multicollinearity. Using a pipeline, we passed in these two processing steps to a linear regression model.

To evaluate the performance based on subsets of the data, we used cross validation to see how well the model generalizes to unseen data while reducing the chance of overfitting.

We wanted to evaluate this model using MSE -- which yielded a score of 3632.74. This score was extremely high, indicating that there was a large deviation froom the actual values. We believe this is not good for our current believe.

## Final Model

Testing akanksha
