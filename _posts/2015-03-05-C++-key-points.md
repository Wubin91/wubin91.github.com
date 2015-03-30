---
layout: post
title: C++ 要点
category: C++
tagline: by wubin
tag: C++
---

####整数的赋值

* 对于 unsigned 类型来说，编译器必须调整越界值使其满足要求。编译器会将该值对 unsigned 类型的可能取值数目求模，然后取得所有值。比如 8 位的 unsigned char，其取值范围从 0 到 255（包括 255）。如果赋给超出这个范围的值，那么编译器就会取该值对 256 求模后的值。C++ 中把负值赋给 unsigned 对象是完全合法的，其结果是该负数对该类型的取值个数求模后的值。所以，如果把 -1 赋给 8 位的 unsigned char，那么结果是 255，因为 255 是 -1 对 256 求模后的值。

* 对于 signed 类型来说，由编译器决定实际赋的值。在实际操作中，很多的编译器处理 signed 类型的方式和 unsigned  类型类似。

####浮点型

* float 型只能保证 6 位有效数字，而 double 型至少可以保证 10 位有效数字。

####字符串字面值

* 为了兼容 C 语言，C++ 中所有的字符串字面值都由编译器自动在末尾添加一个空字符。

####初始化

* C++ 支持两种初始化变量的形式：复制初始化和直接初始化。

{% highlight C++ %}
int ival(1024);     // direct-initialization
int ival = 1024;    // copy-initialization
{% endhighlight %}

* 直接初始化语法更灵活且效率更高。

* 对于内置对象来说，初始化其只有一种方法：提供一个值，并且把这个值复制到新定义的对象中。对内置类型来说，复制初始化和直接初始化几乎没有区别。
* 对类类型的对象来说，有些初始化仅能用直接初始化完成。

* 内置类型变量的初始化：内置类型变量是否自动初始化取决于变量定义的位置。在函数体外定义的变量都初始化成 0，在函数体里定义的内置类型变量不进行自动初始化。

* 类类型变量的初始化：string 类至少提供了两个构造函数，其中一个允许我们通过字符串字面值初始化 string 对象，另外一个允许我们通过字符和计数器初始化 string 对象。

* 默认构造函数：string 类定义了默认构造函数来初始化 string 变量为空字符串，即没有字符的字符串。

{% highlight C++ %}
std::string empty;  // empty is the empty string; empty = ""
{% endhighlight %}

####声明和定义

* 变量的 **定义** 用于为变量分配存储空间，还可以为变量指定初始值。在一个程序中，变量有且仅有一个定义。

* **声明** 用于向程序表明变量的类型和名字。定义也是声明：当定义变量时我们声明了它的类型和名字。可以通过使用 extern 关键字声明变量名而不定义它。不定义变量的声明包括对象名、对象类型和对象类型前的关键字 extern：

{% highlight C++ %}
extern int i;   // declares but does not define i
int i;          // declares and defines i
{% endhighlight %}

* extern 声明不是定义，也不分配存储空间。事实上，它只是说明变量定义在程序的其他地方。程序中变量可以声明多次，但只能定义一次。

* 只有当声明也是定义时，声明才可以有初始化式，因为只有定义才分配存储空间。初始化式必须要有存储空间来进行初始化。如果声明有初始化式，那么它可被当作是定义，即时声明标记为 extern：

{% highlight C++ %}
extern double pi = 3.1416;  // definition
{% endhighlight %}

* 虽然使用了 extern，但是这条语句还是定义了 pi，分配并初始化了存储空间。只有当 extern 声明位于函数外部时，才可以含有初始化式。

* 因为已初始化的 extern 声明被当作是定义，所以该变量任何随后的定义都是错误的：

{% highlight C++ %}
extern double pi = 3.1416;  // definition
double pi;                  // error: redefinition of pi
{% endhighlight %}

* 同样，随后的含有初始化式的 extern 声明也是错误的：

{% highlight C++ %}
extern double pi = 3.1416   // definition
extern double pi;           // ok: declaration not definition
extern double pi = 3.1416;  // error: redefinition of pi
{% endhighlight %}

* 在 C++ 语言中，变量必须且仅能定义一次，而且在使用变量之前必须定义或声明变量。任何在多个文件中使用的变量都需要有与定义分离的声明。在这种情况下，一个文件含有变量的定义，使用该变量的其他文件则包含该变量的声明（而不是定义）。

####作用域

* 语句作用域：例如 for 循环括号内定义的变量

####const 限定符

* const 限定符把一个对象转换成一个常量，因为常量在定以后就不能被修改，所以定义时必须初始化。

* const 对象默认为文件的局部变量，除非作如下定义，才可为别的文件所使用：

{% highlight C++ %}
extern const int bufSize = fcn();
{% endhighlight %}

* 非 const 变量默认为 extern。在文件中作全局变量时无需添加 extern 即可为别的文件所使用。

####引用

**引用是别名**：引用在声明、定义的过程中必须用与该引用同类型的对象初始化。

因为引用只是它绑定的对象的另一名字，作用在引用上的所有操作事实上都是作用在该引用绑定的对象上。当引用初始化后，只要引用存在，它就保持绑定到初始化时指向的对象。不可能将引用绑定到另一对象。初始化是指明引用指向哪个对象的唯一方法。

####const 引用

#####const 引用** 是指向 const 对象的引用：

{% highlight C++ %}
const int ival = 1024;
const int &refval = ival;   // ok: both reference and object are const
int &ref2 = ival;           // error: non const reference to a const object
{% endhighlight %}

将普通的引用绑定到 const 对象是不合法的。

const 引用可以初始化为不同类型的对象或者初始化为右值，如字面值常量：

{% highlight C++ %}
int i = 42;
// legal for const references only
const int &r = 42;
const int &r2 = r + i;
{% endhighlight %}

####术语：const 引用是指向 const 的引用

* const 引用：指向 const 对象的引用

* 非 const 引用：指向非 const 类型的引用

* const 引用可以绑定到与该引用类型不同但相关的对象或绑定到右值

* 非 const 引用只能绑定到与该引用同类型的对象

####typedef

typedef 可以用来定义类型的同义词，通常被用于以下三种目的：

* 为了隐藏特定类型的实现，强调使用类型的目的。

* 简化复杂的类型定义，使其更易理解。

* 允许一种类型用于多个目的，同时使得每次使用该类型的目的明确。

####枚举

枚举的定义包括关键字 enum，其后是一个可选的枚举类型名，和一个用花括号括起来、用逗号分开的 **枚举成员** 列表。

{% highlight C++ %}
// input is 0, output is 1, and append is 2
enum open_modes {input, output, append};
{% endhighlight %}

#####枚举成员是常量：

可以为一个或多个枚举成员提供初始值，用来初始化枚举成员的值必须是一个 **常量表达式**。枚举成员的值可以是不唯一的。

{% highlight C++ %}
// point2d is 2, point2w is 3, point3d is 3, point3w is 4
enum Points {point2d = 2, point2w, point3d = 3, point3w};
{% endhighlight %}

不能改变枚举成员的值。枚举成员本身就是一个常量表达式，所以也可用于需要常量表达式的任何地方。

#####每个 enum 都定义一种唯一的类型：

每个 enum 都定义了一种新的类型。和其他类型一样，可以定义和初始化 Points 类型的对象，也可以以不同的方式使用这些对象。枚举类型的对象的初始化或赋值，只能通过其枚举成员或同一枚举类型的其他对象来进行：

{% highlight C++ %}
Points pt3d = point3d;  // ok: point3d is a Points enumerator
Points pt2w = 3;        // error: pt2w initialized with int
pt2w = polygon;         // error: polygon is not a Points enumerator
pt2w = pt3d;            // ok: both are objects of Points enum type
{% endhighlight %}

注意把 3 赋给 Points 对象是非法的，即使 3 与一个 Points 枚举成员相关联。

####类

* 定义变量和定义数据成员存在非常重要的区别：一般不能把类成员的初始化作为其定义的一部分。当定义数据成员时，只能指定该数据成员的名字和类型。类不是在类定义里定义数据成员时初始化数据成员，二是通过构造函数的特殊成员函数控制初始化。

* 如果使用 class 关键字来定义类，那么定义在第一个访问标号前的任何成员都隐式指定为 private；如果使用 struct 关键字，那么这些成员都是 public。使用 class 还是 struct 关键字来定义类，仅仅影响默认的初始访问级别。

####头文件用于声明而不是用于定义

* 当设计头文件时，记住定义和声明的区别是很重要的。定义只可以出现一次，而声明则可以出现多次。下列语句是一些定义，所以不应该出现在头文件里：

{% highlight C++ %}
extern int ival = 10;   // initializer, so it's a definition
double fica_rate;       // no extern, so it's a definition
{% endhighlight %}

* 虽然 ival 声明为 extern，但是它有初始化式，代表这条语句是一个定义。类似地，fica_rate 的声明虽然没有初始化式，但也是一个定义，因为没有关键字 extern。同一个程序中有两个以上文件含有上述任何一个定义都会导致多重定义链接错误。

* 因为头文件包含在多个源文件中，所以不应该含有变量或函数的定义。

* 对于头文件不应该含有定义这一规则，有三个例外。头文件可以定义类、值在编译时就已知道的 const 对象和 inline 函数。这些实体可在多个源文件中定义，只要每个源文件中的定义是相同的。

* 在头文件中定义这些实体，是因为编译器需要它们的定义（不只是声明）来产生代码。

####一些 const 对象定义在头文件中

* 当我们在头文件中定义了 const 变量后，每个包含该头文件的源文件都有了自己的 const 变量，其名称和值都一样。

* 如果 const 变量不是用常量表达式初始化，那么它就不应该在头文件中定义。相反，和其他的变量一样，该 const 变量应该在一个源文件中定义并初始化。应在头文件中为它添加 extern 声明，以使其能被多个文件共享。

####预处理器的简单介绍

* #include 设施是 C++ **预处理器** 的一部分。预处理器处理程序的源代码，在编译器之前运行。#include 指示只接受一个参数：头文件名。预处理器用指定的头文件的内容替代每个 #include。我们自己的头文件存储在文件中。系统的头文件可能用特定于编译器的更高效的格式保存。无论头文件以何种格式保持，一般都含有支持分别编译所需的类定义及变量和函数的声明。

####避免多重包含



{% highlight C++ %}

{% endhighlight %}
