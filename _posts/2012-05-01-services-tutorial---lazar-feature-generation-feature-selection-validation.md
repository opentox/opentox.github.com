---
layout: post
title: "Lazar Feature Generation, Feature Selection, Validation"
description: "This article illustrates feature generation, feature selection, and validation using opentox-ruby and the REST paradigm. As such, it may be useful as a high-level documentation."
category: "opentox-algorithm"
tags: [feature generation, feature selection, validation]
---
{% include JB/setup %}

**This article illustrates feature generation, feature selection, and validation using opentox-ruby and the REST paradigm. As such, it may be useful as a high-level documentation.**

REST (REpresentational State Transfer) is the programming paradigm that HTTP, the WWWs 'engine', is based upon. This lean technological basis has also been employed to run much more generalized, sophisticated, distributed, computational services such as [OpenTox](http://www.opentox.org). The set of OpenTox compliant services developed by [IST](http://www.in-silico.ch) in the Ruby programming language is called _opentox-ruby_.

Lazar is IST's QSAR modelling framework. It implements published, state-of-the-art machine learning and data mining techniques. Assuming a linux shell and the curl command line client (and optionally a web browser), the following shows how to

* Upload a dataset of chemicals with endpoint information to Lazar, calculate physico-chemical descriptors, and run feature selection to obtain features suitable for prediction of the endpoint
* Create Lazar Models and run single predictions on this model for both physico-chemical descriptors and subgraphs
* Validate the Lazar algorithm using both physico-chemical descriptors and subgraphs.

These steps exemplify only a subset of available options. The links at the end of each section provide complete documentation.

Setup step: paste a little function into your shell that helps extracting results from OpenTox tasks:


    
    
    function get_result {
     task=$1
     while [ 1 ]; do
     result=`curl -H "accept:text/uri-list" $task 2>/dev/null`
     [ $result == $task ] && sleep 1 || break
     done
     echo $result
    }
    



[Download the dataset](http://www.maunz.de/opentox/hamster_carcinogenicity.csv), and open a shell in the directory containing the downloaded file.

Now let's start!


# Upload and Feature Generation/Filtering


Uploading a dataset consists of issuing a POST command containing form field data to the dataset service.


    
    
    # Upload dataset
    task=`\
    curl -X POST \
    -F "file=@hamster_carcinogenicity.csv;type=text/csv" \
    "http://ot-test.in-silico.ch/dataset" \
    2>/dev/null`
    dataset=`get_result $task`
    echo "Dataset: $dataset"
    



Open the dataset URI with a web browser and find the 'features' section. Taking a look at the feature metadata reveals that the endpoint (referred to as prediction feature) is actually recognized as nominal _and_ numeric; nominal, since only two values were submitted (1.0 and 0.0, which could correspond to e.g. 'active' and 'inactive'). [Up to three different values would be regarded a classification problem](http://www.maunz.de/wordpress/opentox/2011/multinomial-fminer) and recognized as nominal.

Calculating physico-chemical (PC) descriptors requires to submit the dataset just created to the respective service, and optionally constrain the type of descriptors (_pc_type_) to be created. Available are constitutional, electronic, cpsa, topological, geometrical, and hybrid descriptors, see links below this section.


    
    
    # Calculate constitutional PC descriptors
    task=`\
    curl -X POST \
    --data-urlencode "dataset_uri=$dataset" \
    --data-urlencode "pc_type=constitutional" \
    "http://ot-test.in-silico.ch/algorithm/pc/AllDescriptors" \
    2>/dev/null`
    echo "Task: $task"
    all_feature_dataset=`get_result $task`
    echo "All Features Dataset: $all_feature_dataset"
    



Creation of descriptors (also referred to as features) may take some time. Monitor the progress by opening the task URI with a web browser. When finished, take a look at the feature metadata of the resulting dataset. A lot of features have been created, thus the next step is to filter out features that are suitable for predicting the endpoint. This is done by submitting both the original and the dataset containing all features to the respective service.


    
    
    # Select features with recursive feature elimination
    task=`\
    curl -X POST \
    --data-urlencode "dataset_uri=$dataset" \
    --data-urlencode "prediction_feature=$dataset/feature/Hamster%20Carcinogenicity" \
    --data-urlencode "feature_dataset_uri=$all_feature_dataset" \
    "http://ot-test.in-silico.ch/algorithm/fs/rfe" \
    2>/dev/null`
    echo "Task: $task"
    feature_dataset=`get_result $task`
    echo "Feature Dataset: $feature_dataset"
    



A quick look at the feature metadata of the result shows how many features have been filtered out.

**See also:**



	
  * [Creating PC descriptors in opentox-ruby](http://www.maunz.de/wordpress/opentox/2012/calculating-physico-chemical-descriptors-with-opentox-ruby)

	
  * [Selecting Features with opentox-ruby](http://www.maunz.de/wordpress/opentox/2012/selecting-features-with-opentox-ruby)




# Lazar Models and Individual Predictions


This section is divided in two parts: using the just-created and filtered physico-chemical descriptors and using subgraphs ([BBRC](http://www.maunz.de/wordpress/bbrc) descriptors). Both will be used to create Lazar models and derive individual predictions from them. This process will be split in turn into a weighted majority vote and SVM classifier (so 4 parts in total).


## Using PC descriptors


The original dataset (containing the endpoint information) must be submitted to the Lazar algorithm, together with the feature dataset (the result of the recursive feature eliminiation). Weighted Majority Voting (WMV) is the default in Lazar.


    
    
    # Create Lazar model (Weighted Majority Vote)
    task=`\
    curl -X POST \
    --data-urlencode "dataset_uri=$dataset" \
    --data-urlencode "feature_dataset_uri=$feature_dataset" \
    --data-urlencode "prediction_feature=$dataset/feature/Hamster%20Carcinogenicity" \
    "http://ot-test.in-silico.ch/algorithm/lazar" \
    2>/dev/null`
    echo "Task: $task"
    lazar_wmv_pc=`get_result $task`
    echo "Lazar Model (WMV, PC): $lazar_wmv_pc"
    



Now the same for the SVM classifier. Since the dataset is very small, the minimum similarity for neighbors is reduced.


    
    
    # Create Lazar model (SVM)
    task=`\
    curl -X POST \
    --data-urlencode "dataset_uri=$dataset" \
    --data-urlencode "feature_dataset_uri=$feature_dataset" \
    --data-urlencode "prediction_feature=$dataset/feature/Hamster%20Carcinogenicity" \
    --data-urlencode "prediction_algorithm=local_svm_classification" \
    --data-urlencode "min_sim=0.0" \
    "http://ot-test.in-silico.ch/algorithm/lazar" \
    2>/dev/null`
    echo "Task: $task"
    lazar_svm_pc=`get_result $task`
    echo "Lazar Model (SVM, PC): $lazar_svm_pc"
    



Both models can be queried to predict the endpoint for 'unknown' query compounds, i.e., submitted by the user. The necessary features are calculated on the fly for the query compound. Then Lazar trains a model on the dataset compounds and predicts the query.

The query compound's InChI string must be determined. To this end, a service such as CACTVS may be used. Example: benzene hast the InChI string 'InChI=1S/C6H6/c1-2-4-6-5-3-1/h1-6H'. The InChI is simply appended to the trunk URI of the compound service, and submitted to the model.


    
    
    # Predict Benzene on WMV model
    lazar_wmv_pc_pred=`\
    curl -X POST \
    --data-urlencode "compound_uri=http://ot-test.in-silico.ch/compound/InChI=1S/C6H6/c1-2-4-6-5-3-1/h1-6H" \
    "$lazar_wmv_pc" \
    2>/dev/null`
    echo "Lazar Prediction (WMV, PC): $lazar_wmv_pc_pred"
    
    # Predict Benzene on SVM model
    lazar_svm_pc_pred=`\
    curl -X POST \
    --data-urlencode "compound_uri=http://ot-test.in-silico.ch/compound/InChI=1S/C6H6/c1-2-4-6-5-3-1/h1-6H" \
    "$lazar_svm_pc" \
    2>/dev/null`
    echo "Lazar Prediction (SVM, PC): $lazar_svm_pc_pred"
    



Open both prediction datasets in a web browser. Find the predicted values by searching for 'predicted/value' repeatedly.


## Using Subgraph Descriptors


The Weighted Majority and SVM models are created and queried for predictions. An important difference to PC descriptors is that  feature datasets are left out in the REST commands. This happens for a reason: the subgraph descriptors used here ([BBRC](http://www.maunz.de/wordpress/bbrc) features) are derived by taking the endpoint into account. Thus, pre-calculating them on the whole dataset (such as PC descriptors) would overfit the individual folds in a crossvalidation (see below).


    
    
    # Create Lazar model (Weighted Majority Vote)
    task=`\
    curl -X POST \
    --data-urlencode "dataset_uri=$dataset" \
    --data-urlencode "prediction_feature=$dataset/feature/Hamster%20Carcinogenicity" \
    "http://ot-test.in-silico.ch/algorithm/lazar"\
    2>/dev/null`
    echo "Task: $task"
    lazar_wmv_sg=`get_result $task`
    echo "Lazar Model (WMV, Subgraphs): $lazar_wmv_sg"
    
    # Create Lazar model (SVM)
    task=`\
    curl -X POST \
    --data-urlencode "dataset_uri=$dataset" \
    --data-urlencode "prediction_feature=$dataset/feature/Hamster%20Carcinogenicity" \
    --data-urlencode "prediction_algorithm=local_svm_classification" \
    --data-urlencode "min_sim=0.0" \
    "http://ot-test.in-silico.ch/algorithm/lazar" \
    2>/dev/null`
    echo "Task: $task"
    lazar_svm_sg=`get_result $task`
    echo "Lazar Model (SVM, Subgraphs): $lazar_svm_sg"
    



Prediction is completely analogous to the PC descriptor case (see above). It is interesting that subgraphs immensely speeds up the process. This is due to the very efficient subgraph enumeration process in [BBRC](http://www.maunz.de/wordpress/bbrc) mining, whereas PC descriptors must be calculated partly from 3D structures and/or retrieved over the internet from other services.

**See also:**



	
  * [Model Building in Lazar (Data Mining and Machine Learning Methods)](http://www.maunz.de/wordpress/opentox/2012/dm-and-ml-in-lazar)

	
  * [Lazar Models and how to trigger them](http://www.maunz.de/wordpress/opentox/2011/lazar-models-and-how-to-trigger-them)

	
  * [Calling BBRC and LAST-PM in three steps](http://www.maunz.de/wordpress/opentox/2011/bbrc-and-last-usage)




# Validating the Lazar Algorithm


Ten-fold crossvalidation is performed on the dataset using Lazar with physico-chemical descriptors and subgraphs (BBRC descriptors), each time using either weighted majority voting or SVMs (4 parts in total). Human readable validation reports for viewing in a web browser will be created.


## Using PC Descriptors


Algorithm, dataset, and additional parameters must be submitted to the validation service. The validation service creates dataset splits and performs the validation. The additional parameters are already familiar from the direct use of models and reflect what was used there (see above).


    
    
    # Validate Lazar using PC descriptors and WMV
    task=`\
    curl -X POST \
    --data-urlencode "algorithm_uri=http://ot-test.in-silico.ch/algorithm/lazar" \
    --data-urlencode "dataset_uri=$dataset" \
    --data-urlencode "prediction_feature=$dataset/feature/Hamster%20Carcinogenicity" \
    --data-urlencode "algorithm_params=feature_dataset_uri=$feature_dataset" \
    "http://ot-test.in-silico.ch/validation/crossvalidation" \
    2>/dev/null`
    echo "Task: $task"
    lazar_val_wmv_pc=`get_result $task`
    echo "Lazar Validation (WMV, PC): $lazar_val_wmv_pc"
    
    # Create report
    task=`\
    curl -X POST \
    --data-urlencode "validation_uris=$lazar_val_wmv_pc" \
    "http://ot-test.in-silico.ch/validation/report/crossvalidation" \
    2>/dev/null`
    lazar_valr_wmv_pc=`get_result $task`
    echo "Lazar Validation Report (WMV, PC): $lazar_valr_wmv_pc"
    
    # Validate Lazar using PC descriptors and SVM
    task=`\
    curl -X POST \
    --data-urlencode "algorithm_uri=http://ot-test.in-silico.ch/algorithm/lazar" \
    --data-urlencode "dataset_uri=$dataset" \
    --data-urlencode "prediction_feature=$dataset/feature/Hamster%20Carcinogenicity" \
    --data-urlencode "algorithm_params=feature_dataset_uri=$feature_dataset;prediction_algorithm=local_svm_classification;min_sim=0.0" \
    "http://ot-test.in-silico.ch/validation/crossvalidation" \
    2>/dev/null`
    echo "Task: $task"
    lazar_val_svm_pc=`get_result $task`
    echo "Lazar Validation (SVM, PC): $lazar_val_svm_pc"
    
    # Create report
    task=`\
    curl -X POST \
    --data-urlencode "validation_uris=$lazar_val_svm_pc" \
    "http://ot-test.in-silico.ch/validation/report/crossvalidation" \
    2>/dev/null`
    lazar_valr_svm_pc=`get_result $task`
    echo "Lazar Validation Report (SVM, PC): $lazar_valr_svm_pc"
    



Inspect carefully the validation reports and the detailed information there.


## Using Subgraph Descriptors


Again, fewer options (in fact none) are needed, since BBRC descriptor calculation does not require a feature dataset.


    
    
    # Validate Lazar using BBRC descriptors and WMV
    task=`\
    curl -X POST \
    --data-urlencode "algorithm_uri=http://ot-test.in-silico.ch/algorithm/lazar" \
    --data-urlencode "dataset_uri=$dataset" \
    --data-urlencode "prediction_feature=$dataset/feature/Hamster%20Carcinogenicity" \
    "http://ot-test.in-silico.ch/validation/crossvalidation" \
    2>/dev/null`
    echo "Task: $task"
    lazar_val_wmv_sg=`get_result $task`
    echo "Lazar Validation (WMV, BBRC): $lazar_val_wmv_sg"
    
    # Create report
    task=`\
    curl -X POST \
    --data-urlencode "validation_uris=$lazar_val_wmv_sg" \
    "http://ot-test.in-silico.ch/validation/report/crossvalidation" \
    2>/dev/null`
    lazar_valr_wmv_sg=`get_result $task`
    echo "Lazar Validation Report (WMV, BBRC): $lazar_valr_wmv_sg"
    
    # Validate Lazar using BBRC descriptors and SVM
    task=`\
    curl -X POST \
    --data-urlencode "algorithm_uri=http://ot-test.in-silico.ch/algorithm/lazar" \
    --data-urlencode "dataset_uri=$dataset" \
    --data-urlencode "prediction_feature=$dataset/feature/Hamster%20Carcinogenicity" \
    --data-urlencode "algorithm_params=prediction_algorithm=local_svm_classification;min_sim=0.0" \
    "http://ot-test.in-silico.ch/validation/crossvalidation" \
    2>/dev/null`
    echo "Task: $task"
    lazar_val_svm_sg=`get_result $task`
    echo "Lazar Validation (SVM, BBRC): $lazar_val_svm_sg"
    
    # Create report
    task=`\
    curl -X POST \
    --data-urlencode "validation_uris=$lazar_val_svm_sg" \
    "http://ot-test.in-silico.ch/validation/report/crossvalidation" \
    2>/dev/null`
    lazar_valr_svm_sg=`get_result $task`
    echo "Lazar Validation Report (SVM, BBRC): $lazar_valr_svm_sg"
    



Inspect carefully the validation reports and the detailed information there.
