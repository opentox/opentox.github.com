---
layout: post
title: "Edit Task object while Task is running"
description: "HowTo edit a Task object inside a Task block."
category: Usage
tags: [Metadata, Object, RDF, Tutorials]
---
{% include JB/setup %}
**This post explains how to edit a Task object with percentageCompleted value.**

_Example:_

```ruby    
    # generate a Task uri
    task_uri = OpenTox::Task.task_uri
    
    # pass 'task_uri' to become new Task uri
    task = OpenTox::Task.run("Message", "Creator", task_uri) do |t|
      
      # get Task object and fill with metadata
      t = OpenTox::Task.find task_uri
      t.metadata
      
      run partial code
      
      # edit progress
      t[RDF::OT.percentageCompleted] = "25"
      t.put
      
      run partial code

      # edit progress
      t[RDF::OT.percentageCompleted] = "50"
      t.put

      run partial code
      "Result URI"
    end
    # wait to complete
    task.wait
```

**If task status is completed task.percentageCompleted == 100%**




