# Recipe Recommender Application for Food Waste Reduction

## Overview
This project aims to help reduce food waste on the consumer level by using collaborative filtering methods to create a recipe recommender that allows users to input food items in their fridge that are about to expire and then outputs recipe recommendations based on the user's past recipe ratings that include that ingredient. 

## Business Problem: 
Food waste - or food that is fit for consumption but disposed of - is a major problem in the US with up to 40% of food produced going uneaten, according to [Harvard’s School of Public Health](https://www.hsph.harvard.edu/nutritionsource/sustainability/food-waste/). Further, there are environmental effects of food waste. 95% of uneaten food goes to landfills. This decomposing food produces methane gasses, which is a significant contributor to global warming and subsequently adds to climate change. The reduction of food waste could not only lead to savings costs for consumers, but also help curb contributors to climate change. 

By creating a food recommendation tool that takes into account what already exists in the consumer's fridge as well as that consumer's preferences, the hope is recipes will not only be relevant to the consumer, but also encourage them to use those ingredients rather than get rid of them. 

## Data Understanding 
Data for this project came from Kaggle and looks at consumer [reviews and ratings](https://www.kaggle.com/datasets/shuyangli94/food-com-recipes-and-user-interactions) of recipes from Food.com. For the purposes of this project, I used the RAW_recipes and RAW_interactions csv files to build new datasets. The original files are too large to add to Github and thus instructions on how to replicate my process can be found below.

### Data Instructions
I downloaded the RAW_recipes.csv and RAW_interactions.csv locally to my computer. These are large files and will take some time to open in a jupyter notebook. First, I cleaned the RAW_interactions.csv file in my juptyer notebook to include only recipes that had at least five reviews and users that had at least five reviews - you can follow the notebook entitled "reading_in_data.ipnyb" to find initial cleaning instructions. I saved off this new dataset as  user_reviews.csv. I then further cleaned the dataset in a notebook entitled "preprocessing_no_0s", which explores the presence of 0 ratings and eventually drops those rows and creates a new dataset of recipes with five reviews and users with five ratings. This dataset, which can be found in the git Data repository is called "user_reviews_no_zero.csv". This data was used for the remainder of the modeling and application. 

Next, I dealt with the RAW_interactions.csv in the jupyter notebook entitled "meta_data_workbook_2". In that notebook I cleaned the data to have usable ingredients columns, created new nuitrition facts columns, and used a function to subcategorize recipes based on their food type. The final metadata recipe dataset was then called recipes_subcat_cleaned.csv and can be found in the git Data repository. 

## Data Preparation
My final dataset included 537,267 reviews of 40,526 recipes from 17,096 users. For collaborative filtering purposes, I cleaned the data to include only user reviews from users that had 5 or more reviews and recipes with only 5 or more reviewers. Ratings initially ranged from 0-5, but upon further inspection, I found that 0 ratings were placeholders for unrated reviews and ranged from positive reviews, negative reviews, and reviews that indicated that the users never actully tried the meals. Because these 0 ratings made up only 2% of the dataset and because the reviews were unreliable, I dropped these values. Further exploration of the reviews using NLP methods with KNN clustering imputation could have helped classify the 0 ratings. However, that process would have lead to data leakage concerns and ultimately was dismissed. Once the 0 ratings were dropped, I had to further reduce the dataset to 5 or more reviews from 5 more reviewers. This resulted in aloss of 25% of recipes and 10% of users. 

![rating distribution](https://github.com/mboland23/Recipe-Recommender/blob/main/Images/rating_dist.png)

As mentioned, for the meta data dataset I did a fair bit of feature engineering to create nuitrition features and food type subcategories. You can see the exact process in the jupyter notebook entitled meta_data_workbook. 

The user data and recipe data are connected through unique recipe_id. 

## Models
I utilized collaborative filtering method to create the recommendation system. Collaborative filtering takes a user-to-user based approach where user profiles are created for like users. Then ratings for unrated recipes are predicted for a user based on these user profiles, i.e. the ratings of like users. The limitation of this approach is that users must have already reviewed recipes in order to have predicted ratings. A content based approach would need to be implemented to solve the cold start problem and allow new users to receive recommendations. Once these new users have reviewed a recipe they can be placed into the collaborative filtering model. 

To create recommendation systems I used the Surprise library. The performance of each of the models run is illustrated below. 

![model performance](https://github.com/mboland23/Recipe-Recommender/blob/main/Images/model_performance.png)
  
Once I determined the best model for my data, I created a recommendation function that takes in the user_id, the requested ingredient, any food types they may be looking to make, and the number of recipes. The tool then outputs the number of recipess containing that ingredient within the subset of food types based on predicted ratings that are calcuulated from that user's preferences compared to like users. An example is below. 
  
<Example of output from collaborative filtering model>

## Recommendation Tool
The collaborative filtering function created for users takes in four main inputs - the user ID, ingredient of interest, recipe type, and number of recipes. The function then outputs a given number of recipes that match the inputs for that user ID and that are predicted to be top rated. Below you can see two examples of the output of the function for the same user. The first is inputting two different ingredients and keeping the food type the same. In this case we use the subcategory of "any", which denotes any recipe (essentially no subcategory filtering". You can see the difference in the recommendations below.

First, we see the recommendations for chicken as the ingredient. 
  
![ingredient = chicken](https://github.com/mboland23/Recipe-Recommender/blob/main/Images/rec_chicken_any.png)

 Now, we see the recommendations for beef as the ingredient.
 
![ingredient = beef](https://github.com/mboland23/Recipe-Recommender/blob/main/Images/rec_beef_any.png)
 
Next, we can take a look at recommendations for the same ingredient, but that fall in different food type subcategories. There are 8 subcategories available to choose from: any (no subcategory), vegetarian, not vegetarian, beverage, dessert, low cal, low carb, and sugar free.
  
Here is a look at recommendations of tomato recipes for the same user as above with the food type subcategory as vegetarian. 

![food type = vegetarian](https://github.com/mboland23/Recipe-Recommender/blob/main/Images/rec_tomato_veg.png)

And here is a look at the recommendations fo tomato recipes for that user with the subcategory as not vegetarian.

![food type = not vegetarian](https://github.com/mboland23/Recipe-Recommender/blob/main/Images/rec_tomato_nonveg.png)

For more on the recommendation function, including the dictionary of ingredient variations that ensures recipes that have variations (diced tomato vs. tomato) are taken into account in the ingredient input, please look at the recommendation_function_workbook. 


## Conclusions 
The final recommendation system is able to predict ratings that are 0.56 points from true ratings, meaning it is fairly accurate in pulling recipes that the user would like to cook — a necessary element to encourage users to cook expiring food rather than waste the food. Further, the recommender app does not recommend recipes that the user has already reviewed — allowing the user to discover new recipes. It also prioritizes the best match (highest expected rating) recipes with the input ingredient for that user. This is especially important for future use. If the user does not like a recipe recommended, they may not come back to the app - running the risk of wasted food. Finally, the recommender system allows users to denote if they would like to make a certain type of meal. This is important for users that have meal limitations. 

While the recommendation application helps encourage the reduction of food waste at the household level, it does have limitations. First, since 79% of our original data comprised of reviews with 5 point rating, the recommender system skews to higher predicted ratings. This means that the difference between .01 predicted points at the higher end could be more significant than at lower levels. To remedy, one could repredict ratings of the original dataset using NLP. Further, the system does not take into account indiscriminate raters - or users that rate all recipes the same. This makes it more difficult to discern these user's preferences and thus their recommendations may not be as accurate. To fix this issue, we could again repredict their ratings based on the language of their reviews or follow up with more specific feedback. Finally, the collaborative filtering approach only works for users that have already reviewed recipes - new users cannot recieve predictions. This cold start probem could be remedied by recommending the highest rated recipes with a given ingredient to that user and asking them to review. They then could be added to the collaborative filtering pipeline. 

Overall, the application can help reduce food waste at the household level by allowing users to input ingredients that they currently have and delivering recipes that are relevant, matched to the user, and expected to be enjoyed. 
  
## Repository Structure




