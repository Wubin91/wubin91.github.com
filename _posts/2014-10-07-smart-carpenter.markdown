---
layout: post
title: "算法题：聪明的木匠(C++)"
category: algorithm
tag: algorithm
---

一位老木匠需要将一根长的木棒切成N段。每段的长度分别为L1,L2,......,LN（1 <= L1,L2,…,LN <= 1000，且均为整数）个长度单位。我们认为切割时仅在整数点处切且没有木材损失。
木匠发现，每一次切割花费的体力与该木棒的长度成正比，不妨设切割长度为1的木棒花费1单位体力。例如：若N=3，L1 = 3,L2 = 4,L3 = 5，则木棒原长为12，木匠可以有多种切法，如：先将12切成3+9.，花费12体力，再将9切成4+5，花费9体力，一共花费21体力；还可以先将12切成4+8，花费12体力，再将8切成3+5，花费8体力，一共花费20体力。显然，后者比前者更省体力。
那么，木匠至少要花费多少体力才能完成切割任务呢？
####Input
* 第1行：1个整数N(2 <= N <= 50000)
* 第2 - N + 1行：每行1个整数Li(1 <= Li <= 1000)



####Output
* 输出最小的体力消耗。
* Input 示例
    * 3
	* 3
	* 4
	* 5
* Output 示例
	* 19

若 N=3, L1=3, L2=4, L3=5, 最省体力的方式是先将这段长为12的木棒切成7+5，耗费12体力，然后再将7切成3+4，耗费7体力，总共耗费12+7=19体力，即：(3+4+5) + (3+4) = 19，多设置几组（例如N＝4、5等等）容易发现：计算总体力时，长度短的(例如3和4)会累计多次，而较长的(例如5)累计次数少一些，这样才能达到最小体力的最终效果。
在计算总体力时，我们可以采用逆向思维，从最后一刀往前逐次累加求得最小体力，这道题可以采用小根堆的排序方法，每次取得最短的两段长度，偷懒一下，可以使用C++提供的STL中的queue中的优先队列（priority_queue），顾名思义，优先队列中保存的数组，当你每次往里边push进一个数，它都可以自动帮你拍好序，省去了很多繁琐的工作。下面是这道题的C++代码：

{% highlight C++ %}
#include<iostream>
#include<queue>
#include<vector>
using namespace std;
struct cmp {
	bool operator ()(const int &i, const int &j) {
		return i > j;
		}
};
int main(){
	int N;
	priority_queue<int, vector<int>, cmp> s;
	cin>>N;
	for(int i = 0; i < N; i++) {
		int val;
		cin>>val;
		s.push(val);
	}
	int sum = 0;
	while(N >= 2) {
		int a = s.top();
		s.pop();
		int b = s.top();
		s.pop();
		sum += a + b;
		s.push(a + b);
		N--;
	}
	cout<<sum<<endl;
	return 0;
}
{% endhighlight %}
