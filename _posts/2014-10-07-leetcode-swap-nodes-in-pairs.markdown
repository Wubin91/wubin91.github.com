---
layout: post
title: leetcode: Swap Nodes in Pairs (C++)
category: algorithm
tagline: by wubin
tag: algorithm
---

####Swap Nodes in Pairs

Given a linked list, swap every two adjacent nodes and return its head.
For example,
Given 1->2->3->4, you should return the list as 2->1->4->3.
Your algorithm should use only constant space. You may not modify the values in the list, only nodes itself can be changed.

基本实现思路是设置三个滑动指针 t, p, q 对 ListNode 进行 swap 操作。

{% highlight C++ %}
/*
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
*/
class Solution {
public:
	 ListNode *swapPairs(ListNode *head) {
		if (head == NULL || head->next == NULL) return head;
		ListNode *t = new ListNode(0); //设置相邻的 3 个指针 t, p, q
		t -> next = head;
		ListNode *p = head;
		ListNode *q = head -> next;
		head = q;
		while (1) {
			t -> next = q;
			p -> next = q->next;
			q -> next = p;
			if(p -> next == NULL || p -> next -> next == NULL) break;
			t = p;
			p = p -> next;
			q = p -> next;
		}
        return head;
	}
};
{% endhighlight %}

