---
layout: post
category: Leetcode
title: Reverse Bits
tags: [Leetcode,C++,algorithm,Bit Manipulation]
tagline: by wubin
---

Reverse bits of a given 32 bits unsigned integer.

For example, given input 43261596 (represented in binary as **00000010100101000001111010011100**), return 964176192 (represented in binary as **00111001011110000010100101000000**).

<!--more-->

**Follow up:**

If this function is called many times, how would you optimize it?

###分析

首先将输入n的逐位右移，然后在对于的输出m中加上1<<(31-index)，index指n中为1的位的索引。

###C++代码如下：

	class Solution {
	public:
	    uint32_t reverseBits(uint32_t n) {
	        int index = 0;
	        uint32_t m = 0;
	        while(n && index < 32) {
	            if(n & 1 == 1) {
	                m += (1 << (31 - index));
	            }
	            n = n >> 1;
	            index++;
	        }
	        return m;
	    }
	};
