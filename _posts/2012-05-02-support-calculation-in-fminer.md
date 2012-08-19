---
layout: post
title: "Support Calculation in Fminer"
description: "Fminer algorithms BBRC and LAST-PM can now be used for automatic support calculation. This post shows how to use it."
category: algorithm
tags: [fminer, feature generation, BBRC, LAST-PM]
---
{% include JB/setup %}

**Fminer algorithms BBRC and LAST-PM can now be used for automatic support calculation. This post shows how to use it.**


## Types of Support in Chemical Datasets


The support of a pattern denotes the number of times it is contained in a chemical (also termed _hits_). The number of hits can be defined differently by counting unique or non-unique matches:



	
  * Unique matches: a SMARTS may only match once on the same atoms.

	
  * Non-unique matches: a SMARTS pattern may match more than once on the same atoms.


For example in a six-membered aromatic ring, the pattern "c:c:c:c:c:c" would have six unique, but twelve non-uniqe matches (since it has two non-unique matches on any six atoms). Fminer algorithms use the **non-unique** matching scheme.


## Usage from the Command Line


Fminer may be called from ruby, python, java, and C++ as those APIs are integrated into [the source](http://github.com/amaunz/fminer2) and supported by the Makefile. For demonstration, we will use the standard front-end application.

Download and install the fminer2 package according to the [BBRC instructions](http://www.maunz.de/wordpress/bbrc) and [LAST-PM instructions](http://www.maunz.de/wordpress/latent-structure-pattern-mining), respectively. Then run fminer from the main directory by setting the environment variable FMINER_NR_HITS first.


    
    
    unset FMINER_LAZAR
    export FMINER_SMARTS=1
    export FMINER_P_VALUES=1
    export FMINER_NR_HITS=1 # Switch on support
    fminer/fminer libbbrc/libbbrc.so -f5  liblast/test/hamster_carcinogenicity.smi \
    liblast/test/hamster_carcinogenicity.class
    



You will receive output such as the following:


    
    
    - [ "[#6&a]:[#6&a]:[#6&a]:[#6&a]:[#6&a]", 13.9894, [ 20018: '24', 20374: '24', 20682: '12', ... ]]
    



This denotes that the SMARTS pattern "[#6&a]:[#6&a]:[#6&a]:[#6&a]:[#6&a]" matches e.g. 24 times on molecule 20018, and twelve times on molecule 20682 (you can check that from the .smi file). Please also see [this post](http://www.maunz.de/wordpress/opentox/2011/multinomial-fminer) on how to check correctness of the hits.

**Note:** BBRC was used for demonstration, but LAST-PM supports the same functionality.


## Integration into Opentox-Webservices

Calculating hits is also  available as a web service within Opentox by passing the additional argument _-d "nr_hits=true"_ to Fminer web services BBRC and LAST-PM. The support will be reported as numeric feature (as defined in the OpenTox ontology) and an integer value, compared to nominal feature and true/false (please read [the post about calling BBRC and LAST-PM](../opentox/2011/bbrc-and-last-usage)).
