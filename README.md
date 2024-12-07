# Recipes and Ratings

by Megha Sharma (meghash@umich.edu) and Akanksha Rai (FILL ME)

## Introduction

Our analysis uses two connected datasets: interactions and recipes which, together, provide insights into user engagement with recipes. The interactions dataset captures feedback on recipes including through ratings and reviews by people with a timestamp (date column) to mark when the interaction occured. Each row demonstrates the interaction between a user and a recipe. The recipes dataset includes information on the recipes themselves, including the name, preparation time in minutes, nutrional content, steps, ingredients, and even tags. To explore these datasets further, we merged them on recipe id, creating 83,193 rows of what we deemed observable data capturing user engagement and characteristics of recipes.

### Key Question

**How do recipe characteristics, like preparation time and ingredient composition relate to the healthiness of a recipe, and can we predict the number of calories in a recipe using these attributes?**

### Why Should You Care?

Food has a crucial role in navigating your health and the choices we make can lead to a healthy lifestyle if done so informatively. Understanding relationships between recipe characteristics and their true healthiness can help people determine what healthier cooking habits are, reducing the risk of disease (for example, diabetes). Knowing how ingredient composition of healthy recipes impact caloric content can be especially helpful for those who want to maximize the nutritional value of the calories they eat daily. Our insights are data-driven so that you can use them to make informed decisions on cooking habits and promote your well-being.

## Data Cleaning and Exploratory Data Analysis

The first step in our analyzation process was cleaning and interpreting the data. First, we took a a look at column types to determine if any conversions were necessary. Most columns were consistent with their type -  columns such as id, minutes, contributor id, n_steps, n_ingredients - were all of integer data type in recipes, and in interactions, user is, recipe id, and the rating were also. 

Our next step included us binning minutes in the recipes dataset. On average, recipes take under 30 minutes to cook. However, we accounted for the fact that some recipes take longer to prepare than others. For example, in many bread recipes, it is important to prepare ingredients overnight. We set our limit to 1440 minutes, or 1 day, in preparation time. We chose to bin values a bit arbitrarily, using real-world scenarios. Our first bin was recipes that take up to 30 minutes in preparation time, as we deemed this to be the most common preparation time in our day-to-day lives and a quick recipe. The second bin was for 30-60 minutes, for moderately longer recipes. The bin edges grow as the number of minutes increases, as the amount of recipes with longer preparation times start to diminish. It accounts for a natural "slowdown" in extreme values, as recipes that take longer are not as common as recipes that require a shorter preparation time. We then removed recipes that did not fit into any of these bins, including recipes that took 0 minutes or recipes that took longer than 24 hours.

Our next step was creating new columns out of the given 'nutrition' column into the percent daily values (PDV %) of different nutrients, including calories, total fat, sugar, sodium, protein, saturated fat, and carbohydrates.

Through research, we determined the PDV percentages for each nutrient that was necessary to be considered a healthy recipe.

Through our analysis we looked at the relationship between ingredients and healthy recipes based on preparation time. 

<iframe
    src="assets/top_20_common.html"
    width="800"
    height="600"
    frameborder="0"
></iframe>

## Framing a Prediction Problem

## Baseline Model

## Final Model
