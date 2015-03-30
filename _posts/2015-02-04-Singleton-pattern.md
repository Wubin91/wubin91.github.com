---
layout: post
title: 单例类设计模式
category: 软件开发
tagline: by wubin
tag: 软件开发
---

单例模式是一种常用的软件设计模式。在它的核心结构中只包含一个被称为单例类的特殊类。通过单例模式可以保证系统中一个类只有一个实例而且该实例易于外界访问，从而方便对实例个数的控制并节约系统资源。如果希望在系统中某个类的对象只能存在一个，单例模式是最好的解决方案。

<!--more-->

拿 iOS 来说，很多情况下一个对象在整个 APP 中只有一个，而且别的页面需要方便地与其进行数据交互（例如该单例类可以实现在不同的窗口之间传递数据），那么将其设置为单例类就最好不过了。既保证了全局可访问，又节约了系统资源。先来看看 OC 里是如何定义一个单例类的：

在 OC 中要实现一个单例类，至少需要做以下四个步骤：

* (1). 为单例对象实现一个静态实例，并初始化，然后设置成 nil；

* (2). 实现一个实例构造方法检查上面声明的静态实例是否为 nil，如果是则新建并返回一个本类的实例；

* (3). 重写 allocWithZone 方法，用来保证其他人直接使用 alloc 和 init 试图获得一个新实例时不产生一个新实例；

* (4). 适当实现 allocWithZone，copyWithZone，release 和 autorelease（后两者在 ARC 模式中无需编写）。

代码如下：

    @implementation customObject

    static customObject *sharedInstance = nil;

    + (customObject *) sharedInstance {
        if (!sharedInstance)
            sharedInstance = [[self alloc] init];
        return sharedInstance;
    }

    // Class behavior defined here

    @end

上面这个代码仍然不能阻止 customObject 通过 alloc 和 init 被新建一个新实例，我们需要重写 allocWithZone 方法。

    + (id) allocWithZone: (NSZone *)zone {
        return [self sharedInstance];	
    }

    - (id) copyWithZone: (NSZone *)zone {
        return self;
    }

同时深拷贝也直接阻止掉多个实例的出现。上面的 allocWithZone 的重载使得这个单例也能够直接用 alloc 或是 allocWithZone 进行初始化，但返回的一如既往是那个 static 的实例。

这样一个objective-c的单例模式才算是完整了。

______________________________________________

单例模式是设计模式中最简单的形式之一。这一模式的目的是使得类的一个对象成为系统中的唯一实例。要实现这一点，可以从客户端对其进行实例化开始。因此需要用一种只允许生成对象类的唯一实例的机制，“阻止”所有想要生成对象的访问。使用工厂方法来限制实例化过程。这个方法应该是静态方法（类方法），因为让类的实例去生成另一个唯一实例毫无意义。

如何保证一个类只有一个实例并且这个实例易于被访问呢？定义一个全局变量可以确保对象随时都可以被访问，但不能防止我们实例化多个对象。一个更好的解决办法是让类自身负责保存它的唯一实例。这个类可以保证没有其他实例被创建，并且它可以提供一个访问该实例的方法。这就是单例模式的模式动机。

###单例模式的要点：

* 一是某个类只能有一个实例

* 二是它必须自行创建这个实例

* 它必须自行向整个系统提供这个实例

**从具体实现的角度来说，就是以下三点：**

* 一是单例模式的类只提供私有的构造函数

* 二是类定义中含有一个该类的静态私有对象

* 三是该类提供了一个静态的共有的函数用于创建或获取它本身的静态私有对象

###优缺点

**优点**

* 实例控制：单例模式会阻止其他对象实例化其自己的单例对象的副本，从而确保所有对象都访问唯一实例。

* 灵活性：因为类控制了实例化过程，所以类可以灵活更改实例化过程。

**缺点**

* 开销：虽然数量很小，但如果每次对象请求引用时都要检查是否存在类的实例，将仍然需要一些开销。可以通过使用静态初始化解决此问题。

* 可能的开发混淆：使用单例对象（尤其在类库中定义的对象）时，开发人员必须记住自己不能使用 new 关键字实例化对象。因为可能无法访问库源代码，因此应用程序开发人员可能会意外发现自己无法直接实例化此类。

* 对象生存期：不能解决删除单个对象的问题。在提供内存管理的语言中（例如基于 .NET Framework 的语言），只有单例类能够导致实例被取消分配，因为它包含对该实例的私有引用。在某些语言中（如 C++），其他类可以删除对象实例，但这样会导致单例类中出现悬浮引用。

**再来看看 C++ 里的单例类是如何定义的：**

    class CSingleton {
    private:
        CSingleton() { // 构造函数是私有的

        }
    public:
        static CSingleton *GetInstance() {
            static CSingleton *m_pInstance;
            if (m_pInstance == NULL) // 判读是否第一次调用
                m_pInstance = new CSingleton();
            return m_pInstance;
        }
    }

参考：百度百科，[Triple_Vip](http://blog.csdn.net/zhaopenghhhhhh/article/details/11768825)