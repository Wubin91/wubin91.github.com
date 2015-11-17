---
layout: post
category: Go编程
title: Package strings Usage
tagline: by wubin
tags: [Go, strings, package]
---

荒废了那么久的博客要重新焕发生机了，抓紧时间为入职做准备！

前段时间买了本《Go语言编程》，看了下整体上还不错，就是省略了很多细节的东西，也许这是中文教材的通病吧，所以就去https://golang.org官网上找资料看了。觉得还是需要边学边记才会加深印象，达到更好的效果。

下面是Go strings包提供的一些函数：

* func Compare(a,b string) int

直接比较a，b两个字符串，若a==b，返回0；若a<b，返回-1；若a>b，返回1。这与Go内置的string比较操作符==，<，>效果是一样的。貌似有点多余了。

* func Contains(s,substr string) bool

判断s中是否包含substr。

	strings.Contains("seafood","")	//true
	strings.Contains("","")			//true

* func ContainsAny(s,chars string) bool

若字符串chars中有一个字符出现在s中，则返回true，否则返回false。

	strings.ContainsAny("team","ab")	//true
	strings.ContainsAny("foo","")		//false
	strings.ContainsAny("","")			//false

* func ContainsRune(s string, r rune) bool

rune类型，即字符char型，在Go中它与int32是同一个类型。
这个函数判断r是否在s中。

	strings.ContainsRune("team",'a')	//true
	strings.ContainsRune("team",97)		//true, 'a'=97


