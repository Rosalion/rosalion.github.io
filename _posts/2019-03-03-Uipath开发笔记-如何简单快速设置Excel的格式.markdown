---
layout:     post
title:      "RPA开发笔记-如何简单快速设置Excel表格格式"
subtitle:   " \"10分钟学会VBA设置格式\""
date:       2019-03-04 20:00:00
author:     "Rosa"
header-img: "img/post_hello world.jpg"
catalog: true
tags:
    - 技术
    - Uipath
    - RPA
    - VBA
---

# Uipath开发笔记-10分钟学会如何给Excel文件设置格式
>本文介绍了如何通过在Uipath中调用VBA来快速设置常见的Excel格式。
>部分内容参考了网络，不属于我本人。如有侵权，请联系我删除。

## 单元格背景/字体颜色

如果你想改变单元格的背景色用:
` rng.Interior.ColorIndex=`
如果你想改变单元格的字体的颜色:
` rng.font.ColorIndex=`
   
设置什么颜色由指定的颜色序号决定：
<img src="https://rosalion.github.io/img/in-post/post_vba-ColorIndex.png" width="450">

## 单元格边框

### 设置框线类型
设置单元格边框使用语句：
`rng.Borders.LineStyle = `
![LineStyles](http://rosalion.github.io/img/in-post/post_vba-LineStyles.png)

其中第一种xlContinuous表示的就是最基础的边框风格：1倍黑色实线边框；其他还有虚线、双线等。
也可以直接指定风格的值而不是名称。

### 设置边框颜色
边框的颜色有两种设置方法：
使用颜色序号：
`rng.Borders.ColorIndex = 3`
使用RGB号码：
`rng.Borders.Color = RGB(0, 255, 0)`

### 设置边框粗细
`rng.Borders.Weight = xlThick`
同样有几种风格可以套用：

![BorderWeight](http://rosalion.github.io/img/in-post/post_vba-BorderWeight.png)

### 设置框线方向
默认是全包的方向，如果要设置某一边，需要在borders后边直接加括号，在括号里说明方向：
`rng.Borders(xlEdgeBottom).LineStyle = xlContinuous`的效果是将该区域的底部加边框：

![xlEdgeBottom](http://rosalion.github.io/img/in-post/post_vba-LineStyle-xlEdgeBottom.png)

`xlInsideHorizontal`则是该区域的内部每个单元格底部加边框。

其他风格可以在MSDN中搜索并使用。



## 列宽、行高
使用`rng.ColumnWidth = 20`设置列宽。
使用`rng.RowHeight = 20`设置列宽。

## 自动换行

使用`rng.WrapText = true`即可设置单元格为自动换行。

## 如何动态地选择要设置格式的区域？
采用currentRegion可以很方便的选择当前的活动表格区域。效果如下图：
![currentRegion](http://rosalion.github.io/img/in-post/post_vba-CurrentRegion.png)

使用方法参考如下代码：

```
Sub lqxs()
Dim rng As Range
#设定范围
Set rng = [a1].CurrentRegion
#使用该范围
rng.Borders.LineStyle = 1

End Sub
```
## 如何方便地针对一个对象设置其多个格式属性？
使用With语句可以方便地针对一个对象设置其多个属性，省略部分代码：
需要注意与EndWith搭配使用。
```
With rng.Borders
.LineStyle = xlContinuous
.ColorIndex = 3
.Weight = xlThick
End With

```

## 如何指定一个范围？

使用`Range(cell_a,cell_b)`可以指定一个从cell_a到cell_b的范围。可以理解成一个对角线的连续范围。

~~也可以用`Range("cell_a,cell_b")`指定一个包括了cell_a和cell_b的范围。可以理解成两个单元格的区域。~~（有待验证）

此外用cell来指定range的用法还有`Range(Cells(2, 2), Cells(4, 4))`，这个用法等同于`Range("B2:D4")` 和 `Range(B2,D4)`

上述cell_a和cell_b也可以是range_a和range_b。理解可以参考下文。

### 理解Range("B2:B4, D2:D4")和Range("B2:B4", "D2:D4")的区别
先看清楚，上面两种格式一个是把两个区域放在一个双引号里面，用逗号隔开；另一个是把两个区域分别放在双引号里面。
用逗号隔开前者是分别引用B2:B4和D2:D4这两个区域，而后者则表示引用的是从B2:B4开始到D2:D4结束为止的这一整个连续的区域。所以后者其实是等价于Range("B2:D4")。所以虽然使用后者的方式来使用Range也不会报错，但其实通常并不会这么使用。

### 

