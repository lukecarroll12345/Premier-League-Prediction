# Predicting Premier League Performances

CT4101 Machine Learning Portfolio Project

## Project question
Can we predict whether a Premier League team playing at home will score at least two goals in a match?
This is a binary classification problem:

- Target = 0: the team scored fewer than 2 goals
- Target = 1: the team scored 2 or more goals

  ## Data
- The source for the data is football-data.co.uk (Premier League, 2021/22 to 2025/26)
- The original datasets for the five Premier League seasons were combined into a single dataset. AI tools assisted with this process, and the resulting dataset was checked to ensure the seasons were combined correctly and the data was suitable for the project.

## Citation
England football results betting odds: Premiership results & betting odds (2026) Football Betting - Football Results - Free Bets. Available at: https://football-data.co.uk/englandm.php (Accessed: 25 September 2026). 

## Prediction
For each match, the features are the average goals, shots, and shots on target from that team's previous 5 home games. I will use these three features to predict whether the team scores 2+ goals in their next home match.

## Audit
During the audit of my data, there is clearly a lot of missing values, with some columns even having as high as 86% of its entries missing. However, when I looked at the columns that I wanted to make my predictions, there was no missing values, which means I don't have to worry about the previous missing values. There was also no duplicates among the columns I was interested in.
However, for one of the games, I saw that the away shots on target was greater than the away shots. This is obviously impossible so it is incorrect information. But since I'm only predicting whether the home team will score 2 or more goals in a game, I don't have to worry about this incorrect value.

## Split
In my dataset, every match appeared twice, first looking at the home team, then the away team. Since I'm only predicting whether a home team will score 2 or more goals in a game, I filtered my data set to only contain the data on the team playing at home.
I then wanted to make a function that would compute the average goals, shots and shots on target by each team in their last 5 games. So first I sorted each team, so the goals scored by each team didn't get mixed up. I couldn't find a function in the notes that computed average so Ai assistance was used creating the function. Although, all the averages are computed then, test set included, shift(1) ensures that when making a prediction for a game, it can only see the results of the games before that game.
The split was then done by season, since there is 5 total season in my dataset, I did my split by date, sorting the first 3 seasons into training, 1 season into validation and 1 season into test.

## Baseline
My Dummy baseline looks at what occurs more often, a team scores 2+ goals or it doesn't. A team scoring 2+ goals happens more often so it predicts this every time for the validation set and gets it right roughly 55.5% of the time

## Assistance Declaration
I used AI (Claude) to help with the pandas rolling averages function (shift and rolling) and to ensure the scikit-learn pipeline was set up correctly to prevent leakage. All code was reviewed and understood by me.

## Setup and Run Instructions

1. Install the required packages:
   pip install -r requirements.txt
2. Open `Split_and_Baseline.ipynb` in Jupyter Notebook or JupyterLab.
3. Click **Kernel → Restart & Run All** to run the entire notebook from top to bottom.

## Repository link
https://github.com/lukecarroll12345/Premier-League-Prediction







