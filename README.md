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
My Dummy baseline looks at what occurs more often, a team scores 2+ goals or it doesn't. A team scoring less than 2 goals is the majority class. A team scoring less than 2 goals happens more often so it predicts this every time for the validation set and gets it right roughly 55.5% of the time. There is a Dummy ROC-AUC of 50% because it assigns the same probability to every match.

## Logistic regression
While Logistic Regression improved the accuracy to 58.68% and the ROC-AUC to 60.06%, it successfully achieved an F1 score of 0.478 for the positive class. This suggests the model is actively identifying high scoring games rather than just relying on the majority class.

## Model comparison: 3 features (own attack only)

The first comparison used three features: the home team's average goals, shots and shots on target over their previous
5 home games. All models were evaluated with time-ordered cross-validation (`TimeSeriesSplit`, 5 folds) on the training
seasons (2021/22 to 2023/24), using ROC-AUC as the main metric. The dummy baseline scored an accuracy of 0.519 and a
ROC-AUC of 0.500. Every learned model beat it on ROC-AUC, but only by small margins. Logistic Regression performed best
(ROC-AUC 0.601, accuracy 0.593, F1 0.446) and was also the most stable across folds (standard deviation 0.030). Random
Forest (ROC-AUC 0.583, std 0.050) and Gradient Boosting (0.569, std 0.039) came next, and k-NN was barely above the
baseline (0.540, std 0.045). The two tree ensembles and k-NN had slightly higher F1 scores (about 0.47) than Logistic
Regression, meaning they predicted the "2+ goals" class more often, but at the cost of accuracy. The more complex models
did not beat the simple one, and the Random Forest was much slower to fit. A likely reason is that these features only
describe the home team's own attacking form, which carries little predictive ability.

## Model comparison: 5 features (adding the opponent's defensive record)

The second comparison added two features: the opponent's average goals conceded and shots on target conceded over their
previous 5 away games. These use only matches played before the one being predicted (using `shift(1)`), so they do not
leak future information. The same models, settings and cross validation folds were used, so the only difference
is the features. ROC-AUC improved for all four learned models: Logistic Regression rose from 0.601 to 0.633, k-NN from
0.540 to 0.575, Random Forest from 0.583 to 0.602 and Gradient Boosting from 0.569 to 0.582. F1 also improved for every
model (for example Logistic Regression from 0.446 to 0.548), while accuracy changed very little, and was flat or
slightly lower for Random Forest and Gradient Boosting. Because the improvement appears across all models, it is unlikely
to be a fluke, which supports the idea that the opponent's defence adds useful information. However, the fold-to-fold
standard deviations (0.03 to 0.05) are similar in size to the gains, so the size of each improvement is uncertain.
Logistic Regression remained the best model, and the 5-feature set was used for all later tuning and validation.

## Tuning

Each model was tuned with `GridSearchCV` on the training seasons only (2021/22-2023/24), using the same time-ordered
`TimeSeriesSplit` (5 folds) and ROC-AUC as the metric, on the 5-feature set. The validation and test seasons were not used.

| Model | CV ROC-AUC before | CV ROC-AUC after | Selected settings |
|---|---|---|---|
| Logistic Regression | 0.633 | 0.634 | C=0.1 (default C=1.0 kept, as the gain is negligible) |
| Random Forest | 0.602 | about 0.621 | shallow trees (max_depth 6), min_samples_leaf=10, 100 trees |
| k-NN | 0.575 | 0.602 | k=21, Manhattan distance (p=1), uniform weights |
| Gradient Boosting | 0.582 | 0.632 | learning_rate=0.01, max_depth=1, 200 trees |

Tuning helped the three more flexible models, and in each case it did so by choosing simpler, more heavily regularised
settings (larger k, shallower trees, a slower learning rate), which suits a noisy problem with few features. Logistic
regression barely changed. The gains are similar in size to the fold to fold standard deviation (0.03-0.05), so they
should be seen as modest.

## Validation results and confusion matrices (2024/25 season)

| Model | Accuracy | F1 | ROC-AUC |
|---|---|---|---|
| Dummy baseline | 0.555 | 0.000 | 0.500 |
| Logistic Regression | 0.571 | 0.486 | 0.610 |
| Random Forest (tuned) | 0.571 | 0.489 | 0.589 |
| k-NN (tuned) | 0.555 | 0.513 | 0.567 |
| Gradient Boosting (tuned) | 0.579 | 0.433 | 0.579 |

Logistic Regression had the best ROC-AUC (0.610) and was the most stable between cross validation (0.634) and
validation. The tuned Gradient Boosting dropped the most (0.632 to 0.579), which suggests its tuned settings were
somewhat over-fitted to the training folds. All models beat the baseline on ROC-AUC, but accuracy gains over the
majority class baseline are small. The confusion matrix for Logistic Regression shows a conservative model: it
identifies low scoring games well (140 true negatives) but misses many high-scoring ones (92 false negatives),
because "fewer than 2 goals" is the majority class. Logistic Regression remains the preferred model. Predicting
whether a team scores 2+ goals is difficult because of match randomness and the limits of rolling average features.


## Error Analysis
The error analysis focuses on the best model, Logistic Regression, on the 2024/25 validation season. The confusion matrix showed that it is conservative: it correctly identified 140 low-scoring matches but missed 92 matches where the home side scored 2+ goals. It therefore under-detects high-scoring games.

Once I split the matches by feature values we can see where it goes wrong. When the home team’s recent scoring form is low, the model predicted 2+ goals in only 16% of matches, but 39% actually had them. When form is high, it predicted 2+ goals in 70% of matches, but only 51% had them. The model’s predictions are more extreme than reality, which suggests the rolling averages carry only a weak signal. Accuracy was lowest (49.6%) for mid-form teams, which is roughly a coin flip. Accuracy also fell as the opponent’s defence got weaker, from 60.9% against teams conceding few goals to 50.8% against teams conceding many. The model could tell that a weak defence raises scoring chances, but not which of those matches would actually produce 2+ goals.

The largest misses were high-scoring matches (4 to 7 goals), such as Nottingham Forest vs Brighton (7 goals) and Man City vs Crystal Palace (5 goals), where the home side’s recent averages were modest (1.6 and 2.2 goals). These are rare, high variance results that rolling averages from the previous five games cannot anticipate.

## Goal difference feature
Ideally, this would've been added before I did the tuning and confusion matrices, however, the idea for adding this feature only came when I looked at the errors on the validation set. Adding the team strength difference feature improved Logistic Regression on the validation season. Accuracy rose from 0.571 to 0.613, F1 from 0.486 to 0.553 and ROC-AUC from 0.610 to 0.641. The confusion matrix shows fewer missed high-scoring games (false negatives fell from 92 to 78), so the model is less conservative than before. Random Forest improved by a similar amount (ROC-AUC 0.589 to 0.637). The two models are now almost level, with Random Forest slightly ahead on accuracy and F1 and Logistic Regression marginally ahead on ROC-AUC. The validation season therefore does not clearly separate them. Logistic Regression is kept as the preferred model because it is simpler, faster and easier to interpret, but the performance gap between the two is within noise.

## Conclusion on models
The current best model is Logistic Regression using the six features. It achieved the highest cross-validation ROC-AUC (0.655) and a strong validation ROC-AUC (0.641). Random Forest performed almost identically on the validation set (ROC-AUC 0.637) and actually scored slightly higher on F1 (0.570 vs 0.553), showing that it handles the minority class just as well. However, Logistic Regression is kept as the preferred model because it is simpler, faster, and more stable between cross-validation and validation. Adding the defensive record and strength difference improved every model, meaning the more complex models never clearly beat the simple one.

### The main limitations are:
- The features still carry little predictive imformation
- Although there was an improvement after adding the 6th feature, the model still misses many high scoring games (77 false negatives in the validation set)
- The features are rolling averages that ignore real world problems such as injuries and mid season transfers.
- The validation set is small (380 matches), so the difference between the top performing models are within the noise.


## Assistance Declaration
I used AI (Claude) to help with the pandas rolling averages function (shift and rolling) and to ensure the scikit-learn pipeline was set up correctly to prevent leakage. It was also used to assist with indexing and small sections of code throughout the project. All code was reviewed and understood by me. 

## Setup and Run Instructions

1. Install the required packages:
   pip install -r requirements.txt
2. Open `Split_and_Baseline.ipynb` in Jupyter Notebook or JupyterLab.
3. Click **Kernel → Restart & Run All** to run the entire notebook from top to bottom.

## Repository link
https://github.com/lukecarroll12345/Premier-League-Prediction







