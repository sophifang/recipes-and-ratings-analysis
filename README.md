# Food.com Recipe Analysis
Authors: Sophia Fang and Carmen Truong
---

## Introduction
This project dives into the world of cooking by taking a look at datasets consisting of recipes and their respective ratings. There are many recipes out there from the finest ones curated to every detail to those created from quickly throwing ingredients together. There are people who do not mind spending hours or days of their time preparing a meal, but is it really worth the time and effort? With such an extensive dataset, we will investigate the correlation between the cooking time and average rating of recipes to see if recipes that take more time are rated higher than the recipes that take less time.

### Datasets Used
We will be looking at two datasets collected from food.com containing recipes and ratings posted since 2008.

This first dataset is for recipes. Recipes contains 83,782 rows for each recipe name and 12 columns of the following descriptions.

| Column         | Description                                                                                                                                                                                       |
|:---------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| name           | Recipe name                                                                                                                                                                                       |
| id             | Recipe ID                                                                                                                                                                                         |
| minutes        | Minutes to prepare recipe                                                                                                                                                                         |
| contributor_id | User ID who submitted this recipe                                                                                                                                                                 |
| submitted      | Date recipe was submitted                                                                                                                                                                         |
| tags           | Food.com tags for recipe                                                                                                                                                                          |
| nutrition      | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| n_steps        | Number of steps in recipe                                                                                                                                                                         |
| steps          | Text for recipe steps, in order                                                                                                                                                                   |
| description    | User-provided description                                                                                                                                                                         |
| ingredients    | Text for ingredients to prepare recipe                                                                                                                                                            |
| n_ingredients  | Number of ingredients  

This second dataset is for ratings. Ratings contains 731,927 rows for each recipe id and 5 columns of the following descriptions.

| Column    | Description         |
|:----------|:--------------------|
| user_id   | User ID             |
| recipe_id | Recipe ID           |
| date      | Date of interaction |
| rating    | Rating given        |
| review    | Review text         |

To answer our question, we merged the datasets of Recipes and Ratings to be able to obtain the `ratings` column. The new merged dataframe has 13 columns and the same 83,782 rows Recipes had. With this new merged dataframe, we then used the columns of `minutes` and `ratings` to see if there really is a relationship between the cooking time and average rating of recipes. 

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
1. **Left merge the recipes and ratings datasets together.**: There is data in the ratings dataset that we wanted to see alongside the recipes dataset. To be able to see all the information in one dataset, we had to merge the data together. Specifically, we kept all the original rows from the recipes dataset, so we used a left merge. By merging the two datasets, we are able to align the rows from each dataset based on their common attributes and columns, most importantly the `rating` column.
2. **Fill all ratings of 0 with np.nan**: With the new merged dataframe, we wanted to modify the values in one column. This column is for `rating` and we filled all the ratings of 0 with np.nan because we did not consider a 0 to be a valid rating since zeroes are often treated like missing values. Instead, there are only 5 possible values for ratings with the lowest rating being 1.
3. **Take the average of the ratings column to assign to the recipes dataset**: There are multiple rows for each recipe with different ratings, so we took the average ratings of each recipe by using groupby on each recipe, applied it to the `rating` column, and took the mean. We then assigned this new column of average ratings to the original recipes dataset.
4. **Convert columns into correct data types**: There are columns in the dataset which are not the correct type. For example, let's examine the column for 'nutrition'. `nutrition` contains values that look like lists, but the values are actually strings that look like lists. We decided the data in the `nutrition` column would be useful for sections later in the project, so we extracted the values from the `nutrition` column and created columns for each individual value in the list of `nutrition`. This was done by splitting the values in the string lists and assigning new columns for each individual value from the `nutrition` list with their data type as floats. A few other columns were in a similar situation as 'nutrition'. These columns are `steps`, `tags`, and `ingredients`, where they contain values that look like lists, but the values are actually strings that look like lists. For `steps`, `tags`, and `ingredients`, we made sure they were lists by stripping the brackets and splitting each value. Lastly, the column for `submitted` originally kept values for dates, but its data type was originally an object. For this column, we used the pd.to_datetime method, so its data type would be an actual date. The final look at the data types for each column can be examined in the following descriptions.
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

| name                                 |     id |   minutes |   n_steps |   n_ingredients |   calories |   total_fat |   sugar |   sodium |   protein |   saturated_fat |   carbohydrates |   rating |
|:-------------------------------------|-------:|----------:|----------:|----------------:|-----------:|------------:|--------:|---------:|----------:|----------------:|----------------:|---------:|
| 1 brownies in the world    best ever | 333281 |        40 |        10 |               9 |      138.4 |          10 |      50 |        3 |         3 |              19 |               6 |        4 |
| 1 in canada chocolate chip cookies   | 453467 |        45 |        12 |              11 |      595.1 |          46 |     211 |       22 |        13 |              51 |              26 |        5 |
| 412 broccoli casserole               | 306168 |        40 |         6 |               9 |      194.8 |          20 |       6 |       32 |        22 |              36 |               3 |        5 |
| millionaire pound cake               | 286009 |       120 |         7 |               7 |      878.3 |          63 |     326 |       13 |        20 |             123 |              39 |        5 |
| 2000 meatloaf                        | 475785 |        90 |        17 |              13 |      267   |          30 |      12 |       12 |        29 |              48 |               2 |        5 |

### Univariate Analysis
For the univariate analysis, we analyzed the distribution of the number of ingredients and the top 10 most used tags among our extensive amount of recipes.

We plotted a histogram to show the distribution for the number of ingredients of each recipe.
<iframe src="assets/dist_n_ingred.html" width=800 height=600 frameBorder=0></iframe>
The plot is approximately a normal distribution skewed to the right. This means the mode is the highest point of the curve, the median follows after at the point where it divides the area under the curve in half, and the mean shows the balance point as it is drawn toward the more extreme observations closer to the tail end of the distribution. The median and mean require more effort to find other than simply examining the distribution, but the mode is in the opposite situation. As explained earlier, because the mode is the highest point of the distribution, we can say the mode is approximately an 8. This means the most shared number of ingredients among a majority of the recipes is an 8.

Next, we plotted a pie chart to show the distribution for the most used tags among the recipes. There are many unique tags used in the dataset, specifically 549, so we wanted to see how often each tag was used by organizing them by their percentages. We chose to show only the top 10 because showing all 549 tags on one pie chart would not have been very helpful to look as they would have become too crowded and the data would be difficult to read. By showing the top 10 most used tags, we provided a better understanding of what tags have been used the most, as well as how often they were used.
<iframe src="assets/top_10_tags.html" width=800 height=600 frameBorder=0></iframe>
The chart shows that the "preparation" and "time-to-make" tags were used the most with 16.1% and 15.5% respectively. This tells us that many people want to share their recipes by letting others know what their recipe will do and how long the recipe will take.

### Bivariate Analysis
For the bivariate analysis, we wanted to see which pair of columns identified possible associations. To be able to see these possible associations among the columns of our dataset, we created a heatmap that shows how closely associated each value of `nutrition` was with the column of `rating`. 

<iframe src="assets/heatmap.html" width=800 height=600 frameBorder=0></iframe>

Examining the heatmap, we chose to investigate the association between the `total_fat` and `calories` of each recipe as our heatmap shows that the two columns show a very strong association other than the direct association of a 1. After we decided which columns to use for our bivariate analysis, we plotted the association using a scatterplot with `total_fat` as the independent variable and `calories` as the dependent variable. 

<iframe src="assets/scatter.html" width=800 height=600 frameBorder=0></iframe>
Examining the scatterplot, we could see there is a positive, linear relationship between `total_fat` and `calories`. This positive, linear relationship implies there is a correlation between the total fat and calories of most recipes.

### Interesting Aggregates
For our interesting aggregates, we studied the average ratings and each value of nutrition.
This is our pivot table for each rating pulled from `rating` as an integer and the values in `nutrition`.

|   rating_integer |   carbohydrates |   protein |   saturated_fat |   sodium |   sugar |   total_fat |
|-----------------:|----------------:|----------:|----------------:|---------:|--------:|------------:|
|                1 |         14.8571 |   29.4048 |         44.0799 |  45.4864 | 76.3367 |     34.5561 |
|                2 |         15.5781 |   31.9141 |         48.1953 |  32.7969 | 76.4234 |     35.5266 |
|                3 |         14.9642 |   33.4653 |         39.0406 |  37.4325 | 81.7409 |     31.7338 |
|                4 |         13.6708 |   35.2527 |         38.7613 |  28.169  | 62.6682 |     31.5453 |
|                5 |         13.7361 |   32.7285 |         40.4875 |  28.5728 | 69.2263 |     32.8387 |

This is the plot we made for our pivot table.
<iframe src="assets/avg_nutrition.html" width=800 height=600 frameBorder=0></iframe>
The interesting result we found in the aggregates data is there were many recipes that consisted of sugar the most compared to the other nutritional values. This shows how much sugar a person would consume with these recipes depending on the percent daily value, which is around 60-80% per rating.

## Assessment of Missingness
After cleaning our data, we noticed that we had a couple of columns containing null values. In this section, we will assessing the various types of missingness in our DataFrame.
### NMAR Analysis
NMAR, or not missing at random, refers to data where chance that a value is missing is dependent on the actual missing value. The reason why this data is missing is due to the data-generating process—the other columns in the dataset won't reveal the reason why the data is missing. A good question to ask to assess whether the given missingness is NMAR is, *Is there a good reason why the missingness depends on the values themselves?*\
\
Based on the aforementioned definitions, we can then conclude that the `rating` column is NMAR because in the data-generating process, we set all ratings of 0 to `np.nan`. Therefore, all the null values in the `rating` column are not missing at random, since they all correspond to the actual value of 0. If we wanted to obtain additional data that could explain the missingness (thereby making it MAR), we could add a taste evaluation for each review where users can use a one-word answer to describe the recipe's taste (e.g. "disgusting", "bad", etc.) that could explain the likelihood that a rating value is missing.
### Missingness Dependency
Now let's take a look at another column: `description`. Assessing the missingness of this column, it is not missing by desgin (MD) because we can not determine the missing value exactly by looking at the other columns nor is it not missing at random (NMAR) because there is not a good reason why missingness depends on the values themselves. Now that we've ruled out MD and NMAR, we will consider the possibility of the missingness of the `description` column being missing at random (MAR). In order to do so, we must compare the `description` column with another column in the DataFrame through a permutation test.
#### Description and Number of Steps
The first column we decided to compare the `description` column with the number of steps in the recipe. Instead of directly comparing the values in the `n_steps` column to the `description` column, we decided to split the values in the `n_steps` column into two categories assigned to a new `steps_type` column: few, recipes with 9 or less steps (approximately the median of the `n_steps` column); and many, recipes with greater than 9 steps. The null hypothesis for this test is that the distribution of `steps_type` when `description` is missing is the same as the distribution of `steps_type` when `description` is not missing. The alternative hypothesis is that the distribution of `steps_type` when `description` is missing is not the same as the distribution of `steps_type` when `description` is not missing.\
\
First we compared the null and non-null `description` distributions for `steps`.

| steps_type   |   description_missing = False |   description_missing = True |
|:-------------|------------------------------:|-----------------------------:|
| few          |                       0.54973 |                     0.485714 |
| many         |                       0.45027 |                     0.514286 |

On intial look, the two columns look similar, which is evidence that `description` column's missingness does not depend on `steps_type` column values. In other words, knowing that a recipe takes fewer steps doesn't make it any more or less likely that the recipe's description is missing than knowing if the recipe takes more steps.

<iframe src="assets/steps_dist_viz.html" width=800 height=600 frameBorder=0></iframe>

From the pivot table and bar plot, we saw that the distribution of `steps_type` is similar whether or not the value in the `description` column is missing. However, to make it precise what we mean by "similar," we can run a permutation test comparing the following distributions:
  1. The distribution of `steps_type` when `description` is missing.
  2. The distribution of `steps_type` when `description` is not missing.


Because we are trying to measure the "distance" between two categorical distributions, we will use total variation distance (TVD) for our test statistic for this permutation test. Because we are comparing two categories, the TVD is the same as the absolute difference in proportions for either category.\
\
Our permutation test simulated 500 TVD results by randomly shuffling `steps_type`. The histogram below visualizes the TVD distribution as well as plots the observed TVD.
<iframe src="assets/steps_fig.html" width=800 height=600 frameBorder=0></iframe>

Our results return a p-value of 0.296, and given a significance level of 5%, we fail to reject the null that the distribution of `steps_type` when `description` is missing is the same as the distribution of `steps_type` when `description` is not missing. Hence, we can conclude that the missingness in the `description` column is not dependent on the values in the `steps_type` column. However, this "fail to reject" result means that this permutation test does not give us any concrete clue of which missingness mechanism is being displayed in the `description` column. In order to figure out whether `description` column missingness is MAR or MCAR, we need to compare `description` with other columns in the DataFrame.
#### Description and Rating
Next we decided to compare the `description` column with whether or not the recipe recieved a high or low rating. Instead of directly comparing the `rating` column to `description`, we decided to split the `rating` column's values into two categories assigned to a new `rating_type` column: low, recipes with rating 3 or less; and high, recipes with rating greater than 3. The null hypothesis for this test is that the distribution of `rating_type` when `description` is missing is the same as the distribution of `rating_type` when `description` is not missing. The alternative hypothesis is that the distribution of `rating_type` when `description` is missing is not the same as the distribution of `rating_type` when `description` is not missing.\
\
First we compared the null and non-null `description` distributions for `rating_type`.

| rating_type   |   description_missing = False |   description_missing = True |
|:--------------|------------------------------:|-----------------------------:|
| bad           |                     0.0776113 |                     0.142857 |
| good          |                     0.922389  |                     0.857143 |

This time on intial look, the distribution of `rating_type` in the two group (`description_missing` == True and `description_missing` == False) is very different.

<iframe src="assets/rating_dist_viz.html" width=800 height=600 frameBorder=0></iframe>

From the pivot table and bar plot, we saw that the distribution of `rating_type` is different depending on whether or not `description` is missing. However, to make it precise what we mean by "different," we can run a permutation test comparing the following distributions:
  1. The distribution of `rating_type` when `description` is missing.
  2. The distribution of `rating_type` when `description` is not missing.


We will also be using TVD for the test statistic of this permutation test since we are trying to measure the "distance" between two categorical distributions (in the case, the TVD is the same as the absolute difference in proportions for either category).\
\
Our permutation test simulated 500 TVD results by randomly shuffling the `rating_type` column. The histogram below visualizes the TVD distribution as well as plots the observed TVD.
<iframe src="assets/rating_fig.html" width=800 height=600 frameBorder=0></iframe>

Our results return a p-value of 0.032, and given a significance level of 5%, we reject the null, meaning that the distribution of `rating_type` when `description` is missing is the same as the distribution of `rating_type` when `description` is not missing. Hence, we can conclude that the missingness in the `description` column is dependent on the values in the `rating_type` column. This means that the missingness in the `description` column is missing at random (MAR) because the likelihood that a value is missing in the `description` column depends on the value in the `rating_type` column.
## Hypothesis Testing
As college students, we often don't have a lot of time to put together meals and are constantly on the look out for quick and easy recipes. That's why we were curious as to if the recipe preparation time affects the recipe rating. We will be doing a permutation test with the following parameters:\
**Null Hypothesis:** There is no difference in ratings between recipes with a quick cooking time (< 20 minutes) and a longer cooking time (20+ minutes).\
**Alternative Hypothesis:** There is a difference in ratings between recipes with a quick cooking time (< 20 minutes) and a longer cooking time (20+ minutes).\
**Test Statistic**: For this permutation test, we will be using the difference between group means (average rating of recipes with fast cooking time - average rating of recipes with slow cooking time). This will allow us to compare the two groups to see if they are statistically similar enough to satisfy our null hypothesis.\
**Significance Level:** We chose a significance level of 5% as it is the most conventional significance level that has become widely accepted and provides a standard benchmark for statistical significance. Results with a p-value of less than 0.05 are considered "statistically significant," which suits our needs and accurary level needed for this project.\
\
Instead of directly comparing the `minutes` column to the `average_rating` column, we decided to split the values in the `minutes` coliumn into two categories assigned to a new `speed` column: fast, recipes that take less than 20 minutes to prepare; and slow, recipes that take 20 or more minutes to complete. Our permutation test simulated 500 differences between randomly shuffled fast and slow recipe cooking times (fast - slow). The histogram below visualizes the distribution of differences as well as plots the observed difference (fast - slow).
<iframe src="assets/differences_fig.html" width=900 height=600 frameBorder=0></iframe>
**p-value:** 1.0\
**Conclusion:** Our results return a p-value of 1, and given a significance level of 5%, we fail to reject the null, meaning that the distribution of ratings in recipes with a quick completion time is not the same as the ditribution of ratings in recipes with a slow completition time. We fail to reject the null, meaning that there is a difference in ratings between recipes with a quick cooking time (< 20 minutes) and a longer cooking time (20+ minutes).
