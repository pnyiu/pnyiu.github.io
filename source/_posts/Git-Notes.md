---
title: Git - Notes
date: 2017-08-02 14:19:40
tags: Git
categories: SCM
toc: true 
comments: false
comment: false
---
## Git submodules
I will use my blog site to illustrate the git submodules features. Recently, I have setup a blog site by using [hexo](https://hexo.io/). The blog will use another hexo theme - [freemind](https://github.com/wzpan/hexo-theme-freemind) from github. First, I have forked the project as https://github.com/pnyiu/hexo-theme-freemind:
1. Scenario one - include other git project in existing git project
{% codeblock line_number:false lang:bash %}
git clone https://github.com/pnyiu/hexo-theme-freemind.git themes/freemind
{% endcodeblock %}
{% codeblock line_number:false lang:bash %}
.
└── blog
    ├── _config.yml
    ├── db.json
    ├── .deploy_git
    ├── .git
    ├── .gitignore
    ├── node_modules
    ├── package.json
    ├── public
    ├── scaffolds
    ├── source
    └── themes
        └── freemind
            ├── _config.yml
            ├── .git
            ├── .gitignore
            ├── languages
            ├── layout
            ├── LICENSE
            ├── package.json
            ├── README.md
            └── source
{% endcodeblock %}
The blog repository simply ignore the freemind repository [stackoverflow](https://stackoverflow.com/questions/10360342/git-clone-into-another-existing-git-repo).
2. **Scenario two** - use git submodules   
   {% blockquote Pro Git https://git-scm.com/book/en/v2/Git-Tools-Submodules "7.11 Git Tools - Submodules" %}
   Submodules allow you to keep a Git repository as a subdirectory of another Git repository. This lets you clone another repository into your project and keep your commits separate.
   
   It’s quite likely that if you’re using submodules, you’re doing so because you really want to work on the code in the submodule at the same time as you’re working on the code in the main project (or across several submodules). Otherwise you would probably instead be using a simpler dependency management system (such as Maven or Rubygems).   
   {% endblockquote %}
   Use `git submodule add <repo_url>` to add submodule as below:
   {% codeblock line_number:false lang:bash %}   
   $ git submodule add https://github.com/pnyiu/hexo-theme-freemind.git themes/freemind
   Cloning into 'themes/freemind'...
   remote: Counting objects: 1390, done.
   remote: Compressing objects: 100% (22/22), done.
   remote: Total 1390 (delta 9), reused 5 (delta 0), pack-reused 1367
   Receiving objects: 100% (1390/1390), 1.91 MiB | 302.00 KiB/s, done.
   Resolving deltas: 100% (814/814), done.
   Checking connectivity... done.
   $ tree -L 1 -a
   .
   ├── _config.yml
   ├── db.json
   ├── .deploy_git
   ├── .git
   ├── .gitignore
   ├── .gitmodules
   ├── node_modules
   ├── package.json
   ├── public
   ├── scaffolds
   ├── source
   └── themes
   $ cat .gitmodules
   [submodule "themes/freemind"]
          path = themes/freemind
          url = https://github.com/pnyiu/hexo-theme-freemind.git
   {% endcodeblock %}
   - New file `.gitmodules` is created to keep track the submodules path and url.
   - Git would get the changes and update the files in the subdirectory but will leave the sub-repository    in what’s called a `detached HEAD` state. This means that there is no local working branch (like `master`, for example) tracking changes.
   - Doesn’t track submodule contents when you're not in that directory
   - For **new project** with submodules, use (`git submodule init` and `git submodule update`) or `git clone --recursive` to initialize and fetch all data from that project and submodules.
   
## Conflicts with modified files when switch to another branch 

It is common to work with multiple branches. For example, if we are working the file test.html:
1. Update test.html in master branch
2. Switch to testbranch branch, update and commit the changes to git
3. Switch to master branch, update the test.html and do not commit the changes Then, switch to testbranch but failed
<!-- more -->
{% codeblock line_number:false lang:bash %}
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
{% endcodeblock %}
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

