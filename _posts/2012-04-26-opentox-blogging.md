---
layout: post
title: "Blogging at opentox.github.com"
description: ""
category: meta
tags: []
---
{% include JB/setup %}

For OpenTox GitHub organization members only

Install Jekyll
--------------
    gem install jekyll

Get opentox.github.com
----------------------
    git clone git@github.com:opentox/opentox.github.com.git

Preview
-------
    cd opentox.github.com
    jekyll --server
view at http://localhost:4000

Blog
----
    rake post title="foo"
edit blog

Categories and tags
-------------------

Categories should match github projects (e.g. opentox-client, algorithm, ...) or "general" for topics that apply to more than one project. Use tags to refer to other concepts (e.g REST, "support vector machines", ...).

Publish
-------
    git add .
    git commit -m "Add new content"
    git push 

<br />
Formatting
-------
* [Markdown Syntax Documentation](http://daringfireball.net/projects/markdown/syntax)
* [github flavoured markdown](http://github.github.com/github-flavored-markdown/)
