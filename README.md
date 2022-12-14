# Recipe Recommender Application for Food Waste Reduction

## Overview
This project aims to help reduce food waste on the consumer level by using collaborative filtering methods to create a recipe recommender that allows users to input food items in their fridge that are about to expire and then outputs recipe recommendations based on the user's past recipe ratings that include that ingredient. 

![food waste](https://github.com/mboland23/Recipe-Recommender/blob/main/Images/food_waste_image.jpeg)

## Business Problem 
Food waste - or food that is fit for consumption but disposed of - is a major problem in the US with up to 40% of food produced going uneaten, according to [Harvard’s School of Public Health](https://www.hsph.harvard.edu/nutritionsource/sustainability/food-waste/). Further, there are environmental effects of food waste. 95% of uneaten food goes to landfills. This decomposing food produces methane gasses, which is a significant contributor to global warming and subsequently adds to climate change.  The reduction of food waste could not only lead to savings costs for consumers, but also help curb contributors to climate change. 

While much of food waste comes at the macro level (transporation networks, restaurants and organizations), households prove to waste a substantial amount food - 1/3 of food obtained by households is wasted, according to [The Economist](https://www.economist.com/the-economist-explains/2016/08/28/why-wasting-food-is-bad-for-the-planet). By creating a food recommendation tool that takes into account what already exists in the consumer's fridge as well as that consumer's preferences, recipes will not only be relevant to the consumer, but also encourage consumers to use those ingredients rather than waste them.

## Data Understanding 
Data for this project came from Kaggle and looks at consumer [reviews and ratings](https://www.kaggle.com/datasets/shuyangli94/food-com-recipes-and-user-interactions) of recipes from Food.com. For the purposes of this project, I used the RAW_recipes and RAW_interactions csv files to build new datasets. The original files are too large to add to Github and thus instructions on how to replicate my process can be found below.

### Data Instructions
- Download the RAW_recipes.csv and RAW_interactions.csv locally to a folder called Data in your repository from the file section of [Kaggle](https://www.kaggle.com/datasets/shuyangli94/food-com-recipes-and-user-interactions) . 
- Follow the [reading_in_data notebook](https://github.com/mboland23/Recipe-Recommender/blob/main/working_notebooks/reading_in_data.ipynb) to clean the RAW_interactions.csv file to include only recipes that have at least five reviews and users with at least 5 reviews. The result of this notebook is a new, trimmed down data file called user_reviews.csv. Save this file locally in the same Data folder.
- Follow the [data-preprocessing](https://github.com/mboland23/Recipe-Recommender/blob/main/working_notebooks/data-preprocessing.ipynb) notebook which takes the user_reviews.csv and explores the presence of zero ratings. The resulting dataset drops the zeros, and trims recipes and users down to the 5 count threshold. The new data set is called user_reviews_no_zero 2.csv. This file is then used in the modeling and final appliction in the final_notebook. 

Next, I dealt with the meta data.
- Open the RAW_interactions.csv in the jupyter notebook entitled [meta_data_cleaning_workbook](https://github.com/mboland23/Recipe-Recommender/blob/main/meta_data_cleaning_workbook.ipynb). In that notebook I cleaned the data to have usable ingredients columns, created new nuitrition facts columns, and used a function to subcategorize recipes based on their food type. The final metadata recipe dataset was then saved off in the Data folder as recipes_subcat_cleaned 2.csv. This file  was used in the final notebook. 

## Data Preparation
My final dataset included 537,267 reviews of 40,526 recipes from 17,096 users. 

### User Review Data
For collaborative filtering purposes, I cleaned the data to include only user reviews from users that had 5 or more reviews and recipes with only 5 or more reviewers. Ratings initially ranged from 0-5, but upon further inspection, I found that 0 ratings were placeholders for unrated reviews and ranged from positive reviews, negative reviews, and reviews that indicated that the users never actully tried the meals. Because these 0 ratings made up only 2% of the dataset and because the reviews were unreliable, I dropped these values. Further exploration of the reviews using NLP methods with KNN clustering imputation could have helped classify the 0 ratings. However, that process would have lead to data leakage concerns and ultimately was dismissed. Once the 0 ratings were dropped, I had to further reduce the dataset to 5 or more reviews from 5 more reviewers. This resulted in aloss of 25% of recipes and 10% of users. 

![rating distribution](https://github.com/mboland23/Recipe-Recommender/blob/main/Images/rating_dist.png)

### Recipe Data
The recipe data - or metadata - contains all of the information regarding the recipes reviewed in the user_reviews dataset. This recipe data is used in the collaborative filtering process to help with subfiltering recommended recipes. 

The recipe data underwent substantial feature engineering - creating new nuitrition columns:
 - calories (#)
 - total fat (PDV)
 - sugar (PDV)
 - sodium (PDV)
 - protein (PDV)
 - saturated fat (PDV)
 - Carbohydrates(PDV)

It also breaks out the ingredients column, which was originally a string of ingredients into a list of ingredients.

Finally, I created a subcategorization function that labeled recipes on 8 different food types that would allow users to subset based on food type in the recommendation tool. The last three subcategorizations are taken from the new nuitrition columns. The recipe type subcategorizations are as follows (note: recipes can fall in multiple categories and all recipes fall under the 'any' subcategorization): 
 - any (no subcategorization)
 - vegetarian
 - not vegetarian
 - beverage
 - dessert
 - low carb
 - low cal
 - sugar free

For a detailed look at the manipulation of the recipe/meta data please refer to the [meta_data_cleaning_workbook](https://github.com/mboland23/Recipe-Recommender/blob/main/meta_data_cleaning_workbook.ipynb). The rest of this notebook uses the final csv file called "recipes_subcat_cleaned 2.csv" from this workbook. 

The user data and recipe data are connected through unique recipe_id. 

## Models
I utilized collaborative filtering method to create the recommendation system. Collaborative filtering takes a user-to-user based approach where user profiles are created for like users. Then ratings for unrated recipes are predicted for a user based on these user profiles, i.e. the ratings of like users. The limitation of this approach is that users must have already reviewed recipes in order to have predicted ratings. Another approach would need to be implemented to solve the cold start problem and allow new users to receive recommendations. Once these new users have reviewed a recipe they can be placed into the collaborative filtering model. 

To create recommendation systems I used the Surprise library. The performance of each of the models run is illustrated below. 

![model performance](https://github.com/mboland23/Recipe-Recommender/blob/main/Images/model_performance.png)
  
Once I determined the best model for my data, I created a recommendation function that takes in the user_id, the requested ingredient, any food types they may be looking to make, and the number of recipes. The tool then outputs the number of recipess containing that ingredient within the subset of food types based on predicted ratings that are calcuulated from that user's preferences compared to like users. An example is found in the section below. 


## Recommendation Tool
The collaborative filtering function created for users takes in four main inputs - the user ID, ingredient of interest, recipe type, and number of recipes. The function then outputs a given number of recipes that match the inputs for that user ID and that are predicted to be top rated. Below you can see two examples of the output of the function for the same user. The first is inputting two different ingredients and keeping the food type the same. In this case we use the subcategory of "any", which denotes any recipe - essentially no subcategory filtering. You can see the difference in the recommendations below.

First, we see the recommendations for chicken as the ingredient. 
  
![ingredient = chicken](https://github.com/mboland23/Recipe-Recommender/blob/main/Images/rec_chicken_any.png)

 Now, we see the recommendations for beef as the ingredient.
 
![ingredient = beef](https://github.com/mboland23/Recipe-Recommender/blob/main/Images/rec_beef_any.png)
 
Next, we can take a look at recommendations for the same ingredient, but that fall in different food type subcategories. There are 8 subcategories available to choose from: any (no subcategory), vegetarian, not vegetarian, beverage, dessert, low cal, low carb, and sugar free.
  
Here is a look at recommendations of tomato recipes for the same user as above with the food type subcategory as vegetarian. 

![food type = vegetarian](https://github.com/mboland23/Recipe-Recommender/blob/main/Images/rec_tomato_veg.png)

And here is a look at the recommendations fo tomato recipes for that user with the subcategory as not vegetarian.

![food type = not vegetarian](https://github.com/mboland23/Recipe-Recommender/blob/main/Images/rec_tomato_nonveg.png)

For more on the recommendation function, including the dictionary of ingredient variations that ensures recipes that have variations (diced tomato vs. tomato) are taken into account in the ingredient input, please look at the [recommendation_function_workbook](https://github.com/mboland23/Recipe-Recommender/blob/main/working_notebooks/recommendation_function_workbook.ipynb). 


## Conclusions 
### Evaluation
The final recommendation system is able to predict ratings that are 0.56 points from true ratings, meaning it is fairly accurate in pulling recipes that the user would like to cook — a necessary element to encourage users to cook expiring food rather than waste the food. Further, the recommender app does not recommend recipes that the user has already reviewed — allowing the user to discover new recipes. It also prioritizes the best match (highest expected rating) recipes with the input ingredient for that user. This is especially important for future use. If the user does not like a recipe recommended, they may not come back to the app - running the risk of wasted food. Finally, the recommender system allows users to denote if they would like to make a certain type of meal. This is important for users that have dietry restrictions. 

### Limitations
While the recommendation application helps encourage the reduction of food waste at the household level, it does have limitations. First, since 79% of our original data comprised of reviews with 5 point rating, the recommender system skews to higher predicted ratings. This means that the difference between .01 predicted points at the higher end could be more significant than at lower levels. To remedy, one could repredict ratings of the original dataset using NLP. Further, the system does not take into account indiscriminate raters - or users that rate all recipes the same. This makes it more difficult to discern these user's preferences and thus their recommendations may not be as accurate. To fix this issue, we could again repredict their ratings based on the language of their reviews or follow up with more specific feedback. Finally, the collaborative filtering approach only works for users that have already reviewed recipes - new users cannot recieve predictions. This cold start probem could be remedied by recommending the highest rated recipes with a given ingredient to that user and asking them to review. They then could be added to the collaborative filtering pipeline. 

### Next Steps
1. Determine whether 5 ratings are unique to our dataset or common to recipe reviews in general. NLP could help standardize ratings and reduce some of the high end skew of our data. 
2. Parse out preferences of indiscriminate reviewers through NLP. 
3. Solve cold start problem by creating a function that recommends the highest reviewed and rated recipes for a given ingredient. This would allow users to enter the pipeline for collaborative filtering. 

Overall, the application can help reduce food waste at the household level by allowing users to input ingredients that they currently have and delivering recipes that are relevant, matched to the user, and expected to be enjoyed. 

## Repository Structure
```
├── Images
│   ├── model_perfomance.png
│   ├── rating_dist.png
│   ├── rec_beef_any.png
│   ├── rec_chicken_any.png
│   ├── rec_tomato_nonveg.png
├── working_notebooks
│   ├── data_preprocessing.ipynb
│   ├── reading_in_data.ipynb
│   ├── recommendation_function_workbook.ipynb
├── .gitignore
├── LICENSE
├── README.md
├── final_notebook.ipynb
├── meta_data_cleaning_workbook.ipynb
└── modeling_workbook.ipynb
├── final_presentation.pdf
```



