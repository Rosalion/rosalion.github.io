---
layout:     post
title:      "RPA开发笔记-Datatable去重的几种方法"
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
> 本文介绍了几种给datatable去重的方法。有些情况下需要删除数据表中的重复行，日常应用中，曾使用过以下三种方法；如果有大佬有更优的方法，欢迎探讨~

## 第一种：采用DataView.ToTable()方法（推荐）

DataView.ToTable 方法
.NET Framework 2.0

其根据现有 DataView 中的行，创建并返回一个新的 DataTable。
`Uipath写法: Assign newdt= new dataview(dt1name).totable(true,"ID","NAME")`

| 表达式 |  |
| --- | --- |
| DataView.ToTable ()  | 根据现有 DataView 中的行，创建并返回一个新的 DataTable。 由 .NET Compact Framework 支持。 |
| DataView.ToTable (String)   | 根据现有 DataView 中的行，创建并返回一个新的 DataTable。 由 .NET Compact Framework 支持。 |
| DataView.ToTable (Boolean, String[]) |  |
| DataView.ToTable (String, Boolean, String[]) |  ToTable (string tableName, **bool distinct**, params string[] columnNames)|
 


## 第二种：循环遍历+DataTable.Select()
利用for循环遍历DataTable的数据行，利用DataTable.Select 方法判断是否重复，如果重复，则利用DataTable.Rows.RemoveAt(Index)删除重复的那一行。
效率太低，不建议此方法。


## 第三种：数据库直接去除重复

SQL代码如下:

`select  distinct * from 表名`

