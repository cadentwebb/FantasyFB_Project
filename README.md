# Fantasy Football Project

## Introduction

Each year millions of Americans participate in fantasy football leagues, often with a pot of money going to the league winner and embarrasing punishments going to the loser. Predicting any sport's outcome is difficult due to the game to game variability, but predicting fantasy football scores is particularly challenging because of the regular injuries and roster changes. On each  fantasy team there is no position more important, or more volatile, than running back. Having a solid group of running backs is often the difference between a winning and losing week.

With that being said, I am going to attempt to use publicly available data from https://www.pro-football-reference.com/ to predict running backs' fantasy scores. Many have attempted to do this with much more data at their disposal than I have, but I hope to create the framework of an effective model that could be improved with more time and data. 

## Data

### Collection

As mentioned above, the data was collected from https://www.pro-football-reference.com/ using the beautiful soup package from python. The inspiration for the code came from a blog by Steven Morse, but was siginifcantly altered to grab the data I wanted for my project. I collected data from the top 51 fantasy running backs for this season (2020). I then went back and grabbed both the game logs and fantasy point totals (using Draft Kings points per reception scoring) for each of these running backs where possible. Obviously, some players entered the leagues more recently than 2017, so there is more data for some than others. My goal is to predict for running backs in general, and not for a specific player, so this will not be a problem.

One thing to note is that there were running backs who were primarily returners or receivers. Any player who was not primarily a rusher (rushing statistics listed first on the website) was omitted. Weeks a player did not play due to injury/suspension are not included on the website.  

### Cleaning

The first step was to combine the collected data, so I merged the gamelogs with their respective fantasy point totals. The way the data was read in left any category without a stat recorded as an N/A, so I replaced those with 0. I combined all of the years together into one big data set, and adjusted for the Raider's locale change this past year from Oakland to Vegas. I saved this as the all_years.csv file that is on the github repository.

### Feature Engineering

Fantasy points are a linear combination of a player's actual stats, so I had to get creative coming up with feautures that would be reflective of a player but not projected into the future. I decided to denote certain players as "high-usage" where usage is the number of rush attempts plus the number of pass targets divided by the total number of their team's snaps. ((rush attempts + targets) / Total Team Snaps). Any player more than one standard deviation above the average usage was denoted as "high usage" with a one in that column. One standard deviation is appropriate because the data is right skewed with some outliers.

Touchdowns are the most rewarded action for a running back, so I created a variable that would denote which players scored the most touchdowns per touches of the ball. The same process as above was used to get a touchdown percentage, and players with a touchdown percentage more than one standard deviation above the average were given a one in that category .

The last variable I made was a boom/bust variable to show which players have larger than normal variability from week to week. Players that score 20 points one week and 6 the next are difficult to project, so this varibale tells the model that a certain player is more volatile. Players with a higher than normal standard deviation were given a one in this category.

### EDA

I started out with an overall description of the data, and then looked at a pairs plot. As expected, fantasy points correlates highly with usage, so I think that variable will be effective. Points also decrease with age, which was another interesting find. I graphed the overall distribution of fantasy points to show the right skew. I also printed some tables and summary descriptions to show the most and least advantageous teams to play againts, and the best and worst performers on average. Lastly, a heat map was created to show the correlations between the variables. It once again showed that the more a player is on the field and touches the ball, the better fantasy output they will have. The following graph shows the distribution of fantasy points, illustrating the right-skew. The tables, heatmap, and other statistics were difficult to put in here, and can be seen in the Fantays.ipynb file.



## Modeling

I decided to use the age, away, season, team, opponent, touchdown percentage, boom/bust, and high usage variables. The season, team and opponent were all one hot encoded. I attempted to use lasso regression, k-nearest-neighbors, gradient boosting, decision trees, and a random forest to predict fantasy outputs. These are the most common models used in this setting, and I tried a few to be able to compare models. I hoped tha the tree algorithm especially would be able to use the binary variables to help produce more accurate predictions.

To be frank, there are a lot of limitations to this analysis. There are a lot of other factors that would affect fantasy output that can't be accounted for with the data at my disposal such as injuries and coaching staffs, among others. It could also be interesting to try a sort of time series analysis to look at how players perform over time. I also don't have a ton of computing power on my laptop, so it isn't feasible to run massive cross-validations and grid searches to find the best parameters. 

To improve model performance I created a grid of parameters to test for each model except lasso, where I stuck with one neutral parameter. Within the grid I sought to minimize mean squared error and pick the best parameters based on that. I also hoped that the engineered features would improve model performanec, although I did not do specific tests to evaluate which ones helped the most. I looked at two similar metrics for evaluation: root mean square error from the test sets and a root mean square error from the default 5 fold cross validation to see how the models performed when used multiple times.

Overall it is difficult to decide which model was truly the best, as they all produced fairly similar scores. The random forest and lasso both had an rmse from the test set of about 9.53, which was the best of the lot. The random forest held up best against the cross-validation with an rmse of 9.44. It was interesting to see that some models did better win the cross-validation while others did not. All of the scores except the cross-validation score from the gradient boosting model were between 9 and 10.

It looks like with the best models we are still around 9.5 points off the actual fantasy score. This is certainly unreliable, but it is a start to be improved upon. 

## Conclusions

I think the most obvious takeaway is that it is very difficult to predict fantasy scores, as the closest I got wa still about 9.5 points away. It would be interesting to get a confidence interval on these predictions. As someone who actively plays I have yet to come across a so called "expert" or even an algorithm that I can rely upon, so it is possible that my model is as good as any. It would be interesting to compare to the projected scores from ESPN and NFL.com to see how it stacks up. It would be beneficial to continue tuning the parameters with larger grid sizes to see what will really minimize error. I also think it would be useful to look at other data and see what the biggest impacts are on fantasy performance. Maybe weather would force a team to rely more on a running back, or another variable would be important. Other research ideas that came to mind are performing this with another position group like quarterback, where I anticipate there would be less variability. One could also look at how fantasy scores affect team success. It could also be easier to predict for a specific player, who could be isolated and analyzed more in depth. Lastly, it could be interesting to look at this from a time series approach to see if it would yield any better results. 



