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


## Models


## Deployment: Application Creation



## Conclusions 



## Next Steps



## Repository Structure




