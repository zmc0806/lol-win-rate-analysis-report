# Analysis of the factors that affect the winning rate of League of Legends games

Name:Mianchen Zhang

### Introduction to the Dataset

The dataset provided contains detailed information on over 10,000 competitive matches from the popular video game League of Legends (LoL), a game that has not only captured the interest of millions of players worldwide but also established a significant presence in the esports industry. This dataset is a comprehensive collection of data from the year 2022, reflecting player and team performances, match outcomes, and various in-game statistics.

### Central Question of the Analysis

The central question that this analysis seeks to explore is: **"Is there a significant correlation between the first team to achieve a specific in-game objective - First Blood the overall match victory?"** This question is pivotal for players, coaches, analysts, and fans alike, as it can offer insights into winning strategies and highlight the importance of early-game objectives in competitive play.

### Relevance of the Dataset and the Question

Understanding the correlation between early-game advantages and match outcomes is crucial in the realm of competitive LoL. It can influence team strategies, individual player focus, and overall understanding of what contributes to a team's success. This analysis can shed light on whether certain early-game objectives significantly impact the chances of winning, which is invaluable for players looking to improve their gameplay, teams strategizing for tournaments, and enthusiasts following the competitive scene.

### Dataset Overview

- **Number of Rows**: The dataset initially contains over 10,000 matches, corresponding to multiple rows per match for individual players and teams.

- **Relevant Columns and Descriptions**:
  1. `gameid`: Unique identifier for each match,each gameid will correspond to two teams.
  2. `teamid`: Identifier for each team in a match.
  3. `result`: Indicates whether the team won or lost.
  4. `firstblood`: Indicates whether the team achieved the first kill (First Blood) in the match.
  5. Other similar columns related to early-game objectives, such as `firsttower` (first team to destroy a tower), `firstbaron`, `firstdragon`, etc.

These columns are instrumental in analyzing the impact of early-game objectives on the final outcome of the matches. By focusing on these aspects, we can derive meaningful insights into the strategies that lead to success in League of Legends competitive play.


### Data Cleaning Steps and Their Impact on Analysis

The process of preparing the League of Legends dataset for analysis involved several crucial data cleaning steps, each impacting the final analysis in significant ways. The steps taken were carefully chosen to ensure data integrity and relevance to the research question about the correlation between early-game objectives and match outcomes.

#### 1. **Initial Data Loading and Understanding**:
   - **Action**: Loaded the dataset using pandas and inspected the first few rows.
   - **Impact**: This initial step provided a foundational understanding of the dataset's structure, including the types of data available and their initial format.

#### 2. **Separating Player-Specific and Team Summary Data**:
   - **Action**: Split the dataset into two distinct datasets based on the `position` column — one for player-specific data and another for team summary data.
   - **Impact**: This separation was crucial for focusing on team-level analysis relevant to our research question. It helped in simplifying the dataset and making it more manageable for targeted analysis.

#### 3. **Handling Missing Values**:
   - **Action**: Identified and removed columns with 100% missing values. Assessed and made decisions on columns with significant but not complete missing values.
   - **Impact**: Removing columns with complete missing values eliminated irrelevant data, reducing noise and potential biases in the analysis. Deciding on columns with significant missing values based on their relevance ensured that only meaningful data was retained.

#### 4. **Correcting Data Types**:
   - **Action**: Converted columns that were genuine boolean (like `firstblood`, `playoffs`) to boolean data types and ensured other columns were appropriately typed.
   - **Impact**: This step ensured that the data types accurately represented the nature of the data, allowing for appropriate analytical techniques to be applied. For example, boolean columns could be directly used in logical comparisons.

#### 5. **Dataset Overview After Cleaning**:
   - **Action**: Assessed the shape of the cleaned datasets.
   - **Impact**: This provided a clear understanding of the dataset's size and scope after cleaning, setting the stage for effective analysis.
     
Since the problem I studied only needed to ensure the integrity of firstblood and result, datacompleteness had no effect on me

#### Showcasing the Cleaned DataFrame

To illustrate the result of these cleaning steps, below is a snapshot of the cleaned team summary dataset:

```python
# Displaying the first 8 rows of the cleaned team summary data according my question.
team_data_cleaned[['gameid','firsttower','firstblood','result']].head(8)
```

| gameid                | firsttower   | firstblood   | result   |
|:----------------------|:-------------|:-------------|:---------|
| ESPORTSTMNT01_2690210 | True         | True         | False    |
| ESPORTSTMNT01_2690210 | False        | False        | True     |
| ESPORTSTMNT01_2690219 | False        | False        | False    |
| ESPORTSTMNT01_2690219 | True         | True         | True     |
| 8401-8401_game_1      | True         | False        | True     |
| 8401-8401_game_1      | True         | True         | False    |
| ESPORTSTMNT01_2690227 | True         | False        | True     |
| ESPORTSTMNT01_2690227 | False        | True         | False    |

This cleaned dataset now serves as the foundation for our analysis, with irrelevant and misleading data removed and the remaining data structured and formatted for accurate interpretation. The cleaning steps, tailored to the unique aspects of this dataset and our specific research question, enhance the validity and reliability of the subsequent analysis.


### Univariate Analysis

##### Distribution of Match Results:
<iframe src="assets/result_distribution.html" width=800 height=600 frameBorder=0></iframe>

Since both teams will have the same game_id for each match, there will be winners for every match there will be losers, so the distribution should be exactly,here is not。That means I missed the details when I was cleaning the data, so I need to go back and check.

I found the problem, some gamids have false results for both teams, both teams can't lose in the same game, this is obviously not normal, so I need to rule this out.

```python
# I cleaned the data again to make sure that only the results of the game were normal
mask = team_data_cleaned.groupby('gameid')['result'].transform('sum') == 1
team_data_cleaned = team_data_cleaned[mask]
team_data_cleaned.head()
```
<iframe src="assets/result_distribution_new.html" width=800 height=600 frameBorder=0></iframe>

Now the distribution is exactly the same, just like I expected

##### Distribution of First Blood in Matches:

Now I also need to make sure that the data on firstblood is cleaned properly, which is very important for my later research, I only need to keep one blood with one team true and one team false.

```python
#Keep only two teams with one true and one false blood
mask_1 = team_data_cleaned.groupby('gameid')['firstblood'].transform('sum') == 1
filtered_data = team_data_cleaned[mask_1]
```
<iframe src="assets/firstblood_distribution.html" width=800 height=600 frameBorder=0></iframe>

Now the distribution is also exactly the same.That means I successfully cleaned all the data.

##### Distribution of gold different at 10min in Matches:

<iframe src="assets/fig_golddiffat10.html" width=800 height=600 frameBorder=0></iframe>

The distribution appears to be roughly symmetrical around the zero mark, suggesting that at the 10-minute point, it's just as common for a team to be ahead as it is to be behind in terms of gold difference.
The majority of values are concentrated around the center of the distribution, which indicates that in most matches, the gold difference at 10 minutes is relatively small. This could imply that games are often quite balanced in the early stages.
There are tails extending to both the left and the right, with the left tail representing matches where one team is significantly behind in gold, and the right tail representing matches where one team is significantly ahead. These tails are less dense, indicating these scenarios are less common but still possible.
The range of gold difference spans from approximately -8000 to +8000, showing that while most games are close around the 10-minute mark, there can be matches with a substantial lead or deficit.

##### Interactive Distribution of Gold Difference at 10 Minutes:

<iframe src="assets/Interactive Distribution of Gold Difference.html" width=800 height=600 frameBorder=0></iframe>

This pie chart shows the distribution of gold difference at the 10-minute mark in League of Legends matches, categorized by the magnitude of the lead or deficit. Here's an analysis of the chart:

- **Balanced (44.6%)**: The largest portion of the pie chart is occupied by matches where the gold difference at 10 minutes is relatively small, suggesting that nearly half of the games are closely contested in the early stages.
- **Slight Lead/Lag (36.0%)**: Over a third of the matches have a slight lead or lag, indicating that a significant number of games have a moderate but not decisive gold difference at the 10-minute mark.
- **Moderate Lead/Lag (15.1%)**: A smaller fraction of matches show a moderate lead or lag, which may suggest that one team is starting to gain a substantial advantage that could impact the mid-game.
- **Large Lead/Lag (4.4%)**: The smallest slice represents games where there's a large lead or deficit. These matches are less common, which could indicate that it's relatively rare for games to be heavily one-sided by the 10-minute mark.

This distribution is valuable for strategists and players as it highlights the state of the game in the early phase and suggests that while many matches are competitive, a significant lead or lag by the 10-minute mark is not very common. Coaches and players might use this data to evaluate the importance of early game decisions and their potential impact on the overall outcome of the match.

```python
# Show the first six rows
filtered_data[['gameid','firstblood','result']].head(6)
```

| gameid                | firstblood   | result   |
|:----------------------|:-------------|:---------|
| ESPORTSTMNT01_2690210 | True         | False    |
| ESPORTSTMNT01_2690210 | False        | True     |
| ESPORTSTMNT01_2690219 | False        | False    |
| ESPORTSTMNT01_2690219 | True         | True     |
| 8401-8401_game_1      | False        | True     |
| 8401-8401_game_1      | True         | False    |


### Bivariate Analysis

##### Distribution of First Blood And Match Results:
<iframe src="assets/firstblood_result.html" width=800 height=600 frameBorder=0></iframe>

From the histogram, we can observe that the count of true match results when the first blood is true is higher than when it's false, indicating a possible correlation between securing the first blood and winning the match. Conversely, the count of false match results (losing the match) is higher when the first blood is false than when it's true.

The colors purple and green are used to differentiate the "Match Result" categories, with purple representing "True" and green representing "False". The x-axis represents the first blood status, while the y-axis shows the count of match results.

In conclusion, this visual suggests that there might be a tendency for a team or individual that achieves the first blood to win the match, and conversely, not securing the first blood correlates with a higher likelihood of losing the match. However, without statistical analysis, Now we cannot determine the strength or significance of this relationship.

##### Boxplot of first blood And Match Results:
<iframe src="assets/firstblood_golddiffat10.html" width=800 height=600 frameBorder=0></iframe>
From the box plot, we can see two distributions:

1. For 'true' first blood:
   - The median is positive, at approximately 691, indicating that on average, when a team gets the first blood, they tend to have a gold advantage at the 10-minute mark.
   - The interquartile range (IQR), from Q1 to Q3 (the edges of the box), shows that the middle 50% of the gold difference values lie between approximately -99 and 1518. This suggests a wide spread of outcomes even when first blood is secured.
   - There are outliers on both ends, but notably, there is a significant positive outlier with a maximum gold difference of 9428, which is much higher than the upper fence of the data (considered the threshold for outliers, at 3942).
   - The lower fence is at -2509, indicating that even with the first blood, some teams find themselves at a gold disadvantage at the 10-minute mark.

2. For 'false' first blood:
   - The median appears to be around zero or slightly negative, suggesting that not securing first blood does not drastically affect the gold difference at the 10-minute mark on average.
   - The spread of the IQR is narrower compared to when first blood is 'true', with less variability in the gold difference.
   - There are fewer outliers, and the extremes are less pronounced than in the 'true' category.

Overall, the box plot suggests that achieving first blood is associated with a higher gold difference at the 10-minute mark in the game, but it also shows that there can be considerable variability. Teams that do not get first blood can still have a range of gold difference outcomes, some of which overlap with the outcomes of teams that do get first blood. The outliers, particularly in the 'true' category, indicate that there can be exceptional cases where the gold difference is significantly higher or lower than the typical range.

### Interesting Aggregates

| First Blood   |   Win Rate |   Number of Matches |
|:-----------------------|-----------:|--------------------:|
| False                  |   0.391052 |               12428 |
| True                   |   0.608948 |               12428 |


When 'First Blood' is 'False', the win rate is approximately 39.1052%, with a total of 12,428 matches played.
When 'First Blood' is 'True', the win rate increases significantly to approximately 60.8948%, with the same total of 12,428 matches played.
This data suggests a strong correlation between securing the 'First Blood' and a team's or player's chances of winning, as the win rate is much higher when 'First Blood' is achieved. It is important to note that correlation does not imply causation, and there could be other factors at play that influence the win rate. However, the data presents a clear trend that achieving 'First Blood' is associated with a higher success rate in matches.

The fact that the number of matches is the same for both categories indicates that the dataset is balanced, which gives equal weight to both scenarios in the analysis. This means that the win rate comparison is fair and not skewed by a larger sample size in one category over the other.

This information can be valuable for teams or players in strategizing to prioritize securing the first point or score, as it appears to have a meaningful impact on the outcome of the matches.

### Assessment of Missingness

#### NMAR Analysis
I'm going to assume that the url is NMAR, because actually this column has nothing to do with information about the game itself, it's just a website that records information about the game, so I'm going to assume it's NMAR. I'm assuming that the missing url will be affected by the result, and I'm assuming that the losing party sometimes doesn't want to upload its own data, so if the missing url is affected by the result, then it will become MAR.

<iframe src="assets/Missingness of URL Dependent on Result.html" width=800 height=600 frameBorder=0></iframe>


#### Analysis Report: Missingness of 'URL' Independent of 'Result'

#### Executive Summary:

A permutation test was executed to investigate the dependency of missing 'url' data on the 'result' (win or loss) of the matches in the dataset.The observed standard deviation of the proportions of missingness was 0.0, indicating no variability in the missingness of 'url' across the different results.The p-value of the permutation test is 1.0, suggesting that the missingness of 'url' is independent of the 'result'.  #### Test Methodology:The permutation test involved shuffling the 'result' column 1,000 times to simulate the null hypothesis that the missingness of 'url' is independent of the match outcome.For each permutation, the standard deviation of the proportions of missing 'url' data was calculated, assuming the 'result' categories had been randomized.  #### Observed Statistics:The observed standard deviation of the proportions of missing 'url' data across 'result' categories was 0.0. This implies that there is no variation in missing 'url' data between wins and losses.  #### Results:The null distribution of the standard deviation of proportions is centered near zero and does not overlap with the observed statistic, which is exactly at 0.0.  The p-value is 1.0, which is the highest possible p-value, leading us to fail to reject the null hypothesis.  #### Interpretation:The lack of variation in the missingness of 'url' across different 'result' categories indicates that the missing data is not dependent on whether a match is won or lost.   This could mean that the missing 'url' data is likely due to factors not related to the match outcome.  #### Consequences for Analysis:Given that the missingness of 'url' appears to be independent of 'result', we can assume that the missingness is Missing Completely At Random (MCAR) with respect to match outcomes.This simplifies the treatment of missing data because it suggests that the missingness does not introduce bias related to the 'result'.  #### Recommendations:The analysis can proceed without imputing missing 'url' data based on 'result', but further investigation into other potential dependencies is advised.   It may still be beneficial to explore if there are other factors associated with the missing 'url' data, or if it can be considered MCAR across all aspects of the dataset.  ---This report explains that the missingness of 'url' seems to be random concerning the 'result' of the matches, and no further action is needed specifically regarding this relationship. However, it's always good practice to explore other potential factors that might explain the missingness, as this could impact the analysis and conclusions drawn from the dataset.

<iframe src="assets/Missingness of URL Dependent on League.html" width=800 height=600 frameBorder=0></iframe>

#### Analysis Report: Missingness of 'URL' Dependent on 'League'

#### Executive Summary:
A permutation test was conducted to assess whether the missingness of the 'url' column in our dataset depends on the 'league' column. The test statistic used was the standard deviation of the proportions of missing values across leagues. The results indicated a significant dependency of missingness on league categories, with a p-value of 0.0 and an observed statistic of 0.2423.

#### Test Methodology:
We performed a permutation test involving 1,000 permutations. In each permutation, the 'league' column was shuffled, and the standard deviation of the proportions of missing 'url' values for each league (now randomly assigned) was recalculated. This process generated a null distribution of the test statistic under the hypothesis that the missingness of 'url' does not depend on 'league'.

#### Observed Statistics:
The standard deviation of the actual proportions of missing 'url' data across leagues was 0.2423. This value was compared against the null distribution of the standard deviation obtained from the permutation test.

#### Results:
The observed standard deviation was markedly higher than any of the standard deviations in the null distribution, as illustrated by the empirical histogram. With the p-value calculated at 0.0, we reject the null hypothesis that the missingness of 'url' is independent of 'league'.

#### Interpretation:
The significant p-value suggests that the missingness of 'url' is not occurring at random with respect to 'league'. This implies that certain leagues may have systemic factors causing URLs to be more frequently unrecorded. The strong dependency indicates that the mechanism behind the missing 'url' data is related to league-specific practices or policies.

#### Consequences for Analysis:
Given the NMAR (Not Missing At Random) nature of 'url', care must be taken when performing analyses that include this variable. Missingness correction techniques such as imputation should be considered carefully, as the pattern of missingness could bias any results if not appropriately handled.

#### Recommendations:
Further investigation into why certain leagues have higher rates of missing 'url' data is recommended. Understanding the data collection process for each league could help identify the underlying causes of missingness and suggest possible corrective actions. For future analyses, it may also be worth considering the collection of additional data that could explain the missingness, transforming it into a Missing At Random (MAR) scenario.



### Hypothesis Testing


- **Null Hypothesis (H0)**: The team that gets first blood does not affect the chance of winning the match.
- **Alternative Hypothesis (H1)**: The team that gets first blood increases the chance of winning the match.
- **Test Statistic**: The difference in win rates between teams that the team that gets one blood first and those that do not.
- **Significance Level (α)**: 0.05.
- **p-value**: 0. The p-value is so small that it did not occur in any of the permutations, suggesting the result is highly significant.
- **Conclusion**: Based on the permutation test, we reject the null hypothesis and conclude that there is a statistically significant association between the team that gets first blood and winning the match.

<iframe src="assets/Hypothesis.html" width=800 height=600 frameBorder=0></iframe>

1. **Observed Statistic**: The red dashed line represents the observed statistic from your actual data.      If this line is far from the center of the distribution (which seems to be the case based on your description), it indicates that the observed effect (the impact of capturing the first tower on winning) is highly unusual under the null hypothesis of no effect.

2. **Hypothesis Test Interpretation**: Since you mentioned that the p-value from this test is 0, it means that none of the permuted differences were as extreme as the observed difference.      This provides strong evidence against the null hypothesis and suggests that capturing the first blood has a statistically significant impact on winning the match.

3. **Statistical Significance**: The fact that the observed statistic lies outside the range of most permuted differences (assuming the red line is far to the right of the histogram peak) implies that the result is statistically significant, and you would reject the null hypothesis in favor of the alternative hypothesis.

4. **Implications**: While the test provides evidence that there is a statistically significant association between first blood and a higher probability of winning, it does not prove causality.    Other confounding factors could also be influencing the match outcome.

In summary, this histogram and your p-value of 0 confirm that the impact of  first blood on winning is statistically significant and not likely due to random chance. 
