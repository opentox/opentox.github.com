---
layout: post
title: "Clever Feature Creation Keeps Your Database Small"
description: ""
category: Development
tags: [Dataset, Feature Generation, Fminer]
---
{% include JB/setup %}

**Avoiding feature generation from scratch can save a lot of memory and potentially speeds up the whole framework**

# The Problem

Consider an `OpenTox::Dataset` object in its fully loaded (populated) state, containing metadata, features, parameters, compounds, and data entries. The 'heavy' parts, containing most of the data, are the data entries and compounds. For this reason, in our implementation, they load directly via SPARQL queries. The 'light' parts are metadata, parameters, and features, as they do not contain tabulated data. The latter can be even filtered through RDF queries quickly.

However, even SPARQL queries take more time when data is stored in a redundant fashion at the 4Store service. This may not be necessary, since most of the data is usually already stored. For example, datasets, or parts of datasets, are often uploaded many times to the same service. Therefore, creating features by

    f=OpenTox::Feature.new
    ... do something with f ... 
    f.put

programmatically creates a new feature every time. The 4Store service knows nothing about the semantics and the programmer is in charge to tell the service that

    f1=OpenTox::Feature.new
    f1.title="Hamster Carcinogenicity"
    f1.put

and 

    f2=OpenTox::Feature.new
    f2.title="Hamster Carcinogenicity"
    f2.put

are actually the same feature. He should *do this* instead:

    f1=OpenTox::Feature.new
    f1.title="Hamster Carcinogenicity"
    f2=f1 # f2 is a pointer to f1
    f1.put

This results in a completely non-redundant setting, because compounds describing the same structure were already non-redundantly store. Therefore, two `OpenTox::Dataset`s describing the same data are now merely pointers to the same data. The features are also only present a single time at the 4Store backend.


# A Solution

The `opentox-client` library contains a method `OpenTox::Feature#find_by_title` that creates features non-redundantly. It accepts two arguments, a string with the feature title and a metadata hash. It searches the 4Store service for features with an identical name, parses the list and compares the metadata. If one of the features has identical metadata, its URI is returned. Otherwise, a new feature is created and its URI is returned.

The method is used like this:

    # Search feature by title
    title = "Foo"
    metadata = {
        RDF.type => [RDF::OT.Feature, RDF::OT.NumericFeature],
        RDF::DC.description => description
      }
    feature = OpenTox::Feature.find_by_title(title, metadata)

In practice, not many features are created per dataset, and the method works quite efficiently. It has very nice effects, for example, when uploading to datasets one after the other, both have identical features. The 4Store backend now knows that they are equal and potentially eliminates duplicate data.
