---
layout: post
title: Google C++ 编程规范总结
category: C++
tagline: by wubin
tag: C++
---

##一、头文件

* #define 的保护

项目 foo 中的头文件 foo/src/bar/baz.h 按如下方式保护：
{% highlight ruby %}
#ifndef FOO_BAR_BAZ_H_
#define FOO_BAR_BAZ_H_
#endif //FOO_BAR_BAZ_H_

#ifdef WINDOWS 
#define MYTYPE long 
#else 
#define MYTYPE float 
#endif

#ifndef WINDOWS 
#define MYTYPE long 
#else 
#define MYTYPE float 
#endif
{% endhighlight %}

用于代码调试：

{% highlight ruby %}
#ifdef DEBUG 
print ("device_open(%p) ", file); 
#endif
{% endhighlight %}

在它前面必须有：

{% highlight ruby %}
#define DEBUG
{% endhighlight %}

起到一个开关作用。

* 内联函数

不要内联超过 10 行的函数。

* 函数参数顺序

定义函数时，输入参数在前，输出参数在后。

`...未完待续...`
