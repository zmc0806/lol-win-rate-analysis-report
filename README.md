# Analysis of the factors that affect the winning rate of League of Legends games
Name:Mianchen Zhang
### Introduction to the Dataset

The dataset provided contains detailed information on over 10,000 competitive matches from the popular video game League of Legends (LoL), a game that has not only captured the interest of millions of players worldwide but also established a significant presence in the esports industry. This dataset is a comprehensive collection of data from the year 2022, reflecting player and team performances, match outcomes, and various in-game statistics.

### Central Question of the Analysis

The central question that this analysis seeks to explore is: **"Is there a significant correlation between the first team to achieve a specific in-game objective - First Tower Captures the overall match victory?"** This question is pivotal for players, coaches, analysts, and fans alike, as it can offer insights into winning strategies and highlight the importance of early-game objectives in competitive play.

### Relevance of the Dataset and the Question

Understanding the correlation between early-game advantages and match outcomes is crucial in the realm of competitive LoL. It can influence team strategies, individual player focus, and overall understanding of what contributes to a team's success. This analysis can shed light on whether certain early-game objectives significantly impact the chances of winning, which is invaluable for players looking to improve their gameplay, teams strategizing for tournaments, and enthusiasts following the competitive scene.

### Dataset Overview

- **Number of Rows**: The dataset initially contains over 10,000 matches, corresponding to multiple rows per match for individual players and teams.

- **Relevant Columns and Descriptions**:
  1. `gameid`: Unique identifier for each match.
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

#### Showcasing the Cleaned DataFrame

To illustrate the result of these cleaning steps, below is a snapshot of the cleaned team summary dataset:

```python
# Displaying the first few rows of the cleaned team summary data
team_data_cleaned.head()
```

This cleaned dataset now serves as the foundation for our analysis, with irrelevant and misleading data removed and the remaining data structured and formatted for accurate interpretation. The cleaning steps, tailored to the unique aspects of this dataset and our specific research question, enhance the validity and reliability of the subsequent analysis.


### Univariate Analysis

##### Distribution of Match Results:
<iframe src="assets/result_distribution.html" width=800 height=600 frameBorder=0></iframe>

Since both teams will have the same game_id for each match, there will be winners for every match there will be losers, so the distribution should be pretty even here, as I expect.

##### Distribution of First Tower Captures in Matches:
<iframe src="assets/plot_firsttower_result.html" width=800 height=600 frameBorder=0></iframe>


The purpose of this diagram is to explain the most basic rules to people who don't know much about it. This picture shows that no matter who can destroy a tower, when the game is won, a tower must be destroyed.

### Bivariate Analysis
Neither the scatter plot nor the box plot makes sense here for my variables because my values are true and false

<iframe src="assets/fig_bi_box.html" width=800 height=600 frameBorder=0></iframe>

<iframe src="assets/fig_bi_scatter.html" width=800 height=600 frameBorder=0></iframe>

##### Distribution of first Tower Destroyed And Match Results:
<iframe src="assets/fig_conditional_firsttower.html" width=800 height=600 frameBorder=0></iframe>

There were 9,079 games in which the team that destroyed a tower first won, and only 3,369 games in which the team that was pushed off a tower won.

##### Distribution of first Tower Destroyed And First Middle Tower Destroyed:
<iframe src="assets/fig_conditional_firstmidtower.html" width=800 height=600 frameBorder=0></iframe>

This graph shows that the team that pushes down the first tower is more likely to push down the first middle tower.It's also easier to win a race by pushing off more towers.

### Interesting Aggregates

| First Tower Captured   |   Win Rate |   Number of Matches |
|:-----------------------|-----------:|--------------------:|
| False                  |   0.316903 |               10631 |
| True                   |   0.636274 |               14269 |


The significance of this table lies in the apparent difference in win rates: teams that capture the first tower have a substantially higher win rate than those that do not.  This suggests that capturing the first tower is a strong indicator of a team's likelihood of winning.

### Assessment of Missingness

#### NMAR Analysis
I'm going to assume that the url is NMAR, because actually this column has nothing to do with information about the game itself, it's just a website that records information about the game, so I'm going to assume it's NMAR. I'm assuming that the missing url will be affected by the result, and I'm assuming that the losing party sometimes doesn't want to upload its own data, so if the missing url is affected by the result, then it will become MAR.

#### Missingness Dependency

<iframe src="assets/NMAR_RESULT.html" width=800 height=600 frameBorder=0></iframe>

This histogram visualizes the empirical distribution of test statistics dependent on the "url" column missing. The observed statistics are marked with vertical dashed lines on the histogram. The "p-value" of the permutation test is "0.983" and the "observed statistic" is about "0.000048", which shows that :- The observed statistic is very close to zero, which indicates that the difference in the proportion of missing between the two groups is very small. The -p value of '0.983' is very high, indicating that there is no significant difference in the miss rate between the groups defined by the 'url' column and the result column. Simply put, we have no evidence that the absence of 'url' depends on the result. So I think the missing url is self-related, so it's NMAR.

### Hypothesis Testing


- **Null Hypothesis (H0)**: Capturing the first tower does not affect the chance of winning the match.
- **Alternative Hypothesis (H1)**: Capturing the first tower increases the chance of winning the match.
- **Test Statistic**: The difference in win rates between teams that capture the first tower and those that do not.
- **Significance Level (α)**: 0.05 (commonly used threshold in hypothesis testing).
- **p-value**: 0. The p-value is so small that it did not occur in any of the permutations, suggesting the result is highly significant.
- **Conclusion**: Based on the permutation test, we reject the null hypothesis and conclude that there is a statistically significant association between capturing the first tower and winning the match.

