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

# Univariate Analysis

We can first view the distribution of calories.

[graph]

The distribution has a very apparent right skew, meaning most recipes have lower amounts of calories. It appears that recipes are concentrated below the 1000 calories mark. This isn't very specific, so let's see if we can break down calorie amount further to get a better representation of the data by creating bins.

[graph]

Now, we can observe that most recipes have between 100 and 500 calories. The distribution is still skewed right for the most part, but we have a better picture of where the recipes fall in terms of their calorie amounts.

# Bivariate Analysis

To address our initial question of the relationship between # of steps and calories, we can plot those columns in a scatter plot and use an OLS trendline to see the relationship.

[graph]

It definitely seems like there is a positive relationship between # of steps and calories! The R^2 might not be very high (~0.02), but we can see from the trend line that as # of steps increases, amount of calories generally increases too.

Let's create a boolean column called "diet" that categorizes recipes as diet (<=500 calories) andn non-diet (>500 calories). Now, we can view the distribution of # of steps for diet and non-diet recipes using a boxplot.

[graph]

# Interesting Aggregates

We can also extract protein from the "nutrition" column and then similarly break it down into bins if we wanted to observe the relationship between protein and calories. Then, we can create a pivot table to see how number of steps changes as binned protein and calories change.

[pivot table]

We can observe the average number of steps needed for each bin of calories and protein. In some columns, it appears that the average # of steps starts to increase, peaks, and then decreases as calories increase (for example, the (10.0, 25.0] protein column). Other columns like (25.0, 50.0] protein display expected behavior overall; as calories increase, average # of ingredients generally increases, which makes logical sense. Finally, looking at the diagonal (top left to bottom right), we can see a similiar trend of increase step count. Higher caloric recipes typically mean larger recipes, which generally lead to more steps being involved.

I also created a pivot table to analyze how mean calorie amount changed as the # of steps in a recipe increased, among the diet and non-diet recipes. The non-diet recipes always had more calories on average--as expected, but it is interesting to note that there a generally increasing trend within the diet foods. As # of steps increases, the calories also increase, which makes sense. However, within non-diet foods, there doesn't appear to be an observable trend in calories as steps increases.

---

## Assessment of Missingness

blah

---

## Hypothesis Testing

blah

---

## Framing a Prediction Problem

blah

---

## Baseline Model

blah

---

## Final Model

blah

---

## Fairness Analysis

blah

---










