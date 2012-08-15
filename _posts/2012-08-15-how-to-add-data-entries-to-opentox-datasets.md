---
layout: post
title: "How to add data entries to OpenTox datasets"
description: ""
category: dataset
tags: []
---
{% include JB/setup %}

Data entries in `OpenTox::Dataset`s follow the table metaphor with feature values in columns and data entries (compounds) in rows.
To define the sequence of features you will have to set the dataset features first (think of it as setting the table header). After that you can append data entries sequentially with the `<<` operator which takes an Array as argument. The first array object is reserved for a `OpenTox::Compound`, the remaining entries are feature values (in the same order as the dataset features). Data entries are sorted in the same sequence as they are created, multiple entries for the same compound are allowed.

Example:

    # create dataset
    dataset = OpenTox::Dataset.new nil, @@subjectid

    # create and save features
    feature1 = Feature.new nil, @@subjectid
    feature1.title = "my first feature title"
    # set more metadata
    feature1.puts
    feature2 = Feature.new nil, @@subjectid
    feature2.title = "my second feature title"
    # set more metadata
    feature2.puts

    # add features (i.e. set table headers)
    dataset.features = [feature1, feature2]

    # add a data entry
    compound = Compound.from_smiles "c1ccccc1"
    dataset << [ compound, true, 0.5 ]

    # add another data entry
    compound = Compound.from_smiles "CCC(CC)CN"
    dataset << [ compound, false, 1.0 ]

    # save
    dataset.save

You can find more examples in [opentox-test/test/dataset.rb](https://github.com/opentox/opentox-test/blob/fbc246e92d4dbfa3d108e6c3fa9b613e9aff9945/test/dataset.rb)
