---
layout: post
title: "BBRC and LAST PM Usage"
description: "This post provides a more 'how-to' like way to using BBRC and LAST-PM via REST than the usage informations for [BBRC](http://bbrc.maunz.de#usage) and for [LAST-PM](http://last-pm.maunz.de#usage)."
category: algorithm
tags: [Fminer, Feature Generation, BBRC, LAST-PM, Tutorials]
---
{% include JB/setup %}

**This post provides a more 'how-to' like way to using BBRC and LAST-PM via REST than the usage informations for [BBRC](http://bbrc.maunz.de#usage) and for [LAST-PM](http://last-pm.maunz.de#usage).**

REST is the technical basis for all OpenTox compliant services. BBRC and LAST-PM are subgraph mining methods that find chemical fragments suitable for predicting endpoints. This tutorial shows how to directly perform calls on them. In practice, they are mostly used in a larger workflow. There is a complete [tutorial](/algorithm/2012/05/01/services-tutorial---lazar-feature-generation-feature-selection-validation) that illustrates this.


# Calling the REST interface


The development server at [http://ot-test.in-silico.ch](http://ot-test.in-silico.ch) is used, which always provides the latest version. The individual calls return task URIs- it is not shown how to extract the result URIs from them (just do `curl -H "accept:yaml"` for human readable format on the task URIs).



	
  1. Prepare yourself by gathering some data. Perhaps you already have a dataset on some Opentox compliant webservice, then skip to step 3. If not, download one [here](http://www.maunz.de/opentox/hamster_carcinogenicity.csv) and upload it to the server.

    
    
    curl -X POST \
    -F "file=@hamster_carcinogenicity.csv;type=text/csv" \
    "http://ot-test.in-silico.ch/dataset"
    


	
  2. Query the result URI to get the prediction feature URI (this is the feature URI you want to mine against). Since BBRC and LAST-PM are supervised procedures, they need target values of the dataset.

    
    
    curl http://ot-test.in-silico.ch/dataset/42.yaml | grep -C 1 "features:"
    


	
  3. Submit everything to the BBRC and LAST-PM services.

    
    
    # launch BBRC
    curl -X POST \
    --data-urlencode "dataset_uri=..." \
    --data-urlencode "prediction_feature=..." \
    "http://ot-test.in-silico.ch/algorithm/fminer/bbrc"
    
    # launch LAST-PM
    curl -X POST \
    --data-urlencode "dataset_uri=..." \
    --data-urlencode "prediction_feature=..."
    "http://ot-test.in-silico.ch/algorithm/fminer/last"
    



are the appropriate calls, i.e. arguments have to be URI encoded.


# More Arguments


Call Nr. 3 above (which does the actual mining) accepts more arguments. Here are the most important ones:


* BBRC


  * To do ordinary, i.e. frequent and class-correlated (no BBRC), subtree mining, use `-d backbone=false` on the BBRC service.


  * Provide `-d min_chisq_significance=x` to set the minimum correlation threshold (default=0.95) on the BBRC service.







* BBRC and LAST-PM


  * To get the support of patterns instead of their mere presence, use `-d nr_hits=true` on the BBRC or LAST-PM service (see [this post](/algorithm/2012/05/02/support-calculation-in-fminer)).


  * To adjust minimum frequency on the BBRC or LAST-PM service, pass `-d min_frequency=5` for a minimum frequency of 5, for example. Note that minimum frequencies are always interpreted as absolute numbers.


  * Disable tree-shaped fragments (resorting to linear fragments) on the BBRC or LAST-PM service by setting `-d feature_type=paths`.







Practical Aspects





	
* Modify `min_frequency` in order to find sensible values for  minimum frequency. It is the parameter that primarily affects running time and output set size. However, finding parameter values for graph mining automatically is an open research question. Therefore, here is a heuristic procedure to find a "good" value for a dataset of _n_ instances (always round to whole numbers, where applicable):




_Initialization:_

    1. Let x = n
    2. Let y = x. Let x = x/2. Let min_frequency = x
    3. Run BBRC/LAST-PM, passing min_frequency as parameter.
    4. Go to 2. until pattern set size "large enough" (determined by you).

_Main phase:_
	
    1. Run BBRC/LAST-PM, passing min_frequency as parameter.
    2. Pattern set size "large enough" (determined by you)?
      1. No:
        Let y = min_frequency.
        Let min_frequency = (x+min_frequency)/2.
        Go to 1.
      2. Yes: Running time "low enough" (determined by you)?
        1. No:
          Let x = min_frequency.
          Let min_frequency = (min_frequency+y)/2.
          Go to 1.
        2. Yes: You are done, i.e. min_frequency has now a suitable value.
	
 For [YAML](http://en.wikipedia.org/wiki/YAML) as an easier alternative to RDF output, try requesting the result with `-H "accept:yaml"` as curl parameter.


* Review the [list of use cases](/algorithm/2012/05/02/use-case-table-for-fminer) supported by BBRC and LAST-PM.


* Try the downloadable version and install locally. Supported platforms (currently): C++, Ruby, Python, Java. Start by visiting [BBRC](http://www.maunz.de/wordpress/bbrc) and [LAST-PM](http://www.maunz.de/wordpress/latent-structure-pattern-mining) webpages and read carefully all the instructions by following links to technical documentation. The procedure for finding appropriate values for minimum frequency is completely analogous in this setting.




# Some Background Information


Graph mining applications fminer/bbrc (BBRC) and fminer/last (LAST-PM) are **complete miners** in the sense that they do not restrict the result set of subgraphs a-priori to a specific amount of patterns. Restricting the output set would contradict the principle of data-driven pattern generation, where no human intervention should be applied to the data mining process.

Instead of hard cutoffs for the set size, the user is expected to bound the mining process with sensible constraints (this is where he is "allowed" to bring expert knowledge in). In the worst case however, he has to apply a trial-and-error strategy for finding such constraints.

**BBRC** has been designed for (binary) class-correlated subgraph mining. In this domain, where each compound (graph) is assigned a true/false value, it can handle very large datasets. The algorithm is optimized for this setting and here the parameters have sensible default values.

**LAST-PM** has been designed for the same (binary) class-correlated subgraph mining setting as BBRC. Since the generated patterns describe hidden motifs in the graph database, the process takes more time to complete. LAST-PM has also sensible default values, which might need to be adapted now and then, however.


## The Regression Case


When dealing with numerical values as target variable ([referred to as *prediction-feature*](/algorithm/2012/05/02/bbrc-and-last-pm-usage)), some pruning techniques (*dynamic upper bound pruning*), which reduce runtime drastically for classification, are not yet applicable -  BBRC and LAST-PM disable it automatically for you. Moreover, the result set might be larger or smaller compared to classification.

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
    



and accepts absolute natural numbers only for x (not percentages).
