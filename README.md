# League of Legends Champion Balance
*Irelia is broken*

by Sawyer Figueroa (s2figuer@ucsd.edu)

---


## Introduction

The dataset being investigated in this analysis is a dataset from Oracle's Elixir at the link: https://oracleselixir.com/tools/downloads.
This dataset contains game data for competitive League of Legend's matches in 2022. Each row corresponds to one player in each game with
two additional rows for full team data. In League of Legends, 10 players play the game at once, with 5 on one team and 5 on the other.
Each player has the option of choosing a "champion" that they will play throughout the entirety of the game. However, not all champions
are equal. The developers, Riot Games, do their best to ensure that each champion is capable of having equal impact on the game as all
others. Some players would argue that this is certainly not the case. "Unbalanced" champion statistics greatly affect the quality
of the game and the competitive integrity of the sport. This dataset of league of legends games at the highest level of play will 
hopefully shed light on what champions truly have a greater impact on the outcome of the game. In this analysis, I will be investigating
the following specific question:


**Does Irelia have more impact on the outcome of competitive matches than other midlaners?**


The dataset in question contains a total of **149400 rows** and **123 columns**. For our purposes, we will only be looking at **17 columns**. These are
*'gameid', 'position', 'champion', 'league', 'playername', 'kills', 'assists', 'teamname','doublekills', 'triplekills', 'quadrakills', 
'pentakills', 'datacompleteness', 'damagetochampions', 'dpm', 'damagemitigatedperminute', and 'totalgold'. The 'gameid', 'playername', 'league',
and 'teamname'* features are important to note because champion picks and overall performance often depend on particular games, players, leagues,
or teams. The 'position' and 'champion' features are arguable the most important because we will need to section the data based on a player's
position (midlane) and champion (Irelia). The remaining columns all generally relate to a specific player's impact on the game. For example,
a player with a pentakill and significantly larger damage to champions certainly had more impact on the game than a player with no multi-kills
and little damage to champions. Without further ado, let's dive in.

---

## Cleaning and EDA

The first step in the cleaning process was extracting the columns of interest as mentioned in the introduction. Second, I removed all rows
pertaining to full team data, as we want to focus and individual players and be sure not to repeat any already known data. The dataset
also contained quite a large amount of missing data, specifically in the 'doublekills', 'triplekills', 'quadrakills', 'pentakills',
and 'damagemitigatedperminute' columns. These values were imputed using *probabilistic imputation* conditioned on champion. The choice
for this imputation was made based on the fact that different champions tend to have different strengths and weaknesses no matter their role.
So it wouldn't be fair to draw a value for the amount of damage to dealt to champions for Yone from the same distribution as Lulu for example.
Missing values from the 'damagetochampions' and 'dpm' columns were dropped as only 10/149400 rows contained missing values. Finally, the
data was filtered to only included players in the 'position' of 'mid'.

Here is final cleaned dataframe:


| gameid                | position   | champion   | league   | playername   |
|:----------------------|:-----------|:-----------|:---------|:-------------|
| ESPORTSTMNT01_2690210 | mid        | LeBlanc    | LCK CL   | Feisty       |
| ESPORTSTMNT01_2690210 | mid        | Viktor     | LCK CL   | FIESTA       |
| ESPORTSTMNT01_2690219 | mid        | Orianna    | LCK CL   | TolanD       |
| ESPORTSTMNT01_2690219 | mid        | Renekton   | LCK CL   | Ten10        |
| 8401-8401_game_1      | mid        | Syndra     | LPL      | Creme        |


|   kills |   assists | teamname                      |   doublekills |   triplekills |
|--------:|----------:|:------------------------------|--------------:|--------------:|
|       2 |         3 | Fredit BRION Challengers      |             0 |             0 |
|       6 |        12 | Nongshim RedForce Challengers |             2 |             0 |
|       2 |         0 | T1 Challengers                |             0 |             0 |
|       5 |         7 | Liiv SANDBOX Challengers      |             0 |             0 |
|       3 |         5 | Oh My God                     |             0 |             0 |


|   quadrakills |   pentakills | datacompleteness   |   damagetochampions |     dpm |   damagemitigatedperminute |   totalgold |   impact_score |
|--------------:|-------------:|:-------------------|--------------------:|--------:|---------------------------:|------------:|---------------:|
|             0 |            0 | complete           |               14258 | 499.405 |                    227.776 |        9715 |        7.65728 |
|             0 |            0 | complete           |               20690 | 724.693 |                    426.935 |       11532 |       13.7298  |
|             0 |            0 | complete           |               23082 | 655.118 |                    366.698 |       15149 |       12.5572  |
|             0 |            0 | complete           |               11261 | 319.612 |                    950.095 |       14999 |       11.5279  |
|             0 |            0 | partial            |               12577 | 552.835 |                    126.741 |        9928 |        7.55822 |




<iframe src="assets/midlane_kills.html" width=800 height=600 frameBorder=0></iframe>

The previous figure depicts the distribution of the number of kills obtained by midlaners across all games. It appears that the majority
of midlaners secure 0-5 kills while very few get above 10 kills.

<iframe src="assets/pos_impact_scores.html" width=800 height=600 frameBorder=0></iframe>

Looking at the impact score distributions across all roles, we can see that support players tend to have lower impact while midlaners
and botlaners have higher impact. This supports our decision to limit the analysis to midlaners, so as not to have confounding variables
in our final results.


|league  |     LCS |     LCK |     LEC |     LPL |
|champion|         |         |         |         |
|-------:|--------:|--------:|--------:|--------:|
|Irelia  | 17836   | 15323   | 10744   | 12474   |
|Sylas   | 14356.7 | 14518.6 | 17322.7 | 14935.2 |
|Malzahar| 16165   | 11252   | 24040   |   nan   |
|Akshan  |   nan   |   nan   |  8853   |   nan   |
|Ahri    | 15417.5 | 16258.1 | 20015   | 16721.3 |


In this table, we compare the total damage to champion average aggregates between 4 popular competitive leagues and 5 popular midlane champions.
It's clear that Irelia has varying performance in different regions due to differences in playstyle, therefore, it could be the case that
Irelia is more impactful to the game in some leagues, but not in others.

---

## Assessment of Missingness

In assessing the missingness of the original dataframe with columns of interest selected, I decided to compare the distributions of the variables
*'league' and 'totalgold'* conditioned on the missingness of the variable 'teamname'. For both cases, I performed a permutation test with
5000 empirical iterations and calculated p-value based on the observed test statistic (total variation distance for 'league' and absolute
mean difference for 'totalgold').

Below is a plot comparing the distributions 'leagues' when 'teamname' is missing vs. when it is not missing.

<iframe src="assets/league_dist.html" width=800 height=600 frameBorder=0></iframe>

The p-value of the permutation test was **0.0**, leading us to *reject* the null hypothesis that distributions come from the same population at the
0.05 and 0.01 significance level. This indicates that, as expected from the plot, the missingness of 'teamname' is dependent on the 
league the match took place under for this dataset. This is an example of MNAR.

Below is a plot showing the distribution of the test statistic for the permutation test comparing the distribution of 'totalgold' 
when 'teamname' is missing vs. when it is not missing.

<iframe src="assets/hyp2.html" width=800 height=600 frameBorder=0></iframe>

The p-value of the permutation test was **0.4312**. We therefore *cannot reject* the null hypothesis that distributions came from the same
population and cannot conclude that this is an example of NMAR.

---

## Hypothesis Testing

---

Finally, we move on to a hypothesis test that attempts to answer the champion in question, Irelia. For this hypothesis test we will be using
a permutation test to see whether or not the distribution of impact scores of Irelia and from other champions come from the same population.
Our **null hypothesis** will be that there is no difference between the distribution of Irelia's impact score and that of other midlaners and our
**alternative hypothesis** will be that the distribution of Irelia's impact score is greater than that of other midlaners. The test statistic we
will be using to perform this permutation test is the **difference in mean impact score** between both groups as shown below:

```py
observed = mid_df[mid_df['champion']=='Irelia']['impact_score'].mean() - mid_df[~(mid_df['champion']=='Irelia')]['impact_score'].mean()
```

It is proper to use a permutation test here because two full distributions are being compared. The difference in means is a valid test
statistic because it accurately sums up the central difference between impact scores of Irelia and other champions. We will not be using
the absolute difference in the mean here because we are interested in whether or not Irelia's impact score is *higher* than that of
other champions as stated by the alternative hypothesis.

The observed distributions look as follows:

<iframe src="assets/irelia_v_others.html" width=800 height=600 frameBorder=0></iframe>

Additionally, we will use a **significance level** of 0.05. After performing the test with 5000 simulated iterations a **p-value** of 0.0232
was obtained. We can therefore *reject* the null hypothesis that the distribution of impact scores from Irelia and from other champions come
from the same population at the 0.05 significance level. 

Although, this is something anyone could figure out by simply playing League of Legends, it is important to reveal imbalances in player
performance based on inherent factors in the game and not just skill issues. Hopefully, further analyses will incite changes in the
development of the game and increase gaming quality and enjoyment for everyone. Thanks for reading!

---

# FIN.