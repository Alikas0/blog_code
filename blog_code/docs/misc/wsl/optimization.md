
## 1.更换apt源

因为Linux子系统的apt源使用的是官方源，需要连接到国外的服务器。所以安装一些软件时下载会很慢，我们可以改用国内的镜像apt源。
 https://mirror.alibaba.com/ubuntu 阿里的镜像站
 
!!! warning 

    注意看版本代号， Ubuntu每个版本的代号都不一样！
    
!!! note "ubuntu 18.04(bionic) 配置如下"
    
    deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse

    deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse

    deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse

    deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse

    deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
    
先进行一下备份。

```linux
$ sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
```

然后

```
$ sudo nano /etc/apt/sources.list
```

选择一个源添加到文件最前面或直接替换掉原文件。
[Ctrl+o 写入 Ctrl+x 退出 Ctrl+k 删除整行，大概就用到这仨吧]

保存后运行

```
$ sudo apt-get update
$ sudp apt-get upgrade
```



## 2.终端的美化

win10下可有、丑了。推荐大家一个开源软件cmder，可以完美解决这一问题。这个软件同样可以在[官网](https://cmder.net/)上下载到，而且是免安装。选择下载mini版即可，因为bash我们已经有了嘛！

![](https://raw.githubusercontent.com/Alikas0/files/master/img/2019040919033478.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3E4MzE4MjAzNA==,size_16,color_FFFFFF,t_70)
  贴张我修改后的图0.0，是不是好看很多。

打开cmder直接进入的是cmd，我们可以在设置中更改它的startup方式，选择command line ，填入`bash -cur_console:p`。
 保存设置，下次打开时就直接进入Linux子系统了。

还可以在colors选项中选择自己喜欢的主题，在transparency中更改主界面的透明度。
 而且cmder还有分屏功能。这些功能请自行发掘0.0

## 3.便捷打开方式！

我们可以将cmder添加进win10的环境变量中，这样我们就可以像在Linux系统中那样，在任意文件目录下直接右键打开cmder并进入当前路径了。
 比如在桌面点击右键，选择cmder here，这样打开cmder就可以直接进入桌面的路径了。

![是不是很方便？](https://raw.githubusercontent.com/Alikas0/files/master/img/20190614160707.png)

设置环境变量的具体方法是，依次进入控制面板->系统和安全->系统->高级->环境变量->编辑系统环境变量Path->新建->把cmder路径添加进来，保存之后就可以用win+R的方式打开cmder了。

接下来，以管理员方式打开cmd，输入命令`Cmder.exe /REGISTER ALL`。之后就可以直接在右键中打开cmder了！

## 4.安装zsh和on-my-zsh

shell的类型有很多种，linux下默认的是bash，虽然bash的功能已经很强大，但对于以懒惰为美德的程序员来说，bash的提示功能不够强大，界面也不够炫，并非理想工具。

而zsh的功能极其强大，只是配置过于复杂，起初只有极客才在用。后来，有个穷极无聊的程序员可能是实在看不下去广大猿友一直只能使用单调的bash, 于是他创建了一个名为`oh-my-zsh`的开源项目，then，我这种蒟蒻也能用上zsh了。

(1)安装zsh

```
sudo aptitude update
sudo aptitude install zsh
```

（2）设`zsh`为默认shell

```
chsh -s /bin/zsh
```

（3）全自动安装on-my-zsh

```
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
```

### zsh主题

通过如下命令可以查看可用的`Theme`：

```
# ls ~/.oh-my-zsh/themes
```

如何修改zsh主题呢？
编辑`~/.zshrc`文件，将`ZSH_THEME="candy"`,将candy修改为你需想要的主题。

但由于主题没有显示完整路径，要做以下修改(以robbyrussell)为例：

```
 nano ~/.oh-my-zsh/themes/robbyrussell.zsh-theme
```

![https://raw.githubusercontent.com/Alikas0/files/master/img/wsl-theme.png](https://raw.githubusercontent.com/Alikas0/files/master/img/wsl-theme.png)

%c修改为[$PWD]

![https://raw.githubusercontent.com/Alikas0/files/master/img/wsl-theme2.png](https://raw.githubusercontent.com/Alikas0/files/master/img/wsl-theme2.png)

```
 source ~/.zshrc
```

重启即可显示完整路径

### zsh扩展

在`~/.zshrc`中找到`plugins`关键字，就可以自定义启用的插件了，系统默认加载`git`。

#### git插件

命令内容可以参考`cat ~/.oh-my-zsh/plugins/git/git.plugin.zsh`。

常用的：

| 简写 | 完整 |
| :---: | :---: |
| gapa | git add --patch |
| gc! | git commit -v --amend |
| gcl | git clone --recursive |
| gclean | git reset --hard && git clean -dfx |
| gcm |git checkout master |
| gcmsg | git commit -m |
| gco | git checkout |
| gd | git diff |
| gdca | git diff --cached |
| gp | git push |
| grbc | git rebase --continue |
| gst | git status |
| gup | git pull --rebase |

完整列表：`https://github.com/robbyrussell/oh-my-zsh/wiki/Plugin:git`

#### extract

解压文件用的，所有的压缩文件，都可以直接`x filename`，不用记忆参数

当然，如果你想要用`tar`命令，可以使用`tar -`加`tab`键，zsh会列出参数的含义。

#### autojump

按照[官方文档](https://github.com/wting/autojump)介绍，需要用如下命令安装：

```
git clone https://github.com/wting/autojump
cd autojump
./install.py or ./uninstall.py
```

安装好之后，需要在`~/.zshrc`中配置一下，在末尾添加一行：

```
[[ -s /root/.autojump/etc/profile.d/autojump.sh ]] && source /root/.autojump/etc/profile.d/autojump.sh
```

安装好之后，记得`source ~/.zshrc`，然后你就可以通过`j+目录名`快速进行目录跳转。支持目录名的模糊匹配和自动补全。

- `j -stat`：可以查看历史路径库

#### zsh-autosuggestions

[zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)

```
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```

在 `~/.zshrc` 中配置

```
plugins=(其他的插件 zsh-autosuggestions)
```

因为箭头`→`不太方便，在`.zshrc`中自定义补全快捷键为逗号，但是又一次遇到了需要输入逗号的情况，所以，并不太推荐如下修改：

```
bindkey ',' autosuggest-accept
```

#### zsh-syntax-highlighting

[zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)

```
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

`~/.zshrc`文件中配置：

```
plugins=(其他的插件 zsh-syntax-highlighting)
```

#### git-open

[git-open](https://github.com/paulirish/git-open)插件可以在你git项目下打开远程仓库浏览项目。

```
git clone https://github.com/paulirish/git-open.git $ZSH_CUSTOM/plugins/git-open
```

##### bat

`bat` 代替 `cat`
`cat` 某个文件，可以在终端直接输出文件内容，`bat` 相比 `cat` 增加了行号和颜色高亮 

```
wget https://github.com/sharkdp/bat/releases/download/v0.11.0/bat_0.11.0_amd64.deb
dpkg -i bat_0.11.0_amd64.deb
```

版本号可去<https://github.com/sharkdp/bat/releases>自行查看0.0

## 常用快捷键

- 命令历史记录
  - 一旦在 shell 敲入正确命令并能执行后，shell 就会存储你所敲入命令的历史记录（存放在`~/.zsh_history` 文件中），方便再次运行之前的命令。可以按方向键↑和↓来查看之前执行过的命令
  - 可以用 `r`来执行上一条命令
  - 使用 `ctrl-r` 来搜索命令历史记录
- 命令别名
  - 可以简化命令输入，在 `.zshrc` 中添加 `alias shortcut='this is the origin command'` 一行就相当于添加了别名
  - 在命令行中输入 `alias` 可以查看所有的命令别名

## 使用技巧

- 连按两次Tab会列出所有的补全列表并直接开始选择，补全项可以使用 ctrl+n/p/f/b上下左右切换
- 智能跳转，安装了 autojump 之后，zsh 会自动记录你访问过的目录，通过 j 目录名 可以直接进行目录跳转，而且目录名支持模糊匹配和自动补全，例如你访问过 hadoop-1.0.0 目录，输入j hado 即可正确跳转。j --stat 可以看你的历史路径库。
- 命令选项补全。在zsh中只需要键入 tar -<tab> 就会列出所有的选项和帮助说明
- 在当前目录下输入 .. 或 ... ，或直接输入当前目录名都可以跳转，你甚至不再需要输入 `cd` 命令了。在你知道路径的情况下，比如 `/usr/local/bin` 你可以输入` cd /u/l/b` 然后按进行补全快速输入
- 目录浏览和跳转：输入 d，即可列出你在这个会话里访问的目录列表，输入列表前的序号，即可直接跳转。
- 命令参数补全。键入` kill <tab>` 就会列出所有的进程名和对应的进程号
- 更智能的历史命令。在用或者方向上键查找历史命令时，zsh支持限制查找。比如，输入ls,然后再按方向上键，则只会查找用过的ls命令。而此时使用则会仍然按之前的方式查找，忽略 ls
- 多个终端会话共享历史记录
- 通配符搜索：`ls -l **/*.sh`，可以递归显示当前目录下的 shell 文件，文件少时可以代替 `find`。使用 `**/` 来递归搜索
- 扩展环境变量，输入环境变量然后按 就可以转换成表达的值
- 在 .zshrc 中添加 `setopt HIST_IGNORE_DUPS` 可以消除重复记录，也可以利用` sort -t ";" -k 2 -u ~/.zsh_history | sort -o ~/.zsh_history `手动清除
