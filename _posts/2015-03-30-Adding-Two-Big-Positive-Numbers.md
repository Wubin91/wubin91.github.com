---
layout: post
category: algorithm
title: 两个超大正整数相加
tags: [algorithm, C++]
tagline: by wubin
---

这道题是实验室波神出给前来面试的师弟师妹们的上机题，我顺手拿来做做，顺便复习复习 string 相关的知识。

题目很简单，就是实现两个超大位数的正整数的加法，这两个数的位数可能超出我们平时用的 int，long，甚至 long long 类型的最大位数，最终的结果用字符串表示后输出即可。这当中要特别注意对进位的处理。

<!--more-->

这道题还可以扩展到任意两个数相加（包括任意位数的正数、负数、带很多位小数的数）。


**代码如下：**

尽量使用 C++ STL 里边提供的类型，因为数组和 C 类型的字符串实在是太蛋疼了，尤其是 C 字符串字面值常量后面带的 '\0'，直接用 string 多方便，还不易出错。（C++ Primer Fourth Edition 如是说）。

    /*
     * 将两个正整数相加，不限这两个正整数的位数。
     */
    #include<iostream>
    #include<string>
    #include<vector>
    using namespace std;
    int main() {
        string a_0, b_0;
        cin>>a_0>>b_0;
        string::size_type i = 0, j = 0;
        while(i < a_0.size() && a_0[i] == '0') {
            i++;
        }
        while(j < b_0.size() && b_0[j] == '0') {
            j++;
        }
        string a(a_0, i), b(b_0, j); //已经去掉a_0和b_0前面的0
        vector<int> result;
        string::size_type index = 0;
        int plus = 0, a_int, b_int;
        while(index < a.size() && index < b.size()) {
            a_int = a[a.size() - 1 - index] - '0';
            b_int = b[b.size() - 1 - index] - '0';
            result.push_back((a_int + b_int + plus) % 10);
            plus = (a_int + b_int + plus) / 10;
            index++;
        }
        while(index < a.size()) {
            a_int = a[a.size() - 1 - index] - '0';
            result.push_back((a_int + plus) % 10);
            plus = (a_int + plus) / 10;
            index++;
        }
        while(index < b.size()) {
            b_int = b[b.size() - 1 - index] - '0';
            result.push_back((b_int + plus) % 10);
            plus = (b_int + plus) / 10;
            index++;
        }
        if(plus > 0) {
            result.push_back(plus);
        }
        if(result.size() > 0) {
            for(vector<int>::size_type st = 0; st < result.size(); st++) {
                cout<<result[result.size() - 1 - st];
            }
        }
        else {
            cout<<0;
        }
        cout<<endl;
        return 0;
    }