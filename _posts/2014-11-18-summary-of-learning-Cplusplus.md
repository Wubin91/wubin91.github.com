---
layout: post
title: C++ 学习总结
category: C++学习总结
tagline: by wubin
tag: [C++]
---

####2014-11-18-Night, C++ 语言程序设计 第四章《类和对象》

####一、多态

多态性是指一段程序能够处理多种类型对象的能力。在C++语言中，这种多态性可以通过强制多态、重载多态、类型参数化多态、包含多态4种形式来实现。
<!--more-->
####二、类的定义

这里以时钟为例：

{% highlight C++ %}
class Clock {
public:
	void setTime(int newH, int newM, int newS);
	void showTime();
private:
	int hour, minute, second;
};
{%endhighlight%}

如果私有成员紧接着类名称，则关键字private可以省略。public | protected | private 关键字可以多次出现，顺序任意。

函数的原型声明要写在类体中，原型说明了函数的参数表和返回值类型。而函数的具体实现是写在“类定义之外”的。


{% highlight C++ %}
void Clock::setTime(int newH, int newM, int newS) {
	hour = newH;
	minute = newM;
	second = newS;
}

void Clock::showTime() {
	cout<<hour<<":"<<minute<<":"<<second<<endl;	
}
{% endhighlight %}

带默认值的类成员函数

{% highlight C++ %}
class Clock {
public:
	void setTime(int newH=0, int newM=0, int newS=0);
	...
};
{% endhighlight %}

这样，如果调用这个函数时没有给出实参，就会按照默认形参值将时钟设置为0:0:0。

####三、内联函数

函数的调用过程要消耗一些内存资源和运行时间来传递参数和返回值，要记录调用时的状态，以便保证调用完后能够正确地返回并继续执行。如果函数成员需要频繁被调用，且代码较简单，则可定义为内联函数（inline function）。内联成员函数的函数体会在编译时被插入到每一个调用它的地方。这样做可以：
优点：

* 减少调用的开销
* 提高执行效率

缺点：

* 但是却增加了编译后代码的长度

内联函数的声明方式：显式声明 和 隐式声明

隐：

{% highlight C++ %}
class Clock {
public:
	void setTime(int newH, int newM, int newS);
	void showTime() {
		cout<<...
	}
}
private:
	...
{% endhighlight %}

显：

{% highlight C++ %}
inline void ... // 在类外函数体具体实现时前面加 inline
{% endhighlight %}

####四、构造函数和析构函数

1、构造函数的作用就是在对象被创建时利用特定的值构造对象，将对象初始化为一个特定的状态。构造函数的函数名和类名相同，而且没有返回值。构造函数通常被声明为公有函数。构造函数在对象被创建时调用。

2、如果定义了带参数的构造函数，那么在建立对象时就必须给出初始值，否则编译时会报语法错误。

#####复制构造函数

其形参是本类对象的引用，其作用是使用一个已经存在的对象（由复制构造函数的参数指定），去初始化同类的一个新对象。如果搬砖工没有定义类的复制构造函数，系统会生成一个隐含的复制构造函数。

{% highlight C++ %}
class Point {
public:
	Point(int xx=0, int yy=0) {
		x=xx;
		y=yy;
	}
	Point(Point &p);
private:
	int x,y;
}

Point::Point(Point &p) {
	x = p.x;
	y = p.y;
}

// 调用时：
Point a(1, 2);
Point b(a);
Point b = a;
{% endhighlight %}

####Note: 只有当对象作为值传递时，才会调用复制构造函数，如果是引用传递，则不调用，故传递较大的对象时，为了减小开销，最好使用引用传递。我们有时候会觉得没必要自定义复制构造函数，用系统默认生成的隐含复制构造函数就行，但是有时候复制并不是单纯的拷贝，而是需要做某些修改，如点的复制时点的偏移。另外，当类的数据成员中有指针类型时，默认的复制构造函数实现的只能是浅复制。浅复制会带来数据安全方面的隐患，要实现正确的复制，也就是深复制，必须编写复制构造函数。

#####扫尾工作：析构函数-做事要有始有终

析构函数用来完成对象被删除前的一些清理工作。在对象的生存期即将结束的时刻被自动调用。它的名称由类名称前面加一个“～”构成，也没有返回值。和构造函数不同的是析构函数不接受任何参数。但可以是虚函数。如果不定义，系统也会生成一个函数体为空的析构函数。

引用型变量必须在初始化时绑定引用的对象！！！

