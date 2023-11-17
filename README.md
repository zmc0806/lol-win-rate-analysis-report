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

Since both teams will have the same game_id for each match, there will be winners for every match there will be losers, so the distribution should be exactly,here is not。That means I missed the details when I was cleaning the data, so I need to go back and check.

I found the problem, some gamids have false results for both teams, both teams can't lose in the same game, this is obviously not normal, so I need to rule this out.

```python
# I cleaned the data again to make sure that only the results of the game were normal
mask = team_data_cleaned.groupby('gameid')['result'].transform('sum') == 1
team_data_cleaned = team_data_cleaned[mask]
team_data_cleaned.head()
```
'| gameid                | datacompleteness   | url                                         | league   |   year | split   | playoffs   | date                |   game |   patch |   participantid | side   | position   | teamname                      | teamid                                  | ban1     | ban2         | ban3         | ban4     | ban5    |   gamelength | result   |   kills |   deaths |   assists |   teamkills |   teamdeaths |   doublekills |   triplekills |   quadrakills |   pentakills | firstblood   |   team kpm |   ckpm | firstdragon   |   dragons |   opp_dragons |   elementaldrakes |   opp_elementaldrakes |   infernals |   mountains |   clouds |   oceans |   chemtechs |   hextechs |   dragons (type unknown) |   elders |   opp_elders | firstherald   |   heralds |   opp_heralds | firstbaron   |   barons |   opp_barons | firsttower   |   towers |   opp_towers | firstmidtower   | firsttothreetowers   |   turretplates |   opp_turretplates |   inhibitors |   opp_inhibitors |   damagetochampions |     dpm |   damagetakenperminute |   damagemitigatedperminute |   wardsplaced |    wpm |   wardskilled |   wcpm |   controlwardsbought |   visionscore |   vspm |   totalgold |   earnedgold |   earned gpm |   goldspent |        gspd |   minionkills |   monsterkills |   monsterkillsownjungle |   monsterkillsenemyjungle |     cspm |   goldat10 |   xpat10 |   csat10 |   opp_goldat10 |   opp_xpat10 |   opp_csat10 |   golddiffat10 |   xpdiffat10 |   csdiffat10 |   killsat10 |   assistsat10 |   deathsat10 |   opp_killsat10 |   opp_assistsat10 |   opp_deathsat10 |   goldat15 |   xpat15 |   csat15 |   opp_goldat15 |   opp_xpat15 |   opp_csat15 |   golddiffat15 |   xpdiffat15 |   csdiffat15 |   killsat15 |   assistsat15 |   deathsat15 |   opp_killsat15 |   opp_assistsat15 |   opp_deathsat15 |\n|:----------------------|:-------------------|:--------------------------------------------|:---------|-------:|:--------|:-----------|:--------------------|-------:|--------:|----------------:|:-------|:-----------|:------------------------------|:----------------------------------------|:---------|:-------------|:-------------|:---------|:--------|-------------:|:---------|--------:|---------:|----------:|------------:|-------------:|--------------:|--------------:|--------------:|-------------:|:-------------|-----------:|-------:|:--------------|----------:|--------------:|------------------:|----------------------:|------------:|------------:|---------:|---------:|------------:|-----------:|-------------------------:|---------:|-------------:|:--------------|----------:|--------------:|:-------------|---------:|-------------:|:-------------|---------:|-------------:|:----------------|:---------------------|---------------:|-------------------:|-------------:|-----------------:|--------------------:|--------:|-----------------------:|---------------------------:|--------------:|-------:|--------------:|-------:|---------------------:|--------------:|-------:|------------:|-------------:|-------------:|------------:|------------:|--------------:|---------------:|------------------------:|--------------------------:|---------:|-----------:|---------:|---------:|---------------:|-------------:|-------------:|---------------:|-------------:|-------------:|------------:|--------------:|-------------:|----------------:|------------------:|-----------------:|-----------:|---------:|---------:|---------------:|-------------:|-------------:|---------------:|-------------:|-------------:|------------:|--------------:|-------------:|----------------:|------------------:|-----------------:|\n| ESPORTSTMNT01_2690210 | complete           | nan                                         | LCKC     |   2022 | Spring  | False      | 2022-01-10 07:44:08 |      1 |   12.01 |             100 | Blue   | team       | Fredit BRION Challengers      | oe:team:68911b3329146587617ab2973106e23 | Karma    | Caitlyn      | Syndra       | Thresh   | Lulu    |         1713 | False    |       9 |       19 |        19 |           9 |           19 |             0 |             0 |             0 |            0 | True         |     0.3152 | 0.9807 | False         |         1 |             3 |                 1 |                     3 |           0 |           0 |        0 |        0 |           0 |          1 |                      nan |        0 |            0 | True          |         2 |             0 | False        |        0 |            0 | True         |        3 |            6 | True            | True                 |              5 |                  0 |            0 |                1 |               56560 | 1981.09 |                3537.2  |                    2364.73 |            74 | 2.5919 |            51 | 1.7863 |                   33 |           197 | 6.9002 |       47070 |        28222 |      988.511 |       44570 | -0.0283123  |           680 |            160 |                     nan |                       nan |  29.4221 |      16218 |    18213 |      322 |          14695 |        18076 |          330 |           1523 |          137 |           -8 |           3 |             5 |            0 |               0 |                 0 |                3 |      24806 |    28001 |      487 |          24699 |        29618 |          510 |            107 |        -1617 |          -23 |           5 |            10 |            6 |               6 |                18 |                5 |\n| ESPORTSTMNT01_2690210 | complete           | nan                                         | LCKC     |   2022 | Spring  | False      | 2022-01-10 07:44:08 |      1 |   12.01 |             200 | Red    | team       | Nongshim RedForce Challengers | oe:team:d2dc3681437e2beb2bb4742477108ff | Lee Sin  | Twisted Fate | Zoe          | Nautilus | Rell    |         1713 | True     |      19 |        9 |        62 |          19 |            9 |             6 |             0 |             0 |            0 | False        |     0.6655 | 0.9807 | True          |         3 |             1 |                 3 |                     1 |           2 |           1 |        0 |        0 |           0 |          0 |                      nan |        0 |            0 | False         |         0 |             2 | False        |        0 |            0 | False        |        6 |            3 | False           | False                |              0 |                  5 |            1 |                0 |               79912 | 2799.02 |                3009.67 |                    2872.33 |            93 | 3.2574 |            51 | 1.7863 |                   45 |           205 | 7.1804 |       52617 |        33769 |     1182.8   |       45850 |  0.0283123  |           792 |            184 |                     nan |                       nan |  34.1856 |      14695 |    18076 |      330 |          16218 |        18213 |          322 |          -1523 |         -137 |            8 |           0 |             0 |            3 |               3 |                 5 |                0 |      24699 |    29618 |      510 |          24806 |        28001 |          487 |           -107 |         1617 |           23 |           6 |            18 |            5 |               5 |                10 |                6 |\n| ESPORTSTMNT01_2690219 | complete           | nan                                         | LCKC     |   2022 | Spring  | False      | 2022-01-10 08:38:24 |      1 |   12.01 |             100 | Blue   | team       | T1 Challengers                | oe:team:6dcacec00a6ba7576c5ab7f30c995cd | Sona     | Jarvan IV    | Caitlyn      | Lulu     | Lucian  |         2114 | False    |       3 |       16 |         7 |           3 |           16 |             0 |             0 |             0 |            0 | False        |     0.0851 | 0.5393 | False         |         1 |             4 |                 1 |                     4 |           0 |           1 |        0 |        0 |           0 |          0 |                      nan |        0 |            0 | True          |         1 |             1 | False        |        0 |            2 | False        |        3 |           11 | False           | False                |              2 |                  3 |            0 |                2 |               59579 | 1690.98 |                2984.02 |                    3109.61 |           119 | 3.3775 |            55 | 1.561  |                   47 |           277 | 7.8619 |       57629 |        34688 |      984.522 |       53945 | -0.207137   |           994 |            215 |                     nan |                       nan |  34.3141 |      14939 |    17462 |      317 |          16558 |        19048 |          344 |          -1619 |        -1586 |          -27 |           1 |             1 |            3 |               3 |                 3 |                1 |      23522 |    28848 |      533 |          25285 |        29754 |          555 |          -1763 |         -906 |          -22 |           1 |             1 |            3 |               3 |                 3 |                1 |\n| ESPORTSTMNT01_2690219 | complete           | nan                                         | LCKC     |   2022 | Spring  | False      | 2022-01-10 08:38:24 |      1 |   12.01 |             200 | Red    | team       | Liiv SANDBOX Challengers      | oe:team:5380cdbc2ad2b8082624f48f99f6672 | LeBlanc  | Yuumi        | Twisted Fate | Karma    | Alistar |         2114 | True     |      16 |        3 |        39 |          16 |            3 |             1 |             0 |             0 |            0 | True         |     0.4541 | 0.5393 | True          |         4 |             1 |                 4 |                     1 |           0 |           2 |        1 |        0 |           0 |          1 |                      nan |        0 |            0 | False         |         1 |             1 | True         |        2 |            0 | True         |       11 |            3 | True            | True                 |              3 |                  2 |            2 |                0 |               74855 | 2124.55 |                2745.72 |                    2868.42 |           129 | 3.6613 |            70 | 1.9868 |                   65 |           346 | 9.8202 |       71004 |        48063 |     1364.13  |       66410 |  0.207137   |          1013 |            244 |                     nan |                       nan |  35.6764 |      16558 |    19048 |      344 |          14939 |        17462 |          317 |           1619 |         1586 |           27 |           3 |             3 |            1 |               1 |                 1 |                3 |      25285 |    29754 |      555 |          23522 |        28848 |          533 |           1763 |          906 |           22 |           3 |             3 |            1 |               1 |                 1 |                3 |\n| 8401-8401_game_1      | partial            | https://lpl.qq.com/es/stats.shtml?bmid=8401 | LPL      |   2022 | Spring  | False      | 2022-01-10 09:24:26 |      1 |   12.01 |             100 | Blue   | team       | Oh My God                     | oe:team:f4c4528c6981e104a11ea7548630c23 | Renekton | Lee Sin      | Caitlyn      | Jayce    | Camille |         1365 | True     |      13 |        6 |        35 |          13 |            6 |           nan |           nan |           nan |          nan | False        |     0.5714 | 0.8352 | True          |         2 |             1 |               nan |                   nan |         nan |         nan |      nan |      nan |         nan |        nan |                        2 |      nan |          nan | True          |       nan |           nan | True         |        1 |            0 | True         |        8 |            3 | True            | True                 |            nan |                nan |            1 |                0 |               40086 | 1762.02 |                2263.25 |                     nan    |            79 | 3.4725 |            33 | 1.4505 |                   32 |           162 | 7.1209 |       45468 |        30167 |     1326.02  |       36908 | -0.00586225 |           nan |            172 |                      98 |                        18 | nan      |        nan |      nan |      nan |            nan |          nan |          nan |            nan |          nan |          nan |         nan |           nan |          nan |             nan |               nan |              nan |        nan |      nan |      nan |            nan |          nan |          nan |            nan |          nan |          nan |         nan |           nan |          nan |             nan |               nan |              nan |'
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

