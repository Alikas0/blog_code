突然间发现子系统这种新鲜东西，故写篇笔记整理一下

基于windows10

## 1.启动Windows Subsystem for Linux

开始WSL之旅的第一步是`启动Windows Subsystem for Linux功能`，有两种方法实现：

### (1)通过命令行

以`管理员`身份打开`PowerShell`。右键单击屏幕左下角`“开始”`菜单，找到“Windows PowerShell(管理员)”并打开

复制以下命令并运行

```text
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

安装完成后重启电脑~

### (2)通过控制面板

打开屏幕左下角“Cortana搜索框”输入“control panel”搜索并打开搜索结果中的“控制面板”

找到启用或关闭Windows功能，如图所示，打开“程序”——“启用或关闭Windows功能”，找到并勾选“适用于Linux的Windows子系统”，点击“确定”。

安装完成后重启电脑~

## 2.通过应用商店安装Linux发行版

打开Win10[应用商店](https://link.zhihu.com/?target=https%3A//www.microsoft.com/zh-cn/store/apps/)搜索你喜欢的Linux发行版并安装。目前，WSL支持[Ubuntu](https://link.zhihu.com/?target=https%3A//www.microsoft.com/zh-cn/store/p/ubuntu/9nblggh4msv6)，[Kali Linux](https://link.zhihu.com/?target=https%3A//www.microsoft.com/zh-cn/store/p/kali-linux/9pkr34tncv07)，[GNU](https://link.zhihu.com/?target=https%3A//www.microsoft.com/zh-cn/store/p/debian-gnu-linux/9msvkqc78pk6)，[OpenSUSE](https://link.zhihu.com/?target=https%3A//www.microsoft.com/zh-cn/store/p/opensuse-leap-42/9njvjts82tjx)等发行版。

以安装`Ubuntu`为例，安装完成后搜索并打开`Ubuntu`执行后续安装。

安装完成后按提示输入默认用户名、密码。

在这里时，在输入用户名、密码时我将安装窗口直接关闭(才不会说是因为点错了呢！)，结果，再次打开就直接是root，算是误打误撞？反正以后打开都是root权限，有、强0.0

而在这里也出现了一个问题，我想装的东西太多了，C盘不够用咋办？软链接到非系统盘呗！
