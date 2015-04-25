---
layout: post
category: Leetcode
title: Happy Number
tags: [Leetcode,C++,algorithm,Hash Table,Math]
tagline: by wubin
---

Write an algorithm to determine if a number is "happy".

A happy number is a number defined by the following process: Starting with any positive integer, replace the number by the sum of the squares of its digits, and repeat the process until the number equals 1 (where it will stay), or it loops endlessly in a cycle which does not include 1. Those numbers for which this process ends in 1 are happy numbers.

<!--more-->

**Example:** 19 is a happy number

	1^2 + 9^2 = 82
	8^2 + 2^2 = 68
	6^2 + 8^2 = 100
	1^2 + 0^2 + 0^2 = 1

###分析

某些输入，在循环中一直计算，最终会为1，如题目中的19；而另外一些，如2：

	2^2 = 4
	4^2 = 16
	1^2 + 6^2 = 37
	3^2 + 7^2 = 58
	5^2 + 8^2 = 89
	8^2 + 9^2 = 145
	1^2 + 4^2 + 5^2 = 42
	4^2 + 2^2 = 20
	2^2 + 0^2 = 4	//注意到此时已经和开头的2^2 = 4重复了，也就是这是个死循环

那么我们要能检测出这种死循环，起初我想使用STL里的set，用set来保存计算的中间结果，然后查询，一旦发现死循环则 return false。但是set的低层实现是红黑树，查找效率为O(logn)，效率其实并不是很高。利用哈希查找，肯定比这快得多。所以选用unordered_map。

unordered_map存储中间结果，如果该结果还未出现，则映射为0，出现过了则映射为1，然后进行判断。

###C++代码如下：

	class Solution {
	public:
	    bool isHappy(int n) {
	        unordered_map<int, int> hashTable;
	        hashTable[n] = 1;
	        int sum = 0;
	        int i = 0;
	        while(n != 1) {
	            sum = 0;
	            while(n) {
	                i = n % 10; //取得最低位
	                sum += i * i;
	                n = (n - i) / 10;
	            }
	            if(sum != 1 && hashTable[sum] == 1) {
	                return false;
	            }
	            hashTable[sum] = 1;
	            n = sum;
	        }
	        return true;
	    }
	};
