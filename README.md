# Food.com Recipe Analysis
## Introduction
## Cleaning and EDA
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
**Null Hypothesis:**
**Alternative Hypothesis:**
**Significance Level:**
**p-value:**
**Conclusion:**
