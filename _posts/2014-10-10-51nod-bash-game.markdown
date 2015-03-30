---
layout: post
title: Bash游戏
category: algorithm
tagline: by wubin
tag: [C++, algorithm]
---

这道题和上一篇博客"[算法题：Bash游戏 V2](http://binwu.net/2014/10/10/51nod-bash-game-v2.html)"非常相似，只是现在每次可以拿的石子数变成了“1、2、···、K”颗：

<!--more-->

有一堆石子共有N个。A B两个人轮流拿，A先拿。每次最少拿1颗，最多拿K颗，拿到最后1颗石子的人获胜。假设A B都非常聪明，拿石子的过程中不会出现失误。给出N和K，问最后谁能赢得比赛。
例如N = 3，K = 2。无论A如何拿，B都可以拿到最后1颗石子。

###Input

第1行：一个数T，表示后面用作输入测试的数的数量。（1 <= T <= 10000)

第2 - T + 1行：每行2个数N，K。中间用空格分隔。（1 <= N,K <= 10^9)


###Output

共T行，如果A获胜输出A，如果B获胜输出B。

**Input**

* 4
* 3 2
* 4 2
* 7 3
* 8 3

**Output**

* B
* A
* A
* B

###分析

同样，这道题也是有规律可循：

* N <= K 时，A 赢
* N = K + 1 时，B 赢
* N = K + 2 时，A 赢
* ······，A 赢
* N = K + K 时，A 赢
* N = 2K + 1 时，A 赢
* N = 2K + 2 时，B 赢
* N = 2K + 3 时，A 赢
* ······，A 赢
* N = 3K + 2 时，A 赢
* N = 3K + 3 时，B 赢
* ······

可以发现谁赢的结果的周期为 T ＝ K + 1，即“BAAA···”

###代码：

    #include<iostream>
    #include<vector>
    using namespace std;

    int main() {
        int n;
        cin>>n;
        vector<char> out;
        int N, K;
        for(int i = 0; i < n; i++) {
            cin>>N;
            cin>>K;
            if(N % (K+1) == 0) out.push_back('B');
            else out.push_back('A');
        }
        for(int j = 0; j < out.size(); j++) {
            cout<<out[j]<<endl;
        }
        return 0;
    }