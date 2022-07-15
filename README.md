# Phase 3 Project: *Tanzanian Water Pump Conditions Classification Model*

![Person washing their hands under a water pump](https://github.com/rjpost20/Tanzania-Water-Classification-Project/blob/main/data/AdobeStock_316365952.jpeg)
Photo by [lovelyday12](https://stock.adobe.com/contributor/203603884/lovelyday12?load_type=author&prev_url=detail) on Adobe Stock

## By Ryan Posternak

### Links

Medium article:

Presentation slidedeck PDF:
https://github.com/rjpost20/Tanzania-Water-Classification-Project/blob/main/Tanzania%20Water%20Classification%20Presentation.pdf

*This is an asssignment for learning purposes. The Tanzanian Ministry of Water is not involved with this assignment in any way.*

<br>

## Overview and Business Understanding

In the United States, most of us take for granted that we always have access to clean and safe water. But for hundreds of millions of people across the world, clean water is anything but guaranteed. One nation that is struggling with water insecurity is Tanzania, where as much as half the country–over 20 million people–<a href="https://lifewater.org/blog/tanzania-water-crisis-facts/" >do not have access to safe drinking water.</a><br>
<br>
In order to combat this crisis, <a href="http://taarifa.org/" >Taarifa</a> and the <a href="http://maji.go.tz/" >Tanzanian Ministry of Water </a> (TMW) have teamed up to gather data on the nations many water pumps - the primary source of safe drinking water for most Tanzanians. Using this data, I build a classification model that predicts whether a water pump is functional or needs attention (either non-functional or in need of repair).<br>
<br>
Using this model, the TMW will be able to improve their efficiency in repairing dysfunctional water pumps, and improve access to clean drinking water for millions of people.<br>
<br>
A note on scoring metrics: The TMW has not made it explicitly clear to us what the model will be used for. As such, we'll assume that the model will be used for both inferential purposes (i.e. to see which features are most deterministic in producing each condition), and for predictive purposes (i.e. to be able to predict the condition of a water pump based on the data on it).

Taking this assumption on the predictive purposes of the model, it's not clear which class label(s) is the most important to achieve high precision or recall scores on. For example, it may be that the TMW would like a high recall score for the functional water pumps, so they don't dispatch precious resources to pumps that are actually functional. It could also be the case that they want a high recall score for non-functional water pumps so that they can dispatch repair teams as quickly as possible for all non-functional pumps in Tanzania. Given the ambiguity here, we will prioritize a balance between precision and recall across classes and prioritize overall accuracy as the deciding metric when determining the best model.

Additionally, the competition will ultimately judge $\hat{y}$ submissions based on their classification rates, which is equal to the percentage of rows in $\hat{y}$ that match the actual y_test labels. This is equivalent to the accuracy score.

<br>

## Understanding the Data

This data was obtained from Taarifa, "an open source platform for the crowd sourced reporting and triaging of infrastructure related issues", who aggregated the data from the Tanzanian Ministry of Water. I obtained this dataset from drivendata.org as part of their competition entitled "Pump it Up: Data Mining the Water Table". The provided data was pre-split into training and testing data. As this dataset is part of a competition, labels were provided for the training data only. The training dataset contains 59,400 rows and 40 columns, while the testing dataset contains 14,850 rows.

There are three unique dependent variables in this dataset, which means that this is a multiclass classification model. The three labels are: 'functional', 'funtional needs repair', and 'non functional'. These labels are encoded as 0, 1, and 2, respectively, in the model.

To gain a better understanding of the distribution of water pumps and their conditions across Tanzania, we plotted a random sample of 250 water pumps on a map of the country, using Folium. The green markers indicate functional water pumps, orange indicates functional needs repair, and red indicates non-functional.<br>
<br>
![Presentation slide 4](https://github.com/rjpost20/Tanzania-Water-Classification-Project/blob/main/data/presentation_images/Slide%204.jpeg)

<br>

## Exploratory Data Analysis and Data Cleaning

Initial exploratory data analysis revealed that the dataset contains 10 numerical features and 30 object (string) features. Many of these feautures turned out to be near-duplicates of each other, or in some cases, complete duplicates. Other features contained no value in terms of prediction or inference - all the values in `recorded_by` for example contained exactly the same value. Some values were categorical descriptors that we assumed contained no inherent predictive value, such as `id`. Ultimately, our final preprocessed dataframe contained 19 of the 40 original columns: four numerical and 15 object. The figure below shows the distributions of the four numerical variables; as can be seen in the graphs three of the distributions are highly zero-inflated.

There were also columns with missing values that contained missing values, we decided to one hot encode in the case of features with NaN values. In the case of the `construction_year` column, we discovered that nearly one-third of the data contained a value of "0", which we assumed to be a placeholder for "Unknown". Rather than one hot encoding this column, or imputing a blunt statistic such as the median or mean of the column, we decided to impute the results of a linear regression on the column where the values were 0.

![Data visualization](https://github.com/rjpost20/Tanzania-Water-Classification-Project/blob/main/visualizations/numerical_feature_distributions.png)

<br>

## Modeling and Results

Below is a chart with compiled training, cross-validation, and test accuracy scores for each model. Models are sorted by descending test accuracy scores, with training accuracy scores breaking the tie in the case of the logistic regression models.

![Presentation slide 5](https://github.com/rjpost20/Tanzania-Water-Classification-Project/blob/main/data/presentation_images/Slide%205.jpeg)<br>
<br>

Our best performing model was the XGBoost model, with a test accuracy score of 79.2%. Below are two visualizations of the performance of the XGBoost model: ROC curves for each of the class labels are plotted on the left and a confusion matrix is plotted on the right.

![Presentation slide 6](https://github.com/rjpost20/Tanzania-Water-Classification-Project/blob/main/data/presentation_images/Slide%206.jpeg)<br>
<br>

As part of the evaluation of the XGBoost model, we determined the top 10 most important features in the dataset in terms of predictive value. Those features are plotted along the x-axis below, with the proportion of class labels for the data associated with each respective feature plotted along the y-axis.

This data and the visualization were instrumental in giving us the inferential capacity to provide specific, tangible recommendations to the TMW in terms of suggested areas of investigation and analysis - the inferential value of the model.

![Data visualization](https://github.com/rjpost20/Tanzania-Water-Classification-Project/blob/main/visualizations/top_10_features_class_proportions_plot.png)

<br>

## Conclusions and Recommendations

We achieved an accuracy score (aka classification rate) of 79.2% on our final model, the XGBoost model. That means we classify just about four out of every five rows correctly. The model is not consistent across class labels however. The model was best at predicting "functional" pumps, next best with "non functional" pumps, and worst with "functional needs repair" pumps. This makes sense - this is the order of frequency of the class labels in the dataset. With only a bit over 7% of the data in the "functional needs repair" category, it's going to be difficult to predict those accurately. With that said, our best precision score was actually with non-functional water pumps, at 83.3%. That means that we have a false positive rate of 16.7% for non-functional pumps. Our best recall score was with functional water pumps, at an impressive 91.0%. That means that we have a false negative rate of 9.0% for these pumps.

The most important features for determining class labels in the XGBoost model were `waterpoint_type`, `quantity`, `region_code`, `lga`, and `extraction_type`. `waterpoint_type` and `quantity_seasonal` especially stood out from the rest of the feature in terms of importance. While some of these five feature are difficult to adjust (after all, you can't change the amount of water avaialable to a source, or the region itself), others are not. waterpoint_type and extraction_type in particular are features that are able to be adjusted for new water pumps. 

In terms of the predictive value of the model, we recommend the TMW use the model to coordinate workforce count and necessary resources ahead of time. By knowing in advance the approximate proportion of each class label for water pumps, the TMW can plan ahead how much resources and employees they will need to meet the demand.

We also recommend the TMW investigate the source of non-functionaly associated with `extraction_type`: other and `waterpoint_type`: other features. As we saw in the class label proportions chart, waterpoint_type: other and extraction_type: other are associated with abnormally high levels of non-functional water pumps. The water pumps with these particular values should be inspected further to see if conclusions can be made on the source of their high non-functionality rate.

The TMW should also consider investigating `installer`: RWE. This installer had a nearly 65% proportion of non-functional water pumps among the pumps that they installed. This is over 70% higher than the average rate of non-functional water pumps among the entire training dataset. This installer should be investigated to determine why their pumps are performing so poorly, and the TMW should consider discontinuing the use of their services depending on the results of the investigation. 

Lastly, we recommend that the TMW examine `region_code`: 11 to determine the source of their success. The proportion of functional water pumps in region_code: 11 is nearly 80% - almost 50% higher than the rate of functional water pumps among the entire training dataset. The features of the pumps in this area should be examined to see if any lessons can be learned from their success.
