# Food.com Recipe Analysis
## Introduction
## Cleaning and EDA
## Assessment of Missingness
After cleaning our data, we noticed that we had a couple of columns containing null values. In this section, we will assessing the various types of missingness in our DataFrame.
### NMAR Analysis
NMAR, or not missing at random, refers to data where chance that a value is missing is dependent on the actual missing value. The reason why this data is missing is due to the data-generating process—the other columns in the dataset won't reveal the reason why the data is missing. A good question to ask to assess whether the given missingness is NMAR is, *Is there a good reason why the missingness depends on the values themselves?*
Based on the aforementioned definitions, we can then conclude that the `rating` column is NMAR because in the data-generating process, we set all ratings of 0 to `np.nan`. Therefore, all the null values in the `rating` column are not missing at random, since they all correspond to the actual value of 0.
### Missingness Dependency
## Hypothesis Testing
