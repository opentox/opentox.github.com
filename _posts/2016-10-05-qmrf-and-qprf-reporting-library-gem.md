---
layout: post
title: "QMRF and QPRF reporting library gem"
description: " QMRF and QPRF reporting extension to OpenTox ruby modules and lazar."
category: Usage
tags: [ruby, Gem, QSAR, QMRF, QPRF, Lazar, nano-lazar]
---
{% include JB/setup %}

## QSAR-Report
QMRF and QPRF reporting extension to ruby, OpenTox ruby modules and lazar.

The QSAR-report gem was developed to extend the lazar and nano-lazar toxicity prediction application with QMRF and QPRF reporting features. 
The library gem is independent from lazar or nano-lazar and can also be used in any other ruby code. The library code is in general public license version 3 (GPLv3). 
The  open source code is freely available and versioned at the Github code hosting platform.

The library has two main classes for the report types **OpenTox::QMRFReport** and **OpenTox::QPRFReport**. Instances of these classes can be easily filled with contents.
There is a full documentation of the library with examples at [RubyDoc.info Code documentation](http://www.rubydoc.info/gems/qsar-report) .

### Classes for QMRF and QPRF reporting.

* **OpenTox::QMRFReport**:
  Provides a ruby OpenTox class to prepare an initial version of a QMRF report.
  The XML output is in QMRF version 1.3 and can be finalized with the QMRF editor 2.0 (https://sourceforge.net/projects/qmrf/)
* **OpenTox::QPRFReport**:
  Provides a ruby OpenTox class to prepare an initial version of a QPRF (version 1.1) report in JSON or HTML.

## Usage

### QMRF

create a new QMRF report, add some content and show output:

```ruby
    require "qsar-report"

    # create a new report
    report = OpenTox::QMRFReport.new
    
    # add a title
    report.value "QSAR_title", "My QSAR Title"
    
    # change 6.2 'Available information for the training set' set inchi and smiles to Yes
    report.change_attributes "training_set_data", {:inchi => "Yes", :smiles => "Yes"}
    
    # add a publication to the publication catalog
    report.change_catalog :publications_catalog, :publications_catalog_1, {:title => "MyName M (2016) My Publication Title, QSAR News, 10, 14-22", :url => "http://myqsarnewsmag.dom"}
    
    # link/reference the publication to the report bibliography
    report.ref_catalog :bibliography, :publications_catalog, :publications_catalog_1
    
    # output
    puts report.to_xml
    
    # validate a report (as created above) against qmrf.xsd
    report.validate

```

### QPRF

create a new QPRF report, add some content and show output:

```ruby
    require "qsar-report"
    report = OpenTox::QPRFReport.new
    report.Title = "My QPRF Report"
    report.Version = "1"
    report.Date = Time.now.strftime("%Y/%m/%d")
    report.value "1.1", "7732-18-5" # set CAS number for H²O
    puts report.to_html
```

### Installation

    gem install qsar-report

### More Documentation

* [RubyDoc.info Code documentation](http://www.rubydoc.info/gems/qsar-report)
* For full information on QSAR reporting see: [JRC QSAR Model Database and QSAR Model Reporting Formats](https://eurl-ecvam.jrc.ec.europa.eu/databases/jrc-qsar-model-database-and-qsar-model-reporting-formats)
