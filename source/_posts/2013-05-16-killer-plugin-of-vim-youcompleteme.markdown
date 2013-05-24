---
layout: post
title: "vim中的杀手级插件: YouCompleteMe"
date: 2013-05-16 11:27
comments: true
keywords: vim, plugin, ycm, youcompleteme, auto completion, 代码补全
categories: vim
---
### Vim代码补全现状
在漫长的Vim发展历史中，代码补全一直是比较被忽视的环节，相比众多IDE，vim本身的代码提示功能包括其众多补全插件显得无比简陋, 这是因为vim的先天不足，它是文本编译器，不能理解程序语意。引用王垠的一段文字：
{% blockquote 《编辑器与IDE》 http://www.yinwang.org/blog-cn/2013/04/20/editor-ide/%}
“文本编辑器”这种东西一般都不真正的理解程序语言。很多 Emacs 和 vi 的用户以为用 etags 和 ctags 这样的工具就能让他们“跳转到定义”，然而这些 tags 工具其实只是对程序的“文本”做一些愚蠢的正则表达式匹配。它们根本没有对程序进行 parse，所以其实只是在进行一些“瞎猜”。简单的函数定义它们也许能猜对位置，但是对于有重名的定义，或者局部变量的时候，它们就力不从心了。
{% endblockquote %}
或许对于python, PHP等动态语言，因为本身的语言特性和丰富的工具支持，也能做到不错的补全效果，但对于C/C+＋代码的补全， AutoComplPop, omnicppcomplete, neocomplcache等插件的确都是在"瞎猜"。
<!-- more -->

还有一个原因是没有大牛来做这些功能。在漫长的Vim或emacs历史中，它都是掌握在少数精英程序员手里，扩展它都需要一定的专业知识，我辈普通程序员只是在使用它。而代码补全并不是大牛们的必须，我曾经和一个瑞典的PSE做Pair Programming, 只使用emacs和grep等linux命令, 他就可以在整个工程里穿梭自如，常用库的调用了然于心，代码结构清晰，几乎没有语法和拼写错误。估计他看到我花里胡哨的vim, 还用代码提示，心里也是一阵鄙视吧。所以尽管大牛们让emacs可以煮咖啡，vim可以发微博，但是vim和emacs的代码提示一如既往的烂了二三十年。

在Apply公司支持的[LLVM/clang](http://clang.llvm.org/)诞生后，事情迎来了转机。clang强大语义分析能力，为C/C+＋/Object-C源代码级别的分析和转化提供了可能，基于clang的语意补全插件开始在vim和emacs上出现。

### YouCompleteMe

[YouCompleteMe](http://valloric.github.io/YouCompleteMe/)是一个比较新Vim代码补全插件，可以基于clang为C/C+＋代码提供代码提示。它安装配置简单，Bug 很少。 对C/C++来说youcompleteme现在应该是最好选择，借助clang的强大功能，补全效率和准确性极高，而且可以模糊匹配（见下面的demo）。不管你的C++代码用什么怪异的写法，只要能编译通过，都能补全，即使是C++11的lambda和auto都没有障碍，比codeblock这些根据tag index补全的IDE都要强大。

{% img 官网的demo /images/ycm-demo.gif %}

### YCM的安装配置
YCM需要最新版本Vim(7.3.584)的支持，从代码编译Vim的方法看[这里](https://github.com/Valloric/YouCompleteMe/wiki/Building-Vim-from-source)。
[使用Vundle安装](/blog/2013/04/12/killer-plugin-of-vim-vundle/) youcompleteme后，需要先编译才能使用。
{% codeblock lang:bash %}
cd ~/.vim/bundle/YouCompleteMe
./install.sh --clang-completer
{% endcodeblock %}
语意补全要正确工作，需要配置好.ycm_extra_conf.py文件，模板在[这里](https://gist.github.com/locojay/4950253)。可以把这个文件放在项目的根目录下，打开项目文件时，YCM会循环向上搜索并加载这个文件，或者在vimrc文件中加入
{% codeblock lang:bash %}
let g:ycm_global_ycm_extra_conf = 'your path to .ycm_extra_conf.py'
{% endcodeblock %}
如果.ycm_extra_conf.py中include path等配置没有问题，YCM已经可以工作了。

YCM更新很频繁，最近版本已经可以和UltiSnip整合了。

