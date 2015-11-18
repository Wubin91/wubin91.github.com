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

* **func Compare(a,b string) int**

直接比较a，b两个字符串，若a==b，返回0；若a<b，返回-1；若a>b，返回1。这与Go内置的string比较操作符==，<，>效果是一样的。貌似有点多余了。

* **func Contains(s,substr string) bool**

判断s中是否包含substr。

	strings.Contains("seafood","")	//true
	strings.Contains("","")		//true

* **func ContainsAny(s,chars string) bool**

若字符串chars中有一个字符出现在s中，则返回true，否则返回false。

	strings.ContainsAny("team","ab")	//true
	strings.ContainsAny("foo","")		//false
	strings.ContainsAny("","")		//false

* **func ContainsRune(s string, r rune) bool**

rune类型，即字符char型，在Go中它与int32是同一个类型。
这个函数判断r是否在s中。

	strings.ContainsRune("team",'a')	//true
	strings.ContainsRune("team",97)		//true, 'a'=97

* **func Cou(s,sep string) int**

计算s中出现了多少个非重叠的sep字符串，如果sep为空""，那么就返回1+s字符串的长度。

	strings.Count("cheese","e")	//3
	strings.Count("five","")	//5

* **func EqualFold(s,t string) bool**

s和t忽略大小写是否相等。

	strings.EqualFold("Go","go")	//true

* **func Fields(s string) []string**

根据s中的一个或多个连续的空格符把s split成一个数组并返回，若s只有空格，则返回一个空的string list

	stringsFields(" foo  bar baz  ")	// ["foo" "bar" "baz"]

* **func FieldsFunc(s string, f func(rune) bool) []string**

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

* **func HasPrefix(s,prefix string) bool**

字符串s是否具有前缀prefix。

	strings.HasPrefix("123","")	//true

* **func HasSuffix(s,suffix string) bool**

字符串s是否具有后缀suffix。

* **func Index(s,sep string) int**

返回在s中找到的第一个sep的位置，如果没找到，则返回-1。

	strings.Index("","")	// 0

* **func IndexAny(s,chars string) int**

返回在s中找到的第一个属于sep的字符的位置，如果没找到，则返回-1。

	strings.IndexAny("","")	// -1

* **func IndexByte(s string,c byte) int**

返回在s中找到的第一个c的位置，如果没找到，则返回-1。

* **func IndexFunc(s string,f func(rune) bool) int**

返回s中第一个满足函数f的字符的位置。

	func main() {
		f := func(c rune) bool {
			return unicode.Is(unicode.Han,c)   
		}
		fmt.Println(strings.IndexFunc("Hello, 世界",f))	// 7
		fmt.Println(strings.IndexFunc("Hello, world",f))	// -1
	}

* **func Join(a []string,sep string) string**

以sep为分隔符，将a组成一个长字符串。

	s := []string{"foo","bar","baz"}
	strings.Join(s,",")	// "foo,bar,baz"

* **func LastIndex(s,sep string) int**

返回在s中找到的最后sep实例的位置。否则返回-1。

* **func LastIndexAny(s,chars string) int**

返回在s中找到的最后一个属于chars的字符的位置，否则返回-1。

* **func LastIndexByte(s string,c byte) int**

返回在s中找到的最后一个c实例的位置，否则返回-1。

* **func LastIndexFunc(s string,f func(rune) bool) int**

返回s中最后一个满足函数f的字符的位置，否则返回-1。

* **func Map(mapping func(rune) rune,s string) string**

返回一个新字符串，该新字符串每一个字符都是从源字符串中的字符通过map函数映射得到，如果某个源字符通过映射函数后得到一个负值，那么该源字符被丢弃。

	func main() {
		rot13 := func(r rune) rune {
			switch {
				case r >= 'A' && r <= 'Z':
					return 'A' + (r - 'A' + 13) % 26
				case r >= 'a' && r <= 'z':
					return 'a' + (r - 'a' + 13) % 26
			}
			return r
		}
		fmt.Println(strings.Map(rot13,"Twas brillig and the slithy gopher..."))
	}
	// "Gjnf oevyyvt naq gur fyvgul tbcure..."

* **func Repeat(s string,count int) string**

返回一个由count个s拷贝组成的新字符串。

* **func Replace(s,old,new string,n int) string**

将s中出现的old字符串n次替换为new字符串，若old=""，而new!=""，那么新字符串为s每一个字符之间插入new之后产生的新字符串。若n=-1，则替换的次数无限制。

	strings.Replace("oink oink oink","k","ky",2)	// oinky oinky oink
	string.Replace("oink oink oink","moo",-1)	// moo moo moo
	
* **func Split(s,sep string) []string**

	strings.Splite("a,b,c","")	//["a" "," "b" "," "c"]
	
* **func SplitAfter(s,sep string) []string**

每次在sep之后split字符串s，返回得到的字符串列表。如果sep=""，那么这将把s分割成一个个的字符形成的字符串，效果和SplitAfterN当count=-1时一样。

	strings.SplitAfter("a,b,c",",")	// ["a," "b," "c"]
	strings.SplitAfter("a,b,c","")	// ["a" "," "b" "," "c"]

* **func SplitAfterN(s,sep string,n int) []string**

与SplitAfter相比，多了一个n参数，指明返回string list中字符串的个数。

	1. n > 0: at most n substrings; the last substring will be the unsplit remainder
	2. n == 0: the result is nil (zero substrings)
	3. n < 0: all substrings

	strings.SplitAfterN("a,b,c",",",2)	// ["a," "b,c"]

* **func SplitN(s,sep string,n int) []string**

与Split相比，多了一个n参数，指明返回string list中字符串的个数。

* **func Title(s string) string**

将字符串中单词首字母大写。

* **func ToLower(s string) string**

将字符串中所有字母变成小写。

* **func ToTitle(s string) string**

把s中所有字母变为大写。

* **func ToUpper(s string) string**

将字符串中所有字母变为大写。

