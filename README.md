# Video Games Sales Analysis
![Video Games](https://miro.medium.com/max/1400/1*rTAjrwwt0Jze_3MqHQ5MfA.jpeg)

## Overview of the Project

For this project, we have selected to analyze video games sales. We have chosen this topic because we are interested in looking at factors affecting video game sales such as genre, developer, critic score, platform and ESRB rating. The first dataset was collected from the Kaggle dataset. There are 55,792 records in the dataset as of April 12th, 2019. It was generated by a scrape of vgchartz.com. The second data set contains information about video-games publishers.

The questions we hope to answer from the analysis are as follows:
- What is the overall trend of global sales for video games? 
- How do factors such as genre, critic score, and developer affect video games sales?
- Can we make a prediction about video games sales based on the following factors mentioned above? 

## ERD (Entity Relationship Diagram)
As we are dealing with multiple dataframes, a conceptual diagram was created to keep track of important relationships in our dataset. 

![ERD](https://github.com/kntln/Video_Games_Sales/blob/karens_branch/figures/ERD.png)


## ETL
![ETL](https://github.com/kntln/Movies-ETL/blob/main/Resources/ETL.png)

### Extract:
The raw datasets that will be used for this analysis are:

Video Game Sales Dataset: https://www.kaggle.com/datasets/ashaheedq/video-games-sales-2019

Videogames-companies-regions Dataset: https://www.kaggle.com/datasets/andreshg/videogamescompaniesregions

First we downloaded the datasets and placed them in the Resources folder. 

Then we inserted the sales data and the region data in the Jupyter notebook using the pandas library. We merged both dataset together with how = Left, left on Publisher and right on country.


### Transform:
We used .count and .columns to review the data. After that we dropped  “Developer_y','NA_Sales','PAL_Sales','JP_Sales','Other_Sales because they are irrelevant to our analysis. We found that total_shipped and global sales also represent the total sales so we added them up and converted it to a new column Total_sales”.

We found that there are substantial null values for some of the columns so we decided to drop the whole column as well since they are not relevant. They are “ESRB_Rating','Critic_Score','User_Score', and 'Country'

We also found that there are 35930 rows with $0 sales, so we have to drop them as well since they won’t provide any result when we are doing comparison based on Sales.

We also drop rows only with the NaN value in Developer_x and Year since they only have 17 and 979 null values out of 55792 row

![Cleaned Dataframe](https://github.com/kntln/Video_Games_Sales/blob/karens_branch/figures/cleaned_df.png)


### Load (AWS Connection):

S3 Bucket Links:
Raw dataset:
https://video-game-dataset-uot-boot-camp-2022-group-4.s3.us-east-2.amazonaws.com/video-games-developers.csv
https://video-game-dataset-uot-boot-camp-2022-group-4.s3.us-east-2.amazonaws.com/vgsales-12-4-2019-short.csv


Cleaned Dataset:
https://video-game-dataset-uot-boot-camp-2022-group-4.s3.us-east-2.amazonaws.com/all_columns_df.csv
https://video-game-dataset-uot-boot-camp-2022-group-4.s3.us-east-2.amazonaws.com/sales_cleaned.csv

- RDS Config (connect to Postgres): 

![Initial Databse](https://github.com/kntln/Video_Games_Sales/blob/karens_branch/figures/postgres.png)

- Connect Postgres & RDS (After creating table in Postgres)

![Configure Setup for RDS](https://github.com/kntln/Video_Games_Sales/blob/karens_branch/figures/postgres2.png)

![Connect RDS to Postgress](https://github.com/kntln/Video_Games_Sales/blob/karens_branch/figures/postgres3.png)

## Tableau Visualization

[Click here for access to Tableau Video Games Sales Analysis](https://public.tableau.com/app/profile/karen.tolentino/viz/Video_Games_Sales_Visualization/VideoGamesSalesVisualization)

For example, here are the following visualizations:

![Critic Score vs Total Sales](https://github.com/kntln/Video_Games_Sales/blob/karens_branch/figures/TotalSales_CriticSccore_Year_Genre.png)

![Publisher vs.Total Sales](https://github.com/kntln/Video_Games_Sales/blob/karens_branch/figures/total_sales_vs_publisher.png)

![Country Vs. Total sales](https://github.com/kntln/Video_Games_Sales/blob/karens_branch/figures/total_sales_vs_country.png)


## Machine Learning Model
We will be using different machine learning models to predict the video game sales, find the clustering patterns and how it affects the sales, and select the model that yields the best result. The following machine learning models that were used are outlined below.

### Linear Regression

Using **`sklearn.linear_model`**'s **`LinearRegression `** we are wanted to predict the sales of a video game based on critic score.

- Model draft: 
    - model = LinearRegression()
    - Target variable (y): Total_Sales
    - X: Critic_Score

- Steps:
    1. Dropping columns that are not needed.
    2. Finding Null Values and dropping them.
    3. Visually prepare and inspect the relationship between Critic Score and Total Sales.
    4. Prepare to use the Linear Regression Model.
    5. Format the data for **`sklearn.linear_model`**.
    6. Examine the X array.
    7. Examining the parameters of our model and find the best fit for our red line.

- Results:
There was a positive correlation between Critic Scores and Total Sales in the preliminary test of the Linear Regression model, showing a coefficient model of 0.51, which indicates that the predictive model is improving. Further analysis is needed to confirm this conclusion.

![Linear Regression](https://github.com/kntln/Video_Games_Sales/blob/karens_branch/figures/Linear_Regression_Video_Games_Sales.png)
![Linear Regression Coefficient](https://github.com/kntln/Video_Games_Sales/blob/karens_branch/figures/Linear_Regression_Coefficient.png)

### Deep Neural Network Model

- Steps:
    1. We first load our dataset in dataframe from “all_column.csv” 
    2. We use .nunique to find how many unique values in each column.
    3. We drop the non-beneficial ID columns - Rank, Name, Year.
    4. We visualize the value counts for binning. We use a bucketing method for the columns which contain high unique values         Developer_x, Publisher, Platform, Critic_Score, because they have too many unique values for each volume, there will be an impact if there are too many unique values when we train and evaluate the model.
    5. We save all object types columns in “cat” in order to use `OneHotEncoder` later.
    6. After that we used OntHotEncoder from Sklearn and to merge with main df bridge_df on key values.
    7. We train and split our target - “total sales'' and use `StandardScaler()` to standardize and scale the data.
    8. Before training our model we first define our model using Deep Neural Network. We set up two hidden layers as 80 and 30, and used both `RELU - Rectified Linear Unit` as the activation.
    9. We compile the model and create a callback that saves the model’s weights every 5 epochs, then we train the model.
    10. We evaluated the model using the test data and got the accuracy as 0.002. The accuracy is relatively low.

- Results:
It shows the model has an accuracy of 0.002. Model optimization is needed such as reducing features or adding more hidden layers. We could also use other machine learning models.

![Deep Neural Network Model](https://github.com/kntln/Video_Games_Sales/blob/karens_branch/figures/Deep_Neural_Network.png)

### Decision Tree
- Model Draft:
    - Target Variable and Features:
    - Target variable (y): “Successful”
    - X: Genre', 'ESRB_Rating', 'Platform', 'Publisher', 'Developer_x', 'Country

- Steps:
    1. We have prepared the data using the same process as the deep neural network model.
    2. Create “Successful” column for Critic Score >= 7.0
    3. Encoded all object into numerical values by `OneHotEncoder`
    4. Train the scalar and transform the data.
    5. Verify that the mean of each column is 0 and its standard deviation is 1.
    6. Define the features and target set.
    7. Split data into train and test data 80/20 split and create `StandardScaler()` instance.
    8. Create decision tree classifier instance and fit the model.
    9. Make predictions using the testing data.
    10. Calculating the confusion matrix, create dataframe from confusion matrix.
    11. Calculating accuracy score and display result.

- Results:
The model has an accuracy of 1.0 which shows that the model is overfitted.One of the reasons it happens when a function is over trained itself on the data. Couple ways to handle overfitting: we can reduce the number of elements in the hidden layers or remove certain features.

![Decision Tree](https://github.com/kntln/Video_Games_Sales/blob/karens_branch/figures/Decision_Tree.png)

### Random Forest Model:
To predict whether the critic score will be high or low based on different features

- Target Variable and Features:
    - Target variable (y) = Critic_Score_Status (low/high)
    - X = Genre, ESRB_Rating, Platform, Publisher, Developer_x, Country, Total_Sales

- Machine Learning Models:
    - rf_model = `RandomForestClassifier`
    - brf_model = `BalancedRandomForestClassifier`
    - eec_model = `EasyEnsembleClassifier`

- Steps:
    1. Preprocessing data, dropping unnecessary columns.
    2. Drop NaNs values
    3. Create an additional 'Critic_Score_Status' column labeling the game by their critic score, the critic score which is higher than or equal to 7 is labeled with high, otherwise is labeled with low.
    4. Bucket the categorical columns to reduce the variables. Keeping top 10, and bin others as 'other'.
    5. Encode the categorical variable using `OneHotEncoder` method
    6. Assign target variable and features:
        -Target variable (y) = Critic_Score_Status (low/high)
        -Features (X) = Genre, ESRB_Rating, Platform, Publisher, Developer_x, Country, Total_Sales
    7. Split the dataset into training and testing and scale the data.
    8. Create the ML models then train and test the model.
    9. Evaluate the model with the accuracy score and classification Report

- Results:
The `RandomForestClassifier` model has an accuracy of 0.72, however, the precision and recall values for the low critic score category are considerably lower than those of the high critic score category, this difference may result from the higher number of variables in the high critic score group (2,435 variables), which is double of the variables in the low critic score (1,144 variables).

The model accuracy for `BalancedRandomForestClassifier` is quite similar to RandomForestClassifier model, however it gives higher recall values for the low critic score category despite the slight drop in precision.

Another issue for this model is that there is a significantly high number of NaN values in the Critic_Score column (15,156 out of 19862 rows), after dropping all NaNs values of every columns, the number of input variables to be used in training and testing the model falls to only 3,579. One consideration in optimizing the model is to see the data distribution and find a way to correlate NaN critic score values to high/low groups instead of dropping them in order to increase the number of inputs.

![Random Forest Classifier](https://github.com/kntln/Video_Games_Sales/blob/karens_branch/figures/Random_Forest.png)

![Balanced Random Forest Classifier](https://github.com/kntln/Video_Games_Sales/blob/karens_branch/figures/Balanced_Classifier.png)

## Resources
Software: Python 3.10, Conda 4.13, jupyter notebook 6.4.11

Data tools and library: SQLAlchemy, numpy, panda, matplotlib, scikit-learn, Tableau.

Video Game Sales Dataset: https://www.kaggle.com/datasets/ashaheedq/video-games-sales-2019

Videogames-companies-regions Dataset: https://www.kaggle.com/datasets/andreshg/videogamescompaniesregions



