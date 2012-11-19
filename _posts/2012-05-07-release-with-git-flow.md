---
layout: post
title: "Release Development with Git Flow"
description: "Release Development with Git Flow"
category: development
tags: [GIT, deployment]
---
{% include JB/setup %}


For complete description of release workflows controlled with git-flow see:


* [git-flow](https://github.com/nvie/gitflow)
* [Jeff Kreeftmeijer's introduction](http://jeffkreeftmeijer.com/2010/why-arent-you-using-git-flow/) 

<br />

## How-To

A simple How-To of using git-flow on a bash shell.
Let's assume a new release in Version 1.0.1 as an example:


    RELEASE="v1.0.1"

<br />
### Initialize git flow

how to initialize git-flow if you haven't jet.

    git flow init  # =>
    Which branch should be used for bringing forth production releases?
      - development
      - master
    Branch name for production releases: [master] master
    Which branch should be used for integration of the "next release"?
      - development
    Branch name for "next release" development: [master] development
    How to name your supporting branch prefixes?
    Feature branches? [feature/] 
    Release branches? [release/] 
    Hotfix branches? [hotfix/] 
    Support branches? [support/] 
    Version tag prefix? []   



<br />
### Start a release branch


    git flow release start $RELEASE # start new branch
    git flow release publish $RELEASE # publish release branch to online repository
    
<br />
### Participate in a release branch


If you want to add changes to a release branch you didn't create. Switching to a new release branch.

  
    git fetch; git pull; git checkout release/$RELEASE

<br />  
### Finish a Release

How to finish the release, adding a tag and delete the online release branch.


    git flow release finish $RELEASE  # finish release
    git push 
    git push --tags  #push the new tag to remote
    git push origin :release/$RELEASE   #delete remote branch
 
<br />
## Tools and Helpers
<br />
###Reset a tag
if something went wrong and you have to reset a tag.

    git tag -d $RELEASE # delete the wrong tag
    git push origin :refs/tags/$RELEASE # remove the remote tag
    git tag $RELEASE # recreate the tag
    git push --tags  # push tag to remote
<br />
###Helper to complete the ChangeLog
suppose you want to know what was done in the development branch since the last release date (e.G.: 2012/03/13). This git command give you each commits in a line. Add --no-merges to remove merge commits.      

    git log --since=2012-03-13 --date=local --pretty="%ad %an: %s" development
    


