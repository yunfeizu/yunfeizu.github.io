---
layout: post
title: "Vim中的杀手级插件：VimIM"
date: 2013-04-23 17:45
comments: true
keywords: vim, plugin, vimim, 输入法，中文输入
categories: vim
---
###一生的Vim路，为了一个可以用的编辑器
看到过一篇文章叫[《Just Use Sublime Text》](http://delvarworld.github.io/blog/2013/03/16/just-use-sublime-text/), 有人翻译成了[中文](http://www.zfanw.com/blog/just-use-sublime-text.html)。作者用Vim写文章，痛陈Vim十大罪状，劝别人不要用Vim。真是典型的Vim症候群啊，恨它却又离不开了。

作为一个Vim"深毒"用户，在开始用octopress写博客时，自然没有考虑过其他可能性，vim编写markdown也无比犀利。但是有一个问题却突然尖锐起来，vim的世界是基于英语的，支持utf8都没几年。虽然可以输入中文，但是在模式切换间，一切都混乱了，不停的在输入法的快捷键和ESC间倒腾，完全不是vim的风采。**Vim的伟大之处就是给了技术宅无限的折腾空间，让他们在不停的自虐中，在承受痛苦和获得满足间不断循环。** 于是Vimim诞生了，一个vim-script实现的VIM内置输入法。

<!-- more -->

### 伟大又让人抓狂的VimIM
其实对VimIM, 我是要吐槽的，[官方文档](http://vimim.googlecode.com/svn/vimim/vimim.html#vimimrc)让人太纠结了，每次都让我怀疑自己的理解能力，我怎么就看不明白怎样做才能把它用起来呢。还好有很多先行者留下的经验，看[这里](http://www.linuxsir.org/bbs/thread360702.html?pageon=1#2051139)，[这里](http://crane.is-programmer.com/posts/12726)，或者[这里](http://www.douban.com/review/5598805/)。

虽然文档让人抓狂，有错误时查不到帮助，五花八门的子功能让人莫衷一是，而且很久没有更新过了，但是不可否认VimIM是一个伟大的插件，是0到1的突破。它让在vim中输入中文不再是障碍，的确称得上杀手级的插件。

### 基本配置和操作
1. 下载[vimim.vim](http://www.vim.org/scripts/script.php?script_id=2506)文件，放到`～/.vim/plugin` 文件夹下
2. 下载[词库](http://vimim.googlecode.com/svn/trunk/plugin/vimim.gbk.bsddb) 或者一个好用的[第三方词库](https://code.google.com/p/vimim-data/downloads/list)，和vimim.vim放在一起。
3. 用Vim编译文本，进入插入模式，快捷键`Ctrl+_`, 可以看到进入INSERT(lang)模式，跟着感觉走，就像其他输入法一样，开始打中文吧。

介于经常连不上google code, 我把vimim.vim和词库都放在了我在Github上的[vim配置](https://github.com/yunfeizu/dotfiles)中。其它的云输入或者点石成金之类的功能，如果有需求再去研究吧。
