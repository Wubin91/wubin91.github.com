---
layout: post
category: 比赛
title: 微软编程之美-回文字符序列
tags: [比赛,C++]
tagline: by wubin
---

###描述

给定字符串，求它的回文子序列个数。回文子序列反转字符顺序后仍然与原序列相同。例如字符串aba中，回文子序列为"a", "a", "aa", "b", "aba"，共5个。内容相同位置不同的子序列算不同的子序列。

<!--more-->

###限制

* 时间限制:2000ms
* 单点时限:1000ms
* 内存限制:256MB

###输入

第一行一个整数T，表示数据组数。之后是T组数据，每组数据为一行字符串。

###输出

对于每组数据输出一行，格式为"Case #X: Y"，X代表数据编号（从1开始），Y为答案。答案对100007取模。

###数据范围

1 ≤ T ≤ 30

* 小数据

字符串长度 ≤ 25

* 大数据

字符串长度 ≤ 1000

**样例输入**

	5
	aba
	abcbaddabcba
	12111112351121
	ccccccc
	fdadfa

**样例输出**

	Case #1: 5
	Case #2: 277
	Case #3: 1333
	Case #4: 127
	Case #5: 17

###我的思路

由于我对动态规划还不能够熟练运用，所以一开始觉得这道题有点难，不过幸亏有王神点拨。动态规划主要就是要找准迭代公式，下面针对这道题来具体分析：

假设我们使用函数 dp(i, j) 来表示字符串 str 从索引位置为 i 到 j 的子字符串的回文子序列个数，那么有以下式子：

* 当 str[i] == str[j] 时，dp("axxxxxa") = 2 * dp("xxxxx") + [dp("axxxxx") - dp(xxxxx)] + [dp("xxxxxa") - dp("xxxxx")] + 1, 即 dp(i,j) = 2*dp(i+1,j-1) + [dp(i+1,j) - dp(i+1,j-1)] + [dp(i,j-1) - dp(i+1,j-1)] + 1 = dp(i+1,j) + dp(i,j-1) + 1；这里的 2*dp("xxxxx") 是由于首尾 "a" 的添加，使得原来 "xxxxx" 中的所有回文子序列的首尾都可以加上 "aa" 形成新的回文子序列，所以计算 "xxxxx" 的回文子序列数时要乘以 2，后面的两个 [] 括起来的部分是计算由于首（尾）单独一个 "a" 的添加，使得 "xxxxx" 的回文子序列与首（尾）的 "a" 能够形成新的回文字符串的个数，所以要减去 dp("xxxxx")，最后的 + 1 是因为要算上 "aa" 这个新回文子序列；
* 当 str[i] != str[j] 时，dp("axxxxxa") = dp("axxxxx") + dp("xxxxxa") - dp("xxxxx")，即 dp(i,j) = dp(i+1,j) + dp(i,j-1) - dp(i+1,j-1)，这个式子应该很容易理解了，这里就不多说了。

动态规划其实就是带记录的递归，它能够将某些步骤的结果利用数组保存下来，方便后面的计算，从而节省了时间。

###代码如下：

	#include<iostream>
	#include<string>
	#include<vector>
	using namespace std;

	int dp(string &str, int i, int j, vector<vector<int> > &pp) {
		if(i > j)
			return 0;
		else if(i == j)
			return 1;
		else if(pp[i][j] != -1)
			return pp[i][j];
		else {
			if(str[i] == str[j])
				return pp[i][j] = (dp(str, i+1, j, pp) + dp(str, i, j-1, pp) + 1) % 100007;
			else
				return pp[i][j] = (dp(str, i+1, j, pp) + dp(str, i, j-1, pp) - dp(str, i+1, j-1, pp)) % 100007;
		}
	}

	int countHuiWen(string &str) {
		if(str.size() == 0) {
			return 0;
		}
		vector<int> p;
		vector<vector<int> > pp;
		p.reserve(str.size());
		pp.reserve(str.size());
		for(int i = 0; i < str.size(); i++) {
			p.clear();
			for(int j = 0; j < str.size(); j++) {
				p.push_back(-1);
			}
			pp.push_back(p);
		}
		return dp(str, 0, str.size() - 1, pp);
	}

	int main() {
		int T;
		cin>>T;
		vector<string> vec;
		vec.reserve(T);
		for(int i = 0; i < T; i++) {
			string str;
			cin>>str;
			vec.push_back(str);
		}
		int showI = 1;
		for(vector<string>::iterator iter = vec.begin(); iter != vec.end(); iter++) {
			cout<<"Case #"<<showI<<": "<<countHuiWen(*iter)<<endl;
			showI++;
		}
		return 0;
	}

