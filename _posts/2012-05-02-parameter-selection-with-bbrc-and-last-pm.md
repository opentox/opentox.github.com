---
layout: post
title: "Parameter Selection with BBRC and LAST PM"
description: "This post gives some information on how parameters for BBRC and LAST could be selected, especially for the case of regression. Please see the [usage information](http://www.maunz.de/wordpress/opentox/2011/bbrc-and-last-usage) on how to apply the hints in your situation."
category: opentox-algorithm
tags: [fminer, BBRC, LAST-PM]
---

{% include JB/setup %}

**This post gives some information on how parameters for BBRC and LAST could be selected, especially for the case of regression. Please see the [usage information](http://www.maunz.de/wordpress/opentox/2011/bbrc-and-last-usage) on how to apply the hints in your situation.**


# Some Background Information


Graph mining applications fminer/bbrc (BBRC) and fminer/last (LAST-PM) are **complete miners** in the sense that they do not restrict the result set of subgraphs _a-priori_ to a specific amount of patterns. Restricting the output set would contradict the principle of data-driven pattern generation, where no human intervention should be applied to the data mining process.
Instead of hard cutoffs for the set size, the user is expected to bound the mining process with sensible constraints (this is where he is "allowed" to bring expert knowledge in). In the worst case however, he has to apply a _trial-and-error_ strategy for finding such constraints.

**BBRC** has been designed for (binary) class-correlated subgraph mining. In this domain, where each compound (graph) is assigned a true/false value, it can handle very large datasets. The algorithm is optimized for this setting and here the parameters have sensible default values.

**LAST-PM** has been designed for the same (binary) class-correlated subgraph mining setting as BBRC. Since the generated patterns describe hidden motifs in the graph database, the process takes more time to complete. LAST-PM has also sensible default values, which might need to be adapted now and then, however.


# The Regression Case


When dealing with numerical values as target variable ([referred to as _prediction-feature_](http://www.maunz.de/wordpress/opentox/2011/bbrc-and-last-usage)), some pruning techniques (_dynamic upper bound pruning_), which reduce runtime drastically for classification, are not yet applicable -  BBRC and LAST-PM disable it automatically for you. Moreover, the result set might be larger or smaller compared to classification.

Regression has not been experimentally validated yet and support is therefore experimental. Meanwhile, here are some hints that alleviate possible problems in this setting. They refer to each other, but try them also individually!


## Hint #1: Revert to ordinary subgraph mining


Try disabling BBRC mining by setting


    
    
    backbone=false
    



This will revert to "ordinary" correlated and frequent subgraph mining by disabling the super-sparse selection of BBRC. You will receive a much larger set of subgraphs (all correlated and frequent subgraphs).


## Hint #2: Use paths instead of trees


If Hint #1 resulted in a too large set of descriptors, you might try setting


    
    
    feature_type=paths
    



(default 'trees'). That way, only linear fragments are mined. I have found paths to be also very expressive in regression models.


## Hint #3: Increase minimum frequency


The default is that every mined fragment must occur in at least 5 per-mil and 8 percent of the molecules in your database for BBRC and LAST-PM, respectively. You can adjust those thresholds: increasing minimum frequency poses higher demands on the features, so fewer will be found. Additionally, this speeds up the mining process. The respective parameter is called


    
    
    min_frequency=x
    


