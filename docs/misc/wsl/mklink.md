
`mklink` : 本质上是一个创建链接的工具，这里使用mklink 欺骗系统，使系统误以为还是安装在了C盘

## **迁移**

### **1.定位Linux子系统的文件系统位置**

正常是：`C:\Users\xxxx\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows`(_xxxxxxx)(xxxxx可能有也可能没有，反正我是有(79rhkp1fndgsc))
直接粗暴的方法的是：先装一遍Linux子系统，在`C:\Users\xxxx\AppData\Local\Packages\`
下查看带有类似 CanonicalGroupLimited.UbuntuonWindows 字眼的新文件夹，记下它的名字

## **2.开始迁移**

### **(1)先卸载Linux子系统**。

卸载的原因在于Linux子系统下的文件系统的权限更改十分复杂，这里面的一些文件不属于Windows下的管理员用户所有，也不属于你的用户，它就是Linux下用户所有的，使用一般的修改权限文件方法很容易出问题。因此还是推荐先备份后再卸载。

### **(2)创建软链接**

使用管理员打开`cmd`， 输入下面的命令：

```
> mklink /j C:\Users\XXXX\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79rhkp1fndgsc  D:\WSL-ubuntu\
```

`D:\WSL-ubuntu\`即非系统盘的位置

### (3)**创建成功后再打开应用商店，安装Linux子系统**

### (4)**问题！**

在这里我花了很长的时间查文档寻求解决方法，结果都是些简单问题0.0....

#### **问题1**： 出现子系统无法安装，错误代码0x80070005

问题出现在文件权限上。解决方法：

对着你非系统盘的储存文件夹，右键->属性->安全->编辑->设置完全控制，即可解决。

或：icacls D:\WSL-ubuntu\ /grant "你的用户名:(OI)(CI)(F)"

#### 问题2：安装启动子系统后，卡在installing.....

这里问题出现在LxssManager服务上。

*Lxss Manager 服务支持运行本机 ELF 二进制文件。该服务提供在 Windows 上运行 ELF 二进制文件所需的基础结构。如果停止或禁用该服务，这些二进制文件将不再运行。*

解决方法：重启电脑->此电脑->右键->管理->服务->找到*LxssManager*->启动

## 迁移总结

同样的道理，使用 `mklink` 工具可以将其他大文件迁移到非系统盘后再创造软链接，用于减小C盘负担是很不错的。接下来开始折腾优化0.0...