---
layout: post
title: "Development: Vanilla Version"
description: ""
category: Development
tags: [Lazar, Fminer, 4store, BBRC, LAST-PM, Tutorials]
---
{% include JB/setup %}

[Install development](http://opentox.github.com/setup/2012/08/09/install-opentox-development-environment/) version, using `bash`. Make sure ot-tools are available (type `ot` and press `<TAB>`).

Source from `$HOME`:

    # configure
    otconfig
    otstart all
    lh="http://localhost"

    # util 
    function get_result {
      task=$1
      while [ 1 ]; do
        result=`curl -H "accept:text/uri-list" $task 2>/dev/null`
        [ $result == $task ] && sleep 1 || break
      done
      echo $result
    }

Load dataset.
 
    # load ds
    cd
    task=`curl -X POST \
    -F "file=@opentox-ruby/opentox-test/test/data/hamster_carcinogenicity.csv;type=text/csv" \
    $lh:8083/dataset`
    ds=`get_result "$task"`
    echo "hamster: $ds" 


<br>
<br>
<hr>
<br>

Calculate benzene PC descriptors, constrained by `pc_type`:

    curl \
    --data-urlencode "pc_type=geometrical"  \
    -X POST http://localhost:8082/compound/InChI=1S/C6H6/c1-2-4-6-5-3-1/h1-6H/pc

Constrained by `lib` and `pc_type`:

    curl \
    --data-urlencode "lib=joelib" \
    --data-urlencode "pc_type=geometrical"  \
    -X POST http://localhost:8082/compound/InChI=1S/C6H6/c1-2-4-6-5-3-1/h1-6H/pc

Calculate dataset PC descriptors, constrained by `pc_type`:

    task=`curl \
    --data-urlencode "pc_type=geometrical"  \
    -X POST $ds/pc`
    pc_fds=`get_result "$task"`
    echo "pc_fds: $pc_fds"

<br>
<br>
<hr>
<br>


Mine subgraphs. 

    # bbrc feature ds
    task=`curl -X POST --data-urlencode "dataset_uri=$ds" $lh:8081/algorithm/fminer/bbrc`
    bbrc_fds=`get_result "$task"`
    echo "bbrc_fds: $bbrc_fds" 

    # last-pm feature ds
    task=`curl -X POST --data-urlencode "dataset_uri=$ds" $lh:8081/algorithm/fminer/last`
    last_fds=`get_result "$task"`
    echo "last_fds: $last_fds" 

Make Lazar model with bbrc descriptors. 
 
    # create lazar m w/ bbrc
    task=`curl -X POST \
    --data-urlencode "dataset_uri=$ds" \
    --data-urlencode "feature_dataset_uri=$bbrc_fds" \
    --data-urlencode "feature_generation_uri=$lh:8081/algorithm/fminer/bbrc" \
    $lh:8081/algorithm/lazar`
    lazar_m_bbrc=`get_result "$task"`
    echo "lazar_m_bbrc: $lazar_m_bbrc"

Make Lazar model with pc descriptors.

    # create lazar m w/ pc
    task=`curl -X POST \
    --data-urlencode "dataset_uri=$ds" \
    --data-urlencode "feature_dataset_uri=$pc_fds" \
    --data-urlencode "feature_generation_uri=$ds/pc" \
    $lh:8081/algorithm/lazar`
    lazar_m_pc=`get_result "$task"`
    echo "lazar_m_pc: $lazar_m_pc"

Feature dataset generated on-the-fly when `feature_dataset_uri` omitted.

<br>
<br>
<hr>
<br>


Make predictions with BBRC.

    # make benzene prediction w/ lazar m w/ bbrc
    task=`curl -X POST \
    --data-urlencode "compound_uri=$lh:8082/compound/InChI=1S/C6H6/c1-2-4-6-5-3-1/h1-6H" \
    $lazar_m_bbrc`
    lazar_p_bbrc=`get_result "$task"`
    echo "lazar_p_bbrc: $lazar_p_bbrc" 


Make predictions with PC.

    # make benzene prediction w/ lazar m w/ pc
    task=`curl -X POST \
    --data-urlencode "compound_uri=$lh:8082/compound/InChI=1S/C6H6/c1-2-4-6-5-3-1/h1-6H" \
    $lazar_m_pc`
    lazar_p_pc=`get_result "$task"`
    echo "lazar_p_pc: $lazar_p_pc" 


Make a database prediction

    # make a database prediction
    task=`curl -X POST \
    --data-urlencode "compound_uri=$lh:8082/compound/InChI=1S/C12H12N2O3/c1-2-12(8-6-4-3-5-7-8)9(15)13-11(17)14-10(12)16/h3-7H,2H2,1H3,(H2,13,14,15,16,17)" \ 
    $lazar_m_bbrc`
    lazar_db_bbrc=`get_result "$task"`
    echo "lazar_db_bbrc: $lazar_db_bbrc" 

Make a dataset prediction

    # make a dataset prediction
    task=`curl -X POST -F "file=@opentox-ruby/opentox-test/test/data/EPAFHM.mini.csv;type=text/csv" http://localhost:8083/dataset`
    mini=`get_result $task`
    echo  "mini: $mini"
    task=`curl -X POST \
    --data-urlencode "dataset_uri=$mini" \
    $lazar_m_bbrc`
    lazar_ds_bbrc=`get_result "$task"`
    echo "lazar_ds_bbrc: $lazar_ds_bbrc"

The result dataset has a format described [here](http://goo.gl/dErI9).


The example below demonstrates how much time each step approximately consumes during prediction. Call is: 

    curl -X POST --data-urlencode "compound_uri=$lh:8082/compound/InChI=1S/C6H6/c1-2-4-6-5-3-1/h1-6H" $lazar_m_bbrc

i.e. predict benzene for hamster carcinogenicity.

    10:23:34 :: Inited p dataset :: /lazar.rb:135
    10:23:35 :: Loaded t dataset :: /lazar.rb:139
    10:23:44 :: Loaded f dataset :: /lazar.rb:143
    10:23:44 :: Calced q fps :: /lazar.rb:149
    10:23:44 :: Custom model :: /lazar.rb:20
    10:23:45 :: Filled m data :: /lazar.rb:163
    10:23:45 :: F: 11x54; R: 54 :: /transform.rb:402
    10:23:45 :: Sims: 11, Acts: 11 :: /transform.rb:403
    10:23:45 :: Transformed m :: /lazar.rb:166
    10:23:45 :: Weighted Majority Vote :: /algorithm.rb:30
    10:23:45 :: Prediction is: '2'. :: /algorithm.rb:56
    10:23:45 :: Confidence is: '0.25281 :: /algorithm.rb:58
    10:23:45 :: Predicted q :: /lazar.rb:174

Time total: 11s, Time Dataset loading: 10s 
