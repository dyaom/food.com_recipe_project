# Project: User Activity on food.com

**Author**: Derrick Yao

This is a project for DSC80: Practice and Application of Data Science at UC San Diego.

## Introduction

The website [food.com](food.com) is an online social network site dedicated to the sharing of recipes and other food-related media. In this project, I will explore user interactions that occur on food.com, exploring the social media aspects of the website. 

Like other social media websites, food.com generates its content through users who share their own recipes. Users can post a recipe to the site, sharing the ingredients needed and the steps to take in order for others to create the dish themselves. In addition, users can leave ratings and reviews on other users' recipes, telling people how much they loved (or didn't!) the recipe.

Since these recipe authors are the backbone of food.com's recipe collection, it's in their interests to determine what factors keep authors active; more recipes posted means more activity and clicks on the website. In this analysis, I'll examine one factor - average ratings - and how it affects how active an user is.

### Datasets

This analysis is conducted using two provided datasets, which I'll refer to as the `interaction` dataset and the `recipe` dataset.

The `recipe` dataset contains information on recipes posted to food.com. Each observation within this dataset is one recipe (i.e. one page on the website). This dataset includes information about how the recipe was posted on the website (e.g. author, date submitted, tags), what the recipe is designed to make (e.g. recipe name, nutrition info, tags), and how to actually make the recipe (e.g. steps, ingredients).

The `interaction` dataset contains information on reviews left on recipes. Each observation within this dataset is one review. This dataset includes identifying information (user, recipe, date) and review information (rating, review).

In order to generate an initial dataset for the analysis, I joined the two datasets into one through a left merge onto the `recipe` dataset. I additionally replaced ratings of 0 with `nan` values to indicate that the user did not leave a rating, and I added a column with the average rating for every recipe.

## Assessment of Missingness

For this section of the analysis, I will look at the missing ratings (as described in the Datasets section) within this dataset.

### NMAR analysis

The ratings data are most likely not missing at random (NMAR); that is, whether the values are missing depends on the missing value itself. Users tend to be more likely to post very positive (5) or very negative (1) ratings across all fields where reviews are collected; as a result, less extreme ratings tend to go unreported.

Some examples of reviews paired with missing ratings include:

- "Just asking...How can this be vegan if using eggs? Vegetarian, maybe, but not vegan. Am I missing something like recipe should read egg substitutes and not just eggs? Thanks for listening, more thanks for responding. :)"
- "I haven't made this, but we called this opekansa. I haven't had this for many years."
- "I am not star rating this because the cabbage was not grilled but cooked in the oven. It's best to use the technique indicated in the recipe. Tasted good though!"

As can be seen, many missing ratings originate from reviews that didn't actually prepare the recipe, or from reviews who didn't express any strong feeling about the dish itself.

### Missingness Dependency

While it may be impossible to fully recover the missing ratings from the reviews above, the reviews do encode some information that differs between present and missing reviews.

I compare the length of the review among present and missing ratings:

<iframe src="assets/fig_miss1.html" width=600 height=450 frameBorder=0></iframe>

The graph clearly shows that reviews with missing ratings tend to be shorter than those with present ratings. I ran a difference of means permutation test on these two populations; with a p-value of 0 in 10,000 iterations, it is almost certain that rating missingness depends on the review's length.

In addition, this project required that I present a feature of the data that rating missingness was not dependent on. After searching through the features, I discovered that rating missingness was generally independent of the number of minutes it took to prepare the recipe. I compared the number of minutes among present and missing ratings (here, I plot only recipes that take less than one day to make):

<iframe src="assets/fig_miss2.html" width=600 height=450 frameBorder=0></iframe>

Although this distribution might also appear visibly different, a difference of means permutation test on the populations gave a p-value of 0.1226. As this p-value is relatively high, I fail to establish dependence between rating missingness and minutes to prepare.

(Oddly enough, this was the only feature in the data I could find that rating missingness wasn't dependent on! I tried a variety of other features, ranging from those I found likely to correlate - average rating, number of steps, date reviewed - to those I thought would certainly not correlate - numerical recipe ID, length of the recipe's name. Every statistic I examined other than minutes to prepare correlated with a p-value of 0 in 1000 iterations.)

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
 

Since the project assigned by this course requires a permutation test, I categorize the authors into two groups based on number of recipes. The first group contains authors with 10 or more recipes; I count these as the highly active authors. The second group contains authors with less than 10 recipes; I count these as "infrequent" members.

### Bivariate Analysis

I start by comparing the two methods of evaluating the average rating. I take the difference between the values to check for any noticeable patterns:

<iframe src="assets/fig_avgdiff.html" width=600 height=450 frameBorder=0></iframe>

The vast majority of recipes have no difference between the averages; of those that do, the differences do not appear to be biased in any direction. From this, I conclude that it's safe to use either method without losing any information; I'll use the recipe-wise average past this point.

I then compare this average among the highly active authors and the infrequent authors:

<iframe src="assets/fig_avgdist.html" width=600 height=450 frameBorder=0></iframe>

The two distributions look visibly different; the distribution of highly active authors is much smoother than the distribution of infrequent authors. This is expected from the nature of the two categories. Highly active authors have more recipes, so they get more reviews, which allows a finer amount of detail in the spread. Infrequent authors, on the other hand, will get less reviews; this makes the average rating an average of few integers, which restricts the spread of average ratings.

While the median is visibly different, this is also an artifact of the shape of the distributions. The difference in means will require a proper hypothesis test to draw any conclusions about.

### Univariate Analysis

<iframe src="assets/fig_recdist.html" width=600 height=450 frameBorder=0></iframe>

The distribution of number of recipes is skewed right. Most authors share only a few recipes, while some authors share much larger amounts; the most active user in this dataset has published nearly a thousand distinct recipes. My cutoff of 10 recipes splits this sample roughly 10:1 between infrequent and highly active authors.

<iframe src="assets/fig_ratdist.html" width=600 height=450 frameBorder=0></iframe> 

The distribution of average ratings by author is similar to the bivariate figure above. Many authors have only received a single review, and so there are large spikes at the integer ratings (1, 2, 3, 4, 5). There are smaller spikes at the halfway points (1.5, 2.5, 3.5, 4.5), and a small number of authors make up the rest of the spread in between.

### Interesting Aggregates

I aggregate the new dataframe , which divides the sample into authors who have published many recipes and authors who have not. While the num_recipes and log_recipes columns are expected, the aggregate table shows that authors who have published many recipes tend to have a slightly higher average rating than those who don't.

|   ('num_recipes', 'count') |   ('num_recipes', 'mean') |   ('avg_rating', 'count') |   ('avg_rating', 'mean') |
|---------------------------:|--------------------------:|--------------------------:|-------------------------:|
|                      13813 |                   1.93383 |                     13813 |                  4.54111 |
|                       1098 |                  49.6002  |                      1098 |                  4.61084 |

## Hypothesis Testing

I wish to analyze whether authors who recieve higher average ratings tend to publish more recipes. In the context of the above preparation, this becomes the following question: Do highly active authors tend to recieve higher average ratings (recipe-wise) than infrequent authors do?

To recap, I've defined a "highly active user" as one who's published at least 10 recipes within this dataset, and an "infrequent user" as one who's published less than 10 recipes.

I propose the following hypotheses:

**Null hypothesis**: The average rating for highly active authors is equal to the average rating for infrequent authors.
**Alternative hypothesis**: The average rating for highly active authors is greater than the average rating for infrequent authors.

I will conduct a permutation test on the difference of means between the two samples. As per tradition, I use a significance level of 0.05.


<iframe src="assets/fig_permutation.html" width=600 height=450 frameBorder=0></iframe>

In the above graph, I compare the distribution of experimentally obtained differences of means (in blue) to the observed difference (red line). It is visually clear that the experimental value rarely exceeds the observed value. The observed difference in average ratings was 0.0697. By running 100,000 iterations of this permutation test, I obtained a p-value of 0.00035.

### Conclusion

With a p-value this small, it is extremely likely that there is a true difference between the average ratings of highly active authors and of infrequent authors.

Although this may be a statistically significant difference, I make two observations on the effect itself:

1. This is a somewhat small effect size. An effect size of about 0.07 corresponds to about 7% of users changing their review by one point. However, the significance of this effect size ultimately depends on how it compares to other factors.
2. Although I propose in the introduction that how active an user is may depend on their average rating, this observational analysis is not sufficient to prove causality. It is also possible that the direction of causality points in the other way, or that some confounding factor affects both average rating and number of recipes.