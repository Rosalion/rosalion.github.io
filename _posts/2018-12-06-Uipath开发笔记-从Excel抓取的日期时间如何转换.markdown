---
layout:     post
title:      "问题研究-Uipath如何从Excel抓取格式化的日期时间"
subtitle:   " \"常用表达式\""
date:       2018-12-06 12:00:00
author:     "Rosa"
header-img: "img/post_hello world.jpg"
catalog: true
tags:
    - 技术
    - Uipath
    - RPA
---
# 1. 单独的日期
Excel中的日期格式读取出来可能是一串数字。这种情况下用`DateTime.FromOADate(42713)` 即可转换。此函数需要double类型的输入。注意转换成datetime后时间会是00:00:00.
# 2. 日期+时间

* 这是标准的datetime日期格式，如果读取出的是double，那么使用1的方法即可；

* 如果读取出的是string，那么可以之间使用string转datetime的函数 :
* string转日期：CDATE(datestring)可以轻松完成此工作。
* convert.toDatetime()也可使用date.parseExtract(str1,str2,nothing)也可使用，使用方法待补充。
# 3. 特殊的，仍然无法轻松转换的情况：
先试用get row item取出Excel中的值(Generic Value)



然后使用format value处理其格式


然后output出这个值看一下具体格式，是否可以直接转换成String使用CDATE函数

***
![image](http://github.com/Rosalion/rosalion.github.io/raw/master/img/Rosa_QR1.jpg)
