---
layout: post
category: Interview
title: 2015阿里实习生C++开发笔试附加题
tags: [Interview,C++,algorithm]
tagline: by wubin
---

4 月 2 日晚 7 点，阿里 2015 实习生招聘线上笔试。由于自己的疏忽，没有投简历，所以只能从同学朋友圈盗笔试题的截图啦（感谢巨哥~），下次要引以为戒，提前做好准备。

这是这次 C++ 开发岗位笔试题的三道附加题，具体如下：

<!--more-->

* 分布式系统中的 RPC 请求经常出现乱序的情况。

写一个算法来将一个乱序的序列保序输出。例如：假设起始序号是 1，对于（1, 2, 5, 8, 10, 4, 3, 6, 9, 7）这个序列，输出是：

    1
    2
    3, 4, 5
    6
    7, 8, 9, 10

上述例子中，3 到来的时候会发现 4, 5 已经在了。因此将已经满足顺序的整个序列（3, 4, 5）输出为一行。

要求：

1. 写一个高效的算法完成上述功能，实现要尽可能的健壮、易于维护
2. 为该算法设计并实现单元测试

分析：在输出 1, 2 后，我们需要找到 3，但是在 3 之前已经存在 5, 4，即在找到所需的 x 之前，将位于 x 之前的 x + 1, x + 2, ... 排好序保存下来，这一功能可以借助于`“优先队列（priority queue）”`，紧接着，输出 3, 4, 5 之后，我们需要从 3 的位置开始往后找到 6，以此类推...

代码如下：

    #include<iostream>
    #include<vector>
    #include<queue>

    using namespace std;

    void sorted_out(int unsorted_array[], unsigned size);

    int main() {
        const unsigned size = 1;
        int unsorted_array[size] = {2};
        sorted_out(unsorted_array, size);
        return 0;
    }

    void sorted_out(int unsorted_array[], unsigned size) {
        int start = 1;
        priority_queue<int, vector<int>, greater<int> > pq;
        for (int i = 0; i < size; i++) {
            if (unsorted_array[i] == start) {
                cout<<unsorted_array[i];
                start++;
                while (!pq.empty() && pq.top() == start) {
                    cout<<','<<pq.top();
                    pq.pop();
                    start++;
                }
                cout<<endl;
            }
            else {
                pq.push(unsorted_array[i]);
            }
        }
    }

如果不太了解 priority_queue，请移步[priority_queue用法](http://blog.chinaunix.net/uid-533684-id-2100009.html)

另附 priority 提供的一些函数：

    头文件:<queue>
    priority_queue();   //默认构造函数，生成一个空的排序队列
    priority_queue(const queue&);   //拷贝构造函数
    priority_queue& operator=(const priority_queue &);    //赋值运算符重载
    priority_queue 的私有成员：
    value_type;    //priority_queue中存放的对象类型，它和priority_queue中的T类型相同
    priority_queue(const Compare& comp);    //构造生成一个空的priority_queue对象，使用comp作为priority_queue的comparison
    priority_queue(const value_type* first, const value_type* last);    //带有两个参数的构造 函数，使用默认的Comparison作为第三个参数
    size_type;    //正整数类型，和Sequence::size_type类型一样。
    bool empty() const;    //判断优先级队列是否为空，为空返回true，否则返回false
    size_type size() const;    //返回优先级队列中的元素个数
    const value_type& top() const();    //返回优先级队列中第一个元素的参考值。
    void push(const value_type& x);    //把元素x插入到优先级队列的尾部，队列的长度加1
    void pop();    //删除优先级队列的第一个值，前提是队列非空，删除后队列长度减1



* 常常会有频繁申请、释放内存的需求，比如在发送网络报文时，每次都要分配内存以存储报文，等报文发送完成后要需要删除报文。为了避免频繁的 new/delete 对系统带来的开销，需要实现一个通用的 FreeList 机制。使用者总是从 free list 中分配内存，如果存在没有使用的内存块就直接摘出来使用，如果没有的话再从系统中分配。使用完毕后并不去直接 delete 该内存块，而是交给 FreeList 保管。

要求：

1. 实现一个对固定大小内存块进行管理的通用 FreeList 类，给出定义和实现。要求不能使用 STL 中的容器类。定义类的接口和实现时注意通用性、健壮性和可测试性。
2. 如果该类的对象可能会被多个 thread 同时访问，请描述如何保证线程安全。有没有办法在保证线程安全的同时尽可能增大并发度？如果有也请描述你的思路。

