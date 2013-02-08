---
layout: post
title: "Calculating physico chemical descriptors with opentox algorithm"
description: "Opentox-ruby has facilities to calculate more than 300 non-proprietary physico-chemical descriptors, whose calculation can be easily triggered with REST calls."
category: Usage
tags: [Algorithm, Feature Generation, Feature Selection, Tutorials]
---
{% include JB/setup %}

**Opentox-ruby has facilities to calculate more than 300 non-proprietary physico-chemical descriptors, whose calculation can be easily triggered with REST calls.**

Three libraries are employed for descriptor calculation:


* [Chemistry Development Kit](http://cdk.sf.net) (CDK), developed by Chris Steinbeck, Egon Willighagen, and others.


* [OpenBabel](http://openbabel.sf.net), developed by Noel O'Boyle and others.


* [Joelib2](http://sourceforge.net/projects/joelib/), developed by Jörg Wegener.


CDK and OpenBabel are actively maintained packages with a large community, while Joelib seems is more or less a single man's work.

Descriptors have been categorized across libraries. It is possible to calculate descriptors individually, or calculate groups of descriptors according to categories and/or libraries.


# Categories


Descriptors were categorized based on work by [R. Guha](http://www.rguha.net/code/java/cdkdesc.html). The list gives code names and number of descriptors in braces:

	
* _geometrical (20)_

* _topological (186)_

* _electronic (10)_

* _cpsa (28)_

* _constitutional (48)_

* _hybrid (23)_




# Libraries


Three libraries are employed. The list gives code names and number of descriptors in braces.

	
* _cdk (263)_

* _openbabel (20)_

* _joelib (32)_



# Creating a Feature Dataset


![Descriptor Calculation](/images/pc.png)


The deployment on `ot-test.in-silico.ch` is used to demonstrate the usage:


	
Get a list of all descriptors: Query the pc descriptor service without parameters.
    
    curl "http://ot-test.in-silico.ch/algorithm/pc"
    
	
Calculate all descriptors (using *AllDescriptors* from the above list) by POSTing a dataset uri.

    
    curl -X POST \
    --data-urlencode "dataset_uri=..." \
    "http://ot-test.in-silico.ch/algorithm/
    pc/AllDescriptors"
    

Pass arguments *pc_type* and/or *lib* to constrain the query to certain categories and/or libraries. Use code names from above as argument values. E.g., to calculate constitutional and electronic descriptors from CDK, use `pc_type=constitutional,electronic` and `lib=cdk`.


    curl -X POST \
    --data-urlencode "dataset_uri=..." \
    --data-urlencode "pc_type=constitutional,electronic", \
    --data-urlencode "lib=cdk" \
    "http://ot-test.in-silico.ch/algorithm/pc/AllDescriptors"


It is possible to combine values by appending them in a comma-separated list. Moreover,

* Calculate individual descriptors by POSTing to one of the other URIs from the list.

* The POST returns a task URI that can be queried for progress.


That's it! The resulting feature dataset contains extensive [OWL feature metadata](http://opentox.org/data/documents/development/RDF%20files):


* `OT.hasSource`: The dataset passed as *dataset_uri* argument

* `DC.creator`: The webservice used to calculate the feature

* `DC.description`: Free text with a human readable description, library, and category of the feature


# Notes
	
* As with any dataset, resulting feature datasets may also be requested in CSV or YAML formats by specifying appropriate accept-Headers.

* Further processing may include feature selection as next step. [An implementation based on Random Forests is available in opentox-ruby](/algorithm/2012/05/02/selecting-features-with-opentox-algorithm).

* The dataset (perhaps after feature selection) can be [passed to the Lazar algorithm](/algorithm/2012/05/02/lazar-models-and-how-to-trigger-them) as feature dataset.

* From a higher perspective: A complete [tutorial](/algorithm/2012/05/01/services-tutorial---lazar-feature-generation-feature-selection-validation) that streamlines the process.



