---
layout: post
category: Leetcode
title: Number of 1 Bits
tags: [Leetcode,C++,algorithm,Bit Manipulation]
tagline: by wubin
---

Write a function that takes an unsigned integer and returns the number of '1' bits it has (also known as the Hamming weight).

For example, the 32-bit integer '11' has binary representation `00000000000000000000000000001011`, so the function should return 3.

###分析

对输入n进行移位操作，然后与1相与，结果为1的话计数器加1，注意如果n已经为0了，那就没必要继续下去了，节省时间。

###C++代码如下：

	class Solution {
	public:
	    int hammingWeight(uint32_t n) {
	        int count = 0;
	        int i = 0;
	        while(n && i < 32) {
	            if(n & 1 == 1) count++;
	            n = n >> 1;
	        }
	        return count;
	    }
	};
