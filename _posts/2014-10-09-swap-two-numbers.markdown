---
layout: post
category: algorithm
title: Swap Two Numbers
tagline: by wubin
tags: [algorithm,C++]
---

前几天在看[互联网面试总结（下）](http://blog.renren.com/share/221293470/14132198643)的时候发现一道很有意思的题目，是作者在参加清华复试时候的一道笔试题，如下：

<!--more-->

	void swap(int *a, int *b) {
		a = ________;
		b = ________;
		a = ________;
	}

非常巧，同一天我在实验室的时候开始看了《算法竞赛-入门经典(第2版)》，里面也讲到类似的交换函数，不借助任何变量：

	void swap(int *a, int *b) {
		*a = *a + *b;
		*b = *a - *b;
		*a = *a - *b;
	}

回宿舍后和室友王神讨论了一下，发现在保证 *a != 0 和 *b != 0 的情况下，用乘除也能实现上述不借助任何变量的交换效果：

	void swap(int *a, int *b) {
		*a = *a * *b;
		*b = *a / *b;
		*a = *a / *b;
	}

其实，上述“加减”和“乘除”的方法都是要考虑溢出的情况的，在 *a 和 *b 足够大的情况下，*a + *b 和 *a * *b 有可能溢出，下面通过例子来说明：

###加减

	#include<iostream>
	#include<limits.h>
	using namespace std;

	int main() {
		int a = INT_MAX;
		int b = INT_MAX - 1;
		cout<<"1: a = "<<a<<" | b = "<<b<<endl;
		a = a + b;
		b = a - b;
		a = a - b;
		cout<<"2: a = "<<a<<" | b = "<<b<<endl;
		return 0;
	}

输出如下：

* 1: a = 2147483647 | b = 2147483646
* 2: a = 2147483646 | b = 2147483647

可以看到，成功实现了 a 和 b 的交换。

###乘除

	#include<iostream>
	#include<limits.h>
	using namespace std;

	int main() {
		int a = INT_MAX;
		int b = INT_MAX - 1;
		cout<<"1: a = "<<a<<" | b = "<<b<<endl;
		a = a * b;
		b = a / b;
		a = a / b;
		cout<<"2: a = "<<a<<" | b = "<<b<<endl;
		return 0;
	}

输出如下：

* 1: a = 2147483647 | b = 2147483646
* 2: a = 2147483646 | b = -1

a 和 b 交换失败。

另外，“异或：^”操作能够完美实现两个数不借助第三个变量进行交换，无需担心溢出问题：

	void swap(int *a, int *b) {
		*a = (*a) ^ (*b);
		*b = (*b) ^ (*a);
		*a = (*a) ^ (*b);
	}

现在回到开头那道题，这里的三个空中的 a 和 b 都是指针，保存的是相应变量的地址，所以即使交换 a 和 b，也达不到交换 *a 和 *b 的效果，所以一方面要保持 a 和 b 不变，另一方面要交换 *a 和 *b，那就只能用 () 把多个式子包含，用逗号隔开，() 里最终的值为最后一个等式的值：

###解法1：

	void swap(int *a, int *b) {
		a = ((*a) ^= (*b), a);
		b = ((*b) ^= (*a), b);
		a = ((*a) ^= (*b), a);
	}

###解法2：

	void swap(int *a, int *b) {
		a = ((*a) = (*a)+(*b), a);
		b = ((*b) = (*a)-(*b), b);
		a = ((*a) = (*a)-(*b), a);
	}
