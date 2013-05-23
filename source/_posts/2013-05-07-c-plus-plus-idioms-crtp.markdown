---
layout: post
title: "C++ Idioms: CRTP"
date: 2013-05-07 15:09
comments: true
keywords: C++ Idioms, CRTP, C++ CRTP, Curiously Recurring Template Pattern, 奇异递归模板模式
categories: C++
---

CRTP是奇异递归模板模式(Curiously Recurring Template Pattern)的缩写，是[Jim Coplien](http://en.wikipedia.org/wiki/Jim_Coplien)在1995年提出的C+＋惯用法。尽管已经接近20年过去了，当使用boost, WTL等C+＋库的时候，依然能经常见到它的应用。

CRTP的基本形式：
{% codeblock lang:cpp %}
class T : public X<T> {…};
{% endcodeblock %}
通过在父类中传入子类的类型，实现在编译期的多态（静态多态）。当然编译期的多态比运行时通过虚函数实现的多态（动态多态）效率要高，但是这并不是CRTP模式最重要的优点。既然名字叫"奇异"，当然有奇异之处。看下面著名的类计数器代码：
<!-- more -->
{% codeblock lang:cpp %}
template <typename T>
struct counter
{
    static int objects_created;
    static int objects_alive;
 
    counter()
    {
        ++objects_created;
        ++objects_alive;
    }
protected:
    ~counter() // objects should never be removed through pointers of this type
    {
        --objects_alive;
    }
};
template <typename T> int counter<T>::objects_created( 0 );
template <typename T> int counter<T>::objects_alive( 0 );
 
class X : counter<X>
{
    // ...
};
 
class Y : counter<Y>
{
    // ...
};
{% endcodeblock %}

需要注意的是，class X和Y是分开计数的，`couter<X>` 和 `couter<Y>` 是不同的基类。这个object counter的问题最早在Meyers Scott的["Counting Objects in C++"](http://www.drdobbs.com/cpp/counting-objects-in-c/184403484)中提出的，使用CRTP的方法，代码简洁到了奇异的地步，但这个并不是典型的应用场景。

个人认为，CRTP的最大用途，还是在于简化接口，提高类的扩展性。看一下boost::enable_shared_from_this 的实现：
{% codeblock lang:cpp %}
template<class T> class enable_shared_from_this
{
protected:

    enable_shared_from_this() BOOST_NOEXCEPT
    {
    }

    enable_shared_from_this(enable_shared_from_this const &) BOOST_NOEXCEPT
    {
    }

    enable_shared_from_this & operator=(enable_shared_from_this const &) BOOST_NOEXCEPT
    {
        return *this;
    }

    ~enable_shared_from_this() BOOST_NOEXCEPT // ~weak_ptr<T> newer throws, so this call also must not throw
    {
    }

public:

    shared_ptr<T> shared_from_this()
    {
        shared_ptr<T> p( weak_this_ );
        BOOST_ASSERT( p.get() == this );
        return p;
    }

    shared_ptr<T const> shared_from_this() const
    {
        shared_ptr<T const> p( weak_this_ );
        BOOST_ASSERT( p.get() == this );
        return p;
    }

public: // actually private, but avoids compiler template friendship issues

    // Note: invoked automatically by shared_ptr; do not call
    template<class X, class Y> void _internal_accept_owner( shared_ptr<X> const * ppx, Y * py ) const
    {
        if( weak_this_.expired() )
        {
            weak_this_ = shared_ptr<T>( *ppx, py );
        }
    }

private:

    mutable weak_ptr<T> weak_this_;
};
{% endcodeblock %}
使用方法如下：
{% codeblock lang:cpp %}
#include <boost/enable_shared_from_this.hpp>
#include <boost/shared_ptr.hpp>
#include <cassert>

class Y: public boost::enable_shared_from_this<Y>
{
public:

    boost::shared_ptr<Y> f()
    {
        return shared_from_this();
    }
};

int main()
{
    boost::shared_ptr<Y> p(new Y);
    boost::shared_ptr<Y> q = p->f();
    assert(p == q);
    assert(!(p < q || q < p)); // p and q must share ownership
}
{% endcodeblock %}
用户自定义的class只需要继承与boost::enable_shared_from_this就可以调用shared_from_this了，用户并不需要重载任何虚函数，或者其他额外的代码，就能使用shared_ptr和shared_from_this。另一个例子请看[这篇文章](http://www.cnblogs.com/mightofcode/archive/2013/04/03/2996323.html)，它分析了WTL中双缓存渲染类CDoubleBufferImpl的设计和应用。

