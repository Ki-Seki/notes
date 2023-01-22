title:      Python 各种包的使用方法  
author:     小 K  
summary:    包含各种 Python 常见包的使用方法  
datetime:   2021-02-27 17:32  
tags:       Python  
            json  
            pickle  
            datetime  
            re
            xlsxwriter
            
[TOC]

# 1. Python 各种包的使用方法

## 1.1. json & pickle

两个包的功能如下：

|包|功能|
|---|---|
|json|Python 类 JSON 对象 ↔ 字符串|
|pickle|Python 任意对象 ↔ 仅 pickle 可读的二进制序列|

他们都有如下方法：

|方法|作用|
|---|---|
|dumps  |字符串化或序列化|
|dump   |将 Python 对象保存为字符或二进制文件|
|loads  |反字符串化或反序列化|
|load   |从文件中解析出 Python 对象|

## 1.2. datetime

包含 3 个常见类：`datetime.datetime`，`datetime.date`，`datetime.time`。

他们都包括一些常见的方法，包括获取当前时间、格式化输入/输出等。

## 1.3. re

Python 正则表达式库。

注意：使用 `r'我是正则表达式'` 以减少转义次数。

最常用的方法：

* 从头开始匹配：`match(pattern, string, flags=0)`
* 从任意位置开始匹配：`search(pattern, string, flags=0)`

他们都返回一个 `match_object`，如果匹配成功；这个时候调用 `match_object.group(index)` 以取出想要的部分。

其他可用方法：

* 检索并替换：`re.sub(pattern, repl, string, count=0, flags=0)`
* 找到所有匹配的子串并返回列表：`findall(string[, pos[, endpos]])`
* 找到所有匹配的子串并返回 `MatchObject` 的迭代器：`re.finditer(pattern, string, flags=0)`

## 1.4. XlsxWriter

### 1.4.1. 安装

`pip install XlsxWriter`

### 1.4.2. 示例

```python
def save_to_excel(data, filename, worksheets):
    """
    Save data to a new Excel file.
    :param data: 3-dimensional (l×m×n) data indicating l worksheets, each of which contains an m×n table.
    :param filename: Name of the Excel file.
    :param worksheets: A string-list consists of worksheets' names.
    """
    workbook = xlsxwriter.Workbook(filename)
    for i, item in enumerate(data):
        worksheet = workbook.add_worksheet(worksheets[i])
        for row, row_data in enumerate(item):
            for col, col_data in enumerate(row_data):
                worksheet.write(row, col, col_data)
    workbook.close()

example = [
    [
        [1, 2, 3],
        [4, 5, 6],
    ],
    [
        ['s1', 's2', 's3'],
        ['s4', 's5', 's6'],
    ],
]

save_to_excel(example, 'example.xlsx', ['number_sheet', 'string_sheet'])
```

### 1.4.3. 注意

此包只能完成 Excel 的创建而不能打开并编辑一个已有的 Excel 文件。