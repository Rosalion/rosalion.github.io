---
layout:     post
title:      "问题研究-Uipath中有关datatable的各种常见数据处理"
subtitle:   " \"常用表达式\""
date:       2018-12-05 12:00:00
author:     "Rosa"
header-img: "img/post_hello world.jpg"
catalog: true
tags:
    - 技术
    - Uipath
    - RPA
---
[TOC]
# datatable的常见表达
>开发过程中经常需要对datatable进行各种各样的数据处理，这里总结了一些常用逻辑，其中部分来自Uipath社区分享。

## datatable去重
### 方法一：采用DataView.ToTable()方法（推荐）

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
 


### 方法二：循环遍历+DataTable.Select()
利用for循环遍历DataTable的数据行，利用DataTable.Select 方法判断是否重复，如果重复，则利用DataTable.Rows.RemoveAt(Index)删除重复的那一行。
效率太低，不建议此方法。


### 方法三：数据库直接去除重复

SQL代码如下:

`select  distinct * from 表名`

## 将数据表中的指定列转换为list
使用linq可以非常方便地实现：
```
(from r in invoiceLog.AsEnumerable() select r.Field(of string)("ERP品名")).ToList()
```


## 删除表中的空白行
Use below code to remove empty row from the table.
（空白行=该行的每一列都没有内容）
```
DataTableName=DataTableName.Rows.Cast(Of DataRow)().Where(Function(row) 
Not row.ItemArray.All(Function(field) field Is DBNull.Value Or field.Equals(""))).CopyToDataTable()
```

## 删除表中特定列空白的行
Remove rows based on particular column is empty.
```
DataTableName.Select("Convert(column3, System.String)< >''").CopyToDataTable()
or
DataTableName.Select("column3< >''").CopyToDataTable()
```

## 将表按照指定列排序
Sorting the data table based on particular column name.
```
dt = dt.Select("",[ColumnName] asc").CopyToDataTable()
```
## 取datatable的前几行

```
DT = DT.AsEnumerable().Take(N).CopyToDataTable<DataRow>()
```


## 获取表中某一列的最大值
Get Max value from the data table column value
```
int MaxValue=Convert.ToInt32(dt.AsEnumerable().Max(Function(row) row("Column2")))
```

## Join 两个datatable
这个需要注意，有以下几个步骤：
**1.指定主键**

![指定主键](https://upload-images.jianshu.io/upload_images/11970634-54af5078ddfcf6f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)
**2.调用Merge方法**
![image.png](https://upload-images.jianshu.io/upload_images/11970634-7de34656d51bcebe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/840)

此外需要注意调用方法时的target与参数选择，上图中target选择dt1则合并后的结果在dt1中。


## 找到同一张表中指定列重复的行
Duplicate records from the same data table and
If you want specific Column alone mention the column name
```
(From p in dt.Select() where( From q in dt.Select() where q("ColumnName").Equals(p("ColumnName")) Select q).ToArray.Count>1 Select p).ToArray.CopyToDataTable()
```

## 同一表中的重复记录
Duplicate records from the same data table
```
(From p in dt.Select() where( From q in dt.Select() where string.Join(",",q.ItemArray).Equals(string.Join(",",p.ItemArray)) Select q).ToArray.Count>1 Select p).ToArray.CopyToDataTable()
```
## 将一行数据从表A复制到表B

**1. （推荐）：使用importRow方法，可以非常便捷的完成这一操作。**
注意Target是要复制到的最终表，要复制的dataRow放在Parameter里。
![ImportRow](http://rosalion.github.io/img/in-post/post_UP-Method-ImportRow.png)

**2. 先将原row转换成ArrayRow再使用add Row活动：**`row.itemArray`

需要注意如果直接在for each row的活动中将row对象使用Add Row活动插入，会导致报错提示“该行已存在于另一个数据表中”

## 如何检索datatable中指定的列
row collection = datatable1.select("[COL NAME] = '指定值 '")
indexof.datatable(row)
