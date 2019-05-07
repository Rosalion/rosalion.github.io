
# Uipath开发笔记-string与security string互相转换
>Windows凭据管理器里获取的密码是加密格式的security string，但有一些网页或应用可能需要明文输入密码。因此需要进行转换。

## Security Str转String
```
String plainStr = new System.Net.NetworkCredential(string.Empty, secureStr).Password
```
## String转Security String
```
(new System.Net.NetworkCredential("", “myString”)).SecurePassword
```