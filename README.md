# StockX - Predict Resell Price of a Shoe

Would you spend $3,000 on a shoe? Some people do! That price is not the retail price, but the resell after market value. I got this idea after speaking with a friend who does this for a liivng. He buys (he has a friend that works a shoe store and holds a pair for him, so he has the inside scoop. Most people don't!) very limited released shoes or a shoes he thinks will resell very high. He reads a bunch or shoe blogs and articles that are up with the current trends to get insights on which shoe to try and get. Below are a few of those blogs if you are interested. 

Sneaker Blogs:
* [Nice Kicks](https://www.nicekicks.com/)
* [High Snobiety](https://www.highsnobiety.com/sneakers/)
* [Flight Club](https://www.flightclub.com/)

The popular buy and sell site stockx is where my data comes from. You can check out the site [here](https://stockx.com/). This is by far the most popular site for this buy and resell market. Early investors included rapper Eminem and model Karlie Kloss, but the site was founded by Dan Gilbert, the owner of the Cleveland Cavaliers. Just to give you an idea of how much this industry makes forbes estimated all sales of aftermarket reached 2 billion last year. 


Note: This work originated as a Capstone project for the Data Science Immersive curriculum at Galvanize in December 2019. The efforts are continuing.

# Background

I've seen a few other models developed for this, but everyything has been very broad, and the data has been very limited. The original thought was trying to predict the price for any shoe on the site, but due to my data collection restraints I narrowed it down to just two types of brands. Addias and Nike. Within those two brands, there are two sub-brands called "Yeezy" and "Off-White". My dataset contains only those two types of shoes. 

There are so many styles and types that it would be very difficult to try and predict the price for all shoes. The biggest benefit of a model that predicts properly would be useful for "Sneaker Heads" to see what they could expect on their investment on these high valued items. 


# Procedures followed

This project uses data gathered from the stockx website [here](https://stockx.com/news/the-2019-data-contest/). Each row of data is a shoe that was sold from the years 2017 to early 2019 and the features that are the type of shoe, size, color, the state which it was sold to, and the sale price. 

The features are consist of: 
* Order Date
* Brand
* Shoe
* Sale Price 
* Retail Price
* Release Date 
* Shoe Size
* Buyer Region
* Order year
* Order month 
* Order day
* Release year
* Release month
* Release day
* average_sale_price
* shoe_premium


An overview of the process is in the image below. The data was prepared using the standard Python stack (numpy, pandas, etc.) to remove or fill missing values and to select features. Finally, Linear Regression, Lasso Regression ,and Random Forest Regession resulted in a MSE from 83.777, 83.690, 84.697.

<p align="center"> 
<img src="img/tech_stack.png" height=80%, width=80%, alt="Workflow and Tech stack"><br> <b>Figure 1:</b> Workflow and Tools
</p>
 
## Data Cleaning: 
My data cleaning process wasn't too bad. There were a few columns that had characters mixed with numbers so I had to deal with that small issues. There were also some data types that were object that needed to be swichted to datetime and then split on that date to get month ordered, day ordered, and year ordered. Below shows that change.

******** PUT PICTURES HERE ***************


### Feature engineering

After trying to run a regression on the entire dataset my model was not very intiative. So after thinking it over, I wanted to simplify my model. My entire dataset consisted of about 50 different unique shoes. Below are the different types of shoes that are for each brand. Even though off-white had more different styles, the yeezy brand and far more obervations. After filtering by brand and then shoe count, I took the shoe that had the highest count of every style and tried to predict that one shoe's resell price. 

Besides deciding to reduce my dataset to one shoe, I also calculated the mark up and the days the shoe was bought from the relase date. My new dataframe can be seen below. 


********* PUT IMAGE ************



<p align="center"> 
<img src="images/data_to_feature_eng.png" height=80%, width=80%, alt="Player Groups" align="middle"><br> <b>Figure 2:</b> Data Grouped by Player
</p>

The next step is removing the data associated with the particular match in question, here the first row.  This is illustrated in Figure 3. Since we cannot use the features from a match to predict the outcome of the _same match_, the blank space highlighted in the red box requires  some kind of agglomeration of previous match values.

<p align="center"> 
<img src="images/feature_eng_applies_to_each_record.png" height=80%, width=80%, alt="Expunge Extra Data" align="middle"><br> <b>Figure 3:</b> Eliminate Data Inaccessible <i>a priori</i>
</p>

This process is completed in Figure 4.  The values populating the first row are the average (mean) values from the remaining and are now associated with the target ("Win or Loss" column) in the first row. The process illustrated in Figures 2-4 is repeated for each observation in the data, namely replacing match features for each player in each match with data from that player's previous year of matches.

<p align="center"> 
<img src="images/result_exmpl_of_feature_eng.png" height=85%, width=85%, alt="Expunge Extra Data" align="middle"><br> <b>Figure 4:</b> Generate New Feature Values Based on Previous Player Data
</p>

This association of the engineered features with the target is used to train the models.  The features of the _test_ data (separate set from the _training_ data) are also similarly engineered in order to predict the target (Win or Loss).

### Models Used

The models used were Logistic Regression, Random Forests, and Gradient Boosted Trees.  Logistic Regression was chosen for its similarity to model used in previous work, while allowing for different features to have prominence in the present project. Tree-based methods (Random Forest, Gradient Boosted Trees) were used since they often provide good performance and seemed to be underutilized in previous tennis predictions.
  
To make as direct a comparison between the models as possible, the same features were used in the analysis. The list of features used is as follows:

>Tournament Features:
* Surface: one-hot encoded for 'Carpet','Clay', 'Hard', 'Grass', 'None'
* Tournament Level: one-hot encoded for 'A'(lowest level), 'C' (mid level), 'M' (masters level),'G' (Grand Slam level), 'F' (end-of-year finals),  'D' (Davis Cup)
>Player Features:
* age: player age
* hand: one-hot encoded for 'L' (left),'R' (right), and 'U' (unknown)
* ht: player height
* rank_points: accrued points for all matches played, used for ordinal ranking 

>Match Features:
* 1stIn: Number of first serves not out when serving
* 1stWon: Number of points won on first serve when serving
* 2ndWon: Number of point won on second serve when serving
* SvGms: Number of service games
* svpt: Number of service points
* ace: number of aces
* bpFaced: number of break points faced when serving
* bpSaved: number of break points saved when serving
* df: double faults committed when serving
* minutes: how long a match lasts


# Results and Discussion

As mentioned earlier, all models used resulted in an accuracy value of 0.6 (or 60%).  All three models are within 1% of this value. Accuracy is an acceptable metric since this is an eminently balanced-class problem: For every winner, there is a loser, known as a 'zero-sum game'.

It is interesting that all three models considered here yielded very similar accuracy using the same features. For the two tree-based methods, the hyper-parameters were tuned.  It is likely that the Random Forest model is nearly optimally tuned, but the Gradient Boosted Tree models may benefit from additional tuning.  However further efforts would likely best be spent on feature selection or on more feature engineering.

## Insights

One can gather insights about what features are more predictive for the outcome of a match from the models used. Here, only the Logistic Regression and Random Forest are considered since all models resulted in very similar value of accuracy.

Figure 5 shows the normalized values of the Logistic Regression coefficients.  The normalization occurs in two steps:
* First, the features are scaled before applying the Logistic Regression
* Second, the resultant coefficients are normalized to the coefficient with the largest magnitude.<br>

The most important features are shown toward the left side of Figure 5. In Logistic Regression, the single-most important features is the rank_points (a measure of the player's relative rank). This matches findings from previous works (Kovalchik, 2016). Additionally, other important variables were based on winning points, either as a measure of consistency (1stWon, 2ndWon) or performance in high-value situations (bpSaved). Unexpectedly, the surface ('Carpet', 'Clay', 'Hard', 'Grass', 'None') had relatively lesser influence, all falling in the broad swath of relatively equally influential coefficients.

<p align="center"> 
<img src="images/logistic_reg_coeff_plot.jpg" height=70%, width=70%, alt="Feature Importance for Logistic Regression"><br> <b>Figure 5:</b> Logistic Regression Coefficients For Normalized Model Features
</p>


Figure 6 shows the feature importance from the Random Forest model. Again, the most important feature is rank_points. One interesting factor that appear important for the Random Forest model is the Tournament Level.  The values 'F', 'M', and 'A' are high in importance and refer to more prestigious tournaments (especially 'F', but also 'M') or the least prestigious tournaments ('A'). It appears that the type of tournaments a player participates in has predictive value. Another way that the Random Forest model validates the Logistic Regression coefficients is that the playing surface is relatively less important. The instances of surface ('Carpet', 'Clay', 'Hard', 'Grass', 'None') appear toward the tail end (right side of Figure 6) of the feature importance parameters.

<p align="center"> 
<img src="images/random_forest_feature_imp_plot.jpg" height=70%, width=70%, alt="Expunge Extra Data" ><br> <b>Figure 6:</b> Feature Importance from Random Forest Model
</p>


# Further Investigation 

Several avenues for further investigation exist:
1. Considering a simpler model, so that there is no need for computing statistics from previous matches.
2. Using clustering to group players to determine a style and utilize these groups to make better predictions.
	* First, gather player statistics (such as aces, service speed, service efficiency, return efficiency, 1st serve percentage, etc.) 
	* Next, applying clustering to these features to get ~5 groups of player styles.
	* Finally, include the cluster a player belongs to as a feature in the original dataset  
3. Distributing the statistics over the surfaces (e.g. aces on grass) because I want to explore further if surface is a major predictor.


# Reference Cited :
Kovalchik, Stephanie Ann. ["Searching for the GOAT of tennis win prediction"](http://vuir.vu.edu.au/34652/1/jqas-2015-0059.pdf) _Journal of Quantitative Analysis in Sports_. 12(3): 127–138 (2016).<br>
Wikipedia contributors, ["Elo rating system"](https://en.wikipedia.org/wiki/Elo_rating_system) _Wikipedia, The Free Encyclopedia_. https://en.wikipedia.org/w/index.php?title=Elo_rating_system&oldid=856785064 (accessed 15 Aug 2018).<br>
Sackmann, Jeff. [tennis_atp Repository](https://github.com/JeffSackmann/tennis_atp) _GitHub, Inc_. https://github.com/JeffSackmann/tennis_atp (accessed 6 Aug 2018).