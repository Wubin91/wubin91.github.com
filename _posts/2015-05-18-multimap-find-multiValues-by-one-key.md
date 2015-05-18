---
layout: post
category: C++ STL
title: multimap查找
tag: [C++,STL,map]
tagline: by wubin
---

在 multimap 中，同一个键关联的元素是相邻存放的，所以，我们可以通过以下三种方法来输出某个键对应的多个值：

<!--more-->

###1. 使用 find 和 count 函数

* count 函数求出某个键出现的次数，find 函数返回一个迭代器，指向第一个拥有正在查找的键的实例。

###2. 使用 lower_bound(key) 和 upper_bound(key)

* lower_bound(key) 返回一个迭代器，指向找到的第一个元素
* upper_bound(key) 返回一个迭代器，指向找到的最后一个元素的下一个位置

###3. 使用 equal_range(key)

* 返回一个迭代器的 pair 对象，first 成员等价于 lower_bound(key)，second 成员等价于 upper_bound(key)

###示例代码如下：

    #include <iostream>
    #include <string>
    #include <map>
    using namespace std;
      
    int main()
    {
        multimap<string,int> m_map;
        string s("中国"),s1("美国");
        m_map.insert(make_pair(s,50));
        m_map.insert(make_pair(s,55));
        m_map.insert(make_pair(s,60));
        m_map.insert(make_pair(s1,30));
        m_map.insert(make_pair(s1,20));
        m_map.insert(make_pair(s1,10));
        //方式1
        int k;
        multimap<string,int>::iterator m;
        m = m_map.find(s);
        for(k = 0;k != m_map.count(s);k++,m++)
            cout<<m->first<<"--"<<m->second<<endl;
        //方式2
        multimap<string,int>::iterator beg,end;
        beg = m_map.lower_bound(s1);
        end = m_map.upper_bound(s1);
        for(m = beg;m != end;m++)
            cout<<m->first<<"--"<<m->second<<endl;
        //方式3
        beg = m_map.equal_range(s).first;
        end = m_map.equal_range(s).second;
        for(m = beg;m != end;m++)
            cout<<m->first<<"--"<<m->second<<endl;
        return 0;
    }