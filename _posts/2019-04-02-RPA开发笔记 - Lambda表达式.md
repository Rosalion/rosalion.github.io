# Lambda表达式

Lambda表达式是使用一些特殊语法表示匿名方法的较短方法。
Lambad表达式在Python和.NET中均可使用。
## 基础Lambda语法

最基本的Lambda表达式语法如下：（参数列表）=>{方法体}
说明：

1. 参数列表中的参数类型可以是明确类型或者推断类型。
2. 如果是推断类型，则参数的数据类型将由编辑器根据上下文自动推断出来。

通过下面的例子可以初步理解Lambda的基本逻辑：
> 现在有一个匿名方法：`delegate(int item) { return item % 2 == 0; };`
> 
> 1. Lambda表达式从匿名方法演变，首先删除delegate关键字和参数类型并添加Lambda运算符=>，演变后的代码如下：
>  ` (item)=>{return item % 2 == 0;}`
>  
> 2. 如果我们只有一个返回值的语句，那么我们不需要花括号、返回和分号，所以我们可以去掉这些符号，演变后的代码如下：
>  `(item)=>item %2 == 0`
>  
> 3. 如果我们只有一个参数，我们也可以删除（），代码如下：
> `item=>item %2 == 0`
> 
> 因此，我们得到最终的Lambda表达式：`item => item % 2 == 0`，其中`item`是参数，`=>`是Lambda运算符，`item % 2 == 0`是正文表达式。

## 多参数的Lambda

如果需要传递多个参数，那么必须将参数括在括号内，如下所示：
`(ints, item) => ints.Contains(item);`
 如果不想使用推断类型，那么可以给出每个参数的类型，如下所示：
 `(int[] ints, int item) => ints.Contains(item)`
 
 ## 不带参数的Lambda
在Lambda表达式中可以没有参数，如下所示：
`() => Console.WriteLine("这是一个不带任何参数的Lambda表达式");`


## 多行表达式的Lambda

前面已经讲过，如果正文表达式有一个语句，那么可以去掉方法体外面的大括号。
如果正文表达式中有多条语句，那么必须用大括号将正文表达式括起来，如下所示：
```
(ints, item) =>
{
        Console.WriteLine("这是包含多条语句的Lambda表达式");
        return ints.Contains(item);
}; 
```

## Lambda表达式中的局部变量

你可以在表达式的主体中声明一个变量，以便在表达式主体的任何位置使用它，如下所示：
```
ints => 
{
       int item = 10;
       return ints.Contains(item);
};
```

## Lambda的内置泛型委托

### Func委托
当你想从lambda表达式返回一些东西时，使用Func <> delegate。 

![title](https://i.loli.net/2019/04/16/5cb57d460a6e0.png)
其中T是输入参数的类型，TResult是返回类型。

示例代码如下：

```
Func<int[], bool> isContains = p => p.Equals(10);
int[] ints = { 5, 2, 0, 66, 4, 32, 7, 1 };
bool isEquals = isContains(ints);
```
在上面的例子中，Func委托期望第一个输入参数是int[]类型，返回类型是boolean。Lambda表达式是p => p.Equals(10)。

### Action委托

与Func委托不同，Action委托只能有输入参数。 当不需要从lambda表达式返回任何值时，请使用Action委托类型。

示例代码如下：

```
Action<int[]> PrintItem = p => 
{
       foreach (int item in p)
       {
             Console.WriteLine(item);
       }
};
int[] ints = { 5, 2, 0, 66, 4, 32, 7, 1 };
PrintItem(ints);
```


## 什么时候应当使用Lambda？


### 在LINQ中使用Lambda

通常情况下，Lambda表达式与LINQ查询一起使用。
枚举静态类包括接受`Func <TSource，bool>`的`IEnumerable <T>`的`Where`扩展方法。`IEnumerable <Int>`集合的`Where（）`扩展方法需要传递`Func <Student，bool>`，如下所示：
![title](https://i.loli.net/2019/04/16/5cb57d28c2d5e.png)


现在，您可以将分配给Func委托的lambda表达式传递给方法语法中的Where（）扩展方法，如下所示：
```
Func<int, bool> isContains = p =>p.Equals (4);
int[] ints = { 5, 2, 0, 66, 4, 32, 7, 1 };
var result = ints.Where(isContains).ToList();
```
### 在Python中使用Lambda
Python中 使用 lambda 来创建匿名函数。
所谓匿名，意即不再使用 def 语句这样标准的形式定义一个函数。

* lambda 只是一个表达式，函数体比 def 简单很多。
* lambda的主体是一个表达式，而不是一个代码块。
* 仅仅能在lambda表达式中封装有限的逻辑进去。
* lambda 函数拥有自己的命名空间，且不能访问自己参数列表之外或全局命名空间里的参数。
* 虽然lambda函数看起来只能写一行，却不等同于C或C++的内联函数，后者的目的是调用小函数时不占用栈内存从而增加运行效率。

**语法：**
lambda 函数的语法只包含一个语句，结构：
`lambda [arg1 [,arg2,.....argn]]:expression`

实例：
```
#!/usr/bin/python3 
# 可写函数说明
sum = lambda arg1, arg2: arg1 + arg2 
# 调用sum函数 
print ("相加后的值为 : ", sum( 10, 20 )) 
print ("相加后的值为 : ", sum( 20, 20 ))
```
以上实例输出结果：
```
相加后的值为 :  30
相加后的值为 :  40
```


## Lambda总结

>1. Lambda表达式是一种表示匿名方法的更短的方法。
>2. Lambda表达式语法：parameters =>正文表达式
>3. Lambda表达式可以在（）中具有零个或多个参数。
>4. Lambda表达式可以在大括号{}中的正文表达式中有一条或多条语句。
>5. Lambda表达式可以分配给Func，Action或Predicate委托。
>6. Lambda表达式可以以类似的方式调用委托。

[参考文章：1. https://www.cnblogs.com/dotnet261010/p/8280669.html](https://www.cnblogs.com/dotnet261010/p/8280669.html)