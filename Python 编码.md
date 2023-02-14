<!-- 
title:      Python 编码  
author:     小 K  
summary:    Python 中的编码规则
datetime:   2021-08-16 12:37  
tags:       Python  
            编码  
            笔记  
-->

[TOC]

# Python 编码

## 编码分类

* Unicode：为全球的任何一个字符提供了唯一对应的编码
* 将 Unicode 转化为其他类型的编码，如 UTF 类编码，GBK 等。

## Python 的字符编码

### 两种编码

* `<class 'str'>`：Py3 的默认编码形式，使用 Unicode 编码，如 `"我们"`
* `<class 'bytes'>`：通过将 Unicode encode 后的代码，如 utf-8 下的 `b"\xe6\x88\x91\xe4\xbb\xac"`

### 关系

Python 中存在的即 Unicode 和其他编码，或者说 str 类型和 bytes 类型的

str 通过 encode 变成 bytes，bytes 通过 decode 变成 str

### 代码举例

```py
s = "我们"
s8 = s.encode("utf-8")
s16 = s.encode("utf-16")
print(type(s), type(s8), type(s16))
print(s, s8, s16)
print(s8.decode("utf-8"))

# 输出结果：

# <class 'str'> <class 'bytes'> <class 'bytes'>
# 我们 b'\xe6\x88\x91\xe4\xbb\xac' b'\xff\xfe\x11b\xecN'
# 我们
```