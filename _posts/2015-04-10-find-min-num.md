---
layout: post
category: algorithm
title: 有序数组中查找最小元素
tags: [Interview,C++,algorithm]
tagline: by wubin
---

这是我在 Cracking the Coding Interview 上看到的一道题：

以个有序数组的元素经过循环移动，元素的顺序可能变为“3 4 5 6 7 1 2”。怎样才能找出数组中最小的那个元素？假设数组中的元素各不相同。

<!--more-->

分析：

* 在一个无序数组中查找最小元素，通常使用一次遍历即可
* 在本题数组是有序的情况下，可通过二分查找法

注意：要考虑特殊情况，比如`“1 2 3 4 5 6 7”`。

代码如下：

	#include<iostream>
	using namespace std;

	int main() {
		const int array_size = 10;
		int array[array_size] = {6,7,8,9,0,1,2,3,4,5};
		int left = 0;
		int right = array_size - 1;
		while(left < right - 1) {
			int mid = (left + right) / 2;
			if(array[mid] < array[right]) {
				right = mid;
			}
			else {
				left = mid;
			}
		}
		cout<<((array[left] > array[right]) ? array[right] : array[left])<<endl;
		return 0;
	}