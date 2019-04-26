---
title: Hexo Notes
date: 2017-08-08 12:03:37
tags:
- Hexo
toc: true
categories:
- Hexo
---
## Installation 
### Basic
{% codeblock line_number:false lang:bash %}
# Install Node.js
sudo apt-get install nodejs

# Install node package manager (npm)
sudo apt-get install npm

# Install git 
sudo apt-get install git # yum install git-core 

# Install hexo
npm install -g hexo-cli
{% endcodeblock %}
### Hexo site folder and install theme
In this blog, I will install [freemind](https://hexo.io/themes/):
{% codeblock line_number:false lang:bash %}
$ hexo init blog #blog is the folder to contains all hexo related files
$ cd blog
$ npm install
$ git clone https://github.com/wzpan/hexo-theme-freemind.git themes/freemind
{% endcodeblock %}
<!-- more -->
### Update site and theme `_config.yml`:
{% codeblock site._config.yml line_number:false lang:yml %}
title: Calvin's Home
subtitle:
description:
author: Calvin Yiu
email: calvin.ypn@gmail.com
language: default
timezone: Asia/Hong_Kong
...
i18n_dir: :lang
...
theme: freemind # Use freemind as the theme
...
deploy:
  type: git
  repo: https://github.com/pnyiu/pnyiu.github.io.git
{% endcodeblock %}

{% codeblock theme._config.yml line_number:false lang:yml %}
links:
  - title: "My Github"
    url: http://www.github.com/pnyiu
    intro: "My Github account."
    icon: "fa fa-github"
  - title: "My LinkedIn"
    url: https://www.linkedin.com/in/pnyiu/
    intro: "My Linkin account."
    icon: "fa fa-linkedin"
...
widgets:
#- search
#- recent_comments
- category
- tagcloud
- recent_posts
- links
...
# Analytics
google_analytics:
  enable: true
  siteid: UA-103782633-2
baidu_tongji:
  enable: false
  siteid:
...
# share widgets
bdshare: false
jiathis: false
{% endcodeblock %}
### Create tags, categories and about pages
{% codeblock line_number:false lang:bash %}
$ hexo new page tags
$ hexo new page about
$ hexo new page categories
{% endcodeblock %}

Update the layout of tags/index.md and categories/index.md to `tags` and `categories` respectively.
{% codeblock line_number:false lang:bash %}
$ tree -L 2
.
├── about
│   └── index.md
├── categories
│   └── index.md
└── tags
    └── index.md
$ cat tags/index.md
title: Tags
layout: tags
---
$ cat categories/index.md
---
title: categories
layout: categories
date: 2017-08-02 16:51:54
---
{% endcodeblock %}



## Basic configuratons
### Website icon - favicon
Create an image in png format and save it under the path specified in theme.config:
{% codeblock _config.yml line_number:false lang:yml %}
favicon: "favicon/favicon.png"
{% endcodeblock %}

## Install filters
### hexo-filter-flowchart

{% codeblock npm line_number:false "https://github.com/bubkoo/hexo-filter-flowchart" hexo-filter-flowchart %}
npm install --save hexo-filter-flowchart
{% endcodeblock %}

{% label Optional info %}

You may download the required Javascript files to your local server: 

1. Download [flowchart.js](http://flowchart.js.org/) and [Raphaël](http://dmitrybaranovskiy.github.io/raphael/).
2. Copy the above two files to \themes\freemind\source\js (substitute freemind to match your theme folder name)

If you choose to use the files saved locally, then you have to modify site _config.yml:
{% codeblock _config.yml line_number:false lang:yml %}
flowchart:
  raphael: /js/raphael.js  # optional, the source url of raphael.js
  flowchart: /js/flowchart.js # optional, the source url of flowchart.js
  options: # options used for `drawSVG`  
{% endcodeblock %}

If it works, then you will see below flowchart:
```flow
st=>start: Start|past:>http://www.google.com[blank]
e=>end: End:>http://www.google.com
op1=>operation: My Operation|past
op2=>operation: Stuff|current
sub1=>subroutine: My Subroutine|invalid
cond=>condition: Yes
or No?|approved:>http://www.google.com
c2=>condition: Good idea|rejected
io=>inputoutput: catch something...|request

st->op1(right)->cond
cond(yes, right)->c2
cond(no)->sub1(left)->op1
c2(yes)->io->e
c2(no)->op2->e
```

## Useful hexo or markdown syntax
### Delimeter for excerpt and full content
{% codeblock line_number:false %}
<!-- more -->
{% endcodeblock %}
### Front-matter
{% codeblock lang:yml line_number:false %}
toc: true # Default: off
tags:
- Tag1
- Tag2
categories:
- Cat1
- Cat2
{% endcodeblock %}
### Commonly used commands
{% codeblock line_number:false lang:bash %}
# Start hexo server
# hexo server

# Generate a new page
hexo new "title"

# Generate static files
hexo generate

# Deploy to github
hexo deploy

{% endcodeblock %} 