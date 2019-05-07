# Uipath开发笔记 - 使用字典批量管理线下配置表参数

> Uipath开发基础中的基础，读过REFramework或官方培训的人应该都知道，这里简单记录一下。
> 


1. 新建Dictionary（务必先new一下，不然执行会报错）
需要注意如果指定字典的参数类型时需要加of：
![title](https://i.loli.net/2019/04/17/5cb6900c3c47e.png)
2. 读取Excel/CSV配置表中的数据
表中Name列是参数名，Value是参数值
![title](https://i.loli.net/2019/04/16/5cb576de86556.png)
3. For each处理数据表，使用Assign将每一行的Name作为Key，Value作为值存入字典中。
![title](https://i.loli.net/2019/04/16/5cb57772560a1.png)
4. 之后就可以直接将整个字典作为参数很方便地在多个子流程中传递了。
![title](https://i.loli.net/2019/04/16/5cb5778e08d38.png)

5. 需要取用某个参数时，使用参数名从字典中取值：
![title](https://i.loli.net/2019/04/16/5cb577ece564a.png)