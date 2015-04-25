---
layout: post
category: Leetcode
title: Remove Linked List Elements
tags: [Leetcode,C++,Linked List,algorithm]
tagline: by wubin
---

Remove all elements from a linked list of integers that have value **val**.

###Example
**Given:** 1 --> 2 --> 6 --> 3 --> 4 --> 5 --> 6, val = 6
**Return:** 1 --> 2 --> 3 --> 4 --> 5

<!--more-->

###分析

这道题特别要注意的是开头的节点的值为指定的val的情况，比如：

val = 1 时的 1->1->1->NULL

C++代码如下：

	/**
	 * Definition for singly-linked list.
	 * struct ListNode {
	 *     int val;
	 *     ListNode *next;
	 *     ListNode(int x) : val(x), next(NULL) {}
	 * };
	 */
	class Solution {
	public:
	    ListNode* removeElements(ListNode* head, int val) {
	        while(head && val == head->val)   //处理头部
	            head = head->next;
	        if(!head) return head;
	        ListNode *p = head;
	        ListNode *q = p->next;
	        while(p && q) {
	            if(val == q->val) {
	                p->next = q->next;
	                free(q);
	                q = p->next;
	            }
	            else {
	                p = p->next;
	                q = q->next;
	            }
	        }
	        return head;
	    }
	};
