---
layout: post
title: "Useful git tricks"
description: "A collection of helpful commands for the SCM system git"
category: General
tags: [git, source code]
---
{% include JB/setup %}

A collection of helpful commands for the SCM system git. Consider reading the [Ubuntu terminal guide](http://opentox.github.com/General/2012/05/18/improved-ubuntu-terminal/) first.

# General git help

There are some good places to start looking for documentation. Start off by visiting [git-scm.com](http://git-scm.com/doc). The official documentation is (contrary to many other projects) the most complete *and* useful documentation available. Specifically, you will want to understand the section about [branching and merging](http://git-scm.com/book/en/Git-Branching-Basic-Branching-and-Merging).

Proceed to [github.com](https://help.github.com/categories/18/articles), where very informative articles about the specifics of this large social coding platform are available. It could be interesting for you to read about [forking](https://help.github.com/articles/fork-a-repo).

[Here](http://cheat.errtheblog.com/s/git) is a cheat sheet and [here](http://nvie.com/posts/a-successful-git-branching-model/) is the development cycle model that IST uses.


# Tricks

Here is how to solve some common issues.

## How to check out a remote branch

Suppose there is a branch on `origin` called `foo` and you want to `checkout` this branch for the first time. Assuming you have cloned the repository, you cannot just do `git checkout foo` if `foo` was created by someone else *after* you have cloned.

    git remote show origin # see what's available on origin
    git fetch              # download everything available on origin
    git checkout foo       # now just checkout foo

Pretty easy, the trick is just in using `fetch`.

## Get a file from a specific revision

Suppose you want file `blub` exactly as it was in commit `MYHASH123`. Assume also `MYHASH123` is 4 commits behind `HEAD`.

    git show HEAD~4:blub

or

    git show MYHASH123:blub

will show you the file in `less`. Use output redirection, such as `git show HEAD~4:blub > blub.old` to store in file `blub.old`.


## Downgrade or delete a remote branch

Suppose your local HEAD on branch `<branch>` is rubbish, you have coded something and committed it already. Assume you want to get rid of the last `n` commits.
First, make sure you are on branch `<branch>`, then remove them locally:

    git reset --hard HEAD~n

Using `git log`, check that you are now on the right commit. Then force a downgrade on the remote (if you have `push`ed already):

    git push origin +<branch>

Now, origin should also be downgraded on branch `<branch>`. Here is how to remove a remote branch completely:

    git push origin :<branch>

