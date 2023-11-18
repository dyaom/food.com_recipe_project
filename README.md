# Project: User Activity on food.com

**Author**: Derrick Yao

This is a project for DSC80: Practice and Application of Data Science at UC San Diego.

## Introduction

The website [food.com](food.com) is an online social network site dedicated to the sharing of recipes and other food-related media. In this project, I will explore user interactions that occur on food.com, exploring the social media aspects of the website. 

Like other social media websites, food.com generates its content through users who share their own recipes. Users can post a recipe to the site, sharing the ingredients needed and the steps to take in order for others to create the dish themselves. In addition, users can leave ratings and reviews on other users' recipes, telling people how much they loved (or didn't!) the recipe.

Since users are the backbone of food.com's recipe collection, it's in their interests to determine what factors keep users active; more recipes posted means more activity and clicks on the website. In this analysis, I'll examine one factor - average ratings - and how it affects how active an user is.

### Datasets

This analysis is conducted using two provided datasets, which I'll refer to as the `interaction` dataset and the `recipe` dataset.

The `recipe` dataset contains information on recipes posted to food.com. Each observation within this dataset is one recipe (i.e. one page on the website). This dataset includes information about how the recipe was posted on the website (e.g. author, date submitted, tags), what the recipe is designed to make (e.g. recipe name, nutrition info, tags), and how to actually make the recipe (e.g. steps, ingredients).

The `interaction` dataset contains information on reviews left on recipes. Each observation within this dataset is one review. This dataset includes identifying information (user, recipe, date) and review information (rating, review).

In order to generate an initial dataset for the analysis, I joined the two datasets into one through a left merge onto the `recipe` dataset. I additionally replaced ratings of 0 with `nan` values to indicate that the user did not leave a rating, and I added a column with the average rating for every recipe.

## Assessment of Missingness

## Cleaning and EDA

### Data Cleaning

I start by cleaning the dataset:

1. First, I start by dropping most of the recipe information. Since my focus in this analysis is on the interactions between users, the actual recipe info is nothing but dead weight slowing down any analysis. I keep the columns from the `interaction` dataset, but drop the majority of non-website information from the `recipe` dataset.
2. I drop recipes without any ratings. Only one such "orphan recipe" exists in the dataset.
3. I drop reviews without ratings. Since these missing ratings are NMAR, attempting to impute the data will not produce more accurate results than simply removing the missing entries from the dataset. 
4. I construct a new dataset, where each observation within the dataset is one author. This dataset will include columns:
  - Number of recipes posted by the author  
  - Average rating given to the author

At this stage in the analysis, I include two ways of measuring the "average rating":  
  - Review-wise average: I take the average rating across all reviews. For example, if recipe A has eight reviews and recipe B has one, then the average will be dominated by reviews from Recipe A, and each review will have equal weight.  
  - Recipe-wise average: I take the average rating across recipes. In the above example, I would take the average of the average ratings of recipes A and B; the average will be equally influenced by the two recipes, but the review on recipe B will have much more weight than the reviews on recipe A.  
 

Since the project assigned by this course requires a permutation test, I categorize the authors into two groups based on number of recipes. The first group contains authors with 10 or more recipes; I count these as the highly active authors. The second group contains authors with less than 10 recipes; I count these as "inactive" members.

### Bivariate Analysis

I start by comparing the two methods of evaluating the average rating. I take the difference between the values to check for any noticeable patterns:

<iframe src="assets/fig_avgdiff.html" width=600 height=450 frameBorder=0></iframe>

The vast majority of recipes have no difference between the averages; of those that do, the differences do not appear to be biased in any direction. From this, I conclude that it's safe to use either method without losing any information; I'll use the recipe-wise average past this point.

I then compare this average among the highly active authors and the inactive authors:

<iframe src="assets/fig_avgdist.html" width=600 height=450 frameBorder=0></iframe>

The two distributions look visibly different; the distribution of highly active users is much smoother than the distribution of inactive users. This is expected from the nature of the two categories. Highly active users have more recipes, so they get more reviews, which allows a finer amount of detail in the spread. Inactive users, on the other hand, will get less reviews; this makes the average rating an average of few integers, which restricts the spread of average ratings.

While the median is visibly different, this is also an artifact of the shape of the distributions. The difference in means will require a proper hypothesis test to draw any conclusions about.

### Univariate Analysis

