---
layout: post
title: JavaScript 字符串替换
category: [JavaScript]
tags: [JavaScript]
---

JavaScript 中字符串替换的方法是 `str.replace("oldStr", "newStr")`

```java
str.replace(regexp/substr,replacement)
```
- regexp/substr: 规定子字符串或要替换的模式的 RegExp 对象。如果该值是一个字符串，则将它作为要检索的直接量文本模式，而不是首先被转换为 RegExp 对象。
- replacement: 一个字符串值。规定了替换文本或生成替换文本的函数。

## 替换第一次出现的字符串

```java
var str = "hello hello world."
str.replace(/hello/,"dali")
// 输出 "dali hello world."
```

## 全局替换

```java
var str = "hello hello world."
str.replace(/hello/g,"dali")
// 输出 "dali dali world."
```

## 参考资料

<http://www.w3school.com.cn/jsref/jsref_replace.asp>