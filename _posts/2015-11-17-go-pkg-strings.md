---
layout: post
category: Go编程
title: Usage of Go "strings" Package
tagline: by wubin
tags: [Go, strings, package]
---

荒废了那么久的博客要重新焕发生机了，抓紧时间为入职做准备！

前段时间买了本《Go语言编程》，看了下整体上还不错，就是省略了很多细节的东西，也许这是中文教材的通病吧，所以就去https://golang.org官网上找资料看了。觉得还是需要边学边记才会加深印象，达到更好的效果。

<!--more-->

下面是Go strings包提供的一些函数：

* ###_func Compare(a,b string) int_

直接比较a，b两个字符串，若a==b，返回0；若a<b，返回-1；若a>b，返回1。这与Go内置的string比较操作符==，<，>效果是一样的。貌似有点多余了。

* ###_func Contains(s,substr string) bool_

判断s中是否包含substr。

	strings.Contains("seafood","")	//true
	strings.Contains("","")		//true

* ###_func ContainsAny(s,chars string) bool_

若字符串chars中有一个字符出现在s中，则返回true，否则返回false。

	strings.ContainsAny("team","ab")	//true
	strings.ContainsAny("foo","")		//false
	strings.ContainsAny("","")		//false

* ###_func ContainsRune(s string, r rune) bool_

rune类型，即字符char型，在Go中它与int32是同一个类型。
这个函数判断r是否在s中。

	strings.ContainsRune("team",'a')	//true
	strings.ContainsRune("team",97)		//true, 'a'=97

* ###_func Cou(s,sep string) int_

计算s中出现了多少个非重叠的sep字符串，如果sep为空""，那么就返回1+s字符串的长度。

	strings.Count("cheese","e")	//3
	strings.Count("five","")	//5

* ###_func EqualFold(s,t string) bool_

s和t忽略大小写是否相等。

	strings.EqualFold("Go","go")	//true

* ###—_func Fields(s string) []string_

根据s中的一个或多个连续的空格符把s split成一个数组并返回，若s只有空格，则返回一个空的string list

	stringsFields(" foo  bar baz  ")	// ["foo" "bar" "baz"]

* ###_func FieldsFunc(s string, f func(rune) bool) []string_

FieldFunc将s中满足函数f的字符(串)当成分割点，将s分割成一个字符串列表后返回。如果s中所有的字符都满足函数f或者s本身为空，那么返回空的列表。

	package main

	import (
		"fmt"
		"strings"
		"unicode"
	)

	func main() {
		f := func(c rune) bool {
			return !unicode.IsLetter(c) && !unicode.IsNumber(c)   
		}
		f2 := func(c rune) bool {
			return unicode.IsLetter(c) || unicode.IsNumber(c)	
		}
		fmt.Prinf("Fields are: %q", strings.FieldsFunc(" foo1;bar2,baz3...",f))
		fmt.Prinf("Fields are: %q", strings.FieldsFunc(" foo1;bar2,baz3...",f2))
	}
	// ["foo1" "bar2" "baz3"]
	// [" " ";" "," "..."]

* ###_func HasPrefix(s,prefix string) bool_

字符串s是否具有前缀prefix。

	strings.HasPrefix("123","")	//true

* ###_func HasSuffix(s,suffix string) bool_

字符串s是否具有后缀suffix。

* ###_func Index(s,sep string) int_

返回在s中找到的第一个sep的位置，如果没找到，则返回-1。

	strings.Index("","")	// 0

* ###_func IndexAny(s,chars string) int_

返回在s中找到的第一个属于sep的字符的位置，如果没找到，则返回-1。

	strings.IndexAny("","")	// -1

* ###_func IndexByte(s string,c byte) int_

返回在s中找到的第一个c的位置，如果没找到，则返回-1。

* ###_func IndexFunc(s string,f func(rune) bool) int_

返回s中第一个满足函数f的字符的位置。

	func main() {
		f := func(c rune) bool {
			return unicode.Is(unicode.Han,c)   
		}
		fmt.Println(strings.IndexFunc("Hello, 世界",f))
		fmt.Println(strings.IndexFunc("Hello, world",f))
	}

* ###_func Join(a []string,sep string) string_

以sep为分隔符，将a组成一个长字符串。

	s := []string{"foo","bar","baz"}
	strings.Join(s,",")	// "foo,bar,baz"


