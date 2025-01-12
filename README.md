<h1>Flint Water Crisis: Analytical Model for Lead Pipe Identification

## Executive Summary
The Flint Water Crisis of 2014 is an infamous incident that has endangered the lives of Flint residents and necessitated the tracking and elimination of lead-based water distribution systems. The city of Flint has launched an effort to identify and replace lead service lines, a task that has proven taxing given the shortage in financial resources and proper documentation. My goal is to aid with the inspection process by developing an analytical model based on a sample of over 20,000 Flint parcels to target the ones most likely to contain lead pipes. The dataset will be partitioned into a training set and a validation set. The ideal model will be identified based on the area under curve (AUC) of the Receiver-Operator Characteristic (ROC) curve for the validation data.

For this study, I examined five different data mining techniques: Logistic Regression, Classification and Regression Trees, Neural Networks, Random Forests, and Gradient Boosting. My model of choice is an ensemble of three models: a simple neural network, a complex neural network, and a gradient boosting model. The chosen model was evaluated against each individual model. In comparison, the model exhibited the largest validation AUC with 96.40% and is, therefore, best suited for predicting parcels most likely to contain lead pipes.

<h2>Tools Used</h2>

- <b>Tableau</b> 
- <b>JMP</b>

## 1. The Dataset
My analysis was carried out using JMP Pro on a dataset containing information for a sample of over 20,000 Flint parcels. The set comprised 40 predictor variables encompassing different aspects of a parcel, and a single target variable, the value of which determined the likelihood of a sample parcel containing lead pipes. To the extent that our purpose was classifying a parcel as likely to contain lead pipes or not, our analysis was a classification problem.

The data was partitioned into a training set and a validation set using a 65%/35% split. The training portion was used to tune the different prediction models, while cross-validation was carried out on the validation set to determine the ideal model. Model performance was evaluated based on the Area under Curve (AUC) of the Receiver-Operator Characteristic (ROC) curve for validation.

## 2. Data Preparation
### i. Data Cleaning

As with most datasets of this size, the one at disposal was not bereft of flaws. The most prominent issue was missing values. Whether brought by due to lack of information, typing errata, or inadequate variable selection, the issue was tackled in one of three ways: (1) highlighting and categorizing the missing values, (2) excluding problematic variables, and (3) utilizing the Informative Missing feature in JMP Pro which allows for intelligent estimation of missing values. Other impediments included duplicates and highly correlated variables, both of which were addressed accordingly.

An important consideration that came up during the variable preparation process related to a subset of variables in our dataset that pertain to location. Those included information such as parcel zip code, latitude and longitude, and precinct, to name a few. The usage of such variables as predictors would introduce a phenomenon dubbed “spatial bias”, whereby proximity to other parcels would influence the classification decision for a sample parcel. Not only would that undermine other potential predictors, but it would also impose spatial restrictions on predictive models that would limit their ability to predict beyond the values within the dataset. A decision was therefore made to alter the data partition in a way that prevents overlap in precinct between training and validation. Moreover, all spatial variables were excluded from use as predictors.

Beyond that, intuition and domain knowledge were used to introduce further modifications to the variables. In that regard, special note was made of the variable representing the age of a parcel. From the start, we had the expectation that it would be an important predictor in any model we build given what we historically know about the legality (or lack thereof) of lead usage in construction. Before putting our expectation to the test, we experimented with the representation of the variable. A decision was made to bin the year built in intervals that captured patterns within the data. The intervals are shown in the plot below along with the percentage of parcels containing lead in our sample for each interval. We used the percentage rather than the number of parcels to mitigate scale issues, as some years are much more abundant than others in our sample.


![Figure in the liquor](https://i.imgur.com/5vtPw31.png)



Historical insight was the main contributor to the choice of intervals, which were made to isolate the two World Wars and highlight the year 1986, in which Congress amended the Safe Drinking Water Act, henceforth prohibiting the use of pipes containing lead in facilities providing water for human consumption. More intervals were added to avoid overfitting the data.

The plot reveals varying percentages for the different intervals, with an early spike dying out after 1939, i.e., the start of WW2. While historical interpretation is beyond the scope of our analysis, it could certainly be utilized to make light of the patterns depicted above.

### ii. Data Manipulation
- The continuous variable Land Improvements Value was replaced by a binary variable, LandImprovementsBinary, to indicate whether a sample parcel has had land improvements.
- Residential Building Value and Commercial Building Value were combined as a single variable named Building Value.
- The variable Zoning was recoded to eliminate typing errata and was subsequently replaced by the recoded variable Zoning 2.
- Housing condition variables for 2012 and 2014 were recoded as binary variables. The variable for 2013 was excluded from analysis because most of its values were missing.
- The variables Max_Lead and Num_Tests were replaced with the binary variables FoundLead and Tested?, respectively.
- The variables Prop Class and Old Prop Class provided redundant information, so the decision was made to exclude one of them, specifically Old Prop class.
- DRAFT Zone was excluded as it represented a symbolic facsimile of Future Landuse.
- The following variables were excluded from analysis: pid, Property Zip Code, Latitude, Longitude, Ward, PRECINCT, CENTRACT, CENBLOCK, SL_TYPE, SL_TYPE2, and Last_Test.

<h2>Objective</h2>

To create a data-driven model using a dataset of over 20,000 Flint parcels to:

- <b>Identify parcels likely to contain lead pipes.</b> 

- <b>Optimize the inspection process by targeting high-risk areas.<b>

- <b>Support the city’s efforts to eliminate lead-based service lines.<b>
<h2>Program walk-through:</h2>

<p align="center">
Launch the utility: <br/>
<img alt="Top 10 Requests" src="https://imgur.com/5vtPw31"/>
<br />
<br />
Select the disk:  <br/>
<img src="https://imgur.com/5vtPw31" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Enter the number of passes: <br/>
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Confirm your selection:  <br/>
<img src="https://i.imgur.com/cdFHBiU.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Wait for process to complete (may take some time):  <br/>
<img src="https://i.imgur.com/JL945Ga.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Sanitization complete:  <br/>
<img src="https://i.imgur.com/K71yaM2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Observe the wiped disk:  <br/>
<img src="https://i.imgur.com/AeZkvFQ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
