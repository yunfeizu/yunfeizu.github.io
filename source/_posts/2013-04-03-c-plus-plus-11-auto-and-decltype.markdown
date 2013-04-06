---
layout: post
title: "c++11: auto 和 decltype"
date: 2013-04-03 16:00
comments: true
categories: [C++11, learning cycle]
---

感谢curise的精彩讲解，内容很短但是所有相关细节都讲的很清楚。learning cycle上有人担心不写明类型会使程序易读性变差，造成不必要的错误。会上时间有限，并没有做深入讨论，这里有必要说一下。

首先，我认为有这种担心的人是错误的理解了auto和decltype的定位和C+＋11引入他们的目的。按我的理解，C+＋引入自动的类型推导，并不是在向动态语言靠拢，而是在保持类型安全的前提下提高代码的可读性，易用性和通用性。要理解这点就必须对C+＋泛型编程和为什么要泛型有一定认识，推荐阅读：[刘未鹏：泛型编程：源起、实现与意义](http://blog.csdn.net/pongba/article/details/2544894)。可以说模板和模板元编程的发展，带来了全新的C+＋，编译期多态的能力，使得C+＋代码可以做到通用行和执行效率的统一，这也是C+＋能在一些领域依然不可替代的原因。而且C+＋设计理念与动态语言完全不同，所以C+＋的大牛们讨论了十几年，不是要弱化类型，而是进一步加强和改进模板编程，因为模板编程有个很大的问题：难。不仅难写，而且难用。因为缺少variadic templates, concept等支持，模板库的作者被迫使用了很多tricky的方法，难度之大已经不是一般程序员能驾驭的了。再说难用，使用一个模板库，经常要写很长的表达式，不小心写错了，编译器就给爆出一堆近似与乱码的错误信息，调试起来更是头疼。C+＋引入auto, 就是要让模板的编写和使用更加容易。auto并不是说这个变量的类型不定，或者在运行时再确定，而是说这个变量在编译时可以由编译器推导出来，使用auto和decltype 只是占位符的作用，告诉编译器这个变量的类型需要编译器推导，从而解放了程序员打更多无意义的字符，避免了手动推导某个变量的类型，甚至有时候需要很多额外的开销才能绕过的类型声明。 

比如很多时候在用泛型算法的时候，并不在乎变量的类型，只关心这个变量能作什么。比如下面例子中：
{% codeblock lang:cpp %}
map< int, map<int,int> > _Map;
  // see the verbose for defining a const iterator of this map
map<int, map<int,int>>::const_iterator itr1 = _Map.begin();
 // now with auto our life gets simplified
const auto itr2 = _Map.begin(); 
{% endcodeblock %}
我只需要知道itr2 是迭代器，能用在一些算法中就够了，完全不需要打出长长的类型，万一打错，都不一定知道错在哪里，而且以后如果要修改_Map 的类型，所有的迭代器都要改。

To be continued ........
