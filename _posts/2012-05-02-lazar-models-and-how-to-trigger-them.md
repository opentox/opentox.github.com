---
layout: post
title: "Lazar Models and how to trigger them"
description: "I have implemented several underlying statistical learners within Lazar. There are kernel models for classification and regression. There are also facilities for physico-chemical descriptor calculation."
category: opentox-algorithm
tags: [Lazar]
---
{% include JB/setup %}

**I have implemented several underlying statistical learners within Lazar. There are kernel models for classification and regression. There are also facilities for physico-chemical descriptor calculation.**

There exist two flavors of Lazar models:



	
  1. based on subgraphs

	
  2. based on physico-chemical descriptors


Type 1. is the default, with the least requirements. It will work with only a dataset uri supplied. Type 2. requires the user to supply a dataset with pre-computed physico-chemical descriptor values (feature dataset).

The next paragraph discusses how to create feature datasets. After that the whole procedure is summarized.


# Creating a Feature Dataset


Lazar models can be equipped with a [feature dataset](http://www.maunz.de/wordpress/opentox/2012/calculating-physico-chemical-descriptors-with-opentox-ruby). This feature dataset must already exist at the time of Lazar model creation. Currently, only physico-chemical descriptors can be supplied as feature datasets. In the feature dataset, features must be annotated with the last substring of the DC.description field being


    
    
    [<pc_type>, <lib>]
    



e.g.


    
    
    Largest Chain [constitutional, cdk]
    



would be an appropriate DC.description entry. If no such annotation exists in the feature dataset, the user must supply parameters _pc_type_ and _lib_ to the lazar algorithm (see below). This ensures that the model can derive descriptor values for unknown query compounds on prediction time.

If no feature dataset is supplied, subgraph descriptors are used in the model.


# Creating a Model


When POSTing a dataset URI to the Lazar algorithm webservice (and optionally a feature dataset URI), a Lazar model is created. Models have parameters and parameters have values (e.g. _params[:foo]=bar_ obtained by setting _-d "foo=bar"_ via curl). The flowchart below shows the decision process for model building:

<img src="http://www.maunz.de/wordpress/wp-content/uploads/2011/05/Workflow_Algorithms6.png" width="600px">

Note: This chart displays only a subset of options. It is not necessary to pass any options apart from _dataset_uri_, which is the URI of the training dataset. The standard models are _weighted_majority_vote _for classification and _local_svm_regression_ for regression.

# Summary of models, see also [this post](http://www.maunz.de/wordpress/opentox/2012/dm-and-ml-in-lazar)

These models are available (textual form of the leaf nodes of the flowchart)

* Weighted Majority Vote for classification <br />
  (prediction_algorithm = _weighted_majority_vote_).
* SVM for classification  <br />
  (prediction_algorithm = local_svm_classification) <br />
  and regression <br />
  (prediction_algorithm = local_svm_regression).
<br />
<br />


# Parameter Summary


Mandatory parameters in bold. Default values are below the list.



	
* **dataset_uri**:

  * uri of the training dataset

* prediction_feature:

  * uri of the prediction feature in the training dataset.

* prediction_algorithm:

  * One of weighted_majority_vote, local_svm_classification, local_svm_regression.


The following are mutually exclusive:

* feature_generation_uri: Used for subgraphs

  * host/algorithm/fminer/bbrc, host/algorithm/fminer/last.

* feature_dataset_uri: Used for physico-chemical descriptors

  * uri of the physico-chemical descriptor feature dataset.

Further parameters:
	
* pc_type: The physico-chemical type(s) when using a feature_dataset_uri.

  * [See this post](http://www.maunz.de/wordpress/opentox/2012/calculating-physico-chemical-descriptors-with-opentox-ruby). Short summary: supply  a comma-separated list, <br />e.g. "pc_type=constitutional,electronic"

* lib: The library or libraries when using a feature_dataset_uri.

  * [See this post](http://www.maunz.de/wordpress/opentox/2012/calculating-physico-chemical-descriptors-with-opentox-ruby). Short summary: supply  a comma-separated list, e.g. "lib=cdk,openbabel"

* nr_hits: Whether subgraphs should be weighted with their occurrence counts in the instances (frequency).

  * One of true, false.

* min_sim: The minimum similarity threshold for neighbors.

  * Numeric value in \[0,1\].

* min_train_performance: The minimum training performance for local_svm_classification (Accuracy) and local_svm_regression (R-squared).

  * Numeric value in \[0,1\].

Here are the default values of some parameters:

* prediction_algorithm=

  * weighted_majority_vote (classification)

  * local_svm_regression (regression)

* feature_dataset_uri= not set

* pc_type=not set (autodetected from feature dataset, if applicable)

* lib=not set (autodetected from feature dataset, if applicable)

* nr_hits=

  * false (classification using weighted_majority_vote)

  * true (all others)

* min_sim=

  * "0.3" (nominal features, no feature dataset used)

  * "0.4" (physico-chemical descriptors, feature dataset used)

* min_train_performance=0.1

# References





	
  * [A post](http://www.maunz.de/wordpress/opentox/2012/dm-and-ml-in-lazar) details data mining and machine learning components involved.

	
  * The [README](https://github.com/opentox/algorithm/tree/development) details all settings.

	
  * From a higher perspective: A complete [tutorial](http://www.maunz.de/wordpress/opentox/2012/services-tutorial-lazar-feature-generation-feature-selection-validation) streamlines the process.


