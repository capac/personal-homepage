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

{% include image.html
    src="/assets/diabetes-readmissions-in-hospitals/null_values.png"
    alt="Null values in data set"
    caption="Percentage of null values per feature in decreasing order. The first three features by null values are removed from further analysis."
%}

As suggested [in the primary research article used for this analysis](https://www.hindawi.com/journals/bmri/2014/781670/), the link between the probability of readmission and the `HbA1c` measurement is contingent on the primary diagnosis, so both of these features are retained in the data analysis.

{% include image.html
    src="/assets/diabetes-readmissions-in-hospitals/diagnosis.png"
    alt="Percentage of patients per condition for primary diagnosis"
    caption=""
%}

The data also suggest that the better attention to diabetes as obtained in the `HbA1c` result may improve patient outcomes and lower the cost of inpatient care, so this become an important feature in the analysis.

{% include image.html
    src="/assets/diabetes-readmissions-in-hospitals/a1c_result.png"
    alt="Percentage of HbA1c measurements per category"
    caption=""
%}

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
 - Categorical variables are encoded for all columns except for the five numerical columns: `time_in_hospital`, `num_lab_procedures`, `num_medications`, `number_diagnoses` and `service_use`.


<!-- # Figure or image without caption
![Plot](/assets/...)

# Figure or image with caption
{% include image.html
    src="/assets/..."
    alt="Image title"
    caption="Image caption"
%} -->
