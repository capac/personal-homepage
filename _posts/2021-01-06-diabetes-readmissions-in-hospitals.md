---
layout: post
title: Determining the causes of diabetes readmissions in hospitals
featured: true
image: /assets/diabetes-readmissions-in-hospitals/analysis-2030265_1280.jpg
image-url: https://pixabay.com/photos/analysis-biochemistry-biologist-2030265/
image-source: Pixabay
image-author: Konstantin Kolosov
tags: ['Machine Learning', 'predictions', 'scikit-learn', 'Categorical Features']
comments: true
---

Diabetes is quickly becoming one of the major causes of mortality in the developing world, due to changing lifestyles and massive urbanization of the population, and currently affecting [over 10% of the US population alone according to the CDC](https://www.cdc.gov/diabetes/data/statistics-report/index.html). Millions of deaths could be prevented each year by use of better analytics, such as non-invasive screening, tailor-made solutions and hospital readmissions.

Unplanned readmissions are the most useful key metric when evaluating the quality of care of a hospital, as it highlights the practitioners' diagnosis or treatment error. Unplanned readmissions are those that occur with 30 days of discharge from the last hospital visit, and are more closely correlated to health care administration. Consequently, decreasing unplanned readmissions are a direct measure of the improvement of patients' health as well as being of great financial relief to health care centers. Therefore, the primary focus of this data anlaysis is to generate a predictive model that can forecast the agents that may be responsible for unplanned hospital readmissions.

# Data summary

The data set employed in this analysis is the [Diabetes 130-US hospitals for years 1999-2008 data set](https://archive.ics.uci.edu/ml/datasets/Diabetes+130-US+hospitals+for+years+1999-2008) from the UCI Machine Learning Repository web site, which represents 10 years of clinical care at 130 US hospitals and integrated delivery networks. It includes over 50 features representing patient and hospital outcomes. The data contain such attributes as patient number, race, gender, age, admission type, time in hospital, medical specialty of admitting physician, number of lab test performed, HbA1c test result, diagnosis, number of medications, diabetic medications, number of outpatient, inpatient, and emergency visits in the year before the hospitalization.

# Exploratory data analysis

To get a better feel for the data set, a few exploratory data analysis plots are displyed below. The first plot highlights the amount of null values in the data set. As a consequence of the high prevalence of null values in the `weight`, `medical_specialty` and `payer_code` features, these are dropped from further analysis.

![Null values in data set](/assets/diabetes-readmissions-in-hospitals/null_values.png)

As suggested [in the primary research article used for this analysis](https://www.hindawi.com/journals/bmri/2014/781670/), the link between the probability of readmission and the `HbA1c` measurement is contingent on the primary diagnosis, so both of these features are retained in the data analysis.

![Percentage of patients per condition for primary diagnosis](/assets/diabetes-readmissions-in-hospitals/diagnosis.png)

The data also suggest that the better attention to diabetes as obtained in the `HbA1c` result may improve patient outcomes and lower the cost of inpatient care, so this is potentially an important feature in the analysis even though the`HbA1c` measurement was only performed in 18.4 % of the inpatient cases. 

![Percentage of HbA1c measurements per category](/assets/diabetes-readmissions-in-hospitals/a1c_result.png)

A summary of the data preparation features that are performed in the analysis are compiled in the bullet form list below:

 - All of the `object` values in the data frame are converted to `category` values.
 - All `NaN` values from the `race` category and the `Unknown/Invalid` subcategory are removed from the `Gender` category.
 - `weight`, `medical_specialty` and `payer_code` columns are removed due to the large presence of `NaN` values.
 - `encounter_id` column is removed since it isn't relevant to the analysis.
 - All variations of `Expired at...` or `Expired` are removed since they won't be responsible for further readmission cases.
 - Following the [analysis conditions laid out in the research article](https://www.hindawi.com/journals/bmri/2014/781670/), duplicate patient data are removed to maintain the statistical independence of the data as required by logistic regression, after which the `patient_nbr` column is dropped.
 - `citoglipton` and `examide` are removed since they don't offer any discriminatory information.
 - `glimepiride-pioglitazone` and `metformin-rosiglitazone` are removed as well due to the lack of discriminatory information. 
 - `number_outpatient`, `number_emergency` and `number_inpatient` are summed into one column called `service_use` and then removed.
 - The primary `diag_1` values are encoded into nine major groups: `circulatory`, `respiratory`, `digestive`, `diabetes`, `injury`, `musculoskeletal`, `genitourinary`, `neoplasms` and `others`.
 - The secondary `diag_2` and additional `diag_3` are removed to simplify the data analysis.
 - `readmitted` column is divided into two `0` and `1` categories, where the `0` category contains the `Not readmitted` and the `> 30 days` cases, and the `1` category consists of the `< 30 days` cases.
 - Categorical variables are encoded for all columns except for the six numerical columns: `time_in_hospital`, `num_lab_procedures`, `num_procedures`, `num_medications`, `number_diagnoses` and `service_use`.

The data set is highly unbalanced due to the very small amount cases of hospital readmissions. To make up for this lack of readmission cases, the minority data set has been oversampled with replacement and added to the rest of the data set. This is accomplished with the [imbalanced-learn](https://imbalanced-learn.org/stable/) package which is part of the [scikit-learn-contrib](https://github.com/scikit-learn-contrib) project. More about imbalanced-learn can be found at [scikit-learn-contrib/imbalanced-learn](https://github.com/scikit-learn-contrib/imbalanced-learn). Due to the prevalence of categorical features, the [**imblearn.over_sampling.RandomOverSampler**](https://imbalanced-learn.org/stable/generated/imblearn.over_sampling.RandomOverSampler.html) class has been employed, since it is the only class that can deal with categories. Moreover, the numeric features have been standardized, by subtracting the mean and dividing by the standard deviation, using Scikit-Learn's [**StandardScaler**](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html) class.

A correlation plot with the numerical features and the readmission cases is plotted in the figure below. Without much surprise, one can notice that `num_medications` is moderately correlated with `time_in_hospital` and `num_of_procedures`.

![Correlation plot of numeric feature with readmission](/assets/diabetes-readmissions-in-hospitals/corr_plot.png)

Let now proceed to the modeling of our data!

# Data modeling

The data modeling make use of three, basic classification algorithms which are all available in Scikit-Learn: [**LogisticRegression**](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html?highlight=logisticregression#sklearn.linear_model.LogisticRegression), [**DecisionTreeClassifier**](https://scikit-learn.org/stable/modules/generated/sklearn.tree.DecisionTreeClassifier.html?highlight=decisiontreeclassifier#sklearn.tree.DecisionTreeClassifier) and [**RandomForestClassifier**](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html?highlight=randomforest#sklearn.ensemble.RandomForestClassifier). The analysis calculates the accuracy, precision, recall, F1 score and Brier score on the data.

<style>
td, th {
          font-size: 140%
       }
</style>
|--------------------------|--------------|---------------|------------|--------------|----------------------|
|                          |   Accuracy   |   Precision   |   Recall   |   F1 score   |  Average Brier score |
|:------------------------:|:------------:|:-------------:|:----------:|:------------:|:--------------------:|
|    Logistic Regression   |    0.5838    |     0.5914    |   0.5223   |    0.5547    |   0.2430 +/- 0.0009  |
| Decision Tree Classifier |    0.6973    |     0.6685    |   0.7738   |    0.7173    |   0.2100 +/- 0.0022  |
| Random Forest Classifier |    0.9959    |     0.9923    |   0.9995   |    0.9959    |   0.0041 +/- 0.0009  |
|--------------------------|--------------|---------------|------------|--------------|----------------------|


<!-- # Figure or image without caption
![Plot](/assets/...)

# Figure or image with caption
{% include image.html
    src="/assets/..."
    alt="Image title"
    caption="Image caption"
%} -->
