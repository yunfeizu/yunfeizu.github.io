---
layout: post
title: "c++11: auto 和 decltype"
date: 2013-04-06 16:00
comments: true
keywords: C++, C++11, C++0x, auto, decltype, 自动类型推导
categories: [C++11, learning cycle]
---

感谢curise的精彩讲解，内容很短但是所有相关细节都讲的很清楚。learning cycle上有人担心不写明类型会使程序易读性变差，造成不必要的错误。会上时间有限，并没有做深入讨论，这里有必要说一下。
<!-- more -->

首先，我认为有这种担心的人是错误的理解了auto和decltype的定位和C+＋11引入他们的目的。C+＋引入自动的类型推导，并不是在向动态语言靠拢，通过弱化类型实现编程的灵活性。而是在保持类型安全的前提下提高代码的可读性，易用性和通用性。

要理解这点就必须对C+＋泛型编程（GP）和为什么要泛型有一定认识，推荐阅读：[刘未鹏：泛型编程：源起、实现与意义](http://blog.csdn.net/pongba/article/details/2544894)。可以说模板和模板元编程的发展，带来了全新的C+＋，编译期多态的能力，使得C+＋代码可以做到通用行和执行效率的统一，这也是C+＋能在一些领域依然不可替代的原因。C+＋11的中很多改进就是要进一步加强和改进模板编程，因为模板编程有个很大的问题：难。不仅难写，而且难用。因为语言本身缺少variadic templates, concept等支持，模板库的作者被迫使用了很多tricky的方法，难度之大已经不是一般程序员能驾驭的了。而使用一个模板库，经常要写很长的表达式，不小心写错了，编译器就给爆出一堆近似与乱码的错误信息，调试起来更是头疼。C+＋引入auto, 就是要让模板的编写和使用更加容易。

auto并不是说这个变量的类型不定，或者在运行时再确定，而是说这个变量在编译时可以由编译器推导出来，使用auto和decltype只是占位符的作用，告诉编译器这个变量的类型需要编译器推导，借以消除C+＋变量定义和使用时的类型冗余，从而解放了程序员打更多无意义的字符，避免了手动推导某个变量的类型，甚至有时候需要很多额外的开销才能绕过的类型声明。

比如很多时候在用泛型算法的时候，并不在乎变量的类型，只关心这个变量能作什么。比如下面例子中：
{% codeblock lang:cpp %}
map< int, map<int,int> > _Map;
// see the verbose for defining a const iterator of this map
map<int, map<int,int>>::const_iterator itr1 = _Map.begin();
// now with auto our life gets simplified
const auto itr2 = _Map.begin(); 
{% endcodeblock %}
我只需要知道itr2 是迭代器，能用来访问容器就够了，完全不需要打出长长的类型，万一打错，都不一定知道错在哪里，而且以后如果要修改_Map 的类型，所有的迭代器都要改, 完全不符合DRY原则。而使用auto 就避免这些问题。

auto并不是让程序员少打一些字这么简单，看下面例子：
{% codeblock lang:cpp %}
struct A
{
    typedef int Type;
    int a;
    int b;
};

struct B
{
   typedef double Type;
   double a;
   double b;
};

template<T>
void some_func(T &t)
{
    typedef typename T::type Type;

    Type c = t.a + t.b;
    ....
}
{% endcodeblock %}
C+＋98中，为了初始化变量C, 必须提供它的类型，所以我们用了三个typedef来做类型推导，在更复杂的模板代码中这个实现会更复杂的，这么做不仅非常麻烦，而且对用户代码是有侵入性的（使用这个函数的class 必须都定义自己的typedef）。其实在编译时，编译器完全可以自己推导出类型，只是为了符合C+＋类型声明和初始化的语法，才不得不让程序员替编译器做这些事。
{% codeblock lang:cpp %}
struct A
{
    int a;
    int b;
};

struct B
{
   double a;
   double b;
};

template<T>
void some_func(T &t)
{
    auto c = t.a + t.b;
    ....
}
{% endcodeblock %}

所以顺理成章，C+＋11引入了auto, 可以交给编译器的工作，都交给编译器做，让程序员更focus在实现代码逻辑上。

关于何时应该使用auto, Andrei Alexandrescu, Scott Meyers 和Herb Sutter在C++ and Beyond 2012的Ask Us Anything panel上给出了意见：鼓励在所有地方使用auto,除非你需要做一个类型转换。当然他们做了很多解释，这段[视频](http://channel9.msdn.com/Shows/Going+Deep/C-and-Beyond-2012-Scott-Andrei-and-Herb-Ask-Us-Anything#time=25m03s)值得多看几遍。

相对与auto, decltype 更有针对性，具体用法参照里learning cycle PPT中的例子，这里不再累述。一句话就是：auto是为所有人准备的，而decltype是提供给模板开发者的。

下面是一些auto和decltype 的用法细节，摘抄自cruise的PPT:

auto variables have the type of their initializing expression:
{% codeblock lang:cpp %}
auto x1 = 10;
// x1: int
std::map<int, std::string> m;
auto i1 = m.begin();
// i1: std::map<int, std::string>::iterator
{% endcodeblock %}
const/volatile and reference/pointer adornments may be added:
{% codeblock lang:cpp %}
const auto *x2 = &x1; // x2: const int*
const auto& i2 = m; // i2: const std::map<int, std::string>&
{% endcodeblock %}
To get a const_iterator, use the new cbegin container function:
{% codeblock lang:cpp %}
auto ci = m.cbegin();
// ci: std::map<int, std::string>::const_iterator
{% endcodeblock %}

For variables not explicitly declared to be a reference:

* Top-level consts/volatiles in the initializing type are ignored.
* Array and function names in initializing types decay to pointers.

{% codeblock lang:cpp %}
const std::list<int> li;
auto v1 = li;
// v1: std::list<int>
auto& v2 = li;
// v2: const std::list<int>&
float data[BufSize];
auto v3 = data;
// v3: float*
auto& v4 = data;
// v4: float (&)[BufSize]
{% endcodeblock %}

Examples from earlier:
{% codeblock lang:cpp %}
auto x1 = 10;
// x1: int
std::map<int, std::string> m;
auto i1 = m.begin();
// i1: std::map<int, std::string>::iterator
const auto *x2 = &x1;
// x2: const int* (const isn’t top-level)
const auto& i2 = m;
// i2: const std::map<int, std::string>&
auto ci = m.cbegin();
// ci: std::map<int, std::string>::const_iterator
{% endcodeblock %}

Yields the type of an expression without evaluating it.
{% codeblock lang:cpp %}
int x, *ptr;
decltype(x) i1; // i1’s type is int
decltype(ptr) p1; // p1’s type is int*
std::size_t sz = sizeof(decltype(ptr[44])); // sz = sizeof(int);
                                           // ptr[44] not evaluated
{% endcodeblock %}
Fairly intuitive, but some quirks, e.g., parentheses can matter:
{% codeblock lang:cpp %}
struct S { double d; }; // double
const S* p; 
... 
decltype(p->d) x1; 
decltype((p->d)) x2; // const double&
{% endcodeblock %}

Primary use: template return types that depend on parameter types.
Common for forwarding templates:
{% codeblock lang:cpp %}
template<typename F, typename... Ts>
auto logAndInvoke(std::ostream& os,
F&& func, Ts&&... args) ->
decltype(func(args...))
{
os << std::chrono::system_clock::now();
return func(args...);
}
{% endcodeblock %}
Also in math-related libraries:
{% codeblock lang:cpp %}
template<typename T1, typename T2>
auto mult(T1&& a, T2&& b) ->
decltype(a * b)
{
return a * b;
}
{% endcodeblock %}

