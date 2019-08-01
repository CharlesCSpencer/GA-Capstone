Machine Learning for Horse Racing:
Charles Spencer: SG-DSI-8



Executive Summary:

This paper outlines the steps we used to develop a computerized horse racing model which targets the probability of a horse winning a race.  To do this we needed to predict the finishing position for each horse in a race, as well as each horse’s required odds/dividend needed to wager profitably over time.  The following sections of this paper addresses: our data requirements; sourcing and cleaning; model selection; feature engineering; performance testing; visualisations and deployment.  

Whilst our model produced a net profit of S$262 on 160 wagers totaling S$800 since Jan-01-2019, there are a number of implementation issues addressed which may reduce actual returns from our backtested model. Despite these challenges, we believe our results illustrate evidence of market inefficiencies and wagering biases which existed during the period of our study. 



Problem Statement: Can we Develop a Profitable Wagering Strategy for the Racetrack?

The racetracks pari-mutuel betting system has more in common with financial market speculation than it does to most casino games.  Importantly, gamblers at the track are not betting against the casino in fixed odds games like roulette, but rather they are wagering against the other market participants in the pari-mutuel pool.  In roulette, for example, there are 38 spaces on the wheel, and if you pick the correct one, the house will pay you at just 35-to-one. In this game, the longer you play, the more you lose, guaranteed. 

However, in a pari-mutuel betting system the winners payoff is calculated from the net pool of losing bets.  As such, if a predictive model could be built with greater forecasting accuracy than the general public’s, it should be possible to develop a profitable wagering strategy at the racetrack, we believe.  Therefore, our goal is not to find the most likely winner in each race, but rather which horse or horses are offering odds that exceed their actual chances of winning.  This won’t happen in every race, but when other players actions help set the odds, occasionally the public makes the wrong price.    

This analogy also carries into stock market investments. For example, if you believe a stock is undervalued and start to buy it, your actions (and others) will help to raise the price which inturn drives down future prospective returns. This point underscores the importance of expected value, as a central concept in any probabilistic exercise. Expected value formalizes the idea that your return on an investment is the product of the probabilities and the various outcomes and the payoff from each outcome.  Investing is fundamentally a probabilistic exercise. 



Building a Predictive Model: 

In 1975 author Andrew Beyer revolutionized the traditional approach to analyzing a horse race in his book ‘Picking Winners’.  He is widely credited with being the creator of the Beyer Speed Figure, which is a measure of how fast a horse has run in its past races.  Because the figures are normalized across track surface and distance, a horse’s speed figures are more easily compared and ranked within than were the raw finishing times.  However, the process of developing a robust dictionary of speed figures each and every horse was an extremely time consuming and tedious exercise.  

Today, a computer can calculate such speed figures in an instance if the appropriate data can be sourced, cleaned and engineered.  As such, the most difficult and time consuming process we faced was the engineering and testing of our selected modeling features.  The type of model we used is a multiple linear regression with over 10,000 rows of past performance data and over a dozen columns of engineered features to train the model.  

To construct a model with significant predictive power we sourced each horse’s past performance data from the Singapore Turf Club website.  Cleaning this data and setting it up for feature engineering involved significant effort due to outliers and data wrangling.  Further, we performed numerous tests and created a number of new data series from our existing data  to ultimately engineer our features and test for their predictive significance.   The final list of features which were tested and included in our current model are:

Engineered Features:
Median Speed figure relative to others in the race (continuous)
Recent Speed figure relative… (continuous)
Last 100m speed figure relative... (continuous)
Best Distance & Surface speed figure relative… (continuous)
Jockey Weight relative... (continuous)
Horse Optimum Weight relative… (continuous)
Handicap Rating relative... (continuous) 
Jockey Win % (continuous)
Trainer Win % (continuous)
Barrier Win % at distance (ordinal)
Change of Equipment (nominal)
Days since Last Race (continuous)
Trouble in past run (text - ordinal)

Target:
Lengths Behind Winner (LBW) (continuous)

Despite the computer speeding up the pace of calculations, the most difficult and time-consuming process step in creating our model was the development and complex coding involved generating some of our engineered features.  For example, whilst the computer can easily separate the fastest and slowest horses at the track from the raw data, these horses generally don’t compete against each.  Instead, horses are grouped into various Class levels for competition, from the slowest in level Class 5 to the fastest in level Class 1.  Additionally, horses compete over different distances, surfaces and weather conditions, making it extremely challenging to compare the past performances of each horse in a race from the raw data.  As such, a number of subroutines are needed to standardise and normalize the raw data into the engineered features which we can then use to compare and rank each horse in an upcoming race.

Target selection was also an area where we spent considerable time and effort on reviewing and testing different targets to predict.  Most other computer horse racing models we have reviewed are some form of classification model, where the predicted target is something like ‘Win’ vs ‘Loss’, or will a horse finish in the ‘Top-3’ or not.  The most significant problem we see with these classification models is that they effectively throw away huge amounts of data by effectively converting 10-rows of past performance data, from a 10-horse race, to just 2-rows (‘Win’ vs ‘Loss’).  As a result, to train the model the quantity of past performance data must be significantly greater than using a model where each horse’s past performance data can be used for training.  

We finalized on targeting lengths behind the winner, for three reasons.  First, it was on easily available series found in our dataset, with an added bonus of already being in a numerical form to one decimal place.  Second, after training our model on lengths behind winner we could also target that in our predictions, giving us the ability to forecast the dispersion at the finish rather than just win/loss.  Finally, we used the targeted dispersion predictions to assign and adjust the odds/dividends required to reflect the competitiveness of a race.  

With the model now built and trained we next tested its performance.  We first used a k-fold CV testing, which splits the training set into k smaller sets, we used 5-folds.  Unexpectedly good performance led us to believe that there was some over-fitting or data-leakage in our testing procedure.  As a result we decided to follow the Walk-Forward testing approach commonly used for time-series data, so as to avoid any potential for leaked information from future races to leak back into the past training data.  Using this approach required us to break-up our training and testing data-set into 13 walk-forward steps of roughly 2-weeks each step.  This ensured that there was no chance for the data leakage problems we suspected we were getting from the k-folds CV testing.  

Our models prediction of lengths behind the winner (y-hat) produced an r^2 of 0.29 and had a root mean square error of 5.2 lengths.  On the surface, these statistics appear to suggest that our model has low predictive ability.  However, in reality we shouldn’t expect to produce a tight fitting model given the inherent randomness of possible outcomes.  For example, an average race has over 10 horse running, many with winning potential.  Additionally, the results highlight wide dispersion totaling nearly 20 lengths between the first and last place finisher.  As such, we need a model and wagering system which can provide a large enough number of advantaged betting opportunities that a sufficient profit can be expected over the long term.  Given that the odds/dividends are set by the betting public we really need a model which can rival the public.  Interestingly, their r^2 is just 0.099 to the target and their RMSE is 5.8 lengths, both of which our model performance comfortably exceeds.  

Finally, we use our models targeted predictions to assign odds/dividends to each horse in the race.  We do this by estimating each horse’s probability of winning the race and assigning it with fair odds (excluding vigorish), calculated from the inverse of the probability.  After this exercise, we have completed our task of assessing each horse’s chance of winning in the upcoming race card, as well as assigning each horse with a minimum acceptable odds/dividend, above which should represent advantaged betting opportunities.  However, we still make some wagering strategy modifications, explained below, to help identify the best advantaged betting opportunities. 
    
    
    
Wagering Strategy: 

In 1988 academic researchers including Richard Thaler and William Ziemba published a study highlighting a meaningful wagering bias of the betting public being the over-betting of longshots, and the under-betting of favorites.  Their research observed that horses with short odds (i.e., favorites) yield on average higher returns than horses with long odds (i.e., longshots). As such, the probability of a longshot winning is actually significantly lower than their odds/dividends imply.  Some reasons offered for this bias include the one-off risk seeking nature of a typical horse bettor, who’s tempted to back longshots in the hope of a big score.  Whilst logical, we are more interested in testing if this bias still exists today as it can help to tune our wagering strategy.

Interestingly, this wagering bias still shows up clearly in the recent Singapore Turf Club races.  For example, over the past 2-years our data set shows that the average loss for a bettor who backed all favorites (paying $21 or less for a $5 wager) would have been -7.5%. However, the losses widen to -17.5% for the bettor who back all longshots (those paying over $21).  Whilst there is no easy profit arbitrage available from this statistic, as both bettor’s lose money over the long term, we can begin to set some rules for our wagering strategy which may help to reduce losses from over betting.  

Given the above bias, we clearly we want to discourage our model from betting on longshots.  We tested raising the hurdle on identifying advantaged betting opportunities and saw improved profit/loss performance. Further, we looked at various cut-off levels to avoid wagering on longshots, even if they were advantaged.  In the end, we found the best profit/loss performance from our model using a dividend cut-off of S$28 (odds of 9/2) which would be avoided from our potential wagers.  We tested this cut-off level and found that at higher levels it led to over-betting with lower net returns, whilst lowering this cut-off lead to reduced betting and missing out on enough advantaged opportunities to lower net returns.  

At this point, we can now use our model to quickly analyze the past performance data, then predict all finishing positions in each of the upcoming races and finally assign a minimum acceptable odds/dividends to each horse running.  At this point we will just watch and wait for advantaged betting opportunities to emerge, which also fall within our wagering strategy cut-off.  Such opportunities generally present themselves two or three times per racing card.  



Visualization & Deployment:

Tableau Public gallery offers an impressive interactive visualization software which allowed us to create and publish our own dashboard to the web for public viewing.  Our dashboard includes two pages linked by tabs at the top of the screen, allowing users to toggle forward and backwards through our pages.  The first page of the dashboard is where viewers can find an attractive visualisation of the predicted finishing positions for each horse forecast to finish within three lengths of the winner in today’s races.  In addition, just below the expected winning horse is its name as well as the minimum dividend required for an advantage betting opportunity to emerge.  Further, this data can be found for all other contenders when the viewer hovers their mouse another horse image on the dashboard.  

The second page of the dashboard is where viewers can find the Profit/Loss Performance data for our models, including the accumulated profit/loss line in the top visualization whilst the bottom visualization shows the daily wager profit/loss.  In addition, the viewer can get more detailed information of performance statistics by hovering their mouse over the images on the top or bottom visualizations.  Finally, to update the chart with new prediction and past performance data requires just a simple click of the refresh data button on the Tableau Public home page where we created and maintain the dashboard. Tableau offers the ability to push these interactive visualizations to social media sites like Twitter and LinkedIn, which we may do in the future. 



Bankroll & Wagering: 

The Rebate System offered for horse race wagering from the Singapore Pools has a S$2,000 minimum bet size required to be eligible for a 10% rebate on losing bets.  Importantly, we assume that bettors will wager in adequate size to be eligible for this rebate, despite all our illustrations and calculations showing figures based on a S$5 wager size.  Simply, the reason we show all figures to a S$5 wager amount is because that is the convention of expected dividends and payout amounts from Singapore Turf Club.  If we were to re-run our profit/loss excluding the rebate it would lower our net profit by $54, which is a meaningful -22% reduction to our accumulated profit/loss calculation.  

Losing streaks are common in this game, with the worst losing streak we’ve seen during our test period being seven consecutive bets in mid-June.  If we had been wagering the minimum required for the rebate, this would have consumed S$14,000 of our bankroll.  If our initial bankroll was just S$10,000 it would have led to financial ruin.  We can actually calculate the likelihood of losing all one's investment capital or extinguishing one's bankroll below the minimum for further play or S$2,000.  Using the risk of ruin formula, we can calculate the size that our initial bankroll should be to reduce our risk of ruin to an acceptable level. For our wagering strategy we estimate it would require an initial bankroll of about S$40,000.

Another related model is the Kelly criterion, which can be used to calculate the optimal bet size to maximize our expected geometric growth rate of wealth.  It’s expressed as a percentage of your bankroll, and we calculate that on average we should be betting about about 6% of our bankroll.      
