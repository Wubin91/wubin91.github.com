---
layout: post
category: algorithm
title: 二叉树相关算法
tagline: by wubin
tag: [C++, algorithm]
---

求二叉树的最大深度和最小深度的递归和非递归方法。

<!--more-->

###一、求二叉树的最大深度

**最简单的就是直接递归：**


    /**
     * Definition for binary tree
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
     * };
     */
    class Solution {
    public:
        int maxDepth(TreeNode *root) {
            if(root==NULL) return 0;
            int x = maxDepth(root->left);
            int y = maxDepth(root->right);
            return x>y?x+1:y+1;
        }
    };


**非递归（层次遍历二叉树）：**


    /**
     * Definition for binary tree
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
     * };
     */
    class Solution {
    public:
        int maxDepth(TreeNode *root) {
            if(root == NULL) return 0;
            queue<pair<TreeNode *, int> > leafQueue;
            int depth = 1;
            leafQueue.push(make_pair(root, depth));
            while(!leafQueue.empty()) {
                if(leafQueue.front().first->left)
                    leafQueue.push(make_pair(leafQueue.front().first->left, leafQueue.front().second + 1));
                if(leafQueue.front().first->right)
                    leafQueue.push(make_pair(leafQueue.front().first->right, leafQueue.front().second + 1));
                if(leafQueue.front().second > depth)
                    depth = leafQueue.front().second;
                leafQueue.pop();
            }
            return depth;
        }
    };


###二、求二叉树的最小深度

**递归（类似求最大深度时的递归方法）：当节点的左右节点有一个为空时，记该节点的深度为非空的子节点的深度加1；当节点的左右节点都非空时，记该节点的深度为左右节点深度较小者加1。**


    /**
     * Definition for binary tree
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
     * };
     */
    class Solution {
    public:
        int minDepth(TreeNode *root) {
            if(root == NULL) return 0;
            if(!root->left && !root->right) //Both left and right are NULL
                return 1;
            else if(root->left && root->right) {  //Both left and right are not NULL, choose the smaller one
                int x = minDepth(root->left);
                int y = minDepth(root->right);
                return x < y ? x+1 : y+1;
            }
            else {                              //one is NULL, another is not NULL, choose the bigger one
                int x = minDepth(root->left);
                int y = minDepth(root->right);
                return x > y ? x+1 : y+1;
            }
        }
    };


**非递归：使用队列 FIFO 的属性来层次遍历二叉树，采用 C++ STL 中的 queue。**


    /**
     * Definition for binary tree
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
     * };
     */
    class Solution {
    public:
        int minDepth(TreeNode *root) {
            if(root == NULL) return 0;
            queue<pair<TreeNode *, int> > leafQueue;
            int depth = 1;
            leafQueue.push(make_pair(root, depth));
            while(!leafQueue.empty()) {
                if(!leafQueue.front().first->left && !leafQueue.front().first->right)
                    return leafQueue.front().second;
                else {
                    if(leafQueue.front().first->left)
                        leafQueue.push(make_pair(leafQueue.front().first->left, leafQueue.front().second + 1));
                    if(leafQueue.front().first->right)
                        leafQueue.push(make_pair(leafQueue.front().first->right, leafQueue.front().second + 1));
                }
                leafQueue.pop();
            }
        }
    };