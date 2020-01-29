# tx-high-school-basketball-elo
Elo Ratings of Texas High School Basketball Teams

# Data
The data used in this model was from the high school sports scores database used by the Houston Chronicle and the San Antonio Express News (http://houston-tpweb.newsengin.com/). The data is limited in that it only contains data for San Antonio and Houston schools, with Dallas/other schools only appearing when an San Antonio/Houston team plays against them in a Tournament. So, these ELO Ratings are only applicable to the San Antonio and Houston Area High Schools.

# ELO Methodology

### All schools start with an ELO Rating of 1500. 
### Based on the result of each game, this rating is adjusted
### This change in ELO is based on the actual game score ratio subtracted from the ELO ratio, with an added bonus for whether the team won or lost the game
<i>ELO Ratio</i> = (home_elo + home_field_advantage_bonus) / (home_elo + home_field_advantage_bonus + away_elo)<br>
<i>Actual Game Score Ratio</i> = home_score / total_score<br>
<b>elo_change</b> = 100 * (<i>Actual Game Score Ratio</i> - <i>ELO Ratio</i>) + <i>Bonus for winning or losing the game</i><br>
- Examples: (With 25-Point Home Field Advantage & 10-Point Win Bonus)<br>
Home Team ELO: <b>1600</b>       |       Away Team ELO: <b>1486</b><br><br>
|||||||||||||||||| <b>Ex. 1</b>:
 Home Team Actual Score: <b>56</b>       |       Away Team Actual Score: <b>43</b><br><br>
 <b>ELO Ratio</b>: (1600 + 25) / (1600 + 25 + 1486) = .522<br>
 <b>Actual Game Score Ratio</b>: 56 / 99 = .566<br>
 <b>Elo Change</b>: 100*(.566-.522) + 10 = <b><i>14.4</i></b><br><br>
 Home Team New ELO: <b>1614.4</b>       |       Away Team New ELO: <b>1471.6</b><br><br>
|||||||||||||||||| <b>Ex. 2</b>:
 Home Team Actual Score: <b>46</b>       |       Away Team Actual Score: <b>64</b><br><br>
 <b>ELO Ratio</b>: (1600 + 25) / (1600 + 25 + 1486) = .522<br>
 <b>Actual Game Score Ratio</b>: 56 / 99 = .418<br>
 <b>Elo Change</b>: 100*(.418-.522) - 10 = <b><i>-20.4</i></b><br><br>
 Home Team New ELO: <b>1579.6</b>       |       Away Team New ELO: <b>1506.4</b><br><br>
- - - - - - - - - - <br>

### Based on the difference between the two team's ELO Rating, an expected win percentage is calculated

<i>ELO Difference</i> = home_elo + home_field_advantage_bonus - away_elo<br>
<b>Home Team Expected Win Percentage</b> = 1 / (10 ^ ((-1 * <i>ELO Difference</i>) / 400) + 1)
- Examples: (With 25-Point Home Field Advantage)<br><br>
|||||||||||||||||| <b>Ex. 1</b>:<br>
Home Team ELO: <b>1600</b>       |       Away Team ELO: <b>1486</b><br>
 <b>ELO Difference</b>: 1600 + 25 - 1486 = <b>139</b><br>
 <b>Home Team Expected Win Percentage</b>: 1 / (10 ^ ((-1 * <b>139</b>) / 400) + 1) = .69 -> <b>69%</b><br><br>
|||||||||||||||||| <b>Ex. 2</b>:<br>
Home Team ELO: <b>1449</b>       |       Away Team ELO: <b>1528</b><br>
 <b>ELO Difference</b>: 1449 + 25 - 1528 = <b>-54</b><br>
 <b>Home Team Expected Win Percentage</b>: 1 / (10 ^ ((-1 * <b>-54</b>) / 400) + 1) = .42 -> <b>42%</b><br><br>
- - - - - - - - - - <br>
 
### This Expected Win Percentage can be used to calculate an expected game spread

<b>Game Spread</b> = 261.14 * (Expected Win Percentage) ^ 3 - 509.52 * (Expected Win Percentage) ^ 2 + 354.66 * (Expected Win Percentage) - 82.5925<br>
69% Expected Win Percentage -> <b>-5.3 Point Spread</b><br>
58% Expected Win Percentage -> <b>-2.6 Point Spread</b><br>
- - - - - - - - - - <br>

### This Expected Win Percentage is then used to calculate an accuracy score for each prediction to measure the accuracy of the model

Accuracy scoring calculations were taken from the scoring used for fivethirtyeight's NFL forecasting game<br>
https://projects.fivethirtyeight.com/2019-nfl-forecasting-game/<br>
The magnitude of the accuracy score is based on the confidence of the prediction<br>

<b>Points if Prediction is Correct</b>:  2 * ((-100 * (Expected Win Percentage - .5) ^ 2) + 100 * (Expected Win Percentage - .5))<br>
<b>Points if Prediction is Incorrect</b>:  -1 * ((Expected Win Percentage - .5) * 400 - <b>Points if Prediction is Correct</b>)
- Examples: (With 25-Point Home Field Advantage)<br>
Home Team ELO: <b>1600</b>       |       Away Team ELO: <b>1486</b><br><br>
|||||||||||||||||| <b>Ex. 1</b>:
 Home Team Actual Score: <b>56</b>       |       Away Team Actual Score: <b>43</b><br><br>
 <b>ELO Difference</b>: 1600 + 25 - 1486 = <b>139</b><br>
 <b>Home Team Expected Win Percentage</b>: 1 / (10 ^ ((-1 * <b>139</b>) / 400) + 1) = .69 -> <b>69%</b><br><br>
 <b>Accuracy Score</b>(Prediction was correct):  2 * ((-100 * (.69 - .5) ^ 2) + 100 * (.69 - .5)) = <b>30.8</b><br><br>
|||||||||||||||||| <b>Ex. 2</b>:
 Home Team Actual Score: <b>46</b>       |       Away Team Actual Score: <b>64</b><br><br>
 <b>ELO Difference</b>: 1600 + 25 - 1486 = <b>139</b><br>
 <b>Home Team Expected Win Percentage</b>: 1 / (10 ^ ((-1 * <b>139</b>) / 400) + 1) = .69 -> <b>69%</b><br><br>
 <b>Accuracy Score</b>(Prediction was incorrect):  -1 * ((.69 - .5) * 400 - 30.8) = <b>-45.2</b><br><br>
 
<b>This accuracy score is what was used to tune the model by changing the following model parameters:</b><br>
<i>Reset Rate</i>: The percentage of a school's ELO Rating away from 1500 that is kept after each season<br>
<i>Home Field Advantage</i>: The size of the ELO Bonus that is given to the home team<br>
<i>Win Bonus(Learning Rate)</i>: The ELO Bonus that is given for winning/losing the game<br>

<b>Optimal Paramters for this data set were found to be:</b><br>
<i>Reset Rate</i>: 0.4<br>
<i>Home Field Advantage</i>: 25<br>
<i>Win Bonus(Learning Rate)</i>: 10<br><br>

These optimal parameters resulted in an average accuracy score of 10.49 per game

# ELO Results

Filtering down to only San Antonio area schools, an average ELO Rating was calculated for each school over the past 10 seasons (2009-2010 to 2018-2019) to determine the top 10 San Antonio Basketball Programs in each UIL Classification over the last 10 years.
