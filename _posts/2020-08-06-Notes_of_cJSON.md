---
layout: post
title: "cJSON源码阅读笔记"
tags: [C++, 源码阅读]
comments: true
---


cJSON源码下载地址：<https://sourceforge.net/projects/cjson>

## 简介

对于学习c语言来说，是一个入门级别的项目，该项目提供了一个JSON的parser，可以解析JSON的文件或字符串，也可以利用这个库生成JSON文件，README文件中有更详细的介绍。

本文不会详细解析源码，只记录一些觉得值得记录的点，包括：

* cJSON结构体定义的解释
* `parse_string()`中utf16转utf8
* `pow2gt(x)`函数的作用及原理


## cJSON结构体定义的解释

`cJSON`结构体定义如下

```C++
typedef struct cJSON {
    struct cJSON *next,*prev;
    struct cJSON *child;

    int type;

    char *valuestring;
    int valueint;
    double valuedouble;

    char *string;
} cJSON;
```

每个`cJSON`表示一个JSON文件中的一个**名称/值**对，创建时，所有变量都初始化为`0`，**名称**存放在`string`中，**值**根据不同类型存放在不同变量中，`type`记录**值**的类型，不同的类型有如下宏定义

```C++
/* cJSON Types: */
#define cJSON_False         0
#define cJSON_True          1
#define cJSON_NULL          2
#define cJSON_Number        3
#define cJSON_String        4
#define cJSON_Array         5
#define cJSON_Object        6
	
#define cJSON_IsReference   256
#define cJSON_StringIsConst 512
```

如果类型为`cJSON_False`或`cJSON_True`，表示**值**为`false`或`true`，并在`valueint`中保存为`0`或`1`。

如果类型为`cJSON_Number`，则在`valueint`中保存数值的整数部分，在`valuedouble`中保存浮点数形式的数值。

如果类型为`cJSON_String`，则在`valuestring`中保存字符串的**值**。

如果类型为`cJSON_Array`，则利用`next/prev`指针组成双向链表，链表中的每一项都是一个`cJSON`，并可以为任意类型。

如果类型为`cJSON_Object`，表示这个**名称/值**对中的**值**为一个完整的cJSON结构(object)，利用`child`指针指向object中的第一个**名称/值**对，即一个cJSON。

利用这个结构体，形成了`N-tree`的数据结构。


## `parse_string()`中utf16转utf8

`parse_string()`中其他部分都好理解，在utf-16转utf-8部分初看起来有点难度，里面有许多数字不知道意义是什么。

要想理解这段代码，首先需要理解Unicode中utf-32、utf-16、utf-8的表示方式，提供一篇比较清晰的解释文章：[Unicode 编码及 UTF-32, UTF-16 和 UTF-8 - 吃冬瓜群众代表的文章 - 知乎](https://zhuanlan.zhihu.com/p/51202412)

理解了不同编码的规则，就能够理解这段代码了，思路为：解析数字->判断范围->解析第二个数字(可选)->分别提取两位数字的后10位(二进制)->计算所需utf-8位数->转为utf-8

有一点需要特别注意，转为utf-8时，巧妙的利用了`switch`语句，在`switch`语句中，每个`case`后面都没有`break`。


## `pow2gt(x)`函数的作用及原理

这个函数的作用是$\min(n), 2^n >= x$。

这个函数处理的是32位的int数据，以8位数据为例进行讲解

将数据`x-1`的二进制表示为
$$
a_7 \\a_6 \\a_5 \\a_4 \\a_3 \\a_2 \\a_1 \\a_0
$$

`x |= x >> 1`后，变为：
$$
a_7 \\
a_7|a_6 \\
a_6|a_5 \\
a_5|a_4 \\
a_4|a_3 \\
a_3|a_2 \\
a_2|a_1 \\
a_1|a_0
$$

继续经过`x |= x >> 2`后，变为：
$$
a_7 \\
a_7|a_6 \\
a_7|a_6|a_5 \\
a_7|a_6|a_5|a_4 \\
a_6|a_5|a_4|a_3 \\
a_5|a_4|a_3|a_2 \\
a_4|a_3|a_2|a_1 \\
a_3|a_2|a_1|a_0
$$

继续经过`x |= x >> 4`后，变为：
$$
a_7 \\
a_7|a_6 \\
a_7|a_6|a_5 \\
a_7|a_6|a_5|a_4 \\
a_7|a_6|a_5|a_4|a_3 \\
a_7|a_6|a_5|a_4|a_3|a_2 \\
a_7|a_6|a_5|a_4|a_3|a_2|a_1 \\
a_7|a_6|a_5|a_4|a_3|a_2|a_1|a_0
$$

从分析结果可以看出，对于`x-1`，任何一位$a_k$为1，经过处理后，$a_0 \to a_k$都会变为1，即等于$2^n-1$，再经过`++x`后，就得到了想要的结果。

对于原函数，处理32位整型数据，原理一致。