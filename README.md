# ![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png) 
# Wine and Terroir: Understanding Climate 
#### Katherine Hickok
#### General Assembly DSI Capstone 
#### March 02, 2022
---
### PROBLEM STATEMENT

**Can we predict the quality of wine greater than the baseline using climate data?**

### BACKGROUND
Wine quality is subjective. Some flavors appeal to certain people and not others. Yet, wine is able to express a flavor profile that is representative of the environmental conditions the grape was grown in. This concept is called terroir and encompasses factors such as terrain, geology/soil, surrounding flora and fauna, winemaking tradition, and climate. ([01](https://www.afoodieworld.com/tersina/7592-rewriting-wine-101-terroir), [02](https://www.masterclass.com/articles/understanding-terroir-elements-of-a-wines-terroir#4-elements-of-a-wines-terroir)).
<br>

When it comes to seasonal climatic influence on flavor, changes in temperature and precipitation during the harvest year can affect the expected flavor. In a vines third year, the plant matures and produces fruit that is exposed to the elements. ([03](https://learn.winecoolerdirect.com/life-cycle-of-a-wine-grape/#:~:text=Making%20wine%20is%20a%20long,another%20two%20years%20after%20that.s)). In mid- to late-summer, the grapes begin to ripen. The warmer the daily temperatures, the more the grapes will ripen and the more sugars they will produce. Cooler nightime temperatures will preserve the acidity in the grape that will normally dissipate overnight under warmer temperatures. This leads to tasteable trends where, overall, warmer climates produce sweeter, fuller wines and cooler climates tend to produce tarter, more acidic wines. ([04](https://winefolly.com/tips/warm-climate-vs-cool-climate-wine/), [05](https://www.winegame.com/blog/2019/2/15/coolvswarm)).
<br>

This project intends to use monthly climatic variables (precipitation, average temperature maximum, temperature maximum, average temperature minimum, temperature minimum) in conjuction with certain wine characteristics to predict a numerical wine quality value. 
<br>

Special shoutout to Roald Schuring, whose project ([GitHub: Studying_Terroir](https://github.com/RoaldSchuring/studying_terroir)) provided an outline for exploring the concept of wine and terroir and whose code helped troubleshoot a lot of problems.
<br>
##### SOURCES: 
[01: foodie-rewriting wine 101: terroir ](https://www.afoodieworld.com/tersina/7592-rewriting-wine-101-terroir)<br>
[02: masterclass- understanding terroir elements of a wines terroir](https://www.masterclass.com/articles/understanding-terroir-elements-of-a-wines-terroir#4-elements-of-a-wines-terroir) <br>
[03: wine cooler direct- the life cycle of a wine grape: from planting to harvest to bottle ](https://learn.winecoolerdirect.com/life-cycle-of-a-wine-grape/#:~:text=Making%20wine%20is%20a%20long,another%20two%20years%20after%20that.s) <br>
[04: wine folly- why you should know where your wine grow? ](https://winefolly.com/tips/warm-climate-vs-cool-climate-wine/) <br>
[05: winegame- taste the difference: warm vs cool climate wines](https://www.winegame.com/blog/2019/2/15/coolvswarm) <br>

---
### DATASETS

The initial dataset contains wine reviews and characteristics scraped from Wine Enthusiast by Samuel Adams McGuire and can be accessed from Kaggle. This was the backbone of the project and was combined with queries from the [NOAA Global Historical Climatology Network Daily Weather Data](https://www.ncei.noaa.gov/products/land-based-station/global-historical-climatology-network-daily) database accessible using the Google Cloud BigQuery API. The Kaggle dataset is not in this GitHub repo due to it's large size, but is easily accessible [here](https://www.kaggle.com/samuelmcguire/wine-reviews-data).
<br>

The files present in the datasets folder are pickled dataframes used in the notebooks. 

---
### NOTEBOOKS
These notebooks contain the process of getting from this original wine reviews and characteristic dataset to a functioning model. They go in order from data acquistion cleaning to modelling and clustering.
<br>
* [`01_import_data_and_geocode`](01_import_data_and_geocode.ipynb): Notebook 01: importing the data, preliminary data cleaning, and geocoding
* [`02_bigquery_API_weather`](02_bigquery_API_weather.ipynb): Notebook 02: accessing Google BigQuery and making full dataset of both wine and climate data
* [`03_more_data_cleaning`](03_more_data_cleaning.ipynb): Notebook 03: more data cleaning and visualizations
* [`04_modelling`](04_modelling.ipynb): Notebook 04: modelling and visualizations
* [`05_clustering`](05_clustering.ipynb): Notebook 05: clustering and visualizations


Other visualizations can be found on my Tableau Public profile including an [animation](https://public.tableau.com/app/profile/katherine.hickok4534/viz/wine_16461585571710/Sheet7) of wine rating and price per year and [map](https://public.tableau.com/app/profile/katherine.hickok4534/viz/Book3_16462372197160/Sheet2?publish=yes) of climate-clustered regions.

---

### ANALYSIS
#### MODELS
Different regression models were developed to predict which the numerical wine quality rating. The O4_modelling notebook contains a more detailed run-thorugh of this but a summary of key models and their respective train and test R^2 scores is found below. The baseline accuracy of the model is 0.1269, which would be the model predicting the most common rating(89).

|MODEL|DESCRIPTION|TRAIN R^2 SCORE|TEST R^2 SCORE|
|---|---|---|---|
|**LR/ SS**|only monthly climate data, rating|0.1846|0.1675|
|**LR/ SS**|dummied varietal , category + all numeric data|0.3901|0.3734|
|**GS/ Lasso/ SS**|found best Lasso parameters|0.39|0.3738|
|**PF/ Lasso/ SS**|2nd order polynomial features, overfit|0.4841|0.3842|
|**Lasso/ PT**|use PT feature transformer|0.4016|0.394|
|**Lasso/ QT**|use QT feature transformer|0.4016|0.3991|
|**SVR/ QT**|the best model|0.4244|0.404|


LR- Linear Regression, SS- Standard Scalar, GS- Grid Search, PF- Polynomial Features, PT- Power Transformer, QT- Quantile Transformer, SVR- Support Vector Regression

Based on these results, the most successful model was Supoort Vector Regressor with a Quantile Transfomer (test R^2 score: ± 0.830). Because the data doesn't completely confrom to LINE assumptions, Standard Scalar (needs normally distributed data) might not perform as well as other feature transformers. The best model utilized the Quantile Transformer, which can manipulate the distribution of the data to become normal.  

#### CLUSTERING
Being able to use the weather and location data to define specific climate regions was an interesting look at what variables the model deems similar. A Kmeans clustering model found 4 distinct climate regions that encompasses all the variability. For example, one cluster has the biggest daily temperature differential, while another has the highest total precipitation. It is interesting to note that they regions aren't solely delineated by latitude/longitude and can also change depending on the year. 

---

### CONCLUSIONS AND FURTHER RESEARCH
Looking for relationships between climatic variables and wine quality rating proved to be not very straightforward. It would be unrealistic to 100% predict wine quality using only climate data so the performance in the above models is not unexpected. In fact, being able to predict wine quality solely on climactic variables (test R^2 score: 0.1675) is quite impressive. There were some limitations to this project that can be addressed in further iterations, including locations for all wineries in a specific appellation being the same and not being able to include other climate variables like sunlight amount and wind speed. 

Further research in wine and terroir relationships could explore the remaining terrori factors (terrain, geologt/soil, etc.) and their impact in wine quality. Also, performing an NLP analysis on reviews to look for key words that represent warm or cool climates will confirm that the reviewers are tasting what they are expected to taste. This project has only just scratched the surface.

# CHEERS!