---
title: Python中的浅拷贝与深拷贝
tags: Python
categories: language
---

* TOC
{:toc}

# Python中的浅拷贝与深拷贝

*声明：本文章中的内存分析图仅作示意图使用，真实的内存状态与此有差别*

## 列表（list）中

### 浅拷贝

首先，介绍一下三种浅拷贝的方法

```python
old_list = [1, 2, [3, 4, 5]]
# method 1
new_list = old_list.copy()
# method 2
new_list = old_list[:]
# method 3
import copy
new_list = copy.copy(old_list)
```

然后，看一下浅拷贝的特性

```python
old_list = [1, 2, [3, 4, 5]]
new_list = old_list[:]

old_list[0] = 6
old_list[2][0] = 6
# ===print======
print('old list')
print(old_list)
print('new list')
print(new_list)

'''result
old list
[6, 2, [6, 4, 5]]
new list
[1, 2, [6, 4, 5]]
'''
```

可以看到，对于第一层的内容，旧的`list`修改后不影响新的`list`，但是，对于`old_list`中的列表，修改之后会对`new_list`有影响。

我们通过内存来分析，两者的内存示意图如下图所示，其中`old_list`前两个元素都是存储的值，而第三个元素存储的是列表的地址，指向`[3, 4, 5]`这个列表。

而对于`new_list`，其只复制第一层的内容，他申请的内存里面存储的内容与`old_list`一致。也就是说，`new_list`第三个元素也是存储的列表的地址。

![内存示意图](./images/python/1.jpg)

所以我们就能够知道，修改`old_list`的前两个元素，对`new_list`没有影响，而如果修改第三个元素，由于`old_list`和`new_list`的第三个元素指向同一个列表，则`new_list`的第三个元素也被修改了。

而如果直接改变`old_list`的第三个值，不是改变其指向的列表的话，会有如下结果

```python
old_list = [1, 2, [3, 4, 5]]
new_list = old_list[:]

old_list[0] = 6
old_list[2] = 3
# ===print======
print('old list')
print(old_list)
print('new list')
print(new_list)

'''result
old list
[6, 2, 3]
new list
[1, 2, [3, 4, 5]]
'''
```

原因：更改之后，内存中的状态如下图

![更改后内存示意图](./images/python/2.jpg)

出现这个结果的原因，与`new_list`中第一个元素仍然保持`1`是一样的。

由于直接改变了`old_list`的第三个元素，更改后，其存储的不是地址了，而是新的内容`3`。而`new_list`里面存储的仍然是嵌套列表的地址。

### 深拷贝

深拷贝可以通过`copy`模块中的`deepcopy()`方法实现，如下

```python
import copy

old_list = [1, 2, [3, 4, 5]]
new_list = copy.deepcopy(old_list)

old_list[0] = 6
old_list[2][0] = 6
# ===print======
print('old list')
print(old_list)
print('new list')
print(new_list)

'''result
old list
[6, 2, [6, 4, 5]]
new list
[1, 2, [3, 4, 5]]
'''
```

通过调用`deepcopy()`函数，将嵌套的列表也拷贝一份，所以有上述结果。

## 字典（list）中

字典与列表是类似的，所以只记录实现的方法，不在进行特性的说明。

### 浅拷贝

浅拷贝方法如下

```python
old_dict = {'1':1, '2':2, '3':[3,4,5]}
# method 1
new_dict = old_dict.copy()
# method 2
import copy
new_dict = copy.copy(old_dict)
```

其特性与列表特性类似，不再赘述。

### 深拷贝

深拷贝的方法也是利用`deepcopy()`函数

```python
import copy

old_dict = {'1':1, '2':2, '3':[3,4,5]}
new_dict = copy.deepcopy(old_dict)

old_dict['1'] = 6
old_dict['3'][1] = 6
# ======print======
print('old dict')
print(old_dict)
print('new dict')
print(new_dict)

'''result
old dict
{'1': 6, '2': 2, '3': [3, 6, 5]}
new dict
{'1': 1, '2': 2, '3': [3, 4, 5]}
'''
```