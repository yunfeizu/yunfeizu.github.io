---
layout: post
title: "为Github账户设置SSH key"
date: 2013-04-10 10:11
comments: true
keywords: blog, octopress, github, pages, ssh key, 公开密匙，SSH加密
categories: [博客设置, 工具]
---
## 什么是SSH key
一直使用SSH连接服务器，但是对它的原理却不太了解。这次设置Octopress的时候，需要使用SSH 方式连接Github, 正好对SSH的工作方式做了下了解。（好像Github推荐使用HTTPS的方式访问repo, 以前Github受到过[SSH密匙攻击](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation)，之后升级了SSH key的安全措施，https方式视乎更方便安全，不过Octopress的设置文档中，我并没有找到怎么使用HTTPS连接Github） 
<!-- more -->
简单来说，SSH提供了两种级别的安全验证：

1. 第一种级别是基于密码的安全验证，知道账号和密码，就可以登陆到远程主机。Team的开发工作中，就是使用这种方式登陆编译服务器，或者开发机器。因为是在内网中，这种级别的安全验证已经足够了。
2. 第二种级别是基于[Public-key cryptography (公开密匙加密）](http://en.wikipedia.org/wiki/Public-key_cryptography)机制的安全验证，原理如下图所示：

{% img /images/public_key_cryptography.png %}

其优点在于无需共享的通用密钥，解密的私钥不发往任何用户。即使公钥在网上被截获，如果没有与其匹配的私钥，也无法解密，所截获的公钥是没有任何用处的。

## 产生SSH key
根据Github提供的help文档，具体过程如下
{% codeblock lang:bash %}
$ cd ~/.ssh
# Checks to see if there is a directory named ".ssh" in your user directory
{% endcodeblock %}
使用ssh-keygen产生新的key
{% codeblock lang:bash %}
$ ssh-keygen -t rsa -C "your_email@example.com"
# Creates a new ssh key using the provided email
Generating public/private rsa key pair.
Enter file in which to save the key (/home/you/.ssh/id_rsa):
{% endcodeblock %}
使用默认的文件名直接enter, 按提示输入密码（如果不提供密码，SSH将无密码连接，如果private key泄露可能会有安全问题）
{% codeblock lang:bash %}
Enter passphrase (empty for no passphrase): [Type a passphrase]
Enter same passphrase again: [Type passphrase again]
{% endcodeblock %}
密匙产生成功
{% codeblock lang:bash %}
Your identification has been saved in /home/you/.ssh/id_rsa.
Your public key has been saved in /home/you/.ssh/id_rsa.pub.
The key fingerprint is:
01:0f:f4:3b:ca:85:d6:17:a1:7d:f0:68:9d:f0:a2:db your_email@example.com
{% endcodeblock %}

## 上传public key到Github账户

1. 登录github
2. 点击右上方的Accounting settings图标
3. 选择 SSH key
4. 点击 Add SSH key

在出现的界面中填写SSH key的名称，填一个你自己喜欢的名称即可，然后将上面拷贝的~/.ssh/id_rsa.pub文件内容粘帖到key一栏，在点击“add key”按钮就可以了。
添加过程github会提示你输入一次你的github密码


## 设置SSH使用HTTPS的403端口
在局域网中SSH的22端口可能会被防火墙屏蔽，可以设置SSH使用HTTPS的403端口。

测试HTTPS端口是否可用
{% codeblock lang:bash %}
$ ssh -T -p 443 git@ssh.github.com
Hi username! You've successfully authenticated, but GitHub does not
provide shell access.
{% endcodeblock %}
编辑SSH配置文件 ~/.ssh/config 如下:
{% codeblock lang:bash %}
Host github.com
  Hostname ssh.github.com
  Port 443
{% endcodeblock %}
测试是否配置成功
{% codeblock lang:bash %}
$ ssh -T git@github.com
Hi username! You've successfully authenticated, but GitHub does not
provide shell access.
{% endcodeblock %}

## 多个Github账号的SSH key切换
如果在一台机器上要登陆多个Github账户，需要一些配置，虽然现在并没有用到，但是先记下来以备不时之需，过程参看[这里](http://justjavac.com/git/2012/04/13/multiple-ssh-keys.html)。
