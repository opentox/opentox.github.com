---
layout: post
title: "Data Mining and Machine Learning Algorithms in Lazar"
description: "Several statistical learners exist within Lazar. This page details important computational components."
category: opentox-algorithm
tags: [Lazar]
---
{% include JB/setup %}

**Several statistical learners exist within Lazar. This page details important computational components.**

# Model Building

![Model Workflow](http://www.maunz.de/wordpress/wp-content/uploads/2012/01/Workflow_Model4.png)

# Data Mining Modules

|Lazar Component|Subgraphs|PC Descriptors|
|:--------------|:--------|:-------------|
|Feature Generation URI|[BBRC](http://bbrc.maunz.de), [LAST‑PM](http://last-pm.maunz.de)|Ambit (several)|
|Feature Generation Algorithm|Substructure.match, Substructure.match_hits|Substructure.lookup|
|Feature Type|nominal or frequency|numeric|
|Transformation|Weighting by Significance|z-scores +Singular Value Decomposition|
|Similarity Algorithm|Weighted Tanimoto|Cosine|

<br /> 
<br /> 
# Machine Learning Algorithms

|Lazar Component|Classification|Regression|
|:--------------|:-------------|:---------|
|Nonlinear Model|C-Support Vector Classification|nu-Support Vector Regression|
|Nearest Neighbor Model|Weighted Majority Vote|none|
