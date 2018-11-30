---
title: 四、const常量中iota用法
date: 2018-11-30 19:45:49
tags: [Golang]
categories: [语言基础]
---

### 1、常规用法
代码：
```
func constTest() {
	const (
		i = iota
		a = iota
		b = iota
		c = iota
	)
	fmt.Println(i, a, b, c)
}
```
输出：

```
0 1 2 3
```

### 2、简洁用法
代码：
```
func constTest() {
	const (
		i = iota
		a
		b
		c
	)
	fmt.Println(i, a, b, c)
}
```
输出：

```
0 1 2 3
```


### 3、特别用法
代码：
```
func constTest() {
	const (
		i = iota
		a ,b = iota ,iota
		c = iota
	)
	fmt.Println(i, a, b, c)
}
```
输出：

```
0 1 1 2
```
==注意：== 当在一行时a,b同时定义则第一个递增后面不递增


