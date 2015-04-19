---
layout: post
category: 比赛
title: 微软编程之美-基站选址
tags: [比赛,C++]
tagline: by wubin
---

###描述

需要在一个N × M的网格中建立一个通讯基站，通讯基站仅必须建立在格点上。

网格中有A个用户，每个用户的通讯代价是用户到基站欧几里得距离的平方。

网格中还有B个通讯公司，维护基站的代价是基站到最近的一个通讯公司的路程（路程定义为曼哈顿距离）。

在网格中建立基站的总代价是用户通讯代价的总和加上维护基站的代价，最小总代价。

###限制

* 时间限制:2000ms
* 单点时限:1000ms
* 内存限制:256MB

###输入

第一行为一个整数T，表示数据组数。

每组数据第一行为四个整数：N, M, A, B。

接下来的A+B行每行两个整数x, y，代表一个坐标，前A行表示各用户的坐标，后B行表示各通讯公司的坐标。

###输出

对于每组数据输出一行"Case #X: Y"，X代表数据编号（从1开始），Y代表所求最小代价。

###数据范围

1 ≤ T ≤ 20

1 ≤ x ≤ N

1 ≤ y ≤ M

1 ≤ B ≤ 100

* 小数据

1 ≤ N, M ≤ 100

1 ≤ A ≤ 100

* 大数据

1 ≤ N, M ≤ 107

1 ≤ A ≤ 1000

**样例输入**

	2
	3 3 4 1
	1 2
	2 1
	2 3
	3 2
	2 2
	4 4 4 2
	1 2
	2 4
	3 1
	4 3
	1 4
	1 3

**样例输出**

	Case #1: 4
	Case #2: 13

###我的思路

这道题没想到什么好的方法，可以让题目的解决变得更容易，省劲，以及有技术含量一些，我使用的方法就是最笨的遍历，计算出最小的代价然后输出。这样的话，小数据的时候可以通过，也许大数据会比较吃力，有超时的风险。

###代码如下：

	#include <iostream>
	#include <vector>
	#include <limits>
	#include <cmath>
	using namespace std;

	class Data
	{
	public:
	    int N, M, A, B;
	    vector<pair<int, int> > vec;
	};

	int minimumCost(Data &data) {
	    int minCost = INT_MAX;
	    for(int i = 1; i <= data.N; i++) {
	        for(int j = 1; j <= data.M; j++) {
				int cost = 0;
	            for(int a = 0; a < data.A; a++) {
	                cost += pow(data.vec[a].first - i, 2) + pow(data.vec[a].second - j, 2);
	            }
				int maintainCost = INT_MAX;
	            for(int b = data.A; b < data.A + data.B; b++) {
					int tmpCost = abs(data.vec[b].first - i) + abs(data.vec[b].second - j);
	                if(tmpCost < maintainCost) {
						maintainCost = tmpCost;
					}
	            }
				cost += maintainCost;
	            if(cost < minCost) {
	                minCost = cost;
	            }
				cost = 0;
	        }
	    }
	    return minCost;
	}

	int main()
	{
	    int T;
	    cin>>T;     //num of data
	    int x, y;   //coordinate
	    vector<Data> dataVec;
	    dataVec.reserve(T);
	    
	    Data data;
	    
	    for(int i = 0; i < T; i++) {
	        data.vec.clear();
	        cin>>data.N>>data.M>>data.A>>data.B;
	        data.vec.reserve(data.A + data.B);
	        for(int j = 0; j < data.A; j++) {
	            cin>>x>>y;
	            data.vec.push_back(pair<int, int>(x, y));
	        }
	        for(int k = 0; k < data.B; k++) {
	            cin>>x>>y;
	            data.vec.push_back(pair<int, int>(x, y));
	        }
	        dataVec.push_back(data);
	    }
	    int showI = 1;
	    for(vector<Data>::iterator iter = dataVec.begin(); iter != dataVec.end(); iter++) {
	        cout<<"Case #"<<showI<<": "<<minimumCost(*iter)<<endl;
	        showI++;
	    }
	    return 0;
	}

