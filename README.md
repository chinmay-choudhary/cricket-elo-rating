### Elo Rating
Elo rating is a method for calculating the relative skill levels of players in competitor-versus-competitor games such as chess and cricket. Originally developed by Arpad Elo, a Hungarian-American physics professor, the Elo rating system has been adapted for various sports, including cricket.

Understanding the Elo Rating System in Cricket
1. Basic Concept:
    - Purpose: The Elo system aims to provide a clear and accurate representation of a team's current strength based on their performance in matches.
    - Dynamic: Ratings are adjusted after each game, ensuring they reflect recent performances and outcomes.

### Elo Rating Update Formula

The Elo rating is updated after each game based on the game outcome. The new rating  R'  is calculated using the formula:

$$
R' = R + K \times (S - E)
$$

Where:
-  R  is the current rating.
-  R'  is the new rating.
-  K  is the K-factor, which determines the maximum possible change in rating.
-  S  is the score of the game (1 for a win, 0.5 for a draw, and 0 for a loss).
-  E  is the expected score.

### Expected Score Formula

The expected score  E  of a player/team is calculated using the formula:

$$
E = \frac{1}{1 + 10^{(R_{\text{opponent}} - R) / 400}}
$$

Where:
-  R  is the player's/team's current rating.
-  R<sub>opponent</sub>  is the opponent's rating.

These formulas are used to calculate and update Elo ratings based on match outcomes, taking into account the relative strength of the opponents as indicated by their current ratings.

### Current IPL teams and their elo ratings

Assuming Punjab Kings and Delhi Capitals as seperate teams than Kings XI Punjab and Delhi Daredevils respectively

|team                       |eloRating|totalMatches|wins|ties|winRatio|
|---------------------------|---------|------------|----|----|--------|
|Chennai Super Kings        |1135.425 |224         |131 |1   |0.585   |
|Royal Challengers Bangalore|1112.128 |240         |114 |3   |0.475   |
|Gujarat Titans             |1087.169 |33          |23  |0   |0.697   |
|Mumbai Indians             |1059.75  |247         |138 |4   |0.559   |
|Punjab Kings               |1036.382 |42          |19  |0   |0.452   |
|Lucknow Super Giants       |1009.63  |30          |17  |0   |0.567   |
|Delhi Capitals             |989.095  |77          |38  |3   |0.494   |
|Rajasthan Royals           |974.368  |206         |101 |3   |0.49    |
|Kolkata Knight Riders      |944.87   |237         |119 |4   |0.502   |
|Sunrisers Hyderabad        |900.657  |166         |78  |4   |0.47    |

Assuming Punjab Kings and Delhi Capitals as same teams than Kings XI Punjab and Delhi Daredevils respectively

|team                       |eloRating|totalMatches|wins|ties|winRatio|
|---------------------------|---------|------------|----|----|--------|
|Chennai Super Kings        |1139.05  |224         |131 |1   |0.585   |
|Royal Challengers Bangalore|1116.023 |240         |114 |3   |0.475   |
|Gujarat Titans             |1084.687 |33          |23  |0   |0.697   |
|Mumbai Indians             |1057.1   |247         |138 |4   |0.559   |
|Punjab Kings               |1044.234 |232         |104 |4   |0.448   |
|Lucknow Super Giants       |1017.219 |30          |17  |0   |0.567   |
|Delhi Capitals             |996.838  |238         |105 |4   |0.441   |
|Rajasthan Royals           |976.936  |206         |101 |3   |0.49    |
|Kolkata Knight Riders      |944.597  |237         |119 |4   |0.502   |
|Sunrisers Hyderabad        |901.56   |166         |78  |4   |0.47    |

All time IPL elo ratings including teams not in the league anymore

|team                       |eloRating|totalMatches|wins|ties|winRatio|
|---------------------------|---------|------------|----|----|--------|
|Chennai Super Kings        |1139.05  |224         |131 |1   |0.585   |
|Royal Challengers Bangalore|1116.023 |240         |114 |3   |0.475   |
|Gujarat Titans             |1084.687 |33          |23  |0   |0.697   |
|Mumbai Indians             |1057.1   |247         |138 |4   |0.559   |
|Punjab Kings               |1044.234 |232         |104 |4   |0.448   |
|Lucknow Super Giants       |1017.219 |30          |17  |0   |0.567   |
|Delhi Capitals             |996.838  |238         |105 |4   |0.441   |
|Gujarat Lions              |991.245  |30          |13  |1   |0.433   |
|Rising Pune Supergiants    |980.733  |30          |15  |0   |0.5     |
|Rajasthan Royals           |976.936  |206         |101 |3   |0.49    |
|Kochi Tuskers Kerala       |962.662  |14          |6   |0   |0.429   |
|Kolkata Knight Riders      |944.597  |237         |119 |4   |0.502   |
|Sunrisers Hyderabad        |901.56   |166         |78  |4   |0.47    |
|Pune Warriors              |900.063  |46          |12  |0   |0.261   |
|Deccan Chargers            |887.053  |75          |29  |0   |0.387   |
