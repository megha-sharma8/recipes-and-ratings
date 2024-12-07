# Recipes and Ratings

by Megha Sharma (meghash@umich.edu) and Akanksha Rai (raiaka@umich.edu)

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

After cleaning our data, we merged the dataset, and created the final DataFrame for our analysis. Our total number of NaN values remained low. Because the context is uncertain, for example it would be nonsensical to impute 'tags' and 'description' using other values in the dataset, and similarly with other values such as the different type of nutrients, we once again decided to not impute NaN values. Imputing values when there are so few could actually skew the accuracy on the test data in our prediction model.

This DataFrame yields 6177 healthy recipes!

## Analysis Models
Through our analysis we looked at the relationship between ingredients and healthy recipes based on preparation time. 

Out of mere curiosity, we first decided to look at the most popular ingredients in healthy recipes. Salt appeared to be most popular ingredient (to no one's surprise) as well as olive oil. Seeing this makes us determine that there may be a correlation between healthy recipes and certain ingredients. 

| name                                             |     id |   minutes |   contributor_id | submitted   | tags                              | nutrition                                |   n_steps | steps                             | description                                           | ingredients                                                                                                                                                                                                              |   n_ingredients | min_bins   |   calories |   total fat (PDV%) |   sugar (PDV%) |   sodium (PDV%) |   protein (PDV%) |   saturated fat (PDV%) |   carbohydrates (PDV%) |   healthy |   average_rating |
|:-------------------------------------------------|-------:|----------:|-----------------:|:------------|:----------------------------------|:-----------------------------------------|----------:|:----------------------------------|:------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------:|:-----------|-----------:|-------------------:|---------------:|----------------:|-----------------:|-----------------------:|-----------------------:|----------:|-----------------:|
| creamy   vegan potato leek soup                  | 343338 |        40 |           451456 | 2008-12-13  | ['lactose', '60-minutes-or-les... | [183.0, 3.0, 10.0, 0.0, 13.0, 1.0, 11.0] |        10 | ['heat olive oil in a 4-quart ... | adapted from alex jamieson's great american detox ... | ['olive oil', 'leeks', 'garlic cloves', 'russet potatoes', 'vegetable broth', 'white beans', 'fresh rosemary', 'salt and pepper']                                                                                        |               8 | (30, 60]   |      183   |                  3 |             10 |               0 |               13 |                      1 |                     11 |         1 |              4.5 |
| quick biscuit bread                              | 302399 |        20 |           213909 | 2008-05-06  | ['30-minutes-or-less', 'time-t... | [124.0, 10.0, 8.0, 13.0, 6.0, 9.0, 4.0]  |        11 | ['preheat oven to 400 degrees'... | this is a wonderful quick bread to make as an acom... | ['refrigerated biscuits', 'olive oil', 'mozzarella cheese', 'garlic salt', 'italian seasoning']                                                                                                                          |               5 | (0-30]     |      124   |                 10 |              8 |              13 |                6 |                      9 |                      4 |         1 |              5   |
| russian  dressing  ww                            | 276567 |       135 |           305531 | 2008-01-05  | ['time-to-make', 'course', 'pr... | [56.5, 5.0, 14.0, 7.0, 2.0, 2.0, 1.0]    |         7 | ['in small bowl or jar with ti... | i found this in the weight watchers complete cookb... | ['orange juice', 'reduced-calorie mayonnaise', 'green bell peppers', 'red bell peppers', 'tomato paste', 'plain nonfat yogurt', 'horseradish', 'onion', 'prepared mustard', 'chili powder', 'fresh ground black pepper'] |              11 | (120, 180] |       56.5 |                  5 |             14 |               7 |                2 |                      2 |                      1 |         1 |              4.5 |
| skordy  new potatoes w rosemary lemon   olive oi | 296983 |        35 |           718054 | 2008-04-08  | ['60-minutes-or-less', 'time-t... | [209.7, 10.0, 7.0, 0.0, 8.0, 4.0, 11.0]  |        14 | ['preheat oven to gas mark 8',... | i took this recipe from a vegan tastes of greece f... | ['new potatoes', 'lemon, juice of', 'lemon, zest of', 'rosemary', 'olive oil', 'garlic cloves']                                                                                                                          |               6 | (30, 60]   |      209.7 |                 10 |              7 |               0 |                8 |                      4 |                     11 |         1 |              5   |
| creamy  mushroom soup                            | 331715 |        35 |           446143 | 2008-10-20  | ['60-minutes-or-less', 'time-t... | [99.3, 1.0, 8.0, 1.0, 12.0, 0.0, 6.0]    |        13 | ['chop mushrooms finely', 'spr... | creamless but creamy, and easily adapted to vegan.... | ['baby portabella mushrooms', 'onion', 'white beans', 'mushroom broth', 'reduced fat margarine', 'white pepper', 'salt', 'bay leaf', 'garlic cloves', 'dried thyme']                                                     |              10 | (30, 60]   |       99.3 |                  1 |              8 |               1 |               12 |                      0 |                      6 |         1 |              5   |

<iframe
    src="assets/top_20_common.html"
    width="800"
    height="600"
    frameborder="0"
></iframe>

## Framing a Prediction Problem

## Baseline Model

## Final Model
