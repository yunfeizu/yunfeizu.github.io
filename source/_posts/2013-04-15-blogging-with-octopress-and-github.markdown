---
layout: post
title: "使用Octopress和Github搭建Blog"
date: 2013-04-15 17:47
comments: true
categories: [博客设置, 工具]
---

Github 对程序员来说已经越来越重要了，不仅是托管自己的开源代码（我业余时间做的[小工具](https://github.com/yunfeizu/svnhistory)，自己的[vim配置](https://github.com/yunfeizu/dotfiles)已经都放到了github上），针对企业用户更有一整套的解决方案，虽然没有接触过github enterprise, 但是看到一些牛人的[讨论](http://www.infoq.com/cn/news/2013/02/weibo-on-git)，心向往之。对个人来说，github的的[Pages](http://pages.github.com/)服务无疑更加有用。用github搭建Blog的人越来越多，网上也有很多攻略。[Jekyll](http://jekyllrb.com/)是一个ruby写的静态站点生成器，它的作者是github开发者之一，Github Pages 支持将就Jekyll代码转换成静态站点。借助[Jekyll-Bootstrap](http://jekyllbootstrap.com/)可以很方便的搭建一个基本的Blog, 中文可以参照[这里](http://zyzhang.github.io/blog/2012/08/29/blogging-like-a-geek/)。但是为了安全原因，Pages不支持第三方的Jekyll插件，所以Blog的功能受到限制。虽然基础功能已经够用，但是在折腾的几次之后，最后决定还是用更灵活的Octopress。
<!-- more -->

octopress一样是基于Jekyll的Blog框架，已经集成了很多插件，并提供了部署到Github的支持，octopress的具体介绍,查看[官网](http://octopress.org/)。相比Jekyll-Bootstrap, 功能更加强大，可配置性更高，但是由于使用了很多第三方插件，不能使用Pages自动把markdown文件转化成网页，需要自己生成静态网页，并部署到Pages上。每次添加新的东西，都要"编译"，有人已经抱怨上百篇文章，产生网页耗时很久了，但是不管怎样，现阶段Octopress无疑是最佳选择。

## 创建github repository
Github Pages分为两种：一种是个人和组织的Pages, 一种是项目的Pages。它们只有[细微的差别](https://help.github.com/articles/user-organization-and-project-pages), 而用来发布Blog的是第一种。需要在Github上创建一个命名格式为username/username.github.io的repository（Pages以前使用的是username.github.com的子域名。最近因为安全原因，迁移到了新域名github.io下。以前创建的.com的repo仍然可以工作，但是[Github推荐](https://help.github.com/articles/should-i-rename-username-github-com-repositories-to-username-github-io)用户新建repo的时使用.io的命名）, 生成的静态网页都要上传到这个repo的master分支。

由于octopress 部署Blog到Pages的时候使用ssh连接Github, 需要设置好Github账户的SSH Key,在局域网中，ssh的22端口可能被屏蔽，可以设置SSH使用HTTPS的443端口，具体步骤在[这里](/blog/2013/04/10/setup-github-ssh-key/)。

## 安装配置Octopress
Octopress的具体安装，[官方文档](http://octopress.org/docs/setup/)讲的很清楚，不再累述。但是在ubuntu 12.04下按照它给出的方法安装Ruby时会有问题，解决方法请看我的另一篇博客[《在ubuntu 12.04 上安装ruby》](/blog/2013/04/01/install-ruby-on-ubuntu/)。

安装成功以后，按照(文档)(http://octopress.org/docs/deploying/github/)配置Octopress deploy到Github Pages。

这个过程中会创建两个branch

1. **master** branch 用来发布产生静态网页（所有_deploy文件夹下的文件）
2. **source** branch 用存放"源代码"（markdown文本，配置文件，插件）

发布博客：
{% codeblock lang:bash %}
rake generate
rake deploy
{% endcodeblock %}

发布之后将所有"源代码"上传
{% codeblock lang:bash %}
git add .
git commit -m 'your message'
git push origin source
{% endcodeblock %}

## Octopress命令汇总：

* rake new_post[‘article name’] 生成博文框架，然后修改生成的文件即可
* rake generate 生成静态文件
* rake watch 检测文件变化，实时生成新内容
* rake preview 在本机4000端口生成访问内容
* rake deploy 发布文件
