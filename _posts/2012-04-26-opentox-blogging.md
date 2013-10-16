---
layout: post
title: "Blogging at opentox.github.com"
description: ""
category: Development
tags: [Blogging]
---
{% include JB/setup %}

Install Jekyll
--------------

    gem install jekyll -v 0.12.0

Get opentox.github.com
----------------------

    git clone git@github.com:opentox/opentox.github.com.git

Preview
-------

    cd opentox.github.com
    jekyll --server

view at `http://localhost:4000`

Blog
----

    rake post title="foo"

Edit blog at `_posts/[...]foo.md`. Use a [spell-checker](/linux/2012/05/18/using-vim/).

Categories and tags
-------------------

Please choose one of the terms *Usage*, *Installation* or *Development* as a category for your post. 
Use tags to refer to related concepts (e.g *REST*, *support vector machines*, ...). Please 

- re-use existing tags as much as possible. 
- when re-using, be sure to use the verbatim expression (e.g. capitalization).

This is important for the 'Related Posts' functionality (although it does not seem to work on Github) and for users who want to get an overview of what is covered.

Publish
-------

    git add .
    git commit -m "Add new content"
    git push 

Formatting
-------
* [Markdown Syntax Documentation](http://daringfireball.net/projects/markdown/syntax)
* [github flavoured markdown](http://github.github.com/github-flavored-markdown/)
