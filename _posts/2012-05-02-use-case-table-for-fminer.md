---
layout: post
title: "Use Case Table for Fminer"
description: "Important use-cases and command line arguments / web service switches are presented below. The list is not complete! See [fminer README](https://github.com/amaunz/fminer2/blob/master/fminer/README) and [web services README](http://github.com/opentox/algorithm) for a more complete overview."
category: algorithm
tags: [Fminer, Feature Generation, BBRC, LAST-PM, Tutorials]
---
{% include JB/setup %}

**Important use-cases and command line arguments / web service switches are presented below. The list is not complete! See [fminer README](https://github.com/amaunz/fminer2/blob/master/fminer/README) and [web services README](http://github.com/opentox/algorithm) for a more complete overview.
**


## BBRC


|Usecase&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|Commandline&nbsp;&nbsp;|Webservice&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|API|
|:-------|:-----------|:----------|:--|
|_n_-Frequent Subgraph Mining (w/o adding activities)|-f _n_|-|SetMinfreq( _n_ )<br />SetChisqActive(false)|
|_n_-Frequent Subgraph Mining (w added activities)|-d -b -f _n_ -p _0.0|-|SetDynamicUpperBound(false)<br /> SetBackbone(false)<br /> SetMinfreq( _n_ )<br /> SetChisqSig( _0.0_ )|
|_n_-Frequent<br /> Class-Correlated ( _p_ -value as lower bound) Subgraph Mining|-d -b -f _n_ -p _p_|backbone=false<br /> min_frequency= _n_<br /> min_chisq_significance= _p_ |SetDynamicUpperBound(false)<br /> SetBackbone(false)<br /> SetMinfreq( _n_ )<br /> SetChisqSig( _p_ )|
|Backbone Refinement Class Mining ( _p_ -value as lower bound)|-f _n_ -p _p_ |min_frequency= _n_<br /> min_chisq_significance= _p_|SetMinfreq( _n_ )<br /> SetChisqSig( _p_ )|



## LAST-PM



|Usecase&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|Commandline&nbsp;&nbsp;|Webservice&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|API&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
|:-------|:-----------|:----------|:--|
|Latent Structure Pattern Mining|-f _n_ |min_frequency= _n_|SetMinfreq( _n_ )|



**Note:**

	
* In BBRC, two modes for _n_-frequent subgraph mining are available. The first one does not use activity information, whereas the second one uses it to indicate occurrences of subgraphs per class (for nominal activities), which can be interesting despite deliberately not taking class correlation into account during the mining process.

	
* For all settings (except web service) [environment variables](https://raw.github.com/amaunz/fminer2/master/fminer/README) play a major role for the output format. Supported are gSpan and SMARTS, the latter as CSV or YAML. Moreover, _p_-values can be requested instead of $latex \chi^2$ test statistics.** **A special case is the [output of hit counts](/algorithm/2012/05/02/support-calculation-in-fminer). While on the command line, there is a special environment variable, the webservice must be passed a special switch: -d "nr_hits".


