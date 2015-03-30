---
layout: post
title: "算法题：不重叠的线段"
category: algorithm
tag: [C++, algorithm]
---

####51nod-1133. 不重叠的线段

X轴上有N条线段，每条线段有1个起点S和终点E。最多能够选出多少条互不重叠的线段。（注：起点或终点重叠，不算重叠）。
例如：[1 5][2 3][3 6]，可以选[2 3][3 6]，这2条线段互不重叠。

####Input

* 第1行：1个数N，线段的数量(2 <= N <= 10000)
* 第2 - N + 1行：每行2个数，线段的起点和终点(-10^9 <= S,E <= 10^9)

####Output

* 输出最多可以选择的线段数量。


####Input 示例

* 3
* 1 5
* 2 3
* 3 6

####Output 示例

* 2

####分析

这道题采用“贪心算法”，首先将 N 条线段按照起点从小到大排序：

1. [a1, b1], [a2, b2], ···, [aN, bN],其中 a1 <= a2 <= ··· <= aN    
2. 遍历所有线段：   
    * 如果相邻的两条线段不重叠，则同时保留这两条线段
    * 如果相邻的两条线段 [x(k-1), y(k-1)] 与 [xk, yk] 重叠，则比较它们的终点坐标 y(k-1) 与 yk，保留 y 坐标较小的线段，抛弃 y 坐标较大的（因为它与接下来的线段重叠的概率要更大）

####代码

{% highlight C++ %}
#include<iostream>
#include<vector>
#include<algorithm>
using namespace std;
int main() {
    vector<vector<int> > s; // s 为二维向量数组，用来存储线段 [xi, yi]
    int n;
    cin>>n;
    if(n == 0) {
        cout<<0<<endl;
        return 0;
    }
    for(int i = 0; i < n; i++) {
        vector<int> t;  // t[0] 和 t[1] 分别为线段的起点和终点
        int a, b;
        cin>>a;
        cin>>b;
        t.push_back(a);
        t.push_back(b);
        s.push_back(t);
        t.clear();
    }
    if(n == 1) {
        cout<<1<<endl;
        return 0;
    }
    sort(s.begin(), s.end());  // 对所有线段按起点大小升序排列
    int count = 1;
    int q = s[0][1];  // q 初始化为第一条线段的终点坐标
    for(int j = 1; j < n; j++) {
        if(q <= s[j][0]) {  // 如果 q 小于下一条的起点，那么说明这两条线段无重叠，计数器加 1
            count++;
            q = s[j][1]; // q 更新为最新保留的线段的终点
        }
        else {
            q = q < s[j][1] ? q : s[j][1];  // 如果发现两条线段有重叠，则更新 q 为终点较小的线段的终点，计数器保持不变
        }
    }
    cout<<count<<endl;
    return 0;
}
{% endhighlight %}
