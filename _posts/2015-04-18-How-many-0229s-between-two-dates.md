---
layout: post
category: 比赛
title: 微软编程之美-2月29日
tags: [比赛,C++,algorithm]
tagline: by wubin
---

###描述

给定两个日期，计算这两个日期之间有多少个2月29日（包括起始日期）。

只有闰年有2月29日，满足以下一个条件的年份为闰年：

1. 年份能被4整除但不能被100整除
2. 年份能被400整除

一些限制：

* 时间限制:2000ms
* 单点时限:1000ms
* 内存限制:256MB

###输入

第一行为一个整数T，表示数据组数。

之后每组数据包含两行。每一行格式为"month day, year"，表示一个日期。month为{"January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November" , "December"}中的一个字符串。day与year为两个数字。

数据保证给定的日期合法且第一个日期早于或等于第二个日期。

###输出

对于每组数据输出一行，形如"Case #X: Y"。X为数据组数，从1开始，Y为答案。

###数据范围

1 ≤ T ≤ 550

小数据：

2000 ≤ year ≤ 3000

大数据：

2000 ≤ year ≤ 2×109

**样例输入**

	4
	January 12, 2012
	March 19, 2012
	August 12, 2899
	August 12, 2901
	August 12, 2000
	August 12, 2005
	February 29, 2004
	February 29, 2012

**样例输出**

	Case #1: 1
	Case #2: 0
	Case #3: 1
	Case #4: 3

###我的思路：

首先将输入的月份的 string map 到 1~12 的整数，然后比较两个年份：

* 若起止年份相等，那么只需判断 2 月 29 日是否在两个日期内；
* 若起止年份不等，首先判断起始年份是否为闰年，若为闰年，起始日期是否早于 2 月 29 日，若早于，那么 count++；然后判断终止年份是否为闰年，若为闰年，终止日期是否晚于 2 月 29 日，若晚于，那么 count++；剩下的除起始年份和终止年份之间的年份，则按照与起始年份最接近的能够被 4 整除的年份开始遍历，步长为 4，若为闰年，则 count++。

通过以上步骤后，输出 count 即可。

####代码如下：

	#include <iostream>
	#include <string>
	#include <vector>
	#include <map>
	using namespace std;

	class Dates
	{
	public:
	    //begin
	    unsigned beginYear;
	    unsigned beginMonth;
	    unsigned beginDay;
	    //end
	    unsigned endYear;
	    unsigned endMonth;
	    unsigned endDay;
	};

	bool isLeapYear(unsigned year) {
	    return ((year % 4 == 0) && (year % 100 != 0)) || (year % 400 == 0);
	}

	int count0229(Dates &dates) {
	    int count = 0;
	    if(dates.beginYear == dates.endYear) {
	        if(isLeapYear(dates.beginYear)) {
	            if(dates.beginMonth*100 + dates.beginDay <= 229 && dates.endMonth*100 + dates.endDay >= 229)
	                count++;
	        }
	    }
	    else {
	        if(isLeapYear(dates.beginYear)) {
	            if(dates.beginMonth*100 + dates.beginDay <= 229)
	                count++;
	        }
	        if(isLeapYear(dates.endYear)) {
	            if(dates.endMonth*100 + dates.endDay >= 229)
	                count++;
	        }
	        unsigned index;
	        if(dates.beginYear % 4 ==0) {
	            index = dates.beginYear + 4;
	        }
	        else {
	            index = dates.beginYear + 4 - (dates.beginYear % 4);
	        }
	        while(index < dates.endYear) {
	            if(isLeapYear(index))
	                count++;
	            index += 4;
	        }
	    }
	    return count;
	}

	int main()
	{
	    map<string, unsigned> mapStringToUnsigned;
	    mapStringToUnsigned.insert(pair<string, unsigned>("January", 1));
	    mapStringToUnsigned.insert(pair<string, unsigned>("February", 2));
	    mapStringToUnsigned.insert(pair<string, unsigned>("March", 3));
	    mapStringToUnsigned.insert(pair<string, unsigned>("April", 4));
	    mapStringToUnsigned.insert(pair<string, unsigned>("May", 5));
	    mapStringToUnsigned.insert(pair<string, unsigned>("June", 6));
	    mapStringToUnsigned.insert(pair<string, unsigned>("July", 7));
	    mapStringToUnsigned.insert(pair<string, unsigned>("August", 8));
	    mapStringToUnsigned.insert(pair<string, unsigned>("September", 9));
	    mapStringToUnsigned.insert(pair<string, unsigned>("October", 10));
	    mapStringToUnsigned.insert(pair<string, unsigned>("November", 11));
	    mapStringToUnsigned.insert(pair<string, unsigned>("December", 12));
	    
	    int size;
	    cin>>size;  //输入的组数
	    vector<Dates> vec;
	    vec.reserve(size);
	    string month;
	    char comma;
	    for(int i = 0; i < size; i++) {
	        Dates dates;
	        cin>>month;
	        dates.beginMonth = mapStringToUnsigned.find(month)->second;
	        cin>>dates.beginDay>>comma>>dates.beginYear;
	        cin>>month;
	        dates.endMonth = mapStringToUnsigned.find(month)->second;
	        cin>>dates.endDay>>comma>>dates.endYear;
	        vec.push_back(dates);
	    }
	    int showI = 1;
	    for(vector<Dates>::iterator iter = vec.begin(); iter != vec.end(); iter++) {
	        cout<<"Case #"<<showI<<": "<<count0229(*iter)<<endl;
	        showI++;
	    }
	    return 0;
	}