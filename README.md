<h1>Flint Water Crisis: Analytical Model for Lead Pipe Identification

## Executive Summary
The Flint Water Crisis of 2014 is an infamous incident that has endangered the lives of Flint residents and necessitated the tracking and elimination of lead-based water distribution systems. The city of Flint has launched an effort to identify and replace lead service lines, a task that has proven taxing given the shortage in financial resources and proper documentation. My goal is to aid with the inspection process by developing an analytical model based on a sample of over 20,000 Flint parcels to target the ones most likely to contain lead pipes. The dataset will be partitioned into a training set and a validation set. The ideal model will be identified based on the area under curve (AUC) of the Receiver-Operator Characteristic (ROC) curve for the validation data.

For this study, I examined five different data mining techniques: Logistic Regression, Classification and Regression Trees, Neural Networks, Random Forests, and Gradient Boosting. My model of choice is an ensemble of three models: a simple neural network, a complex neural network, and a gradient boosting model. The chosen model was evaluated against each individual model. In comparison, the model exhibited the largest validation AUC with 96.40% and is, therefore, best suited for predicting parcels most likely to contain lead pipes.

<h2>Tools Used</h2>

- <b>Tableau</b> 
- <b>JMP</b>

## 1. Data Preparation
### The Dataset [Download Dataset](https://github.com/HassanBerro05/Flint-lead-pipe-analysis/blob/main/flint_data.csv)

My analysis was carried out using JMP Pro on a dataset containing information for a sample of over 20,000 Flint parcels. The set comprised 40 predictor variables encompassing different aspects of a parcel, and a single target variable, the value of which determined the likelihood of a sample parcel containing lead pipes. To the extent that my purpose was classifying a parcel as likely to contain lead pipes or not, my analysis was a classification problem.

The data was partitioned into a training set and a validation set using a 65%/35% split. The training portion was used to tune the different prediction models, while cross-validation was carried out on the validation set to determine the ideal model. Model performance was evaluated based on the Area under Curve (AUC) of the Receiver-Operator Characteristic (ROC) curve for validation.

## 2. Data Process
### i. Data Cleaning

As with most datasets of this size, the one at disposal was not bereft of flaws. The most prominent issue was missing values. Whether brought by due to lack of information, typing errata, or inadequate variable selection, the issue was tackled in one of three ways: (1) highlighting and categorizing the missing values, (2) excluding problematic variables, and (3) utilizing the Informative Missing feature in JMP Pro which allows for intelligent estimation of missing values. Other impediments included duplicates and highly correlated variables, both of which were addressed accordingly.

An important consideration that came up during the Data process related to a subset of variables in our dataset that pertain to location. Those included information such as parcel zip code, latitude and longitude, and precinct, to name a few. The usage of such variables as predictors would introduce a phenomenon dubbed “spatial bias”, whereby proximity to other parcels would influence the classification decision for a sample parcel. Not only would that undermine other potential predictors, but it would also impose spatial restrictions on predictive models that would limit their ability to predict beyond the values within the dataset. A decision was therefore made to alter the data partition in a way that prevents overlap in precinct between training and validation. Moreover, all spatial variables were excluded from use as predictors.

Beyond that, intuition and domain knowledge were used to introduce further modifications to the variables. In that regard, special note was made of the variable representing the age of a parcel. From the start, we had the expectation that it would be an important predictor in any model I build given what I historically know about the legality of lead usage in construction. Before putting our expectation to the test, I experimented with the representation of the variable. I decided to bin the year built in intervals that captured patterns within the data. The intervals are shown in the plot below along with the percentage of parcels containing lead in our sample for each interval. I used the percentage rather than the number of parcels to mitigate scale issues, as some years are much more abundant than others in our sample.


<img src="https://i.imgur.com/ANofYM9.png" height="50%" width="50%" alt="Binning Years Built"/>




Historical insight was the main contributor to the choice of intervals, which were made to isolate the two World Wars and highlight the year 1986, in which Congress amended the Safe Drinking Water Act, henceforth prohibiting the use of pipes containing lead in facilities providing water for human consumption. More intervals were added to avoid overfitting the data.

The plot reveals varying percentages for the different intervals, with an early spike dying out after 1939, i.e., the start of WW2. While historical interpretation is beyond the scope of my analysis, it could certainly be utilized to make light of the patterns depicted above.

### ii. Data Manipulation
- The continuous variable Land Improvements Value was replaced by a binary variable, LandImprovementsBinary, to indicate whether a sample parcel has had land improvements.
- Residential Building Value and Commercial Building Value were combined as a single variable named Building Value.
- The variable Zoning was recoded to eliminate typing errata and was subsequently replaced by the recoded variable Zoning 2.
- Housing condition variables for 2012 and 2014 were recoded as binary variables. The variable for 2013 was excluded from analysis because most of its values were missing.
- The variables Max_Lead and Num_Tests were replaced with the binary variables FoundLead and Tested?, respectively.
- The variables Prop Class and Old Prop Class provided redundant information, so the decision was made to exclude one of them, specifically Old Prop class.
- DRAFT Zone was excluded as it represented a symbolic facsimile of Future Landuse.
- The following variables were excluded from analysis: pid, Property Zip Code, Latitude, Longitude, Ward, PRECINCT, CENTRACT, CENBLOCK, SL_TYPE, SL_TYPE2, and Last_Test.

## 3. Variable Selection and Our First Models
With Data processing complete, we proceeded to the modeling phase, in which we examined five different predictive models: Logistic Regression, Classification and Regression Trees (CART), Random Forests, Gradient Boosting, and Neural Networks. A preliminary step in modeling is variable selection, i.e., the inputs to the different models. Out of the models mentioned above, Logistic Regression and CART models have the distinction of providing automated variable selection, an option
we utilized for two purposes: (1) reducing the number of variables into a subset that can be input into the more sophisticated models, thereby reducing complexity and providing more parsimonious models, and (2) understanding the most important predictors in our analysis. Further, the two models were assessed as standalone models and represented a benchmark against which the more complex models were measured. The models are discussed in the next 2 subsections.

### i. Logistic Regression

Variable selection with Logistic Regression was done through a Stepwise approach. The technique yields 13 variables that are then input into a Logistic Regression model. After some experimenting, we opted to eliminate one of the variables, specifically house condition in 2012, for a slight improvement in performance. This hearkens back to our discussion on issues within the dataset. There were two variables describing house condition in the set, one for 2012 and one for 2014. Not only was there no obvious distinction between the two, but the descriptions themselves were ambiguous at best. It came as no surprise to us, therefore, that the model seemed to perform better with those variables excluded.

The Effect Summary dialog for the logistic regression model is shown in figure below. The model includes the variables Year Built Binned, SL_Lead, Zoning 2, Hydrant Type, FoundLead?, Building Value, SL_private_inspection, Land Value, HomeSEV, Residential Building Style, Future Landuse, and Tested?. The variable Housing Condition 2012 2 was originally part of the model but was removed for better validation performance.

<img src="https://i.imgur.com/BZXieFb.png" height="50%" width="50%" alt="Effect Summary"/>

I utilized another useful feature of Logistic Regression models, which is assessing variable importance. This gave us a general idea of what variables among the ones in the model were most useful in explaining/predicting the target variable.

<img src="https://i.imgur.com/JNLQ3zw.png" height="50%" width="50%" alt="Variable Importance Logistic Regression"/>

<img src="https://i.imgur.com/HaPPhiB.png" height="50%" width="50%" alt="ROC of Logistic Regression"/>

The validation AUC for this model is 0.9568, or 95.68%

 There is a similar feature in CART models of which we also took advantage. The idea was to compare, and ultimately merge the results of the two variable selection techniques into a single defining set.

### ii. Classtification Tree (CART) Model

Variable selection in CART is more straightforward given that it is applied by default in the process of growing out the classification tree. We first present the validation ROC curve of this model in figure 3. The validation AUC for this model is 0.9558, or 95.58%.

<img src="https://i.imgur.com/QQaWrcu.png" height="50%" width="50%" alt="ROC CART"/>

There are 16 splits in the final tree with 8 variables included in at least 1 split. Conveniently, all 8 of the aforementioned variables were also recommended by stepwise regression, i.e., as a whole, they represented a subset of the variables included in our logistic regression model. For that reason, we adopted the entire set composed of 12
variables discussed in the previous section as an overarching variable set.

 <img src="https://i.imgur.com/tO8imAH.png" height="50%" width="50%" alt="CART simplified"/>

We used the Column Contributions feature for CART models in JMP Pro to assess variable importance. The results were compared to those obtained for Logistic Regression. The two models seemed to agree on the importance of four variables that we examine in the next section.

The Leaf Report and Column Contributions platforms for this model are shown in the two figures below. The final tree contains 16 splits and includes the variables Year Built Binned, SL_Lead, Zoning 2, Hydrant Type, SL_private_inspection, Land Value, HomeSEV, FoundLead?, Residential Building Style, Building Storeys, Future Landuse, and Tested?.

<p align="center">
Leaf Report: <br/>
 <img src="https://i.imgur.com/DbG8rzI.png" height="50%" width="50%" alt="Leaf Report"/>

 <p align="center">
Column Contribution: <br/>
 <img src="https://i.imgur.com/DQOffcO.png" height="50%" width="50%" alt="Column Contribution Table"/>

 ## 4. Significant variables

 ### Year Built 
 
 This variable was discussed in depth earlier. Its inclusion in this list vindicates our intuitive expectations regarding its importance.

 ### Servic Line Type

 This variable, dubbed SL_Lead, describes whether the service line of a parcel contains lead based on historical records that were retrieved from the basement of Flint City Hall. The records were hand-written and unannotated, and much work was done to digitize them. Figure 5 shows the percentage of lead-infested parcels by the variable SL_Lead.

 <img src="https://i.imgur.com/yETBTEX.png" height="50%" width="50%" alt="% of parcels with Lead Pipes"/>

 It is clear from the figure that the percentage of parcels with lead pipes increases dramatically when SL_Lead=1, i.e., for parcels thought to contain lead pipes according to the recovered records. This lends some credibility to the records and is a testament to the work of the UM students who handled the digitization process.
 
### Zoning Classiication

Zoning allows local governments to regulate which areas under their jurisdiction may have real estate or land used for particular purposes. Classifications are provided by the city of Flint, and are ascribed alphanumeric acronyms, each with a different interpretation. It is worthy to note that the variable for zoning contained multiple cases of typing errata. Figure 6 shows the number of parcels containing lead pipes by official zoning classification. Zones A-2 and B exhibit the most cases by far. The official Flint zoning classification defines the former as a “Single-Family Medium-Density District” and the latter as a “Two-Family District”. The implication that family homes are at the center of the crisis is a stark reminder of its severity and a call for bigger strides to be taken towards ending it once and for all.

<img src="https://i.imgur.com/j9yfLda.png" height="50%" width="50%" alt="Lead by Zones"/>


### Hydrant type

Figure 7 shows the number and percentage of parcels containing lead pipes by the closest hydrant type to a parcel. The inclusion of this variable is a little peculiar. However, the figure does reveal some interesting observations. For example, parcels with proximity to a fire hydrant of type T.C are the most prevalent in our sample, yet as a percentage, are the smallest. Contrast that to parcels with proximity to a hydrant of type Dar, which despite being less than half as abundant as T.C, are the most lead-infested and several questions arise regarding the fire hydrant system in Flint.

<img src="https://i.imgur.com/or1WDBe.png" height="50%" width="50%" alt="% of Lead by Hydrant Type"/>

 ## 5. More Complex Models

### Gradient Boosting

This model was run using the same 12-variables subset discussed above. No further modifications were made.

<img src="https://i.imgur.com/zxCELgg.png" height="50%" width="50%" alt="ROC Gradient Boosting"/>

### Random Forest

Similarly, this model was run using the 12-variable input subset. A random seed was set to suppress randomness and reproduce results.

<img src="https://i.imgur.com/Ci2r1NF.png" height="50%" width="50%" alt="ROC Random Forest"/>

### Neural Network

A neural network was initially run with the 12-variable subset discussed in logistic regression as input, with the exception of Future Landuse, which was removed to obtain better validation performance.. The number of hidden layers and number of nodes were tuned manually. The first neural network was run using one hidden layer and three nodes of hyperbolic tangent function, whereas the second was run using two hidden layers with 14 hyperbolic tangent nodes, 10 in the first and 3 in the second. The random seed was set to 12 to eliminate the randomness in the model brought by the initialization of the weights and biases of nodes. Validation ROC curves for the two best performing models are displayed below. 

<img src="https://i.imgur.com/b9Yinna.png" height="50%" width="50%" alt="ROC Neural 1"/>

<img src="https://i.imgur.com/st7vTk1.png" height="50%" width="50%" alt="ROC Neural 2"/>

### Ensembles

Before arriving at a final decision, we extended our analysis to ensembles, whereby we examined different combinations of the models at our disposal. The best performing model was an average of the gradient boosting model with the two neural network models. The validation ROC curve for this model is displayed in figure 11.

<img src="https://i.imgur.com/XdbMUlY.png" height="50%" width="50%" alt="ROC Ensembles"/>


 ## 6. Results

 Figure 12 shows validation AUC comparisons for all models described in this analysis. With an AUC of 96.40%, the ensemble model exhibits the best performance, and is therefore our model of choice for predicting whether a parcel is likely to contain lead pipes.

 <img src="https://i.imgur.com/usXQ8vM.png" height="50%" width="50%" alt="ROC Comparison"/>

 ## 7. Future Testing 

 The foregoing analyses apply to the dataset at our disposal, whereby cross-validation was used to assess predictive performance. With that said, the analyses can be extended to include external data. In fact, we have appended an additional test set to our existing dataset so that any modifications done to our variables would reflect onto those in the test set.
 




