## 0x00 签到

flag{buu_ctf}

## 0x01 二维码

题目：[二维码](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/MISC/二维码.zip)

打开来是张二维码，手机扫一下`secret is here`,没啥用。

010editor打开发现zip文件头：
![](https://raw.githubusercontent.com/Alikas0/files/master/img/20190707230810.png)

导出，发现需要密码，文件名提示密码长度为4，直接ARCHPR爆破，得到密码`7639`

打开得flag：
CTF{vjpw_wnoei}（`提交时要修改CTF为flag`）

## 0x02 N种方法解决

题目：[N种方法解决](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/MISC/N种方法解决.zip)

拿到题是个KEY.exe，无法打开，file一下发现：

```
file KEY.exe
KEY.exe: ASCII text, with very long lines, with no line terminators

```

010editor打开：

```
data:image/jpg;base64,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

```

[在线工具](http://tool.chinaz.com/tools/imgtobase)，base64转图片：

![https://raw.githubusercontent.com/Alikas0/files/master/img/20190707231916.png](https://raw.githubusercontent.com/Alikas0/files/master/img/20190707231916.png)

[在线扫一下](http://tool.oschina.net/qr?type=2)，得到flag：

KEY{dca57f966e4e4e31fd5b15417da63269}(`提交时KEY改flag`)

## 0x03 金三胖

题目：[金三胖](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/MISC/金三胖.zip)

打开是个gif，Stegsolve打开，逐帧观看，发现flag：
![](https://raw.githubusercontent.com/Alikas0/files/master/img/金三胖.png)

![](https://raw.githubusercontent.com/Alikas0/files/master/img/%E9%87%91%E4%B8%89%E8%83%962.png)

![](https://raw.githubusercontent.com/Alikas0/files/master/img/%E9%87%91%E4%B8%89%E8%83%963.png)

flag{he11ohongke}

## 0x04 大白

题目：[大白](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/MISC/大白.zip)

根据提示，直接修改png文件高度，得flag：

![](https://raw.githubusercontent.com/Alikas0/files/master/img/Snipaste_2019-07-07_23-52-10.png)

flag{He1l0_d4_ba1}

## 0x05 基础破解

题目：[基础破解](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/MISC/基础破解.zip)

根据提示,四位密码直接跑：`2563`

得到`flag.txt`：

```
ZmxhZ3s3MDM1NDMwMGE1MTAwYmE3ODA2ODgwNTY2MWI5M2E1Y30=

```

base64解密，得flag：

flag{70354300a5100ba78068805661b93a5c}

## 0x06 你竟然赶我走

题目：[你竟然赶我走](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/MISC/你竟然赶我走.zip)

010editor打开，在文件尾得到flag

flag IS flag{stego_is_s0_bor1ing}

## 0x07 LSB

题目：[LSB](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/MISC/LSB.zip)

Stegsolve打开，根据提示以及red，blue，green三色0通道的不寻常噪点：

![](https://raw.githubusercontent.com/Alikas0/files/master/img/20190708003308.png)



![](https://raw.githubusercontent.com/Alikas0/files/master/img/20190708002952.png)

![](https://raw.githubusercontent.com/Alikas0/files/master/img/20190708003349.png)

Analyse->Data Extract->导出三色0通道文件。

![https://raw.githubusercontent.com/Alikas0/files/master/img/20190708011817.png](https://raw.githubusercontent.com/Alikas0/files/master/img/20190708011817.png)

根据文件头发现是png文件。修改文件后缀名，得一张二维码：

![](https://raw.githubusercontent.com/Alikas0/files/master/img/1.png)

[在线工具](http://tool.oschina.net/qr?type=2)扫，得flag：

flag{1sb_i4_s0_Ea4y}

## 0x08 ningen

题目：[ningen](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/MISC/ningen.zip)

010editor打开，文件尾发现PK头，为zip，复制保存，发现有密码，根据提示，爆破4位数字密码得：`8368`

解压出ningen文件，得flag：
flag{b025fc9ca797a67d2103bfbc407a6d5f}

## 0x09 rar

题目：[rar](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/MISC/rar.zip)

根据提示，工具直接爆破得密码：`8795`

得flag文件:
flag{1773c5da790bd3caff38e3decd180eb7}

## 0x0A qr

题目：[qr](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/MISC/qr.zip)

直接扫出flag：
Flag{878865ce73370a4ce607d21ca01b5e59}(提交时修改F为f)

## 0x0B 乌镇峰会种图

题目：[乌镇峰会种图](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/MISC/乌镇峰会种图.zip)

010editor打开，文件尾得flag：
flag{97314e7864a8f62627b26f3f998c37f1}

## 0x0C 文件中的秘密

题目：[文件中的秘密](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/MISC/文件中的秘密.zip)

010editor打开，文件中发现flag：

![](https://raw.githubusercontent.com/Alikas0/files/master/img/20190708011111.png)

将00全部删除，得：

flag{870c5a72806115cb5439345d8b014396}

## 0x0D 假如给我三天光明

题目：[假如给我三天光明](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/MISC/假如给我三天光明.zip)

解压后出现两个文件：pic.jpg和musci.zip，压缩包需要密码。

![](https://raw.githubusercontent.com/Alikas0/files/master/img/20190708110105.png)

pic.jpg图片的密文为盲文，解密后为`kmdonowg`

解压出misc.wav,播放一下发现是摩斯电码。使用audacity分析:

![](https://raw.githubusercontent.com/Alikas0/files/master/img/20190708104336.png)

长短用-.表示：

```
-.-. - ..-. .-- .--. . .. ----- ---.. --... ...-- ..--- ..--.. ..--- ...-- -.. --..

```

然后用摩斯解码工具进行解码即可得到flag：

ctfwpei08732?23dz(`提交时ctf换flag{,并在末尾补}`)

## 0x0E 镜子里面的世界

题目：[镜子里面的世界](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/MISC/镜子里面的世界.zip)

打开图片，叫我们`Look very closely ;)`

![](https://raw.githubusercontent.com/Alikas0/files/master/img/20190708111232.png)

Stegsolve打开，仔细观看每个通道，发现三色零通道都有如下异常：

![](https://raw.githubusercontent.com/Alikas0/files/master/img/20190708111404.png)

Analyse->Data Extract->Preview:

![https://raw.githubusercontent.com/Alikas0/files/master/img/20190708111520.png](https://raw.githubusercontent.com/Alikas0/files/master/img/20190708111520.png)

复制，修改格式得flag：

flag{st3g0_saurus_wr3cks}

## 0x0F FLAG

题目：[FLAG](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/MISC/FLAG.zip)

打开是一张图片,Stegsolve打开分析，没有特别异常的地方，猜测0通道问题,Analyse->Data Extract->Preview:

![](https://raw.githubusercontent.com/Alikas0/files/master/img/20190708112450.png)

文件头为PK，则为zip压缩包，Save Bin导出，解压发现压缩包损坏：
![https://raw.githubusercontent.com/Alikas0/files/master/img/20190708112925.png](https://raw.githubusercontent.com/Alikas0/files/master/img/20190708112925.png)

RAR修复即可解压，得到一个文件，file一下：

```
1: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/l, for GNU/Linux 2.6.24, BuildID[sha1]=8df45089fa39fec83423ec37a944e81065d16bee, not stripped

```

运行得flag：

hctf{dd0gf4c3tok3yb0ard4g41n~~~}（`提交时hctf改flag`）

## 0x10 wireshark

题目：[wireshark](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/MISC/wireshark.zip)

题目提示是登录网站找password，那就关注寻找GET和POST，最终在GET中发现:

![](https://raw.githubusercontent.com/Alikas0/files/master/img/20190710155113.png)

flag{ffb7567a1d4f4abdffdb54e022f8facd}

## 0x11 来首歌吧

题目:[来首歌吧](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/MISC/来首歌吧.zip)

解压得到steg100.wav，用Audacity音频分析软件打开：
![](https://raw.githubusercontent.com/Alikas0/files/master/img/20190710160237.png)

猜测第一为flag，放大，猜测为摩斯电码：

![](https://raw.githubusercontent.com/Alikas0/files/master/img/20190710160423.png)

则有：

```
..... -... -.-. ----. ..--- ..... -.... ....- ----. -.-. -... ----- .---- ---.. ---.. ..-. ..... ..--- . -.... .---- --... -.. --... ----- ----. ..--- ----. .---- ----. .---- -.-. 

```

解码得flag：

flag{5BC925649CB0188F52E617D70929191C}

## 0x12 爱因斯坦

题目：[爱因斯坦](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/MISC/爱因斯坦.zip)

下载解压得到一张图片misc2.jpg。

尝试对着图片binwalk，分解出一个加密压缩包，里面带有flag.txt：

```
$binwalk -e misc2.jpg

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             JPEG image data, JFIF standard 1.01
30            0x1E            TIFF image data, big-endian, offset of first image directory: 8
4474          0x117A          Unix path: /www.w3.org/1999/02/22-rdf-syntax-ns#"/></x:xmpmeta>
68019         0x109B3         Zip archive data, encrypted at least v1.0 to extract, compressed size: 51, uncompressed size: 39, name: flag.txt
68230         0x10A86         End of Zip archive

```

备注信息里有一句`this_is_not_password`，丢进去试试：

![](https://raw.githubusercontent.com/Alikas0/files/master/img/20190710164742.png)

解压成功.........得flag：

flag{dd22a92bf2cceb6c0cd0d6b83ff51606}

## 0x13 小明的保险箱

题目：[小明的保险箱](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/MISC/小明的保险箱.zip)

打开得到一张图片，binwalk提取出一个加密压缩包，根据提示爆破出四位密码：`7869`,得到flag：

flag{75a3d68bf071ee188c418ea6cf0bb043}

## 0x14 easycap

题目：[easycap](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/MISC/easycap.zip)

流量题，wireshark追踪一下TCP流量,直接得flag：

flag{385b87afc8671dee07550290d16a8071}

## 0x15 梅花香自苦寒来

题目：[梅花香自苦寒来](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/MISC/梅花香自苦寒来.zip)

看注释信息有：

![](https://raw.githubusercontent.com/Alikas0/files/master/img/20190710203625.png)

由题目`图穷匕见`，用010editor打开，发现图片后面有一大串未知码：

![https://raw.githubusercontent.com/Alikas0/files/master/img/20190710214149.png](https://raw.githubusercontent.com/Alikas0/files/master/img/20190710214149.png)

复制出来，到notepad++：

```
28372C37290A28372C38290A28372C39
290A28372C3130290A28372C3131290A
....

```

hex->16进制：

```
(7,7)
(7,8)
....

```

整理一下：

```
7,7
7,8
...

```

写个脚本画出来：

```
from PIL import Image, ImageDraw, ImageFilter
f = open("flag.txt","r")
width = 272
height = 272

img = Image.new('RGB', (width, height), (255, 255, 255))
draw = ImageDraw.Draw(img)
for lines in f:
    t = lines.strip().split(",")
    x,y = eval(t[0]),eval(t[1])
    draw.point((x,y),fill = (0,0,0))

img.save("flag.bmp","bmp")

```

得到一张二维码：

![https://raw.githubusercontent.com/Alikas0/files/master/img/flag.bmp](https://raw.githubusercontent.com/Alikas0/files/master/img/flag.bmp)

扫码得flag:

flag{40fc0a979f759c8892f4dc045e28b820}