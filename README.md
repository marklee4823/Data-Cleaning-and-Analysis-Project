# Data-Cleaning-and-Analysis-Project

Final Project for DSC 80 (Practice and Application of Data Science) at UC San Diego

By Mark Lee

---

## Introduction

Cooking remains one of America's favorite pasttimes, and there are more recipes
in circulation than ever with the introduction of online recipe sharing websites
like food.com. Food.com provides us with a public dataset with over 80,000
unique recipes (83,782 to be exact), their respective ratings, nutrition facts,
and a host of other useful attributes that can help individuals choose their
next homemade meal. 

With an influx of health-conscious indviduals in today's world, calories are one of--if not the most--talked about nutrition fact concerning a recipe. Individuals trying to lose weight, athletes, or just a parent trying to cook a healthy meal for their children are all influenced by calories in one way or another regarding their choice to make the meal or not. This analysis will help individuals priortize their specific dietary needs by highlighting factors of lower or higher calorie recipes.

In this project, my goal is to extenxively analyze the dataset with a focus on a singular question: How are the calories of a recipe related to its corresponding number of steps? The number of steps can give insight into how easy a recipe is to make, or otherwise, how likely someone is to actually make the recipe. We will also touch upon other aspects relating to calories like amount of protein and # of ingredients to help people better define their dietary goals.

There are 18 columns in the original dataset, but many are not directly relevant for this analysis. In particular, we will use 8 original columns:
1. "name": name of the recipe
2. "id": unique identifier for the recipe
3. "minutes": how long the recipe takes to make
4. "tags": short, descriptive features of the recipe
5. "n_steps": how many steps the recipe takes
6. "n_ingredients": how many ingredients are in the recipe
7. "nutrition": common nutritional amounts, like calories and protein
8. "mean_rating": average rating of the recipe

Along the way, we will also create new columns from the existing features to add another layer of analysis.

---

## Data Cleaning and Exploratory Data Analysis

The first step of data cleaning was performing the required merging of the raw recipes dataframe and the interactions dataframe (containing the ratings). After merging on the common columns containg the recipe's unique ID, we were able to obtain the full recipes dataframe, of which we will perform the rest of the analysis in this project on.

I replaced all '0's with NaN in the ratings column as a rating of 0 probably means that the user did not make a rating at all. Then I calculated the mean rating of each recipe and added "mean_rating" as a new column. 

Many columns in the dataframe are unnecessary for this project, like "date" or "contributor_id". As mentioned in the introduction, we are only interested in 8 columns that can help us, so I access only those relevant columns.

We want the dataframe to have as many rows as unique *recipes*, but we can see that the dataframe has a row for each *rating* of a recipe. Because this analysis doesn't focus on ratings, we drop duplicate rows (based of the ID column) and get a dataframe with all unique recipes. 

I noticed that the columns that have values as list types actually aren't lists; they are a string instead. This hinders my ability to extract meaningful values from the columns, so for both the "nutrition" and "tags" columns, I convert each value to a list and the corresponding values inside of the lists to float and string, respectively.

I can now extract calorie amount for each recipe by obtaining the first value in the nutrition column list. I create a new column called "calorie_amt" that includes the extracted calorie information.

Although technically possible, I decided to remove all recipes with calories over 5000. This could potentially disqualify extremely large or calorie-dense recipes, but upon manual inspection, it appeared that many of the 5000+ calorie recipes were typos. I also removed all recipes with less than 1 calorie. Because it is impossible for a recipe to have negative calories and because pure water is the only true zero calorie food/drink, these recipes did not qualify as legitimate recipes to me.

I also removed all rows with a minutes value of 5000+ minutes (>83 hours). Again, this *could* potentially disqualify recipes with extensive cooking times, but these recipes were mostly outliers or mistakes.

Cleaned dataframe: 

| name                                 |     id |   minutes | tags                                                                                                                                                                                                                                                                                               |   n_steps |   n_ingredients | nutrition                                     |   mean_rating |   calorie_amt |
|:-------------------------------------|-------:|----------:|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------:|----------------:|:----------------------------------------------|--------------:|--------------:|
| 1 brownies in the world    best ever | 333281 |        40 | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'for-large-groups', 'desserts', 'lunch', 'snacks', 'cookies-and-brownies', 'chocolate', 'bar-cookies', 'brownies', 'number-of-servings']                                                                        |        10 |               9 | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]      |             4 |         138.4 |
| 1 in canada chocolate chip cookies   | 453467 |        45 | ['60-minutes-or-less', 'time-to-make', 'cuisine', 'preparation', 'north-american', 'for-large-groups', 'canadian', 'british-columbian', 'number-of-servings']                                                                                                                                      |        12 |              11 | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0]  |             5 |         595.1 |
| 412 broccoli casserole               | 306168 |        40 | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                                                                                               |         6 |               9 | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]     |             5 |         194.8 |
| millionaire pound cake               | 286009 |       120 | ['time-to-make', 'course', 'cuisine', 'preparation', 'occasion', 'north-american', 'desserts', 'american', 'southern-united-states', 'dinner-party', 'holiday-event', 'cakes', 'dietary', 'christmas', 'thanksgiving', 'low-sodium', 'low-in-something', 'taste-mood', 'sweet', '4-hours-or-less'] |         7 |               7 | [878.3, 63.0, 326.0, 13.0, 20.0, 123.0, 39.0] |             5 |         878.3 |
| 2000 meatloaf                        | 475785 |        90 | ['time-to-make', 'course', 'main-ingredient', 'preparation', 'main-dish', 'potatoes', 'vegetables', '4-hours-or-less', 'meatloaf', 'simply-potatoes2']                                                                                                                                             |        17 |              13 | [267.0, 30.0, 12.0, 12.0, 29.0, 48.0, 2.0]    |             5 |         267   |

With the dataset adequately cleaned, we can now move onto exploratory data analysis and observe interesting aggregates.

### Univariate Analysis

We can first view the distribution of calories.

[graph]

The distribution has a very apparent right skew, meaning most recipes have lower amounts of calories. It appears that recipes are concentrated below the 1000 calories mark. This isn't very specific, so let's see if we can break down calorie amount further to get a better representation of the data by creating bins.

[graph]

Now, we can observe that most recipes have between 100 and 500 calories. The distribution is still skewed right for the most part, but we have a better picture of where the recipes fall in terms of their calorie amounts.

### Bivariate Analysis

To address our initial question of the relationship between # of steps and calories, we can plot those columns in a scatter plot and use an OLS trendline to see the relationship.

[graph]

It definitely seems like there is a positive relationship between # of steps and calories! The R^2 might not be very high (~0.02), but we can see from the trend line that as # of steps increases, amount of calories generally increases too.

Let's create a boolean column called "diet" that categorizes recipes as diet (<=500 calories) andn non-diet (>500 calories). Now, we can view the distribution of # of steps for diet and non-diet recipes using a boxplot.

[graph]

### Interesting Aggregates

We can also extract protein from the "nutrition" column and then similarly break it down into bins if we wanted to observe the relationship between protein and calories. Then, we can create a pivot table to see how number of steps changes as binned protein and calories change. Note that the column labels are the protein bins.

| binned_calories   |   (-0.001, 10.0] |   (10.0, 25.0] |   (25.0, 50.0] |   (50.0, 100.0] |   (100.0, 1100.0] |
|:------------------|-----------------:|---------------:|---------------:|----------------:|------------------:|
| (0.999, 100.0]    |          7.57217 |        8.37336 |        7.65789 |        nan      |         nan       |
| (100.0, 500.0]    |          9.32652 |        9.90256 |       10.1662  |         10.2999 |           9.81844 |
| (500.0, 1000.0]   |          9.71954 |       12.8419  |       11.4682  |         12.1734 |          11.9142  |
| (1000.0, 2500.0]  |          6.11957 |       12.2739  |       12.1356  |         12.8682 |          13.1699  |
| (2500.0, 5000.0]  |          7.85714 |       10.7     |       11.5263  |         11.5299 |          12.5783  |

We can observe the average number of steps needed for each bin of calories and protein. In some columns, it appears that the average # of steps starts to increase, peaks, and then decreases as calories increase (for example, the (10.0, 25.0] protein column). Other columns like (25.0, 50.0] protein display expected behavior overall; as calories increase, average # of ingredients generally increases, which makes logical sense. Finally, looking at the diagonal (top left to bottom right), we can see a similiar trend of increase step count. Higher caloric recipes typically mean larger recipes, which generally lead to more steps being involved.

I also created a pivot table to analyze how mean calorie amount changed as the # of steps in a recipe increased, among the diet and non-diet recipes (diet=True, non-diet=False). 

|   n_steps |    False |    True |
|----------:|---------:|--------:|
|         1 | 1045.22  | 164.441 |
|         2 | 1042.34  | 180.446 |
|         3 |  979.002 | 190.733 |
|         4 |  915.199 | 210.511 |
|         5 |  922.581 | 226.23  |

The non-diet recipes always had more calories on average--as expected, but it is interesting to note that there a generally increasing trend within the diet foods. As # of steps increases, the calories also increase, which makes sense, considering those recipes might be more complicated due to the amount of food being prepared. However, within non-diet foods, it actually appears to have the oppiste effect: as # of steps increases, calories seem to decrease.

---

## Assessment of Missingness

In the merged dataframe, we can see that description is sometimes missing. From an NMAR perspective, this missingness can be viewed as the recipe itself is very self-explanatory or simple, and thus doesn't need a description to elaborate on what the recipe is. If we wanted additional data to make the missingness MAR, we could collect data on a type of metric that measures commonness/straightforwardness of each recipe (for example, a standard cheeseburger scoring a 1 vs. Carribean white sea urchin ceviche scoring a 10).

From existing columns, however, we can test the missingness of description on a column like "n_ingredients", or the # of ingredients. We can compare the distributions of # of ingredients when description is missing or present.

[graph]

Because the two distributions *roughly* have similiar shapes, we can use differences of means as the test statistic.

Null hypothesis: The mean number of ingredients in recipes with their description present is the same as the mean number of ingredients in recipes with their description missing.

Alternative hypothesis: The mean number of ingredients in recipes with their description present is more than the mean number of ingredients in recipes with their description missing.

Test statistic: The mean number of ingredients in recipes with their description present minus the mean number of ingredients in recipes with their description missing. (differences in mean)

Using a permutation test to shuffle the missingness of the description column 1000 times and simulate test statistics under the null, we can view the distribution of the simulated statistics and compare our observed statistic with it to find the p-value.

[graph]

As shown, we get a p-value of 0, so we reject the null hypothesis that there is no difference between the mean # of ingredients between description missing and present. Thus, we can conclude that missingness of description is MAR dependent on the "n_ingredients" column.

We can also test the missingenss of description on "n_steps", or number of steps in the recipe. We can view the distribution of # of steps when description is missing or present.

[graph]

Again, we can use differences of means as our test statistic.

Null hypothesis: The mean number of steps in recipes with their description missing is the same as the mean number of steps in recipes with their description present.

Alternative hypothesis: The mean number of steps in recipes with their description missing is more than the mean number of steps in recipes with their description present.

Test statistic: The mean number of steps in recipes with their description missing minus the mean number of steps in recipes with their description present. (differences in mean)

Again, using a permutation test to shuffle the missingness of the description column 1000 times and simulate test statistics under the null, we can view the distribution of the simulated statistics and compare our observed statistic with it to find the p-value.


[graph]

As shown, we get a p-value of []. Using the standard cutoff of 0.05, we fail to reject the null hypothesis that there is a significant difference between the mean number of steps in recipes with their descriptions missing and the mean number of steps in recipes with their descriptions present. As such, we cannot conclude that the missingness of description is MAR dependent on the "n_steps" column.

---

## Hypothesis Testing

It appears that non-diet recipes have on average more ingredients, which could be as expected, as one could hypothesize that more less calorie-dense foods are easier to make with less ingredients. Is this difference actually significant, or is it just by chance? Again, diet recipes are defined as having less than or equal to 500 calories, and non-diet recipes have more than 500 calories. This information is stored in the "diet" column.

Null hypothesis: There is no difference between the average number of ingredients in diet recipes than the average number of ingredients in non-diet recipes.

Alternative hypothesis: Non-diet recipes have on average more ingredients than diet recipes.

Test statistic: The average number of ingredients in non-diet recipes minus the average number of ingredients in diet recipes. (difference in means)

We'll use the standard p-value cutoff of 0.05.

Similiar to our missingness tests, we can view the distribution of # of ingredients in diet and non-diet recipes in a plot.

[graph]

Then, we use a permutation test to shuffle the boolean "diet" column 1000 times and simulate test statistics under the null. We can view the distribution of the simulated test statistics and compare our observed statistic with it to find the p-value.

[graph]

As shown, we get a p-value of 0. Using the cutoff of 0.05, we reject the null hypothesis that there is no difference between the number of ingredients in diet and non-diet recipes.

---

## Framing a Prediction Problem

Now, we can use our dataset to frame a prediction problem. After consideration, I chose the response variable to be predicting the amount of protein in a recipe. Because protein is a numerical variable, we will be using regression as our prediction type.

I chose this variable because protein is a very common nutritional fact that many athletes and gym-goers focus on in terms of choosing their meals, so I felt that predicting protein had some relevance in my own life.

We will be using root mean square error (RMSE) to evaluate our model. Because we are running regression, this metric made the most sense, especially due to the interpretable nature of RMSE. Additionally, RMSE is very robust to outliers, which helps us as there are many outliers in this dataset as explored in the data cleaning section of this project.

---

## Baseline Model

For my rudimentary baseline model, I chose only features that were present in the original dataframe to predict the protein column. These were the columns "minutes", "n_steps", and "n_ingredients". The original dataframe did not have many categorical columns, and thus, all of these columns are quantitative. Because this was just the baseline model, I performed only simple transformations on the features.

1. I applied a square root function on both the "n_steps" and "minutes" column. Because the square root mitigates the impact of outliers, I thought that this would be a simple but effective way of transforming these columns.

2. Next, I applied a Binarizer on the "n_ingredients" column with a threshold of 10. This would divide up the recipes into larger, more complex recipes, and smaller, easier recipes. 

3. After applying the column transformations, I ran a linear regression model and fit it to my training data. After fitting, I tested the model on my testing data and received an RMSE of about 37.75.

RMSE is pretty high, so it is clear that this is not the best model, and we can improve upon this by creating new features and applying other transformations.

This makes logical sense because the columns that I used don't make much sense to have a strong correlation with protein amount, even when applying some transformations. For example, the longer it takes to prepare a recipe does not exactly mean that the amount of protein in that recipe will be higher. We can do a better job picking more relevant features in the next section.

---

## Final Model

Now, we move on to the final model.

We can add a new column from the existing column "tags". One of the tags is called "high-protein". Because we are predicting the actual *amount* of protein in the recipe (not if the recipe has a lot of protein or not), this would be a good column to add to our dataframe to work on this regression.

In this model, I chose the columns "n_ingredients", "calorie_amt", "is_high_protein", and "minutes". The quantitative columns are "n_ingredients", "calorie_amt", and "minutes". The other column "high_protein" is boolean, which is True or False depending on if the recipe is a high protein recipe. 

Already, one of the main improvements of this model is that the features chosen have logical relevance to the amount of protein we are trying to predict in the recipe. For example, higher calorie foods typically have more protein, thus the usage of the "calorie_amt" column. Additionally, with the usage of the "is_high_protein" column, this directly tells us whether we can expect a larger amount of protein or not in the recipe.

Now, onto the transformations themselves.

1. Again, I felt that applying a square root function was an effective way of reducing variance of the data, thus I applied it on the "calorie_amt" column. From a generated KDE plot, there was a clear positive relationship between calories and protein, and among further inspection, the relationship became stronger when I used square rooted calories vs. protein. 

2. 


---

## Fairness Analysis

blah

---










