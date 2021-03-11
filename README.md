# Food Sales Predictions
Predicting the sales of various items at RamDrian Stores

## Objectives

RamDrian Stores has provided us with sales data, covering 10 Outlets (Established between 1985-2009), of varying Outlet types, locations and sizes. They are interested in seeing which factors influence sales the most, as well as having a predictive model set up so that they can predict the future sales of items.

## Factors

The following factors/features were provided in the dataset:

- Item Identifier (ID of each item)
- Item Weight
- Item Fat Content
- Item Visibility (% of store floor space used to display item)
- Item Type (Category of item, such as Dairy, snack foods etc.)
- Item MRP
- Outlet Identifier (Unique ID for each Outlet)
- Outlet Establishment Year
- Outlet Location Type
- Outlet Type (Grocery store, supermarket etc.)
- Outlet Size
- Outlet Sales 

Outlet Sales is the target value we are trying to predict.

 ## Considerations

With any dataset, we first have to deal with missing values. In this dataset we had missing values for Item Weight and Outlet Size. Below is how we decided to deal with these values:

### Item Weight

Item weights for the year 1985 were missing. After consulting with the owner, we agreed that since the desired result for this analysis is to predict food sales in the future, and since we have 9 other years (where outliets were established) to analyze, AND most importantly, since 1985 is the oldest year in our data set, that we can remove all observations from outlets established in the year 1985 without worrying about its impact on our model. This will remove ~1500 observations from our data BUT, we still have ~7000 observations left to work with that is more than sufficient.

### Outlet Size

The items missing an outliet size are all from certain stores (eg. ALL stores labeled a,b, and c have NO outlet size, but ALL stores labeled d,e, and f HAVE outlet sizes with 0 missing values)

Therefore, excluding them from the analysis would be detrimental to the model as we would be eliminating multiple entire outlets. Since the owner of the business is new and does not know how nor have access to the outlet sizes for the missing records and I dont feel trying to 'guess' the outlet size based on just outlet type and outlet location type would give us accurate results since there - at least at first glance - does not appear to be any relationship between those features and outlet size. My proposed solution was to replace the missing values with a 'Unknown Size' value. That way the information from ~2400 observations will not be lost, but will not influence (too much) the results of our final model. In addition, I added a missing Outlet Size column so that we could see if this decision influenced the end results of the model significantly (The end of our analysis showed that this decision did not influence the end results in a significant manner, and therefore the change was maintained).

### Dropped or Changed Features

Item ID was eliminated from the final model for both performance reasons (there are 1555 unique Item IDs which would make our model overly complex due to the fact that we would have to create a new column for every item in order for our model to calculate. This could potentially lead to over-fitting and performance issues), and due to the fact that - after speaking with some SMEs - the assumption was made that an individual item, in general, should not have a grant effect on sales. Instead, Item Type would be a more natural identifier of larger or smaller sales prices (for example, furniture would probably gross more sales than snack foods).

Another change that was made was the dropping of the Outlet Establishment Year column for our final model. This was because it was found that each outlet ID corresponded to exactly one year. Therefore outlet ID and Outlet Establishment Year had a 1:1 relationship and could be thought of as dependant variables. It would be pointless to add both to the model. For the sake of maintaining the concept of a 'generational effect' of the year a store opened, I instead created a new Decade column which denoted the decade a store was opened in (80s, 90s, etc.). This imporoved the performance of the model, and it was found at the end of our modeling process that Decade itself also had no significant effect on the model.

##Preliminary Analysis - Data Exploration

Before starting the modeling process, the following observations of note were made:

![image](https://user-images.githubusercontent.com/77307911/110734487-8b588a80-81ed-11eb-91e0-252d6a58bf95.png)

The distribution of item sales was consistent regardless of Outlet Size

![image](https://user-images.githubusercontent.com/77307911/110734603-c78beb00-81ed-11eb-91d0-93f6919c340e.png)

Besides a large amount of low volume sales for Outlets in location Tier 3, the distribution of sales was consistent regardless of Outlet Location Type

![image](https://user-images.githubusercontent.com/77307911/110734718-fefa9780-81ed-11eb-9785-2c34251820de.png)

Though sales distributions are some what similar (at least for Supermarkets) It is clear that Outlet Type's of Supermarket Type 1 have a way larger volume os sales then other outlet types

![image](https://user-images.githubusercontent.com/77307911/110734874-500a8b80-81ee-11eb-911a-645a55f94add.png)

With the exception of Outlet 10, all outlets appear to have similar sales distributions, meaning - at first glance - no stores drastically outperform any other stores

![image](https://user-images.githubusercontent.com/77307911/110735025-995adb00-81ee-11eb-8192-9b7bc56924c9.png)

![image](https://user-images.githubusercontent.com/77307911/110735064-a4ae0680-81ee-11eb-95c1-bf5292b7b589.png)

Item Visibility and Item MRP seem to have some correlation with sales, albeit weak. This is confirmed below:

![image](https://user-images.githubusercontent.com/77307911/110735158-d0c98780-81ee-11eb-9e98-a74e5399c0aa.png)

![image](https://user-images.githubusercontent.com/77307911/110735230-01112600-81ef-11eb-9b15-697ff81d284a.png)

We see item visibility has an extremely weak (practically negligable) negative correlation with sales

MRP has an average positive correlation with sales

![image](https://user-images.githubusercontent.com/77307911/110735501-87c60300-81ef-11eb-921f-de651bc03ed0.png)

This visual provided some interesting insights as to which Item Types had contributed the most to sales. Overall, Fruit & Veg and snack foods grossed the most sales, followed by household items.

### In Summary:

From our initial EDA, it appears as if outlet ID, Outlet size and outlet Location Type will not impact sales that greatly, and that MRP and perhaps item visibility and item type might be influencing factors when it comes to sales. However, correlation does not imply causation and so the only way to confirm any of this is which a model

## Modeling Process

Within the time constraint, 3 different types of models were tested on the data:
- Linear Regression
- KNN Regression
- Random Forest

For KNN and Random Forest, multiple different hyperparameters were chosen and fitted to the models in an attempt to try optimize.
(Non-technical translation: We tried multiple small adjustments to our predictive models to try and make them more effective and come up with the model that would best predict unseen data)

## Model Results

Without going into extreme detail (which can be found in the assocaited code of this repo), at first KNN seemed to produce the best results as it had a very high R^2 (accuracy measure) - in other words it was good at fitting the data it was given. However, it underperformed (though only slightly) when it came to the RMSE of our testing data which is a metric which tells  - in this case - us the average amount of dollars our predictions are off by (average error). Due to the fact that both KNN and Radom Forest had less than ideal R^2 scores (both less than 65%), Our final decision was to rather go with the model that produced the lowest RMSE for testing data (unseen data), and that was Random forest.

## Conclusion

The final model we chose was a Random Forest with n_neighbors = 200, min_samples_leaf = 2 and min_samples_split =2

This model had an r^2 of .4722 (47.22%) and an RMSE of 1076.84 which means our average error when predicting sales was \~1076


Contrary to some of our preliminary findings, we noticed the following in terms of the features of the model:

- Item Visibility (at least according to our Linear regression model) did significantly impact sales - it appears that as visibility increases, sales decrease. Which is surprising
- Location Type does influence sales. This is probably due to Tier 3 having much larger volumes of lower sales amounts
- According to our Linear Regression Model, Outlet ID did impact sales

In-line with our original findings, the following was discovered:

- Outlet Size, Outlet ID, Year Established/Decade, and Fat Content did not significantly impact sales
- Outlet type did influence sales
- MRP and Item Visibility did significantly influence sales

## In conclusion

Since we chose Random Forest as our main model, according to it's results, the most influental features were (in order):

1. MRP
2. Item Visibility
3. Outlet Type

## Recommendations

Data-wise:
- Since both the R^2 (accuracy) and RMSE of the best model tested were less than ideal, we recommend collecting more data for further analysis
- Should more time be allocated to this project in future, addition model types can be tested to make an attempted at better predicted values

Enterprise-wise:
- Stock more items with higher MRP
- Reassess item visibility procedures. Perhaps items need less space than they current utalize
- Even though seafood produced the lowest overall sales, an item sold in the seafood category would increase sales on average by about \~327 - which is significant. Perhaps there is an unmet demand for seafood which if jumped on, could result in greater sales

