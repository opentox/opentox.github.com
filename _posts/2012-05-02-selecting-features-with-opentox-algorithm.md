---
layout: post
title: "Selecting Features with opentox algorithm"
description: ""
category: 
tags: []
---
{% include JB/setup %}

**Opentox-ruby has selection routines to filter out useful features for predictive models.**

Features are filtered using non-parametric resampling (bootstrapping) with 500 trees in a random forest. In a preliminary step, the usefulness of every feature is assessed, then subsets of most useful features are tested. Several subset sizes, ranging from 30% to 70% in steps of 2% are investigated. The whole process is embedded in an outer loop of bootstrapping which repeats the process 150 times and over which the results are averaged.


# Creating a Feature Dataset with Selected Features


In order to run feature selection on an existing dataset and obtain a dataset with fewer descriptors that are better suited for predicting the endpoint, pass the dataset containing the prediction feature, as well as the input feature dataset, to the feature selection service.


    
    
    curl -X POST \
    --data-urlencode "dataset_uri=..." \
    --data-urlencode "prediction_feature=..." \
    --data-urlencode "feature_dataset_uri=..." \
    "http://ot-test.in-silico.ch/algorithm/fs/rfe"
    



This procedure may be most useful together with physico-chemical descriptors, [which can be easily calculated in opentox-ruby](http://www.maunz.de/wordpress/opentox/2012/calculating-physico-chemical-descriptors-with-opentox-ruby).

By default, missing entries are imputed by a k-nearest-neighbor approach in the rfe algorithm, which happens behind the scenes. If your dataset, however, contains some compounds that miss most or all entries, while the others have very few and random missing values, then it may be more appropriate to simply delete the former. In rfe, this can be achieved by setting the parameter _del_missing_ to _true_, e.g.


    
    
    curl -X POST \
    --data-urlencode "dataset_uri=..." \
    --data-urlencode "prediction_feature=..." \
    --data-urlencode "feature_dataset_uri=..." \
    --data-urlencode "del_missing=true" \
    "http://ot-test.in-silico.ch/algorithm/fs/rfe"
    



In this case, no imputation is tried, and all compounds with missing values are removed.


# Notes





	
  * [Physico-chemical descriptor calculation in opentox-ruby](http://www.maunz.de/wordpress/opentox/2012/calculating-physico-chemical-descriptors-with-opentox-ruby)

	
  * From a higher perspective: A complete [tutorial](http://www.maunz.de/wordpress/opentox/2012/services-tutorial-lazar-feature-generation-feature-selection-validation) that streamlines the process.


