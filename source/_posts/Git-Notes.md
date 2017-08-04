---
title: Git - Notes
date: 2017-08-02 14:19:40
tags: Git
categories: SCM
toc: true 
comments: false
comment: false
---
## Conflicts with modified files when switch to another branch 

It is common to work with multiple branches. For example, if we are working the file test.html:
1. Update test.html in master branch
2. Switch to testbranch branch, update and commit the changes to git
3. Switch to master branch, update the test.html and do not commit the changes Then, switch to testbranch but failed


    $ git init
    $ git status
    $ echo "Created at master branch" > test.html
    $ git add test.html
    $ git commit -m "Commit test.html at master branch"
    $ git checkout -b testbranch
    $ echo "Modified at testbranch" >> test.html
    $ git add .
    $ git commit -m "Commit at testbranch"
    $ git checkout master
    $ echo "Modified at master branch" >> test.html
    $ git checkout testbranch
    error: Your local changes to the following files would be overwritten by checkout:
    test.html
    Please, commit your changes or stash them before you can switch branches.
    Aborting

![Workflow](https://docs.google.com/drawings/d/1OfHSNc2aVpOdHyTT1z7sMNRG8K7Z0QwgKGbIARsatS8/pub?w=950&amp;h=780)

## Useful Commands
### Update remote url
    $ git remote -v
    origin  https://github.com/calvinypn/calvinypn.github.io (fetch)
    origin  https://github.com/calvinypn/calvinypn.github.io (push)
    $ git remote set-url origin https://github.com/pnyiu/pnyiu.github.io
    $ git remote -v
    origin  https://github.com/pnyiu/pnyiu.github.io (fetch)
    origin  https://github.com/pnyiu/pnyiu.github.io (push)

