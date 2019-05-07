# Uipath开发笔记 - LINQ初学

## 什么是LINQ？
> LINQ（Language Integrated Query）即语言集成查询，可以简单理解成一种能够跨语言实现查询的语法。
> 它可以在C#，SQL，XML，VB，.NET中通用，代码紧凑且已读，可以用一个查询语句在多个数据源中完成查询。

## LINQ语句的结构和语法

LINQ查询由3个部分组成：**获取数据源 - 创建查询- -执行查询。**
下图体现了这一结构：
![title](https://i.loli.net/2019/04/16/5cb580da8959d.png)


注意在 LINQ 中，查询的执行与查询本身截然不同；换句话说，如果只是创建查询变量，则不会检索任何数据。

LINQ语句有两种写法。均可实现想要的效果。
如果要在UIpath使用，在基础LINQ语句的基础上还要注意加上数据整理和转换的方法。
### 查询表达式语法Query Expression
此方法更接近SQL语法，结构如下：
```
1. from<range variable> in <IEnumerable<T> or IQueryable<T> Collection>
2. <Standard Query  Operators> <lambda expression>
3. <select or groupBy operator> <result   formation>
```
1.语句由from开头，from语句指定一个变量元素，在指定的集合中完成查询。
2.from之后可以使用不同的标准查询运算符来过滤，分组，连接集合的元素。通常使用lambda语句来完成。最常见的查询就是where xxx = xxx
3.select 或 groupby结尾

实例1：
`var result = from p in ints where p % 2 == 0 select p;`

实例2（一个在Uipath中使用的表达式）：
`(from r in invoiceLog.AsEnumerable() select r.Field(of string)("ERP品名")).ToList()`

### 方法语法Fluent Syntax
此方法比查询表达式语法更为紧凑，主要利用System.Linq.Enumerable类中定义的扩展方法和Lambda表达式方式进行查询，类似于如何调用任何类的扩展方法。

实例1，扩展方法为where：
`var result = ints.Where(p => p % 2 == 0).ToArray();`
实例2，扩展方法为select，表达式采用的是SQL：
`DataTableName.Select("column3< >''").CopyToDataTable()`

### LINQ中的常用方法
#### Select
#### Where
#### GroupBy
#### Join
#### 


### 关于lambda表达式
`=>`被称为lambda运算符。可以看出上面的实例1中`p => p % 2 == 0`就是一个lambda表达式。

LINQ中不是必须使用lambda表达式，但是lambda具有一些很强大的特性。

1、Lambda表达式是一种表示匿名方法的更短的方法。 
2、Lambda表达式语法：parameters =>正文表达式
3、Lambda表达式可以在（）中具有零个或多个参数。 
4、Lambda表达式可以在大括号{}中的正文表达式中有一条或多条语句。 
5、Lambda表达式可以分配给Func，Action或Predicate委托。
6、Lambda表达式可以以类似的方式调用委托。


## 更多资料
[C#中的LINQ - MSDN](https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/linq/getting-started-with-linq)