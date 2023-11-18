# Project: User Activity on food.com

**Author**: Derrick Yao

This is a project for DSC80: Practice and Application of Data Science at UC San Diego.

## Introduction

The website [food.com](food.com) is an online social network site dedicated to the sharing of recipes and other food-related media. In this project, I will explore user interactions that occur on food.com, exploring the social media aspects of the website. 

Like other social media websites, food.com generates its content through users who share their own recipes. Users can post a recipe to the site, sharing the ingredients needed and the steps to take in order for others to create the dish themselves. In addition, users can leave ratings and reviews on other users' recipes, telling people how much they loved (or didn't!) the recipe.

Since users are the backbone of food.com's recipe collection, it's in their interests to determine what factors keep users active; more recipes posted means more activity and clicks on the website. In this analysis, I'll examine one factor - average ratings - and how it affects how active an user is.

#### Datasets

This analysis is conducted using two provided datasets, which I'll refer to as the `interaction` dataset and the `recipe` dataset.

The `recipe` dataset contains information on recipes posted to food.com. Each observation within this dataset is one recipe (i.e. one page on the website). This dataset includes information about how the recipe was posted on the website (e.g. author, date submitted, tags), what the recipe is designed to make (e.g. recipe name, nutrition info, tags), and how to actually make the recipe (e.g. steps, ingredients).

The `interaction` dataset contains information on reviews left on recipes. Each observation within this dataset is one review. This dataset includes identifying information (user, recipe, date) and review information (rating, review).

## Assessment of Missingness

## Cleaning and EDA

#### Data Cleaning

