---
layout: post
title: Bash游戏 V2
category: algorithm
tagline: by wubin
tag: [C++, algorithm]
---

####51nod: 1067. Bash游戏 V2

有一堆石子共有N个。A B两个人轮流拿，A先拿。每次只能拿1，3，4颗，拿到最后1颗石子的人获胜。假设A B都非常聪明，拿石子的过程中不会出现失误。给出N，问最后谁能赢得比赛。

例如N = 2。A只能拿1颗，所以B可以拿到最后1颗石子。

<!--more-->

Input

    * 第1行：一个数T，表示后面用作输入测试的数的数量。（1 <= T <= 10000)
    * 第2 - T + 1行：每行1个数N。(1 <= N <= 10^9)

    * 3
    * 2
    * 3
    * 4

Output

    * 共T行，如果A获胜输出A，如果B获胜输出B。

    * B
    * A
    * A

###分析：

初看这道题，很有可能会觉得应该通过“回溯法”来解决，下面一步一步分析：

    * N = 1：A 拿 1 个，A 赢
    * N = 2：A 拿 1 个，B 拿 1 个，B 赢
    * N = 3：A 拿 3 个，A 赢
    * N = 4：A 拿 4 个，A 赢
    * N = 5：
        * A 拿 1 个，剩 4 个 -> B win
        * A 拿 3 个，剩 2 个 -> A win
        * A 拿 4 个，剩 1 个 -> B win
        * A 最终选择一开始拿 3 个 A 赢

    * N = 6：
        * A 拿 1 个，剩 5 个 -> B win
        * A 拿 3 个，剩 3 个 -> B win
        * A 拿 4 个，剩 2 个 -> A win
        * A 最终选择一开始拿 4 个 A 赢

    * N = 7：
        * A 拿 1 个，剩 6 个 -> B win
        * A 拿 3 个，剩 4 个 -> B win
        * A 拿 4 个，剩 3 个 -> B win
        * 无论 A 一开始拿多少个 B 赢

    * N = 8：
        * A 拿 1 个，剩 7 个 -> A win
        * A 拿 3 个，剩 5 个 -> B win
        * A 拿 4 个，剩 4 个 -> B win
        * A 最终选择一开始拿 1 个 A 赢

    * N = 9：
        * A 拿 1 个，剩 8 个 -> B win
        * A 拿 3 个，剩 6 个 -> B win
        * A 拿 4 个，剩 5 个 -> B win
        * 无论 A 一开始拿多少个 B 赢

    ······

再接下去算 N = 10, 11, 12···, 发现随着 N 的递增，谁赢的结果呈现出周期 T ＝ 7 的规律，即：`“ABAAAAB”`，那么只需对输入的 N 值模“7”就可得到最终结果，而不用使用“回溯法”，代码如下：

    #include<iostream>
    #include<vector>
    #include<string>
    using namespace std;

    int main() {
        int n;
        cin>>n;
        vector<char> c;
        int m;
        string s = "BABAAAA";
        for(int i = 0; i < n; i++) {
            cin>>m;
            c.push_back(s[m%7]);
        }
        for(int j = 0; j < c.size(); j++) {
            cout<<c[j]<<endl;
        }
        return 0;
    }