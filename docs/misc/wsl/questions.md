## **1.子系统运行32位程序问题**

```
sudo apt update
sudo apt install lib32ncurses5 lib32z1
sudo apt install qemu-user-static
sudo update-binfmts --install i386 /usr/bin/qemu-i386-static --magic '\x7fELF\x01\x01\x01\x03\x00\x00\x00\x00\x00\x00\x00\x00\x03\x00\x03\x00\x01\x00\x00\x00' --mask '\xff\xff\xff\xff\xff\xff\xff\xfc\xff\xff\xff\xff\xff\xff\xff\xff\xf8\xff\xff\xff\xff\xff\xff\xff'
```

## **2.由第1个问题引发的依赖包问题-aptitude**安装

aptitude 与 apt-get 一样，是 Debian 及其衍生系统中功能极其强大的包管理工具。与 apt-get 不同的是，aptitude 在处理依赖问题上更佳一些。举例来说，aptitude 在删除一个包时，会同时删除本身所依赖的包。这样，系统中不会残留无用的包，整个系统更为干净；aptitude在安装软件时，会同时安装/更新其依赖包。

> **不过在国内源安装aptitude是会将apt删除的，而删除后就找不到安装源了，所以我安装的时候是用的Ubuntu自带源安装的，就不会覆盖apt，也就是apt和aptitude共存**

以下是大佬(不是我0.0)总结的一些常用 aptitude 命令，仅供参考。

*aptitude update 更新可用的包列表*
*aptitude upgrade 升级可用的包*
*aptitude dist-upgrade 将系统升级到新的发行版*
*aptitude install pkgname 安装包*
*aptitude remove pkgname 删除包*
*aptitude purge pkgname 删除包及其配置文件*
*aptitude search string 搜索包*
*aptitude show pkgname 显示包的详细信息*
*aptitude clean 删除下载的包文件*
*aptitude autoclean 仅删除过期的包文件*

- aptitude软件包列表中的软件包状态:

  | v    | 虚拟       |
  | ---- | ---------- |
  | B    | 损坏       |
  | u    | 解包       |
  | C    | 预配置     |
  | H    | 预安装     |
  | c    | 卸载未清除 |
  | p    | 清除软件   |
  | i    | 已经安装   |
  | E    | 内部错误   |

- 在aptitude软件包列表中的请求操作:

  | h    | 保持         |
  | ---- | ------------ |
  | p    | 清除         |
  | d    | 删除（卸载） |
  | B    | 损坏         |
  | i    | 安装         |
  | r    | 重装         |
  | u    | 升级         |

  有的问题 apt-get 解决不了，必须使用 aptitude 解决，有的问题，用 aptitude 解决不了，必须使用 apt-get

  **aptitude 解决得更好的地方： install, remove, reinstall（apt-get无此功能）, show（apt-get无此功能）, search（apt-get无此功能）, hold（apt-get无此功能）, unhold（apt-get无此功能）,*

  **apt-get 解决得更好的地方： source（aptitude无此功能）, build-dep （低版本的aptitude没有build-dep功能）*

  *apt-get 跟 aptitude 没什么区别的地方：update, upgrade (apt-get upgrade=aptitude safe-upgrade, apt-get dist-upgrade=aptitude full-upgrgade)*

  ### 3.GUI界面无法链接

  提示

  ```linux
  /usr/lib/python2.7/dist-packages/gtk-2.0/gtk/__init__.py:57: GtkWarning: could not open display
    warnings.warn(str(e), _gtk.Warning)
  Traceback (most recent call last):
    File "/usr/bin/ccsm", line 94, in <module>
      import ccm
    File "/usr/lib/python2.7/dist-packages/ccm/__init__.py", line 1, in <module>
      from ccm.Conflicts import *
    File "/usr/lib/python2.7/dist-packages/ccm/Conflicts.py", line 26, in <module>
      from ccm.Constants import *
    File "/usr/lib/python2.7/dist-packages/ccm/Constants.py", line 30, in <module>
      CurrentScreenNum = gtk.gdk.display_get_default().get_default_screen().get_number()
  AttributeError: 'NoneType' object has no attribute 'get_default_screen'
  ```

  无法解决。