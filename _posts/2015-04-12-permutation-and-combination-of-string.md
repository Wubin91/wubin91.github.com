---
layout: post
category: algorithm
title: 打印字符串所有可能的排列组合
tags: [algorithm,C++]
tagline: by wubin
---

设计一种算法，打印某个字符串所有可能的排列组合。为简单起见，假设字符串中没有重复字符。

以字符串 abcdefg 为例：

<!--more-->

* 只有“a”的情况，结果为：{"a"}
* 然后是“ab”，结果为：{"ab", "ba"}
* 在然后是“abc”，结果为：将“c”字符插入 P("ab") 得到的所有字符串的任意位置 = {"cab","acb","abc","cba","bca","bac"}
* 递归...

分析：我们可以设计一个递归算法。要生成字符串 s,,1,,...s,,n,, 的所有排列，可以先“砍掉”最后一个字符，首先生成 s,,1,,...s,,n-1,, 的所有排列。得到 s,,1,,...s,,n-1,, 所有排列的结果列表之后，我们循环遍历这个列表，并在每个字符串的任意位置插入 s,,n,,。利用递归法实现。

代码：
	
	#include<iostream>
	#include<string>
	using namespace std;

	int cnt = 0;

	void printAllString(string input_str, string str, int index);

	int main() {
		string input_str;
		cin>>input_str;
		if(input_str.size() == 0) {
			cout<<"The input should not be empty!"<<endl;
		}
		else if(input_str.size() == 1) {
			cout<<"All combinations are: "<<input_str<<endl;
			cnt++;
		}
		else {
			string start_str(1, input_str[0]);
			printAllString(input_str, start_str, 1);
		}
		cout<<cnt<<endl;
		return 0;
	}

	void printAllString(string input_str, string str, int index) {
		if(index == input_str.size() - 1) {
			for(int i = 0; i <= str.size(); i++) {
				string printStr(str);
				cout<<printStr.insert(i, 1, input_str[index])<<endl;
				cnt++;
			}
			return;
		}
		else {
			for(int i = 0; i <= str.size(); i++) {
				string tmpStr(str);
				printAllString(input_str, tmpStr.insert(i,1,input_str[index]), index+1);
			}
		}
	}

上面的代码中，若输入 "abcdefg"，输出 cnt = 7! = 5040，说明代码是无误的。

下面对这道题进行一些扩展，将 "abcdefg" 的子串（包含它本身）的所有可能的排列组合考虑在内。那么此时的集合为：{"a","b","c","d","e","f","g","ab","ba","ac","ca",...}。

此时，需要考虑某个字符是否存在的问题。我们可以设置一个 n 位二进制数，n 为输入的字符串的长度，例如："abcdefg" 对应 7 位二进制数，0001000 表示只有 "d" 存在，1110000 表示 "abc"，求得这些子串后，再考虑它们内部的字符的排列组合即可（可以复用前面的 printAllString 函数）。

代码如下：

	
	#include<iostream>
	#include<string>
	#include<bitset>
	#include<cmath>
	using namespace std;

	int cnt = 0;

	void printAllString(string input_str, string str, int index);

	int main() {
		string input_str;
		cin>>input_str;
		if(input_str.size() == 0 || input_str.size() > 10) {
			cout<<"The size of input string should be between [1, 10]"<<endl;
		}
		else if(input_str.size() == 1) {
			cout<<"All combinations are: "<<input_str<<endl;
			cnt++;
		}
		else {
			int max = pow(2, input_str.size());
			for(int i = 1; i < max; i++) {
				bitset<10> bs(i);
				string newStr;
				for(int j = 0; j < bs.size(); j++) {
					if(bs[j] == 1) {
						newStr += input_str[j];
					}
				}
				if(newStr.size() < 2) {
					cout<<newStr<<endl;
					cnt++;
				}
				else {
					string startStr(1, newStr[0]);
					printAllString(newStr, startStr, 1);
				}
			}
		}
		cout<<cnt<<endl;
		return 0;
	}

	void printAllString(string input_str, string str, int index) {
		if(index == input_str.size() - 1) {
			for(int i = 0; i <= str.size(); i++) {
				string printStr(str);
				cout<<printStr.insert(i, 1, input_str[index])<<endl;
				cnt++;
			}
			return;
		}
		else {
			for(int i = 0; i <= str.size(); i++) {
				string tmpStr(str);
				printAllString(input_str, tmpStr.insert(i,1,input_str[index]), index+1);
			}
		}
	}


还可以继续扩展这道题，求给定字符 'a','b','c','d','e','f','g' 能够组成长度不超过所给字符串个数 n 的所有字符串，允许重复。那么情况有：

* "a","aa","aaa","aaaa","aaaaa","aaaaaa","aaaaaaa"
* "ab","abb",...
* ...

分析：类似于上面的 bitset，此时我们可以设置一个 n 位的 n+1 进制数。对于 "abcdefg" 来说就是 7 位 8 进制数，1234567 -> "abcdefg"，1111111 -> "aaaaaaa", 0000023 -> "bc"，以此类推。我们将这个 7 位 8 进制数从 1 循环至 7777777，每一个值对应题目要求的一种字符串情况。

代码如下：
	
	#include<iostream>
	#include<vector>
	#include<cmath>
	#include<map>

	using namespace std;

	long cnt = 0;

	int main() {
		string input_str;
		cin>>input_str;
		if(input_str.size() == 1) {
			cout<<input_str<<endl;
			cnt++;
		}
		long max = pow(input_str.size() + 1, input_str.size());

		vector<long> savedRlt;
		long init = 1;

		for(int i = 0; i < input_str.size(); i++) {
			savedRlt.push_back(init);
			init *= (input_str.size() + 1);
		}

		map<int,char> mapIntChar;
		mapIntChar.insert(pair<int,char>(0,'\0'));
		for(int i = 0; i < input_str.size(); i++) {
			mapIntChar.insert(pair<int,char>(i+1,input_str[i]));
		}

		for(long i = 1; i < max; i++) {
			string newStr;
			long tmp_i = i;
			for(int j = input_str.size() - 1; j >= 0; j--) {
				long rlt = tmp_i / savedRlt[j];
				map<int,char>::iterator iter = mapIntChar.find(rlt);
				newStr += (*iter).second;
				tmp_i -= rlt * savedRlt[j];
			}
			cout<<newStr<<endl;
			cnt++;
		}
		cout<<"cnt = "<<cnt<<endl;
		return 0;
	}

但是这种情况会导致有重复情况发生，例如：1000000 -> "a"，0000001 -> "a"。暂时还没想到解决办法。
