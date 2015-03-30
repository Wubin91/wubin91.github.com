---
layout: post
title: "算法题：回文字符串"
category: algorithm
tag: [C++, algorithm]
---

####51nod-1092. 回文字符串

回文串是指aba、abba、cccbccc、aaaa这种左右对称的字符串。每个字符串都可以通过向中间添加一些字符，使之变为回文字符串。
例如：abbc 添加2个字符可以变为 acbbca，也可以添加3个变为 abbcbba。方案1只需要添加2个字符，是所有方案中添加字符数量最少的。

####Input

* 输入一个字符串Str，Str的长度 <= 1000。

####Output

* 输出最少添加多少个字符串可以使之变为回文字串。


####Input 示例

* abbc

####Output 示例

* 2

####分析

假设现在字符串 str1 = abcdefd，str2 = dfedcba 为 str1 的逆反字符串，要使 str1 成为回文字符串，至少需要添加 4 个字符，这时 str1 = abcdefedcba。

#####注意到这时 str1 和 str2 的最大相同子序为“ded”或“dfd”(最大相同子序各个字符在原字符串中的位置不一定连续，但必须是由小至大)，长度为 3，而 str.length = 7，7 - 3 = 4，因此要求得一个字符串变为回文字符串需要添加的最小字符数，只需求得原字符串长度，以及它和它逆反字符的最大相同子序的长度，再求两者之差即可。

之前在 51nod 上提交的代码一直不通过，原因是我把“求 str1 和 str2 的最大相同子序(最大相同子序各个字符在原字符串中的位置不一定连续，但必须是由小至大)”弄错为“求 str1 和 str2 的最大公共子串(最大公共子串各个字符在原字符串中的位置一定连续)”。

求两个字符串的最大相同子序的过程与求它们的编辑距离非常相似，都要用到动态规划的方法，假设有两个字符串，分别为 X = {x1, x2, ..., xm} 和 Y = {y1, y2, ..., yn}，长度分别为 strlen(X) = m，str(Y) = n，它们的最长相同子序列为 Z = {z1, z2, ..., zk}，那么：

* 若 xm = yn，则 zk = xm = yn，且Z[k-1]是X[m-1]和Y[n-1]的最长相同子序列
* 若 xm != yn，且 zk != xm，则Z是X[m-1]和Y的最长相同子序列
* 若 xm != yn，且 zk != yn，则Z是X和Y[n-1]的最长相同子序列

**上述三种情况形成的递归结构如下：**

定义一个(m+1)x(n+1)大小的矩阵 c[m+1][n+1]

* 当i = 0 或 j = 0 时，c[i][j]=0
* 当i, j > 0 且 xi = yj 时，c[i][j] = c[i-1][j-1] + 1
* 当i, j > 0 且 xi != yj 时，c[i][j] = max{ c[i][j-1], c[i-1][j] }

**最终 X 和 Y 的最长子序列长度 L = c[m][n]，与两个不同字符串编辑距离的求解方法非常相似** 

下面通过 X = "abcdefd" 和 "Y = dfedcba" 这个例子来说明求最长相同子序列长度的过程：

![Alt text](/img/table-huiwen.png)

从上图可以看到 X = "abcdefd" 和 "Y = dfedcba" 最长相同子序列长度为 3。

####代码

{% highlight C++ %}
#include<iostream>
#include<string>
#include<cstring>
using namespace std;

int main() {
    string s1;
    cin>>s1;
    int len = s1.length();
    string s2;
    for(int k = len - 1; k >= 0; k--) {
        s2 += s1[k];
    }
    int c[len+1][len+1];
    for(int i = 0; i <= len; i++) {
        c[i][0] = 0;
    }
    for(int j = 0; j <= len; j++) {
        c[0][j] = 0;
    }
    for(int i = 1; i <= len; i++){
        for(int j = 1; j <= len; j++){
            if(s1[i-1] == s2[j-1]) c[i][j] = c[i-1][j-1] + 1;
            else c[i][j] = c[i][j-1] > c[i-1][j] ? c[i][j-1] : c[i-1][j];
        }
    }
    cout<<len - c[len][len]<<endl;
    return 0;
}
{% endhighlight %}