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

### Data Collection and Prep
The data used for calculating the elo ratings can be downloaded and unzipped using the following code:

```python
import requests
import zipfile
import os

def downloadFile(url, destination):
    fileId = url.split('/')[5]
    

    url='https://drive.google.com/uc?export=download&id=' + fileId
    

    response = requests.get(url)
    

    with open(destination, "wb") as file:
        file.write(response.content)

fileUrl = "https://drive.google.com/file/d/1Emdky32QEjyNCAxktJV90G2hxO8NHEeI/view?usp=sharing"  
path = "ipl_json.zip"  
downloadFile(fileUrl,path)
with zipfile.ZipFile(path, 'r') as zip_ref:
        zip_ref.extractall()
```

This download the match-by-match data which are stored in json files and stored in the zip file

We then extract the relevant data:

```python
import os
import json
import pandas as pd
import pydash
import plotly.express as px
import plotly.graph_objects as go
from plotly.subplots import make_subplots
import numpy as np


'''
@function getValueFromJson
@description Returns value from the json for the key passed
@default returns zero if key is not found
'''
def getValueFromJson(json, key, default=0): 
    return pydash.get(json, key, default)

path = 'ipl_json/'
filelist = os.listdir(path)

extract = []
playOffEvent = set()
for file in filelist:
    with open(f'{path}/{file}','r') as f:
        data = json.load(f)
        event = getValueFromJson(data,'info.event.match_number')
        team1 = getValueFromJson(data,'info.teams.0')
        team2 = getValueFromJson(data,'info.teams.1')
        outcome = getValueFromJson(data,'info.outcome.winner')
        date = getValueFromJson(data,'info.dates.0')
        season = getValueFromJson(data,'info.season')
        if outcome == 0:
            outcome = getValueFromJson(data,'info.outcome.result')
        if event == 0:
            event = getValueFromJson(data,'info.event.stage')
            playOffEvent.add(event)
        extract.append({
            'date' : date,
            'team1' : team1,
            'team2' : team2,
            'outcome' : outcome,
            'event': event,
            'season' : str(season)
        })
```

and then conver the data to a dataframe and clean the data according to our assumption:

```python
df = pd.DataFrame(extract)
df.season.replace(['2007/08','2009/10','2020/21'],['2008','2010','2020'],inplace=True)
df.team1.replace('Rising Pune Supergiant','Rising Pune Supergiants',inplace=True)
df.team2.replace('Rising Pune Supergiant','Rising Pune Supergiants',inplace=True)
df.outcome.replace('Rising Pune Supergiant','Rising Pune Supergiants',inplace=True)

# comment the below code to get rid of the assumption that Kings XI Punjab and Delhi Daredevils are the same teams as Punjab Kings and Delhi Capitals
df.team1.replace('Kings XI Punjab','Punjab Kings',inplace=True)
df.team2.replace('Kings XI Punjab','Punjab Kings',inplace=True)
df.outcome.replace('Kings XI Punjab','Punjab Kings',inplace=True)

df.team1.replace('Delhi Daredevils','Delhi Capitals',inplace=True)
df.team2.replace('Delhi Daredevils','Delhi Capitals',inplace=True)
df.outcome.replace('Delhi Daredevils','Delhi Capitals',inplace=True)
```

this is the sample df:

|date      |team1                      |team2        |outcome                    |event|season|
|----------|---------------------------|-------------|---------------------------|-----|------|
|2014-04-26|Kolkata Knight Riders      |Punjab Kings |Punjab Kings               |15   |2014  |
|2013-04-23|Royal Challengers Bangalore|Pune Warriors|Royal Challengers Bangalore|31   |2013  |
|2013-04-07|Pune Warriors              |Punjab Kings |Punjab Kings               |6    |2013  |
|2016-05-18|Royal Challengers Bangalore|Punjab Kings |Royal Challengers Bangalore|50   |2016  |
|2014-05-14|Sunrisers Hyderabad        |Punjab Kings |Punjab Kings               |39   |2014  |

### Calculation

Once the data collection is done we then iterate over each row of the dataframe and calculate the elo score with these assumptions:

- Each team is given a base elo rating of 1000
- Base K-factor for each match is taken as 30
- Playoff Events are given a higher K-factor 40
- These events were considered playoff events 
    - 'Qualifier 2'
    - 'Qualifier 1'
    - '3rd Place Play-Off'
    - 'Semi Final'
    - 'Eliminator'
    - 'Elimination Final'
    - 'Final'
- If the current difference between the elo rating of team is > 25 the K-factor is increased by a value of 10
- The score S for a win, loss, tie, no result is taken as 1, 0, 0.5, no change repectively

The code used to calculate the rating is:

```python
# calculating expected score
def expectedScore(ratingA, ratingB):
    return 1 / (1 + 10 ** ((ratingB - ratingA) / 400))

# calculating and returning new elo scores based on current score
def updateElo(ratingA, ratingB, outcome, baseKFactor):
    # Calculate the rating difference
    ratingDiff = abs(ratingA - ratingB)

    if ratingDiff > 25:  
        kFactor = baseKFactor + 10  
    else:
        kFactor = baseKFactor

    expectedA = expectedScore(ratingA, ratingB)
    expectedB = expectedScore(ratingB, ratingA)

    if outcome == 'win':
        scoreA = 1
        scoreB = 0
    elif outcome == 'tie':
        scoreA = 0.5
        scoreB = 0.5
    else:  # no_result
        return ratingA,ratingB
        

    newRatingA = ratingA + kFactor * (scoreA - expectedA)
    newRatingB = ratingB + kFactor * (scoreB - expectedB)

    return round(newRatingA, 3), round(newRatingB, 3)
```
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

Elo Rating progression of each team by season:

![](https://github.com/chinmay-choudhary/cricket-elo-rating/blob/develop/output.gif)

to-do
- add elo ratings for test matches
- add elo rating for odi matches
- add elo rating for t20i matches
- add elo rating for womens format as well