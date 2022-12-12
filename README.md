# Streaming Video Analytics
Final Project for UofT Data Analytics Bootcamp
Data Cleaning, Analysis, Dashboard, Machine Learning


## Selected Topic: the Streaming Video Industry

According to PwC's latest Global Entertainment & Media Outlook 2022, the streaming video industry was valued at $79.1 billion in revenues worldwide in 2021 and will continue to grow at a pace of 7-10% annually for the next few years. The big driver of opportunity is a major shift by all major players in the subscription video space (Netflix, Amazon Prime Video, Disney+, Paramount+, Peacock) to hybrid streaming models that combine lower-priced, ad-supported tiers with more premium, ad-free tiers.

Across the streaming video industry, content providers and distributors are moving into big data to analyze subscriber funnels and viewership patterns to optimize content production/acquisition costs, help with programming decisions, improve content recommendation to their users and ultimately drive subscriber and advertising revenue.


## Our Client

Our real-world client is an online distributor of online video based in Western Europe and specialized in music content, mostly full-length concerts and documentaries. They offer 3 advertising-backed, linear channels (free/no subscription), a premium subscription service and a large library of titles available for sale to other conventional channels.

Their channels are available worldwide and designed primarily for free, ad-funded platforms such as PlutoTV, Roku, YoutubeTV, Plex, Samsung TV Plus, LG Channels and other OEM services. Their premium SVOD service is available primarily through traditional pay-tv distributors and on a direct-to-consumer basis.

Each of the 3 channels (and each piece of content on the channels) are embedded with unique identifiers and markers 24/7 to automate and maximize advertising sales: position and duration of the ad breaks available, viewer profile, location by country, device, viewership history etc. All those parameters are used to customize the experience and offer as much audience targeting as possible to advertisers (who are then willing to spend more to reach a more targeted audience).


## Client's and Project's Objectives

As the Client rolls out their services across an increasing number of platforms around the world and the number of viewers increases as well, the amount of data collected daily skyrockets exponentially. At this stage in their development (3 years after commercial launch), the Client collects on average 120,000 lines of viewership data per day and up to twice as much advertising data depending on granularity. The Client is no longer able to cope with traditional Excel and PowerBI tools, and is now looking to hire one or more data analysts to take the company to the next level.

Our study will focus on viewership, programming and advertising revenue data for the 3 linear channels to help answer the following:
- identify viewership patterns by channel, content, country, platform... ;
- identify revenue trends and determine which channel/content/genre brings in more revenue, by country or region ;
- make revenue projections into the next 2 fiscal quarters ;
- make content recommendation based on internal content tags.


## Data

The client has made the following available to us:
- 550 CSV files containing viewership data for 3 channels across 14 operators in 67 countries from 18Feb21 to 6Nov22
- 1 CSV file containing advertising revenue for their 3 channels across 19 main territories from 28Feb22 to 25Oct22
- Programming details, IDs, genres, tags
- Channels mapping
- Operators mapping
- Countries mapping

The data has been anonymized to preserve the confidentiality of the client.


## Software & Resources

Python:
- Data Cleanup: Python 3.7.13, 3.9.13

- Exploratory Data Analysis: Python 3.7.13, 3.9.13, Microsoft Excel
	- Libraries Used: PANDAS, Matplotlib, numpy, sqlalchemy

- Machine Learning: Python 3.9.13, Apache Spark 3.2.2, Hadoop 2.7, Java 8 or Higher, Nvidia CUDA 11.0 or higher, Nvidia Driver Version 452.39 or higher, CUDA SDK 11.8 support for compute capability 3.5 – 9.0 (Kepler (in part), Maxwell, Pascal, Volta, Turing, Ampere, Ada Lovelace, Hopper)

	- THESE MODELS WERE BUILD USING A LOCAL INSTALLATION OF APACHE SPARK, PLEASE MAKE SURE YOU HAVE LOCAL SPARK OR MODIFY FOR USE IN GOOGLE COLLAB APPROPRIATELY

	- Libraries Used: 
		- MLV1: Python 3.9.13, PANDAS, PySpark, Matplotlib, Numpy, sklearn.linear_model Linear Regression
		- MLV2: Python 3.9.13, PANDAS, PySpark, Matplotlib, Tensorflow_gpu, Keras, sklearn.model_selection train_test_split, sklearn.preprocessing StandardScaler and OneHotEncoder
		- MLV3: Python 3.9.13, PANDAS, PySpark, Matplotlib, Tensorflow_gpu, Keras, sklearn.model_selection train_test_split, sklearn.preprocessing StandardScaler and OneHotEncoder
		- MLV4: Python 3.9.13, PANDAS, PySpark, Matplotlib, statsmodels.tsa.statstools adfuller, statsmodels.api, itertools
	
	- Additional Requirements for Apache Spark (.conf)
		- spark.driver.memory           	32g
		- spark.rapids.sql.enabled          true
		- spark.driver.maxResultSize		0

	- Additional Requirements for Apache Spark (.jar)
		- rapids-4-spark_2.12-22.10.0.jar
		- cudf-0.15-cuda10-1.jar
		- postgresql-42.5.0.jar

- Data Storage: Amazon AWS: S3, RDS
	- PostgreSQL: pgAdmin4 6.8 


## Methodology / Steps / Decisions made while cleaning (to be updated as we go)

Minute Level Aggregation Table:
- Merge all 550 viewership files into one
- Anonymize the data pertaining to channel and operator names using anonymization key table. (CONFIDENTIAL DATA, NOT AVAILABLE TO PUBLIC)
	- Applied a function to each row to find string in list of anonymization key table for both channel and operator under unanonymized and now removed 'channel' column
	- Code should be refactored in future revisions or before deployment to improve speed. Currently takes 500 minutes to run for loop for each function.
- Anonymized 'content_id' column containing exact name of media content provided using Media Library key from data provider for programs and generated an anonymization key table for playlists. (CONFIDENTIAL DATA, NOT AVAILABLE TO PUBLIC)
	- Applied regex filters to obtain program or playlist numbers and created new columns containing anonymization key
	- Merged columns with .fillna
- Used Anonymized 'content_id' to match with genre data from data provider.
	- Merged columns on anonymized 'content_id' and removed extra columns.
	- Method was applied to 52 million rows with about 3000 rows of anonymized data keys.  Method of using PANDAS .merge (similar to SQL or Excel VLOOKUP) is faster by about 250x
- Final DataFrame was exported as .csv for development purposes, but should be uploaded to PostgreSQL database.

Advertising Data (42,045 rows):
- drop columns as indicated by the client: "'bid_timeouts_rate", "render_rate", "fillrate", "avg_winning_bid (‚Ç¨)" and "avg_imp_ecpm (‚Ç¨)"
- convert date column from 'object' to 'date' with to_datetime
- drop rows that contain either all null values OR an "endpoint_request" value and all null values otherwise (853 rows)
- drop rows without a "country" value (12 rows)
- convert country codes to country names, and add a "region" column
- create new columns for CPM and pod drop rates
- replace "no viewership data" values in "channel" and "operator" columns with "unknown" (7,217 rows)


## Our Initial Analysis

The results of our initial analysis are available on Google Slides here:
https://docs.google.com/presentation/d/1ZYtJm8cpZripwMaD_qk0EF06C857dtl9DBsFkQhWB-g/edit?usp=sharing


## Database

Based on the data that we received and our initial EDA, we poject the database will look like the following after data cleaning:

<img src="Dashboard Demo Charts and Figures/Images and Screenshots/ERD v2.png" height=200>

- Data Storage and Handling was done via Amazon AWS RDS and S3
- Data Storage Service was connected to PostgreSQL database accessed using pgAdmin4
- Cleaned data was uploaded to database using local installation of PySpark


## Dashboard

Our live dashboard is available here:
https://public.tableau.com/app/profile/julien4866/viz/StreamingVideoAnalytics/Story?publish=yes


## Provisional Machine Learning Model

Revenue projections will be made following multiple regression analyses based on the revenue table down to date and country level.
Content recommendations will follow a more advanced machine learning model (tbd)


## TRIANGLE Update #1
- Dropped the faulty advertsing table due to improper data types.
- Created a query to properly format the advertising data table, in order to load the csv file into it.


## TRIANGLE Update #2
- Created a query to merge the advertising data with the merged viewership and mapping data.


## Triangle Update #3
- dropped corrupt data from the AWS RDS, created a new table with proper compatible data types for the final dashboard.


## Dashboard Blueprint

The blueprint outline for the dashboard was completing using Google Slides.  It can be assessed via the following link:

(https://docs.google.com/presentation/d/e/2PACX-1vRofIl3PT3YXPaovaF8rEX-PCgZCxjt9Zt3KBfHO7ltMDcQT0FBkzJcU7c9aA_-wMeRTi-FqwvJdDzF/pub?start=false&loop=false&delayms=3000&slide=id.p)

The dashboard blueprint can also be accessed in pdf format from the link below:

[Dashboard Blueprint PDF](https://github.com/Peter-Nguyen96/Capstone-Project-Analytics-Dashboard-and-ML-Model/blob/60ea90400cb2adca390fe1d4aa5950c5387db118/Dashboard_Blueprint/Dashboard_Blueprint_v4.pdf)


# Machine Learning Update #1

MLV1: A simple linear regression machine learning model using sklearns Linear Regression model.  Exploratory data analysis was conducted to look at the revenue over time where a discernable but poor linear pattern was observed.  An attempt at linear regression was made fitting the data from January 1st to October 25th of the 2022 calander year.  The data from the advertising table in the database was grouped by date creating a daily level aggregation, and used to train a linear regression model.  The model performed poorly, and was highly sensitive to extreme outliers and noise in the data.


# Machine Learning Update #2

MLV2: This model attempts to increase the dataflow into the machine learning model to predict revenue based on requested_pod_duration/delivered_pod_duration ratio.  

## Data Preprocessing:
The advertising data was merged with the viewership data using viewership_channels_mapping_table which contained feed numbers and their respective channels and operators.  The mapping table was merged to the viewership table to convert feed ID into channels and operators, then the new merged mapping viewership was merged into the advertising table on date, chanel, operator, country, and region.  Some additional data cleaning was nessessary.  Viewership data contained additional segmentation by device type (TV,phone,laptop ect.) which needed to be merged and summed.  Additionally, the provided mapping table contained duplicate channel/operator pairs causing some rows in the merged table to create duplicate rows with two different feeds.  Cleaning efforts produced ~43000 rows when expected was ~41000 rows of data.  There is a suspected label mishandelling caused by the data provider where some NaN columns also got duplicated during the merge.  For the machine learning model, NaN rows had to be dropped anyways, so this level of data cleaning was deemed acceptable after NaN rows were dropped. Categorical data was encoded using OneHotEncoding, and scaled using StandardScaler. For this model the date was converted to integer by determine the number of days from the first day in the dataset (2022-01-01).

##  Description of preliminary feature engineering and Explaination of Model Choice.
Features were selected based on columns in the merged dataframe that would be known ahead of air time.  Country, Operator, Channel, and Requested Pod Duration (requested ad time) were used to predict revenue.

## Modelling:
A neural network built using Keras and Tensorflow was made with 3 hidden layers and relu activation functions. The data was split using test_train_split and used to train the model for 500 epochs with mean squared error for both the loss and training metric with the 'adam' optimizer.  After 500 epochs the mean squared error was reduced from 1700 initially to 1300, and the model was able to with some reasonable degree of acccuracy determine predict the revenue.  However this generation of model was ultimately abandoned due to the lack of accuracy as well as difficult utility. The multiple input fields are required and training time for this model is quite high.  Based on the output during training, additional hidden layers or increased length of training may produce greater accuracy but again require more input fields to produce a prediction. This model did however demonstrate the vastly superior tensorflow-gpu library for training machine learning algorithms, and future iterations of MLV2 with increased training time would be a simple matter with Nvidia GPU equipped hardware.


# Machine Learning Update #3
MLV3: This model takes an entirely new approach to dveloping a machine learning model with the goal of first predicting viewership and then predicting revenue, and takes advantage of a vastly larger dataset available in this module. The minute aggregation data contains viewership information, programatic data, as well as genre data at the minute level for a larger length of time than the advertising information. The goal of this third generation machine learning model was to increase available training data as well as take advantage of GPU acceleration demonstrated in MLV2. 

## Data Preprocessing and EDA:

Due to the large size of this dataset, for development, only data from the UK (the largest represented country) was subsampled from the dataset.  The data was further aggregated to the hourly level and only within the time range of the advertising data (2022-01-01 to 2022-10-25). The genre column was split and encoded using the pd.get_dummies function in order to quantify the variety of genres of media consumed in each hour. The total_sessions column was dropped and the data was set up to predict the total_session_duration_seconds given time and encoded categorical genre information.  This was done because at the minute and hourly level, it was found that cyclic or seasonal patterns emerged from the data which also prompted the use of timeseries analysis. 

## Modelling:
Similar to MLV2 a neural network model was built using Keras and Tensorflow. The data was split using train_test_split, and the model was trained for 1000 epochs with a batch size of 256 and a learning rate of 0.01. For this model, the LSTM activation function instead of RELU was used to use the datetime data in the native datetime format instead of encoding with integers like in MLV2.  This also served to create a true timeseries model.  The loss metric used was mean squared error, and success metric used was accuracy.  After 1000 epochs of training, while loss was going down, val_loss was increasing, and accuracy did not improve. The model also showed signs of overfitting and is likely hampered by noise due to the over 200 encoded genres that were fed as categorical data. While the concept of this model was promising, it required significantly more data cleaning, by generalization of this genre data as well as improvements to the design of the neural network. With additional hidden layers, perhaps with RELU to handle the categorical values and LTSM to handle time series.  Further optimizations are possible for development of the third generation MLV3 model.

# Machine Learning Update #4

MLV4: This model is an evolution of MLV2 and MLV3 and attempts to simplify factors and increase accuracy using a timeseries analysis, and a Seasonal Auto-Regressive Integrated Moving Average (SARIMA) model to decompose the complex compound function that makes up the revenue curve, and make a prediction.

## Data Preprocessing and EDA:
The revenue table was imported from the database.  The data was grouped by date, and a dataframe was made containing the date, and sum revenue data for each date (aggregate of countries, channels, and operators). This created a daily aggregation, and this was plotted against a weekly mean resample in order to see if there was a greater trend when we smoothed out the data. Statsmodel.api tsa.seasonal_decompose was used to perform a Fourier Transform like operation to determine the functions that aggregate together to form the revenue curve (overall trend, seasonal or cyclic events, and residual variation). The data was also determined to be non-stationary based on a quick plot of the raw data against the rolling mean, as well as through using statsmodel.tsa.stattools adfuller.  The data was detrended to make it stationary with 90% confidence. The data was made stationary so that time series prediction would not be dependent on the time at which the series was observed. Finally a function was used to iterate through the data and seasonal period to determine the best parameters for the SARIMA model (parameters with the minimum AIC) which was (1,1,1)x(1,1,1,52)

## Modelling:
For this type of model, only the time in days (as an index) and the revenue was required as feature and prediction respectively. During training diagnostic figures were created including Standardized residual for 'r', estimated density, normal Q-Q, and a correlogram were made. The ML model was written to predict and plot the revenue for the remainder of the 2022 calander year as well as provide the predicted mean, lower bound, and upper bound for the revenue for the next 5 days after the last available data point. 

## Square Update
- link to the published Tableau Story : https://public.tableau.com/app/profile/julien4866/viz/StreamingVideoAnalytics/Story?publish=yes
