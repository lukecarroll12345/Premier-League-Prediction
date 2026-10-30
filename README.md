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
I'm going to get the average goals, shots and shots on target in the team I'm predicting's last 5 home games. I'll then use this to make a prediction on whether this team will score 2 or more goals in its next home game based off this data.

## Audit
During the audit of my data, there is clearly a lot of missing values, with some columns even having as high as 86% of its entries missing. However, when I looked at the columns that I wanted to make my predictions, there was no missing values, which means I don't have to worry about the previous missing values. The was also no duplicates among the columns I was interested in.
However, for one of the games, I saw that the away shots on target was greater than the away shots. This is obviously impossible so it is incorrect information. But since I'm only predicting whether the home team will score 2 or more goals in a game, I don't have to worry about this incorrect value.

## Split
In my dataset, 











