---
layout: post
category: algorithm
title：有序数组找中位数
tags: [C++,algorithm]
tagline: by wubin
---

两个有序数组 A 和 B，大小都为 n，求两个数组合并后的中位数。

中位数：如果数组元素个数为奇数，中位数就是该数组有序时中间的树；如果数组元素为偶数，那么中位数是中间两个数的平均数。

下面用两种方法来解答这个问题，并分析其时间复杂度：

<!--more-->

###方法一：合并时计数

类似于有序数组的 Merge 操作，遍历两个数组，设置 index，找出两个数组有序合并后 n-1 和 n 位置的两个数，再返回它们的平均值即可。这种方法时间复杂度为 O(n)。

	#include <iostream>
	#include <string>
	using namespace std;

	float getMedian(int *par1, int *par2, int n);

	int main() {
	    const int n = 5;
	    int ar1[n] = {1,2,3,4,5};
	    int ar2[n] = {6,7,8,9,10};
	    cout<<getMedian(ar1, ar2, n);
	    return 0;
	}

	float getMedian(int *par1, int *par2, int n) {
	    int index = 0;
	    int i = 0;
	    int j = 0;
	    int median1 = 0;
	    int median2 = 0;
	    while(index < n) {
	        if(par1[i] <= par2[j]) {
	            median1 = par1[i];
	            i++;
	        }
	        else {
	            median1 = par1[j];
	            j++;
	        }
	        index++;
	    }
	    if(i == n) {
	        median1 = par1[n-1];
	        median2 = par2[0];
	    }
	    else if(j == n) {
	        median1 = par2[n-1];
	        median2 = par1[0];
	    }
	    else {
	        if(par1[i] <= par2[j])
	            median2 = par1[i];
	        else
	            median2 = par2[j];
	    }
	    return (median1 + median2) / 2.0;
	}

###方法二：比较两个数组的中位数

算法过程如下：

1. 首先分别求出两个数组的中位数 m1 和 m2；
2. 比较 m1 和 m2：
	* 若 m1 = m2，则直接返回 m1
	* 若 m1 < m2，那么中位数在 ar1[n/2+1,...,n-1] 和 ar2[0,...,n/2] 之间
	* 若 m1 > m2，那么中位数在 ar1[0,...,n/2] 和 ar2[n/2+1,...,n-1] 之间
3. 重复以上操作，直到 ar1 和 ar2 的长度都为 2；
4. 如果两个数组的长度都为 2，那么用以下式子得到最终结果：
	* Median = (max(ar1[0],ar2[0]) + min(ar1[1],ar2[1])) / 2.0

这种方法时间复杂度为 O(log n)。

	#include <iostream>
	using namespace std;

	int max(int a, int b) {
	    return a > b ? a : b;
	}

	int min(int a, int b) {
	    return a < b ? a : b;
	}

	float getMedian(int *p, int n) {
	    if(n % 2 == 0) {
	        return (p[n/2-1] + p[n/2]) / 2.0;
	    }
	    else {
	        return p[n/2];
	    }
	}

	float getWholeMedian(int *p1, int *p2, int n) {
	    if(n == 0)
	        return -1;
	    else if(n == 1)
	        return (p1[0] + p2[0]) / 2.0;
	    else if(n == 2)
	        return (max(p1[0], p2[0]) + min(p1[1], p2[1])) / 2.0;
	    
	    float m1 = getMedian(p1, n);
	    float m2 = getMedian(p2, n);
	    
	    if(m1 == m2)
	        return m1;
	    else if(m1 < m2) {
	        if(n % 2 == 0) {
	            return getWholeMedian(p1 + n/2, p2, n/2);
	        }
	        else {
	            return getWholeMedian(p1 + n/2, p2, n/2 + 1);
	        }
	    }
	    else {
	        if(n % 2 == 0) {
	            return getWholeMedian(p1, p2 + n/2, n/2);
	        }
	        else {
	            return getWholeMedian(p1, p2 + n/2, n/2 + 1);
	        }
	    }
	}

	int main() {
	    const int n = 5;
	    int ar1[n] = {1,2,3,4,5};
	    int ar2[n] = {1,2,3,4,5};
	    cout<<getWholeMedian(ar1, ar2, n);
	    return 0;
	}
