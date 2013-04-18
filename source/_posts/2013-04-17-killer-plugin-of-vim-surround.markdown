---
layout: post
title: "vim中的杀手级插件: surround"
date: 2013-04-17 14:48
comments: true
keywords: vim, plugin, surround, text object, motion
categories: [vim]
---

大多数编程语言的语法都用到了配对符号surrounding: (), [], {}, <>, '', ""，标记语言xml html 等更是完全依赖与这种语法。正常输入时，所有的编辑器都能胜任，大部分会在你输入一个括号时帮你补全另一半。vim 也有这样的插件[auto-pair](https://github.com/jiangmiao/auto-pairs), 但是这个不是重点，真正的难题是当你需要为已一些存在的代码加上括号，删除一对括号但保留其中的内容，或者把一对()改成[]，又或者把`<div></div>`改成`<p></p>`。
<!-- more -->
这些操作虽然改动很小，但是却需要你精确的找到要修改的地方，修改后，再找到匹配的地方，修改另一半。对一般编辑器，需要两次用鼠标定位并用键盘修改，视乎并不复杂，但是不要忘了，键盘上打出这些配对符号大多是需要按住Shift的，通常你需要两只手同时操作键盘，这就意味着你的手需要在鼠标和键盘之间做三次切换，每次从键盘换回鼠标，你都要让重新眼睛适应鼠标的位置。对一个小修改，这些操作不仅很浪费时间，更加影响心情。当然你可以不用鼠标而用方向键移动光标，效果好一些，但是一样需要你的手在主键盘和小键盘之间切换，而且如果两个配对符号距离较远，用方向键并不容易。

对vim来说，当然不能容忍这么低效的方式。对于修改括号，我曾经用一种方法：先定位到前半个括号通常用f，使用括号匹配%移动到后半个括号，修改，然后\` \`跳回前半个，修改完成, 所有的输入：``f ( % r ] ` ` r [ `` ，删除括号： ``f ( % x ` ` x``, 视乎还可以接受，但是如果这些括号跨多行或者是一些复杂的tag，事情就难办了。直到发现了大牛[Tim Pope](http://www.vim.org/account/profile.php?user_id=9012)的[surround](https://github.com/tpope/vim-surround)的插件，一切都迎刃而解了。（对的就是那个写了[pathogen](https://github.com/tpope/vim-pathogen)的家伙，他一个人就贡献了30多个vim插件，仰视之）。[使用vundle安装](/blog/2013/04/12/killer-plugin-of-vim-vundle/)非常简单，就不再说了，直接贴出文档中的使用方法


### 示例
{% codeblock%}
 Old text                  Command     New text ~
  "Hello *world!"           ds"         Hello world!
  [123+4*56]/2              cs])        (123+456)/2
  "Look ma, I'm *HTML!"     cs"<q>      <q>Look ma, I'm HTML!</q>
  if *x>3 {                 ysW(        if ( x>3 ) {
  my $str = *whee!;         vlllls'     my $str = 'whee!';
  <div>Yo!*</div>           dst         Yo!
  <div>Yo!*</div>           cst<p>      <p>Yo!</p>
{% endcodeblock %}
解释一下，*代表当前光标位置，添加替换时使用后半括号)]}，添加的括号和内容间就没有空格（如第2个示例），反之会在内容前后添加一个空格（如第4个实例）。第6个示例中的t代表一对HTML或者xml tag。其他表示范围的符号：w代表word, W代表WORD(被空格分开的连续的字符窜），p代表paragraph。
###命令列表
{% codeblock%}
Normal mode
-----------
ds  - delete a surrounding
cs  - change a surrounding
ys  - add a surrounding
yS  - add a surrounding and place the surrounded text on a new line + indent it
yss - add a surrounding to the whole line
ySs - add a surrounding to the whole line, place it on a new line + indent it
ySS - same as ySs

Visual mode
-----------
s   - in visual mode, add a surrounding
S   - in visual mode, add a surrounding but place text on new line + indent it

Insert mode
-----------
<CTRL-s> - in insert mode, add a surrounding
<CTRL-s><CTRL-s> - in insert mode, add a new line + surrounding + indent
<CTRL-g>s - same as <CTRL-s>
<CTRL-g>S - same as <CTRL-s><CTRL-s>
{% endcodeblock %}

### Vim基于surrounding的文本编辑
提到了surround插件，不得不提一下Vim中广为人知的对surrounding内文本的编辑功能，其实surround插件就是对Vim这部分功能的增强。原理和细节请参照vim中的[text-object motion](http://vimdoc.sourceforge.net/htmldoc/motion.html)，这里只列举一些常见用法。可以不夸张的说，任何习惯了vim中[operation+motion](http://blog.carbonfive.com/2011/10/17/vim-text-objects-the-definitive-guide/)操作的人，都会上瘾的，其他编辑器都是个渣了。

以修改surrounding内文本为例：
{% codeblock%}
ci[ ci( ci< ci{ 删除一对 [], (), <>, 或{} 中的所有字符并进入插入模式
ci” ci’ ci` 删除一对引号字符 ”  ‘ 或 ` 中所有字符并进入插入模式
cit 删除一对 HTML/XML 的标签内部的所有字符并进入插入模式
{% endcodeblock %}

其他常见operation
{% codeblock%}
ci: 例如，ci(，或者ci)，将会修改()之间的文本；
di: 剪切配对符号之间文本；
yi: 复制；
ca: 同ci，但修改内容包括配对符号本身；
da: 同di，但剪切内容包括配对符号本身；
ya: 同yi，但复制内容包括配对符号本身。
PS. dib等同于di(。diB等同于di{。
{% endcodeblock %}
