---
layout: post
title: Predicting Earthquake Damage with Ensemble Learners
featured: True
draft: True
image: /assets/predicting-earthquake-damage-with-ensemble-learners/sebastian-pena-lambarri-7ky9_t_mpRI-unsplash.jpg
image-url: https://unsplash.com/photos/7ky9_t_mpRI
image-source: Unsplash
image-author: Sebastian Pena Lambarri
tags: ['Machine Learning', 'Ensemble Learners', 'Categorical Features', 'scikit-learn']
---

One of the main appeals of machine learning is that one can immediately start making fairly good data predictions without having estensive domain knowledge on the subject at matter, which at times can produce unexpected and surprising insights. This happens to be the case concerning the [machine learning data set available at _DrivenData_](https://www.drivendata.org/competitions/57/nepal-earthquake/) on the [Gorkha earthquake in Nepal](https://en.wikipedia.org/wiki/April_2015_Nepal_earthquake), which on April 25, 2015 caused thousands of deaths and extreme hardship for all of the survivors, as well as massive damage to the country's public buildings, infrastructure and private households.

<!-- In the earthquake fallout the Nepalese government carried out an extensive survey of households to estimate building damage, primarily to establish beneficiaries eligible for government assistance for housing reconstruction. The data set contains detailed information about the buildings' structure, legal ownership, terrain conditions and other useful socio-economic information. -->

The main goal of the _DrivenData_ machine learning project is to understand which distinct conditions and characteristics were to blame for the buildings that sustained the most damage from the earthquake. The level of damage is recorded by three ordinal variables, _low_, _medium_ and _high_, the last one of which corresponds to near complete destruction. The measure of performance of the algorithms on the data set is given by the _micro averaged F1 score_, which is a variant of the [_F1 score_](https://en.wikipedia.org/wiki/F1_score). <!-- The [F1 score](https://en.wikipedia.org/wiki/F1_score) is the harmonic mean of the precision and recall of a classifier. Conventionally it is used to evaluate performance on a binary classifier, but due to the three ordinal variables the _micro averaged F1 score_ is employed here. -->

First of all, it's necessary to perform some exploratory data analysis of the features, the majority of which are categorical. The complete list of features present in the data set can be view on [the specific _DrivenData_ website](https://www.drivendata.org/competitions/57/nepal-earthquake/page/136/#features_list). The following plot shows the damage sustained by the buildings from the earthquake according to the three damage levels. To take into account the different ratio of damage levels in the training labels of the data, the class imbalance is treated by using Scikit-Learn's [**StratifiedShuffleSplit**](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.StratifiedShuffleSplit.html) class which samples the training data in the same ratio as present in the training labels.

![Building damage by level](/assets/predicting-earthquake-damage-with-ensemble-learners/damage-level-by-grade.png)

As mentioned, most of the variables are categorical. Several of these are shown in the bar plot below. There appear to be a number of labels in each category that stand out from the others, such as the `r` label in the `foundation_type` plot or the `s` in `position`.

![Category plot](/assets/predicting-earthquake-damage-with-ensemble-learners/categorical-bar-plot.png)

To confront the significant amount of categorical features in the data set, the [category_encoders (2.2)](http://contrib.scikit-learn.org/category_encoders/) package from the [scikit-contrib](https://github.com/scikit-learn-contrib) repository is employed here. With this package a noticeable accuracy improvement with the [**TargetEncoder**](http://contrib.scikit-learn.org/category_encoders/targetencoder.html) class is found. As explained in the documentation, _"for the case of categorical targets, [the] features are replaced with a blend of [the] posterior probability of the target given particular categorical value and the prior probability of the target over all the training data"_. This particular feature engineering allows for greater separation of the categories during model training, and consequent improvement in model prediction power.

For the five numerical variables a correlation plot has been generated to investigate any dependencies among variables. Beside an unsurprising relationship between number of floors in the building before the earthquake, `count_floor_pre_eq`, and the normalized height of the building area, `height_percentage`, there are no other obvious correlations that jump to the eye.

![Correlation plot](/assets/predicting-earthquake-damage-with-ensemble-learners/correlation.png)

Even when a breakdown of the numerical variables according to damage level is performed, besides the obvious relationship between `height_percentage` (height normalized to building surface area) and `count_floors_pre_eq` (number of floors in the building prior to earthquake), no other prominent relationships can be gleaned from the scatter plot, as shown below.

![Pair plot](/assets/predicting-earthquake-damage-with-ensemble-learners/pairplot-with-reg.png)

## Enter Ensemble Learning

Ensemble learning is a machine learning technique that has been very successful in many machine learning competitions, and when employed properly the increase in prediction accuracy can be quite remarkable. The principle idea is to build a prediction model from the combinations of different _weak learners_. A weak learner is lower-performing learner that performs bearly better than random. The greatest benefit of ensemble learning comes from using learners that are as diverse as possible from one another, which in turn will assure us that their errors are also very different as well.

Scikit-Learn offers the [**VotingClassifier**](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.VotingClassifier.html) class which allow the combination of weak classifiers into an ensemble model with greater model prediction than that given by the individual learners. The model training was performed over the entire data set, made up of 260,601 records and 38 features.

The learners that were employed in the analysis were actually fairly high in prediction power themselves, and were the  [**RandomForestClassifier**](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html), [**CatBoostClassifier**](https://catboost.ai/docs/concepts/python-reference_catboostclassifier.html), [**XGBClassifier**](https://xgboost.readthedocs.io/en/stable/python/python_api.html#xgboost.XGBClassifier) and [**SGDClassifier**](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.SGDClassifier.html) classifiers. The first three classes are ensemble learners in their own right. For the VotingClassifier class the `voting=soft` option was employed, which uses the class with the highest probability as the prediction. The following plot ilustrates the confusion matrix heat map for the actual and predicted damage levels for the buildings of our model. The greater part of the buildings are properly predicted along the main diagonal, with a lower amount of building misclassfied off the main diagonal.

![Confusion matrix heat map](/assets/predicting-earthquake-damage-with-ensemble-learners/confusion-matrix-heatmap.png)

As of the date of posting, for the specific _DrivenData_ competition this technique was able to achieve a micro averaged F1 score of 0.7498, which places it in the 98th percentile of the leaderboard ranking. You can review the Python code on my GitHub repository at [capac/predicting-earthquake-damage](https://github.com/capac/predicting-earthquake-damage). Most of the code makes use of latest version of [Scikit-Learn (0.23.2)](https://scikit-learn.org/stable/), but also of the [CatBoost (0.24.2)](https://catboost.ai) and [XGBoost (1.20)](https://xgboost.ai) gradient boosting packages.