# Food.com Recipe Analysis
## Introduction
This project dives into the world of cooking by taking a look at datasets consisting of recipes and their respective ratings. There are many recipes out there from the finest ones curated to every detail to those created from quickly throwing ingredients together. Some people do not mind spending hours or days of their time preparing a meal, but is it really worth the time and effort? With such an extensive dataset, we will investigate the correlation between the cooking time and average rating of recipes to see if recipes that take more time are rated higher than the recipes that take less time.

### Datasets Used
We will be looking at two datasets collected from food.com containing recipes and ratings posted since 2008.

This first dataset is for recipes. Recipes contains 83,782 rows for each recipe name and 12 columns of the following descriptions:
|Column	                 |Description|
|---                     |---        |
|`'name'	`              |Recipe name|
|`'id'`	                 |Recipe ID|
|`'minutes'`	           |Minutes to prepare recipe|
|`'contributor_id'`	     |User ID who submitted this recipe|
|`'submitted'`	         |Date recipe was submitted|
|`'tags'`	               |Food.com tags for recipe|
|`'nutrition'`	         |Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein    (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value”|
|`'n_steps'`	           |Number of steps in recipe|
|`'steps'`	             |Text for recipe steps, in order|
|`'description'`	       |User-provided description|
|`'ingredients'`	       |Text for ingredients to prepare recipe|
|`'n_ingredients'`	     |Number of ingredients|

This second dataset is for ratings. Ratings contains 731,927 rows for each recipe id and 5 columns of the following descriptions:
|Column|Description|
|---   |---|
|`'user_id'`	|User ID|
|`'recipe_id'`|Recipe ID|
|`'date'`	    |Date of interaction|
|`'rating'`	  |Rating given|
|`'review'`	  |Review text|

To answer our question, we merged the datasets of Recipes and Ratings to be able to obtain the `ratings` column. The new merged dataframe has 13 columns and the same 83,782 rows as Recipes had. With this new merged dataframe, we then used the columns of `minutes` and `ratings` to see if there really is a relationship between the cooking time and average rating of recipes. 

## Cleaning and EDA
### Data Cleaning
Before cleaning the datasets, we looked at the data types for recipes and ratings to inspect which columns would need to be changed for our project.
Recipes
```
name              object
id                 int64
minutes            int64
contributor_id     int64
submitted         object
tags              object
nutrition         object
n_steps            int64
steps             object
description       object
ingredients       object
n_ingredients      int64
dtype: object
```

Ratings
```
user_id       int64
recipe_id     int64
date         object
rating        int64
review       object
dtype: object
```

We then used the following steps to clean our dataframe:
1. **Left merge the recipes and ratings datasets together.**: There is data in the ratings dataset that we want to see alongside the recipes dataset. To be able to see all the information in one dataset, we had to merge the data together. Specifically, we wanted to keep all the original rows from the recipes dataset, so we used a left merge. By merging the two datasets, we are able to align the rows from each dataset based on their common attributes and columns, most importantly the `rating` column.
2. **Fill all ratings of 0 with np.nan**: With the new merged dataframe, we want to modify the values in one column. This column is for `rating` and we filled all the ratings of 0 with np.nan because we did not consider a 0 to be a valid rating since zeroes are often treated like missing values. Instead, there will only be 5 possible values for ratings with the lowest rating being 1.
3. **Take the average of the ratings column to assign to the recipes dataset**: There are multiple rows for each recipe with different ratings, so we took the average ratings of each recipe by using groupby on each recipe, applied it to the `rating` column, and took the mean. We then assign this new column of average ratings to the original recipes dataset to use for the rest of the project.
4. **Convert columns into correct data types**: There are columns in the dataset which are not the correct type. For example, let's examine the column for 'nutrition'. `nutrition` contains values that look like lists, but the values are actually strings that look like lists. We decided the data in the `nutrition` column would be useful for sections later in the project, so we wanted to extract them from the `nutrition` column and create columns for each individual value in the list from `nutrition`. This was done by splitting the values in the string lists and assigning new columns for each individual value from the `nutrition` list with their data type as floats. A few other columns were in a similar situation as 'nutrition'. These columns are `steps`, `tags`, and `ingredients`, where they contain values that look like lists, but the values are actually strings that look like lists. For `steps`, `tags`, and `ingredients`, we made sure they were lists by stripping the brackets and splitting each value. Lastly, the column for `submitted` originally kept values for dates, but its data type was originally an object. For this column, we used the pd.to_datetime method, so its data type would be an actual date. The final look at the data types for each column can be examined in the following descriptions:
```
name                      object
id                         int64
minutes                    int64
contributor_id             int64
submitted         datetime64[ns]
tags                      object
nutrition                 object
n_steps                    int64
steps                     object
description               object
ingredients               object
n_ingredients              int64
rating                   float64
calories                 float64
total fat                float64
sugar                    float64
sodium                   float64
protein                  float64
saturated fat            float64
carbohydrates            float64
dtype: object
```

The cleaned dataframe is shown below with the columns we have selected for our question we want to investigate that examines the correlation between the amount of minutes it takes to prepare the meal and its respective ratings.

print(cleaned_df[['name', 'minutes', 'rating']].head().to_markdown(index=False))

### Univariate Analysis
For the univariate analysis, we analyzed the distribution of the number of ingredients and the top 10 most used tags among our extensive amount of recipes.

We plotted a histogram to show the distribution for the number of ingredients of each recipe. The plot is approximately a normal distribution skewed to the right. This means the mode is the highest point of the curve, the median follows after at the point where it divides the area under the curve in half, and the mean shows the balance point as it is drawn toward the more extreme observations closer to the tail end of the distribution. The median and mean are not very easy to find by simply examining the distribution, but the mode is. Because the mode is the highest point of the distribution, we can say that the mode is approximately an 8. This means the most shared number of ingredients among a majority of the recipes is an 8.
(histogram for number of ingredients)

Next, we plotted a pie chart to show the distribution for the most used tags among the recipes. There are many unique tags used in the dataset, specifically 549, so we wanted to see how often each tag was used by organizing them by their percentages. We chose to show only the top 10 because showing all 549 tags on one pie chart would not have been very helpful to look as they would have become too crowded and the data would be difficult to read. By showing the top 10 most used tags, we are able to provide a better understanding of what tags have been used the most, as well as how often they were used. The chart shows that the `preparation` and `time-to-make` tags were used the most with 16.1% and 15.5% respectively. This tells us that many people want to share their recipes by letting others know what their recipe will do and how long they will take.
(pie chart for top 10 tags)

### Bivariate Analysis

### Interesting Aggregates

## Assessment of Missingness
After cleaning our data, we noticed that we had a couple of columns containing null values. In this section, we will assessing the various types of missingness in our DataFrame.
### NMAR Analysis
NMAR, or not missing at random, refers to data where chance that a value is missing is dependent on the actual missing value. The reason why this data is missing is due to the data-generating process—the other columns in the dataset won't reveal the reason why the data is missing. A good question to ask to assess whether the given missingness is NMAR is, *Is there a good reason why the missingness depends on the values themselves?*\
\
Based on the aforementioned definitions, we can then conclude that the `rating` column is NMAR because in the data-generating process, we set all ratings of 0 to `np.nan`. Therefore, all the null values in the `rating` column are not missing at random, since they all correspond to the actual value of 0. If we wanted to obtain additional data that could explain the missingness (thereby making it MAR), we could add a taste evaluation for each review where users can use a one-word answer to describe the recipe's taste (e.g. "disgusting", "bad", etc.) that could explain the likelihood that a rating value is missing.
### Missingness Dependency
Now let's take a look at another column: `description`. Assessing the missingness of this column, it is not missing by desgin (MD) because we can not determine the missing value exactly by looking at the other columns nor is it not missing at random (NMAR) because there is not a good reason why missingness depends on the values themselves. Now that we've ruled out MD and NMAR, we will consider the possibility of the missingness of the `description` column being missing at random (MAR). In order to do so, we must compare `description` with another column in the DataFrame through a permutation test.
#### Description and Number of Steps
The first column we decided to compare `description` with the number of steps in the recipe. Instead of directly comparing `n_steps` to `description`, we decided to split `n_steps` to two categories assigned to a new `steps` column: `few`, recipes with `n_steps` 9 or less (approximately the median of `n_steps`); and `many`, recipes with `n_steps` greater than 9. The null hypothesis for this test is that the distribution of `steps` when `description` is missing is the same as the distribution of `steps` when `description` is not missing. The alternative hypothesis is that the distribution of `steps` when `description` is missing is not the same as the distribution of `steps` when `description` is not missing.\
\
First we compared the null and non-null `description` distributions for `steps`.\
\
{insert chart}\
\
On intial look, the two columns look similar, which is evidence that `description`'s missingness does not depend on `steps`. In other words, knowing that a recipe takes fewer steps doesn't make it any more or less likely that the recipe's description is missing than knowing if the recipe takes more steps.\
\
{insert double bar chart}\
\
From the pivot table and bar plot, we saw that the distribution of `steps` is similar whether or not `description` is missing. However, to make it precise what we mean by "similar," we can run a permutation test comparing the following distributions:
  1. The distribution of `steps` when `description` is missing.
  2. The distribution of `steps` when `description` is not missing.


Because we are trying to measure the "distance" between two categorical distributions, we will use total variation distance (TVD) for our test statistic for this permutation test. Because we are comparing two categories, the TVD is the same as the absolute difference in proportions for either category.\
\
Our permutation test simulated 500 TVD results by randomly shuffling `steps`. The histogram below visualizes the TVD distribution as well as plots the observed TVD. \
{insert histogram}\
\
Our results return a p-value of 0.296, and given a significance level of $\alpha$ = 0.05, we fail to reject the null that the distribution of `steps` when `description` is missing is the same as the distribution of `steps` when `description` is not missing. Hence, we can conclude that the missingness in the `description` column is not dependent on the values in the `steps` column. However, this "fail to reject" result means that this permutation test does not give us any concrete clue of which missingness mechanism is being displayed in `description`. In order to figure out whether `description` is MAR or MCAR, we need to compare `description` with other columns in the DataFrame.
#### Description and Rating
Next we decided to compare `description` with whether or not the recipe recieved a high or low rating. Instead of directly comparing `rating` to `description`, we decided to split `rating` into two categories assigned to a new `rating_type` column: `low`, recipes with `rating` 3 or less; and `high`, recipes with `rating` greatered than 3. The null hypothesis for this test is that the distribution of `rating_type` when `description` is missing is the same as the distribution of `rating_type` when `description` is not missing. The alternative hypothesis is that the distribution of `rating_type` when `description` is missing is not the same as the distribution of `rating_type` when `description` is not missing.\
\
First we compared the null and non-null `description` distributions for `rating_type`.\
\
{insert chart}\
\
This time on intial look, the distribution of `rating_type` in the two group (`description_missing` == True and `description_missing` == False) is very different.\
\
{insert double bar chart}\
\
From the pivot table and bar plot, we saw that the distribution of `rating_type` is different depending on whether or not `description` is missing. However, to make it precise what we mean by "different," we can run a permutation test comparing the following distributions:
  1. The distribution of `rating_type` when `description` is missing.
  2. The distribution of `rating_type` when `description` is not missing.


We will also be using TVD for the test statistic of this permutation test since we are trying to measure the "distance" between two categorical distributions (in the case, the TVD is the same as the absolute difference in proportions for either category).\
\
Our permutation test simulated 500 TVD results by randomly shuffling `rating_type`. The histogram below visualizes the TVD distribution as well as plots the observed TVD. \
{insert histogram}\
\
Our results return a p-value of 0.032, and given a significance level of $\alpha$ = 0.05, we reject the null, meaning that the distribution of `rating_type` when `description` is missing is the same as the distribution of `rating_type` when `description` is not missing. Hence, we can conclude that the missingness in the `description` column is dependent on the values in the `rating_type` column. This means that the missingness in `description` is missing at random (MAR) because the likelihood that a value is missing in `description` depends on the value in the `rating_type` column.
## Hypothesis Testing
As college students, we often don't have a lot of time to put together meals and are constantly on the look out for quick and easy recipes. That's why we were curious as to if the recipe preparation time affects the recipe rating. We will be doing a permutation test with the following parameters:\
**Null Hypothesis:** There is no difference in ratings between recipes with a quick cooking time (< 20 minutes) and a longer cooking time (20+ minutes).\
**Alternative Hypothesis:** There is a difference in ratings between recipes with a quick cooking time (< 20 minutes) and a longer cooking time (20+ minutes).\
**Test Statistic**: For this permutation test, we will be using the difference between group means (average rating of recipes with long cooking time - average rating of recipes with quick cooking time)\
**Significance Level:** We chose a significance level of 5%\
**p-value:** 1.0\
**Conclusion:** We fail to reject the null, meaning that there is a difference in ratings between recipes with a quick cooking time (< 20 minutes) and a longer cooking time (20+ minutes).
