---
layout: post
title:      "What Does it Take to Win a Chess Game?"
date:       2019-08-01 16:50:32 +0000
permalink:  what_does_it_take_to_win_a_chess_game
---

*A Review of the Importance of Domain Knowledge in the Process of Feature Engineering*

![img](https://i.imgur.com/RFtMyVW.png)
<br>
*“Chess is the struggle against the error.” *
<br>
– Johannes Zukertort

The origins of the game of Chess are debated amongst historians. Some say the game originated in China, though most agree that the game was birthed in India, traveling across the globe from culture to culture, its name and rules evolving throughout the journey.  Despite some disagreements as to the origins of the game, there’s no denying that there is something special about a game that’s remained in popularity for over 1500 years. It’s a game that has spawned its own legends and timeless champions whose games are still studied hundreds of years after completion. It’s a game that has outlived men, nations, and empires alike…but why?  How, and why, could a board game have such a storied history and why on Earth would we care about Chess as data scientists?’

Before touching on why Chess might be of interest to a Data Scientist, let’s briefly break down its rules.  It’s no secret that the game of Chess is a complicated one with a tremendous skill ceiling. Chess is a 2-player game, with one player using the white pieces and the other using the black.  In Chess, the player piloting white always goes first. Each player has a total of 16 pieces: 8 pawns, 2 rooks, 2 bishops, 2 knights, a King, and a Queen.  The modern chess board has 8 rows and 8 columns that make up 64 total squares  Each piece type moves differently, and the game traditionally ends when one player captures the enemy King, or one or both players are no longer able to make progress, resulting in a draw.

Perhaps it’s this incredible variety and ridiculous skill-cap that has captivated human attention for so long.  In fact, the fascination with automation (yes, really) and theory crafting in Chess dates back hundreds of years, perhaps originating with the famous ‘Mechanical Turk’, an ‘automated’ Chess playing puppet that shocked the public with its prowess in the 18th century. Though the Mechanical Turk was later debunked when it was found that there was a man hiding underneath the table controlling the pieces moved by the puppet, the fascination with automation and ‘solving’ Chess has only grown in the last few hundred years.

The advent of computers and data science has pushed Chess automation further than ever before.  In the modern era, humans have created powerful computers capable of defeating even the most skilled human Chess players in the world with ease. In the 1990's Garry Kasparov was without a doubt the best living Chess player on the planet, almost untouchable during most of his reign as world champion. However, the world of Chess would change forever in 1997, when IBM's Deep Blue famously defeated Kasporov with a match score of 3.5-2.5.  It was the first time a computer had ever beaten a World Champion player in a match, and would mark the beginning of the computer Chess age, an ongoing period of immense growth in Chess strategy.  

![img](https://i.imgur.com/fSwCsOD.jpg)

Despite this, the most popular way to play Chess has been, and probably always will be against other humans. In this project, we’ll be using a dataset of 20,000 Chess games from LiChess, a popular chess site, to explore classification techniques in Machine Learning.

Classification is a supervised learning method used to predict the class (or outcome) of a set of input datapoints. In [this](https://towardsdatascience.com/machine-learning-classifiers-a5cc4e1b0623) 2018 article by Sidath Asiri, classification is defined as, “the task of approximating a mapping function (f) from input variables (X) to discrete output variables (y). Classification is an extremely important supervised learning technique with a wide variety of applications such as: credit approval, spam detection, target marketing, and diagnosing medical conditions. In this project, we sought to predict the winner of a game of chess between 2 players using the data provided by LiChess. 

Now that we’ve outlined the focus of our project, let’s start talking **numbers**.  There are a total of 20 possible moves for each player on the first move of the game, meaning that after each player has made their first move, there are 400 potential board states. With games often taking 50+ moves per player to complete, the possible combinations of moves quickly becomes unfathomably high.  
> In fact, the complexity of Chess is so notable that it has its own definition, denoted by the Shannon number.  The Shannon number characterizes Chess game-tree complexity, or the number of possible positions in a game of Chess.  This number is actually larger than the number of atoms in the known universe.

So, other than me being a giant Chess nerd, why would I give so much time to outlining the rules and history of Chess? We have our cleaned data, and as Data Scientists we’re comfortable tweaking the parameters of models to improve their predictive scores.  After all, it’s only predicting the winner of the games that we care about right? Why would chess theory possibly matter? To answer that question, we will review the features provided by the LiChess dataset.

```py
print(df.info())
print(‘’)
print(df.iloc[2])
```

```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 20058 entries, 0 to 20057
Data columns (total 16 columns):
id                20058 non-null object
rated             20058 non-null bool
created_at        20058 non-null float64
last_move_at      20058 non-null float64
turns             20058 non-null int64
victory_status    20058 non-null object
winner            20058 non-null object
increment_code    20058 non-null object
white_id          20058 non-null object
white_rating      20058 non-null int64
black_id          20058 non-null object
black_rating      20058 non-null int64
moves             20058 non-null object
opening_eco       20058 non-null object
opening_name      20058 non-null object
opening_ply       20058 non-null int64
dtypes: bool(1), float64(2), int64(4), object(9)
memory usage: 2.3+ MB
None

id                                                            mIICvQHh
rated                                                             True
created_at                                                 1.50413e+12
last_move_at                                               1.50413e+12
turns                                                               61
victory_status                                                    mate
winner                                                           white
increment_code                                                    5+10
white_id                                                        ischia
white_rating                                                      1496
black_id                                                          a-00
black_rating                                                      1500
moves                e4 e5 d3 d6 Be3 c6 Be2 b5 Nd2 a5 a4 c5 axb5 Nc...
opening_eco                                                        C20
opening_name                     King's Pawn Game: Leonardis Variation
opening_ply                                                          3
first_capture                                                    white
count_captures                                                      17
white_captures                                                      14
black_captures                                                       3
count_5_captures                                                 white
first_castle                                                      None
rating_difference                                                   -4
Name: 2, dtype: object
```

Without an understanding of Chess, we’d still be able to fit the data above into a model and do an ‘okay’ job with the features provided by LiChess.  However, applying what we know about Chess, we’re able to manipulate the existing features of the dataset to produce new input variables that will hopefully improve the predictive accuracy of our model(s). This process, known as **Feature Engineering** is crucial to the implementation and improvement of classification models.

As an example, let’s review the ‘moves’ column, which displays a string containing all of the moves played by both players.  Here's an example of the recorded moves for a Chess game from the dataset:
```
'd4 d5 c4 c6 cxd5 e6 dxe6 fxe6 Nf3 Bb4+ Nc3 Ba5 Bf4'
```

At first consideration, this feature doesn't appear to possess much value as a predictor, as we know there are trillions of potential combinations of Chess moves that can produce a completed game. However, applying a modest amount of Chess know-how, we can put this feature to work in a big way through feature engineering.

Since we know that white always moves first, by splitting the moves column into individual moves (as elements of a list), we can determine that every odd element represents a move made by white. From there, we're able to continue to produce new features based what moves are made, and when. For example, castling is a move that can be made in special circumstances. For the purposes of our analysis, these circumstances are unimportant. What is important to know is that castling is denoted by either ‘O-O’ or ‘O-O-O’ , helps to protect the King, and that by making the decision to castle earlier, one has less potential to fall victim to enemy attacks.

We can determine which player castled first by searching for the index of its notation within our newly manipulated 'moves' column.  Since we're pretty sure that castling early (a fundamental theory of Chess) is a good thing, we can add this feature to our model to evaluate its impact on predictive efficacy.  We'll use this same strategy to engineer other features as well, such as counting the number of enemy pieces captured by each player, calculating the difference in player rating between white and black, and the number of pieces captured by each player in the example functions defined below:

```py
def white_captures(moves):
    white_captures = 0
    black_captures = 0
    for index, move in enumerate(moves.split()):
        if 'x' in move:
            if index%2 == 0:
                white_captures += 1
            else:
                black_captures += 1
    return white_captures

def first_castle(moves):
    for index, move in enumerate(moves.split()):
        if move in ('O-O', 'O-O-O'):
            color = 'white' if index%2==0 else 'black'
            return '{}_{}'.format(color, move.replace("-", "_"))
    return None 
```

Evaluating the importance of each feature suggests that our theory was correct! Of the top 15 features, ranked by importance, 12 were produced as a result of feature engineering.

```py
# Feature Importance
features = pd.DataFrame(columns = ['feature', 'importance'])
features['feature']    = X.columns
features['importance'] = rf.feature_importances_
features.sort_values('importance', ascending=False)[0:15]
```
```
	feature	importance
6	rating_difference	0.161352
5	black_captures	0.123291
4	white_captures	0.120992
1	turns	0.088998
3	count_captures	0.079273
2	opening_ply	0.041986
7	minutes	0.041567
8	increment_secs	0.036361
37	victory_resign	0.012351
0	rated	0.012199
35	victory_mate	0.011370
33	first_castle_white_O_O	0.010107
31	first_castle_black_O_O	0.009356
36	victory_outoftime	0.008974
22	first_e4	0.007752
```

Armed with new, stronger predictors gathered by applying a modest amount of Chess knowledge to the features of the dataset, we’re ready to continue the process of modeling. For more information on fitting and tweaking various classification models such as: Logistic Regression, Adaboosting, Decision Tree, Random Forest, and more, you can review my code and results using [this link](https://github.com/pawoody/dsc-3-final-project-online-ds-ft-041519) or reach me via [LinkedIn](https://www.linkedin.com/in/paul-woody/).

Paul Woody

