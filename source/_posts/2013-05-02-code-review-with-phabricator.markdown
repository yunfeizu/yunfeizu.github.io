---
layout: post
title: "在Phabricator中创建Code Review任务"
date: 2013-05-02 15:40
comments: true
keywords: code review, peer review, phabricator, svn, 代码审查, arcanist
categories: [code review, 工具]
---
{% img right /images/phabricator-differential.png %}

Phabricator集成了众多有用的软件开发的工具，现在为止只在Team中试验了用Differential做code review和用Diffusion查看代码。Phabricator提供了一个CLI工具Arcanist, 可以用命令行操作大多数phabricator功能。Phabricator推荐用户使用Arcanist创建和管理revision (在Differential中，每一个code review任务被称作一个revision)，当然用户可以完全可以使用web客户端，手动创建revision。

下面简要介绍下两种操作方式，所有操作都是在linux下，以svn为例。

<!-- more -->

##使用arcanist创建revision
###在linux下的安装arcanist
####1. 安装PHP和Git

unbuntu:

{% codeblock lang:bash %}
  sudo apt-get install php5 php5-curl git
{% endcodeblock %}

fedora: 

{% codeblock lang:bash %}
  sudo yum install php git 
{% endcodeblock %}

####2. 下载libphutil 和arcanist

{% codeblock lang:bash %}
  $ mkdir somewhere/
  $ cd somewhere/
  somewhere/ $ git clone https://github.com/facebook/libphutil.git
  somewhere/ $ git clone https://github.com/facebook/arcanist.git
{% endcodeblock %}

####3. 把arcanist/bin/加入PATH

{% codeblock lang:bash %}
  $ export PATH="$PATH:/somewhere/arcanist/bin/"
{% endcodeblock %}

或者建立一个软连接到可执行目录

{% codeblock lang:bash %}
  $ sudo ln -s somewhere/arcanist/bin/arc /usr/local/bin/arc
{% endcodeblock %}


###配置Project
####1.在Project 目录下创建.arcconfig 文件

{% codeblock lang:bash %}
  $ cd yourproject/
  yourproject/ $ $EDITOR .arcconfig
  yourproject/ $ cat .arcconfig
  {
    "project_id" : "yourprojectname",
    "conduit_uri" : "http://10.140.115.163/"
  }
{% endcodeblock %}

####2. 配置Credentials

{% codeblock lang:bash %}
  $ cd yourproject/
  yourproject/ $ arc install-certificate
  ...
{% endcodeblock %}
  
按照命令行提示用浏览器打开链接，使用Phabricator用户名密码登录，在命令行输入网页上提示的token, 完成验证
 

###使用arcanist 提交review diff

{% codeblock lang:bash %}
  $ cd yourproject/
    yourproject/ $ arc diff [paths]
    ...
{% endcodeblock %}


### 手动创建revision
####使用svn diff 产生patch

设置源文件上下文长度
{% codeblock lang:bash %}
  svn diff --diff-cmd=diff --extensions '-U 100' > review.diff 
{% endcodeblock %}

####上传diff文件到Phabricator

在differential中，点击右上角Create Diff
