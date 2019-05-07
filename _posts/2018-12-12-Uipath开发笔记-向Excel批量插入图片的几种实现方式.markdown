---
layout:     post
title:      "问题研究-实现Excel批量插入图片的几种方式"
subtitle:   " \"VBA与Python\""
date:       2018-12-12 12:00:00
author:     "Rosa"
header-img: "img/post_hello world.jpg"
catalog: true
tags:
    - 技术
    - Uipath
    - RPA
    - Python
---


>最近做了一个项目，需要将RPA的操作过程截图形成证据链文档，因此研究了一下Excel批量插入图片的几种方法。部分方法学习自网络上的分享想必有前辈已经实现类似项目了，如果有更好的方案，希望各位多多指教~

# Excel 前台操作
大家应该都会，稳定简单，就是效率低了点。具体方法不赘述。
# VBA操作 - shapes.addPicture方法
此方法可以简单快速的往Excel里插入图片，还能很方便的调整长宽高。`worksheet.AddPicture( _Filename_ , _LinkToFile_ , _SaveWithDocument_ , _Left_ , _Top_ , _Width_ , _Height_ )`
官方提供的VB实例:
```
Set myDocument = Worksheets(1) 
myDocument.Shapes.AddPicture _ 
    "c:\microsoft office\clipart\music.bmp", _ 
    True, True, 100, 100, 70, 70
```
[官方文档解释](https://docs.microsoft.com/zh-TW/office/vba/api/Excel.Shapes.AddPicture)
![MSDN上关于AddPicture的解释](http://rosalion.github.io/img/in-post/post_vba-AddPicture.png)
示例：
```
'代码来源网络
Sub 插入图片()
For Each shp In ActiveSheet.Shapes
shp.Delete
Next

On Error Resume Next
addr = ThisWorkbook.Path & "\图库"
AcolumnLastRow = Cells(Rows.Count, 1).End(xlUp).Row

For i = 2 To AcolumnLastRow
    Set Rng = Cells(i, 2)
    Filename = addr & "\" & Trim(Cells(i, 1).Text) & ".jpg"
    '下面的表达式可以实现将图片插入并且匹配单元格的高宽
    Set pic = ActiveSheet.Shapes.AddPicture(Filename, False, True, Rng.Left, Rng.Top, -1, -1)
    pic.Height = Rng.Height
    pic.Left = (Rng.Width - pic.Width) / 2 + Rng.Left
Next i

End Sub
```
有VBA基础的人使用此方法可以非常简洁快速地实现效果。

在Uipath里有两个方法运行VBA，一个是Invoke VBA，一个是Excute Macro。区别是前者是独立运行VBA代码，可以通过txt导入代码；后者则是调用Excel scope指定的Excel文件里的宏。

不过我的电脑上因为一些权限问题Uipath没能成功使用 Invoke VBA执行脚本（在信任中心勾选后仍然保持不允许VB project），所以后来使用了下面的第三种方法。

# Python操作 - 第三方库XlsxWriter（或xlwt）
XlsxWriter中可以使用`insert_image(row, col, image[, options])`实现此功能。除了可以指定插入图片的单元格位置外，还可以调整多种参数。


简单实例如下：
```
import xlsxwriter

book = xlsxwriter.Workbook(r'C:\zrpa\Translation\tryInsertIMG.xlsx')

sheet = book.add_worksheet('demo')

sheet.insert_image('G2', r'C:\Users\Rosa Luo\Desktop\picL.jpg')

book.close()

```
插入的图片支持png,jpg,jpeg,bmp等格式。

*在运行Python脚本时发生了一点小插曲，Run Python Script时Python崩溃，后来换成Load Python Script + Python Method后解决此问题。*
此外还需要考虑权限问题，Python的这个方法可能会受到UAC用户账户控制的影响；我会在一篇文章会里详述Python的权限获取的有关研究。

***
>以下是关于inser_image方法，来源网络的一些高级用法和解释：

>**参数的使用：**
>- row(int) - 单元格所在的行（索引从0开始计数）
>- col(int) - 单元格所在的列（索引从0开始计数）
>- image(string) - 图片文件名（如有需要含路径）
>- options(dict) - 可选的图片位置，缩放，url参数

insert_image()方法接受字典形式的可选参数来定位和缩放图片。可用的参数和它们的默认值有：
```
{
    'x_offset': 0,
    'y_offset': 0,
    'x_scale': 1,
    'y_scale': 1,
    'url': None,
    'tip': None,
    'image_data': None,
    'positioning': None,
}
```
offset的值是以像素为单位的：
```
worksheet1.insert_image('B2', 'python.png', {'x_offset': 15, 'y_offset': 10'})
```
offset的值可以大于基础单元格的宽度与高度。如果想将两个及以上的图片相对于同一单元格对齐，这有时会很有用。

x_scale和y_scale参数可以用于水平及垂直的缩放图片：
```
worksheet.insert_image('B3', 'python.png', {'x_scale': 0.5, 'y_scale': 0.5})
```
url参数可以为图片添加超链接/url，tip参数为含有超链接的图片提供可选的鼠标悬停时的提示信息：
```
worksheet.insert_image('B4', 'python.png', {'url': 'http://python.org'})
```

image_data参数用于在io.BytesIO中添加内存中的字节流：
```
worksheet.insert_image('B5', 'python.png', {'image_data': image_data})
```

当使用image_data参数时文件名必须传递到insert_image()因为这是Excel要求的。在前面的例子中文件名是从URL字符串中提取的。

positioning参数可以用来控制图片对象的位置：
```
worksheet.insert_image('B3', 'python.png', {'positioning': 1})
```
postioning有以下允许的值：

1.移动和调整单元格的大小

2.移动但不调整单元格的大小（默认）

3.不移动或调整单元格的大小

***

注意：

如果由于字体大小大于默认字体大小或者打开了文本换行而导致更改了默认行高，则图片的缩放可能会受到影响。
如果它与插入的图像交叉，你应该使用set_row()方法明确的设定行高来避免此问题。

BMP图片只向后兼容。由于BMP图片不能压缩，通常情况下最好避免使用BMP图片。如果使用BMP图片，必须是24bit、true color、bitmap的。

***

以下是来自网络的示例：[向工作表中插入图片](https://blog.csdn.net/auserbb/article/details/79259328)

```
##############################################################################
#
# An example of inserting images into a worksheet using the XlsxWriter
# Python module.
#
# Copyright 2013-2017, John McNamara, jmcnamara@cpan.org
#
import xlsxwriter


# 创建一个新Excel文件并添加一个工作表。
workbook = xlsxwriter.Workbook('images.xlsx')
worksheet = workbook.add_worksheet()

# 加宽第一列使文本更清晰。
worksheet.set_column('A:A', 30)

# 插入一张图片。
worksheet.write('A2', '向单元格插入一张图片：')
worksheet.insert_image('B2', 'python.png')

# 插入一张位偏移图片。
worksheet.write('A12', '插入一张位偏移图片：')
worksheet.insert_image('B12', 'python.png', {'x_offset': 15, 'y_offset': 10})

# 插入一张缩放了的图片。
worksheet.write('A23', '插入一张缩放了的图片：')
worksheet.insert_image('B23', 'python.png', {'x_scale': 0.5, 'y_scale': 0.5})

workbook.close()
```

以下是来自网络的示例：[从URL或字节流向工作表插入一张图片](https://blog.csdn.net/auserbb/article/details/79259328)

```
##############################################################################
#
# An example of inserting images from a Python BytesIO byte stream into a
# worksheet using the XlsxWriter module.
#
# Copyright 2013-2017, John McNamara, jmcnamara@cpan.org
#

# 导入字节流处理器（byte stream handler.）
from io import BytesIO

# 从Python 2或3之一导入urlopen()
try:
    from urllib.request import urlopen
except ImportError:
    from urllib2 import urlopen


import xlsxwriter

# 创建一个工作簿并添加一个工作表。
workbook  = xlsxwriter.Workbook('images_bytesio.xlsx')
worksheet = workbook.add_worksheet()


# 从远端URL读取图片。
url = 'https://raw.githubusercontent.com/jmcnamara/XlsxWriter/' + \
      'master/examples/logo.png'

image_data = BytesIO(urlopen(url).read())

# 向单元格写入字节流图片。注意，文件名必须显式指定。在这个例子中文件名会从URL字符串读取。
worksheet.insert_image('B2', url, {'image_data': image_data})


# 读取本地图片文件至字节流。注意，insert_image()方法可以直接这么干，此例只是为了演示。
filename   = 'python.png'

image_file = open(filename, 'rb')
image_data = BytesIO(image_file.read())
image_file.close()


# 将字节流图片写入单元格，文件名必须显式指定。
worksheet.insert_image('B8', filename, {'image_data': image_data})

workbook.close()
```
