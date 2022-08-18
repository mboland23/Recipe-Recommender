# Recipe Recommender Application for Food Waste Reduction

## Overview
This project aims to help reduce food waste on the consumer level by using collaborative filtering methods to create a recipe recommender that allows users to input food items in their fridge that are about to expire and then outputs recipe recommendations based on the user's past recipe ratings that include that ingredient. 

## Business Problem: 
Food waste - or food that is fit for consumption but disposed of - is a major problem in the US with up to 40% of food produced going uneaten, according to [Harvard’s School of Public Health](https://www.hsph.harvard.edu/nutritionsource/sustainability/food-waste/). Further, there are environmental effects of food waste. 95% of uneaten food goes to landfills. This decomposing food produces methane gasses, which is a significant contributor to global warming and subsequently adds to climate change. The reduction of food waste could not only lead to savings costs for consumers, but also help curb contributors to climate change. 

By creating a food recommendation tool that takes into account what already exists in the consumer's fridge as well as that consumer's preferences, the hope is recipes will not only be relevant to the consumer, but also encourage them to use those ingredients rather than get rid of them. 

## Data Understanding 
Data for this project came from Kaggle and looks at consumer [reviews and ratings](https://www.kaggle.com/datasets/shuyangli94/food-com-recipes-and-user-interactions) of recipes from Food.com. For the purposes of this project, I used the RAW_recipes and RAW_interactions csv files to build new datasets. The original files are too large to add to Github and thus instructions on how to replicate my process can be found below.

Data Instructions
I downloaded the RAW_recipes.csv and RAW_interactions.csv locally to my computer. These are large files and will take some time to open in a jupyter notebook. First, I cleaned the RAW_interactions.csv file in my juptyer notebook to include only recipes that had at least five reviews and users that had at least five reviews - you can follow the notebook entitled "reading_in_data.ipnyb" to find initial cleaning instructions. I saved off this new dataset as  user_reviews.csv. I then further cleaned the dataset in a notebook entitled "preprocessing_no_0s", which explores the presence of 0 ratings and eventually drops those rows and creates a new dataset of recipes with five reviews and users with five ratings. This dataset, which can be found in the git Data repository is called "user_reviews_no_zero.csv". This data was used for the remainder of the modeling and application. 

Next, I dealt with the RAW_interactions.csv in the jupyter notebook entitled "meta_data_workbook_2". In that notebook I cleaned the data to have usable ingredients columns, created new nuitrition facts columns, and used a function to subcategorize recipes based on their food type. The final metadata recipe dataset was then called recipes_subcat_cleaned.csv and can be found in the git Data repository. 

## Data Preperation
My final dataset included data from XX recipes with reviews from XX users. For collaborative filtering purposes, I cleaned the data to include only user reviews from users that had 5 or more reviews and recipes with only 5 or more reviewers. Ratings initially ranged from 0-5, but upon further inspection, I found that 0 ratings were placeholders for unrated reviews and ranged from positive reviews, negative reviews, and reviews that indicated that the users never actully tried the meals. Because these 0 ratings made up only 2% of the dataset and because the reviews were unreliable, I dropped these values. Further exploration of the reviews using NLP methods with KNN clustering imputation could have helped classify the 0 ratings. However, that process would have lead to data leakage concerns and ultimately was dismissed. Once the 0 ratings were dropped, I had to further reduce the dataset to 5 or more reviews from 5 more reviewers. This resulted in aloss of 25% of recipes and 10% of users. 

As mentioned, for the meta data dataset I did a fair bit of feature engineering to create nuitrition features and food type subcategories. You can see the exact process in the jupyter notebook entitled meta_data_workbook. 

The user data and recipe data are connected through unique recipe_id. 

## Models
I utilized collaborative filtering method to create the recommendation system. Collaborative filtering takes a user-to-user based approach where user profiles are created for like users. Then ratings for unrated recipes are predicted for a user based on these user profiles, i.e. the ratings of like users. The limitation of this approach is that users must have already reviewed recipes in order to have predicted ratings. A content based approach would need to be implemented to solve the cold start problem and allow new users to receive recommendations. Once these new users have reviewed a recipe they can be placed into the collaborative filtering model. 

To create recommendation systems I used the Surprise library. The performance of each of the models run is illustrated below. 

<RMSE graph for all the models> 
  
Once I determined the best model for my data, I created a recommendation function that takes in the user_id, the requested ingredient, any food types they may be looking to make, and the number of recipes. The tool then outputs the number of recipess containing that ingredient within the subset of food types based on predicted ratings that are calcuulated from that user's preferences compared to like users. An example is below. 
  
<Example of output from collaborative filtering model>

## Deployment: Application Creation



## Conclusions 



## Next Steps



## Repository Structure




