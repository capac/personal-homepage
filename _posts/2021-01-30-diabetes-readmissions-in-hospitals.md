---
layout: post
title: Determining the Causes of Diabetes Readmissions in Hospitals
featured: true
image: /assets/diabetes-readmissions-in-hospitals/analysis-2030265_1280.jpg
image-url: https://pixabay.com/photos/analysis-biochemistry-biologist-2030265/
image-source: Pixabay
image-author: Konstantin Kolosov
tags: ['Machine Learning', 'predictions', 'scikit-learn', 'Categorical Features']
comments: true
---

Diabetes is quickly becoming one of the major causes of mortality in the developing world, due to changing lifestyles and massive urbanization of the population, and is currently affecting [over 10% of the US population alone according to the CDC](https://www.cdc.gov/diabetes/data/statistics-report/index.html). Millions of deaths could be prevented each year by use of better analytics, such as non-invasive screening, tailor-made solutions and hospital readmissions.

Unplanned readmissions are the most useful key metric when evaluating the quality of care of a hospital, as it highlights the practitioners' diagnosis or treatment error. Unplanned readmissions are those that occur within 30 days of discharge from the last hospital visit, and are more closely correlated to health care administration. Consequently, decreasing unplanned readmissions are a direct measure of the improvement of patients' health as well as being of great financial relief to health care centers. Therefore, the primary focus of this data analysis is to generate a predictive model that can forecast the agents that may be responsible for unplanned hospital readmissions.

# Data summary

The data set employed in this analysis is the [Diabetes 130-US hospitals for years 1999-2008 data set](https://archive.ics.uci.edu/ml/datasets/Diabetes+130-US+hospitals+for+years+1999-2008) from the UCI Machine Learning Repository web site, which represents 10 years of clinical care at 130 US hospitals and integrated delivery networks. It includes over 50 features representing patient and hospital outcomes. The data contain such attributes as patient number, race, gender, age, admission type, time in hospital, medical specialty of the admitting physician, number of lab test performed, HbA1c test results, diagnosis, number of medications, diabetic medications, number of outpatient, inpatient, and emergency visits in the year before the hospitalization.

# Exploratory data analysis

To get a better feel for the data set, a few exploratory data analysis plots are displayed below. The first three plots highlight the percentage of null values in the data set, the feature composition for the medical specialty of the admitting primary physician and the percent breakdown for payer codes. As a consequence of the high prevalence of null values in the `weight`, `medical_specialty` and `payer_code` features, these features are dropped from further analysis.

<!-- ![Percentage of null values in data set](/assets/diabetes-readmissions-in-hospitals/null_values_payer_codes_speciality.png) -->

<div class="click-zoom">
  <label>
    <input type="checkbox">
    <img src="/assets/diabetes-readmissions-in-hospitals/null_values_payer_codes_speciality.png">
  </label>
</div>

As shown below, the analysis exposes a higher percentage predominance of elder patients above the age of 40. Moreover, the middle plot shows the majority of patients of Caucasian origins, while the last plot displays a slightly greater presence of female patients than male patients.

<!-- ![Percentage presence for age, race and gender](/assets/diabetes-readmissions-in-hospitals/age_race_gender.png) -->

<div class="click-zoom">
  <label>
    <input type="checkbox">
    <img src="/assets/diabetes-readmissions-in-hospitals/age_race_gender.png">
  </label>
</div>

[The primary research article used for this analysis](https://www.hindawi.com/journals/bmri/2014/781670/) suggests that the probability of readmission is contingent on the `HbA1c` measurement in the primary diagnosis, so both the `HbA1c` measurement and the primary diagnosis features are retained in the data analysis, even though the`HbA1c` measurement was only performed in less than 19% of the inpatient cases. 

<!-- ![Percentage presence for A1Cresult, primary patient diagnosis and cases for readmission](/assets/diabetes-readmissions-in-hospitals/a1c_diagnosis_readmitted.png) -->

<div class="click-zoom">
  <label>
    <input type="checkbox">
    <img src="/assets/diabetes-readmissions-in-hospitals/a1c_diagnosis_readmitted.png">
  </label>
</div>

## Correlation plot

A correlation plot with the numerical features and the readmission cases is plotted in the figure below. Without much surprise, one can notice that `num_medications` is moderately correlated with `time_in_hospital` and `num_of_procedures`. However, no substantive correlation with readmitted cases can be evinced from the plot.

![Correlation plot of numeric feature with readmission](/assets/diabetes-readmissions-in-hospitals/corr_plot.png)

# Data preparation and feature engineering

A summary of the data preparation and feature engineering that are performed in the analysis are compiled in the bullet list below:

 - All of the `object` values in the data frame are converted to `category` values.
 - All null values from the `race` category and the `Unknown/Invalid` subcategory are removed from the `Gender` category.
 - As already mentioned, `weight`, `medical_specialty` and `payer_code` columns are removed due to the large presence of null values.
 - `encounter_id` column is removed since it isn't relevant to the analysis.
 - Null, not admitted and not mapped values are removed from `admission_type_id`.
 - All variations of `Expired at...` or `Expired` in `discharge_disposition_id` are removed since they won't be responsible for further readmission cases. Null, not admitted and unknown/invalid values in `discharge_disposition_id` are removed as well.
 - Null, not available, not mapped and unknown/invalid values are removed from `admission_source_id`.
 - Following the analysis conditions laid out in [the primary research article for this work](https://www.hindawi.com/journals/bmri/2014/781670/), duplicate patient data are removed to maintain the statistical independence of the data as required by logistic regression, after which the `patient_nbr` column is dropped.
 - `citoglipton` and `examide` are removed since they don't offer any discriminatory information.
 - `glimepiride-pioglitazone` and `metformin-rosiglitazone` are removed as well due to the lack of discriminatory information. 
 - `number_outpatient`, `number_emergency` and `number_inpatient` are summed into one column called `service_use` and then removed.
 - The primary `diag_1` values are encoded into nine major groups: `circulatory`, `respiratory`, `digestive`, `diabetes`, `injury`, `musculoskeletal`, `genitourinary`, `neoplasms` and `others`.
 - The secondary `diag_2` and additional `diag_3` are removed to simplify the data analysis.
 - `readmitted` column is divided into two `0` and `1` categories, where the `0` category contains the `Not readmitted` and the `> 30 days` cases, and the `1` category consists of the `< 30 days` cases.
 - Categorical variables are encoded for all columns except for the six numerical columns: `time_in_hospital`, `num_lab_procedures`, `num_procedures`, `num_medications`, `number_diagnoses` and `service_use`.

## Dealing with unbalanced data

The data set is highly unbalanced for what concerns readmission to non-readmission and >30 days cases, due to the very small amount cases for hospital readmissions (just above 11% of cases). To make up for this lack of readmission cases, the minority data set has been oversampled with replacement and added to the rest of the data set. This is accomplished with the [imbalanced-learn](https://imbalanced-learn.org/stable/) package which is part of the [scikit-learn-contrib](https://github.com/scikit-learn-contrib) project. More about imbalanced-learn can be found at [scikit-learn-contrib/imbalanced-learn](https://github.com/scikit-learn-contrib/imbalanced-learn).

Due to the widespread presence of categorical features in the data set, the [**imblearn.over_sampling.RandomOverSampler**](https://imbalanced-learn.org/stable/references/generated/imblearn.over_sampling.RandomOverSampler.html) class has been employed, since it is the only class in _imblearn.over_sampling_ that can deal with categories. Moreover, the numeric features have been standardized, by subtracting the mean and dividing by the standard deviation, using Scikit-Learn's [**StandardScaler**](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html) class.

Let now proceed to the modeling of our data!

# Data modeling

For the sake of interpretation, the data modeling makes use of three, simple classification algorithms, all of which are available in Scikit-Learn: [**LogisticRegression**](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html), [**DecisionTreeClassifier**](https://scikit-learn.org/stable/modules/generated/sklearn.tree.DecisionTreeClassifier.html) and [**RandomForestClassifier**](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html). For each of these algorithms in the table below, the analysis calculates the accuracy, precision, recall, F-score and cross-validated average [Brier score](https://en.wikipedia.org/wiki/Brier_score) for readmitted cases.

<style>
td, th {
          font-size: 1.3em;
       }
</style>
|--------------------------|--------------|---------------|------------|--------------|----------------------|
|                          |   Accuracy   |   Precision   |   Recall   |    F-score   |  Average Brier score |
|:------------------------:|:------------:|:-------------:|:----------:|:------------:|:--------------------:|
|    Logistic regression   |    0.5838    |     0.5914    |   0.5223   |    0.5547    |   0.2430 +/- 0.0009  |
| Decision tree classifier |    0.6973    |     0.6685    |   0.7738   |    0.7173    |   0.2100 +/- 0.0022  |
| Random forest classifier |    0.9965    |     0.9935    |   0.9995   |    0.9965    |   0.0035 +/- 0.0010  |
|--------------------------|--------------|---------------|------------|--------------|----------------------|

From a first view one can see that the random forest classifier easily comes ahead of all the other algorithms. The high [F-score](https://en.wikipedia.org/wiki/F-score) tell us how well the random forest classifier performs on the data set, as a high F-score reflects both a high recall and precision. 

## Confusion matrix heat map plots

Following the initial analysis shown in the table, the heat map plot of the confusion matrices is generated to give a visual representation of the performance of the models. The values in the plot are the number of the predictions in each category divided by the sum of the values along the rows. The values shown correspond exactly in the upper left-hand corner to the precision for the non-readmitted cases and in the lower right-hand corner to the precision for the readmitted cases.

The decision tree model performs better than the logistic regression model, although there are still quite a few outliers on the transverse diagonal as compared to the main one. However, the random forest classifier confusion matrix accomplishes the best selection between all cases of true positives, true negatives, false positives and false negatives, as shown in most right-hand side plot below.

<!-- ![Logistic regression, decision tree classifier and random forest classifier confusion matrix plots](/assets/diabetes-readmissions-in-hospitals/confusion_matrix_plots.png) -->

<div class="click-zoom">
  <label>
    <input type="checkbox">
    <img src="/assets/diabetes-readmissions-in-hospitals/confusion_matrix_plots.png">
  </label>
</div>


## ROC curves and AUC

The [_receiver operating characteristic_ curve](https://en.wikipedia.org/wiki/Receiver_operating_characteristic), or ROC curve, are also capable of showing the greater performance of the random forest classifier compared to the other two algorithms. The ROC curve displays the true positive and false positive rates against a series of thresholds that produce these rates, and the best curve is the one that produces the highest true positive rate against the smallest false positive rate. These plots also contain the _area under the curve_ (AUC) calculations in the bottom right corner. The bigger this value is the more snug the ROC curve will be along the left and top axes of the plot. In this regard against the AUC value, the random forest classifier achieves the best performance among the three algorithms used.

<!-- ![Receiver operating characteristic curve for logistic regression, decision tree classifier and random forest classifier](/assets/diabetes-readmissions-in-hospitals/auc_plots.png)
 -->

<div class="click-zoom">
  <label>
    <input type="checkbox">
    <img src="/assets/diabetes-readmissions-in-hospitals/auc_plots.png">
  </label>
</div>

## Learning curves

To clarify for cases of possible model overfitting, the learning curves with one standard deviation error bands are calculated against all three models. As can be seen in the plots below, while a case can be made for overfitting in the logistic regression and decision tree models, there doesn't seem to be any case of overfitting with the random forest classifier model.

<!-- ![Learning curves](/assets/diabetes-readmissions-in-hospitals/learning_curves_plot.png) -->

<div class="click-zoom">
  <label>
    <input type="checkbox">
    <img src="/assets/diabetes-readmissions-in-hospitals/learning_curves_plot.png">
  </label>
</div>

## Feature importances

Finally, the normalized feature importance plot is shown below, which highlights the features that appear to be most influential for readmission. The `num_lab_procedures`, `num_medications` and to a lesser extent the `time_in_hospital` features are the ones that appear to be more helpful in determining readmission cases, which does after all make some sense. As mentioned in [the main reference article](https://www.hindawi.com/journals/bmri/2014/781670/) for this analysis, `primary_diag` also bears some relationship with the possibility of readmission, even though it isn't as strong as the formers. The plots shows one standard deviation errors bars, which help even more to signal out those features that are related to readmission.

![Feature importances](/assets/diabetes-readmissions-in-hospitals/feature_importances.png)

## Grid search

A brief attempt was undertaken to fine tune the hyperparameters of the algorithm using Scikit-Learn's [**GridSearchCV**](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GridSearchCV.html#sklearn.model_selection.GridSearchCV) class, but it didn't change the final outcome in any significant manner. The hyperparameters chosen (`n_estimators` and `max_depth`) maximize the parameter values at the chosen seed, but there appears to be little room for improvement by changing these values and the results for the random forest classifier model impress enough to not warrant further analysis.

# Conclusions

The random forest classifier shines above the other two models. The model uses bagging, or sampling with replacement, as the default. The final prediction from this classifier is a hard voting on the individual predictors. During training each individual decision tree learner of the random forest is trained on a random subset of the data, given by the square root of the number of features.

The better performance of the random forest classifier is likely due to the advantage of the ensemble technique itself which is inherent in the random forest algorithm. By random selection of the features, no individual feature is predominant over the others, and averaging the outcome of the decision trees by hard voting allows for a noticeable decrease in spurious noise effects. The model performs very well on the validation data as well, which demonstrates the lack of the model overfitting the training data. It is also possible that the random forest algorithm benefits the most from the duplication with replacement of data performed by the _imblearn.over_sampling.RandomOverSampler_ class, compared to the other two models.

The challenge in this analysis was to confront the large number of categorical features and the overwhelming ratio of not-readmitted to readmitted cases in the data, and the complete data analysis procedure seems to have satisfactorily tackled the problem. If you have any comments or suggestions, please feel free to make remarks in the section below. You are more than welcome to take a look at the [code in my GitHub repository](https://github.com/capac/diabetes-readmission-in-US-hospitals). For the analysis, the following software packages were used: [scikit-learn](https://scikit-learn.org/stable/index.html) (version 0.24.1), [pandas](https://pandas.pydata.org/) (version 1.2.1), [matplotlib](https://matplotlib.org/) (version 3.3.3), [seaborn](http://seaborn.pydata.org/) (version 0.11.1) and [imbalanced-learn](https://imbalanced-learn.org/stable/) (version 0.7.0).
