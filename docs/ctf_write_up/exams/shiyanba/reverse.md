## 有一个程序加密得如下密文

题目:[有一个程序加密得如下密文](http://www.shiyanbar.com/ctf/59)

看到pyc文件肯定是反编译的啦，这里我用的是`uncompyle2`。（再次怒夸子系统）

安装过程如下：

```linux
git clone https://github.com/wibiti/uncompyle2
cd uncompyle2
python setup.py install
```

然后进行反编译

```linux
 uncompyle2 reverse300.pyc > reverse300.py
```

接下来打开reverse300.py查看代码

```python
# 2019.04.13 21:35:38 DST
#Embedded file name: ./rev200.py
import sys
from hashlib import md5
import base64
from time import time
from datetime import datetime
UC_KEY = '123456789'

def authcode(string, operation = 'DECODE', key = UC_KEY, expiry = 0):
    ckey_length = 4
    if key == '':
        key = md5(UC_KEY.encode('utf-8')).hexdigest()
    else:
        key = md5(key.encode('utf-8')).hexdigest()
    keya = md5(key[0:16].encode('utf-8')).hexdigest()
    keyb = md5(key[16:32].encode('utf-8')).hexdigest()
    if ckey_length == 0:
        keyc = ''
    elif operation == 'DECODE':
        keyc = string[0:ckey_length]
    elif operation == 'ENCODE':
        keyc = md5(str(datetime.now().microsecond).encode('utf-8')).hexdigest()[-ckey_length:]
    else:
        return
    cryptkey = keya + md5((keya + keyc).encode('utf-8')).hexdigest()
    key_length = len(cryptkey)
    if operation == 'DECODE':
        string = base64.b64decode(string[ckey_length:])
    elif operation == 'ENCODE':
        if expiry == 0:
            string = '0000000000' + md5((string + keyb).encode('utf-8')).hexdigest()[0:16] + string
        else:
            string = '%10d' % (expiry + int(time())) + md5((string + keyb).encode('utf-8')).hexdigest()[0:16] + string
    else:
        return
    string_length = len(string)
    result = ''
    box = range(256)
    rndkey = [0] * 256
    for i in range(256):
        rndkey[i] = ord(cryptkey[i % key_length])

    j = 0
    for i in range(256):
        j = (j + box[i] + rndkey[i]) % 256
        tmp = box[i]
        box[i] = box[j]
        box[j] = tmp

    a = j = 0
    for i in range(string_length):
        a = (a + 1) % 256
        j = (j + box[a]) % 256
        tmp = box[a]
        box[a] = box[j]
        box[j] = tmp
        result += chr(ord(string[i]) ^ box[(box[a] + box[j]) % 256])

    if operation == 'DECODE':
        if not result[0:10].isdigit() or int(result[0:10]) == 0 or int(result[0:10]) - int(time()) > 0:
            if result[10:26] == md5(result[26:].encode('utf-8') + keyb).hexdigest()[0:16]:
                return result[26:]
            else:
                return ''
        else:
            return ''
    else:
        return keyc + base64.b64encode(result)


if __name__ == '__main__':
    if len(sys.argv) < 3:
        exit(1)
    ex = 20
    for i in range(1, len(sys.argv), 2):
        a = sys.argv[i]
        b = sys.argv[i + 1]
        if a == '-t':
            ex = int(b)
        elif a == '-e':
            encoded = authcode(b, 'ENCODE', expiry=ex)
            print encoded
        elif a == '-d':
            decoded = authcode(b, 'DECODE', expiry=ex)
            print decoded
+++ okay decompyling reverse300.pyc 
# decompiled 1 files: 1 okay, 0 failed, 0 verify failed
# 2019.04.13 21:35:38 DST

```

### 解法一

查看主函数，因为涉及解密，那直接去看decode。

看到`return '` '直接进行修改，把后两个`return ' '`改为`return result[26:],`保存。

核心代码如下：

```python
if operation == 'DECODE':
        if not result[0:10].isdigit() or int(result[0:10]) == 0 or int(result[0:10]) - int(time()) > 0:
            if result[10:26] == md5(result[26:].encode('utf-8') + keyb).hexdigest()[0:16]:
                return result[26:]
            else:
                return ''   #修改这里
        else:
            return ''      #和这里
    else:
        return keyc + base64.b64encode(result)
```

此时执行

```linux
python reverse300.py -d 0be6770IigHXZpz9hQYR1fpl15R0z9MUalmYEPhJeEN/sRklL6wQw5yQ7SAyT6tKGJNY0AxnyzS/L7zWQII=
```

然后就掉坑里了

```linux
python reverse300.py -d 0be6770
IigHXZpz9hQYR1fpl15R0z9MUalmYEPhJeEN/sRklL6wQw5yQ7SAyT6tKGJNY0AxnyzS/L7zWQII=
  File "reverse300.py", line 87
    +++ okay decompyling reverse300.pyc
                       ^
SyntaxError: invalid syntax
```

改一下，完美运行！

```linux
python reverse300.py -d 0be
6770IigHXZpz9hQYR1fpl15R0z9MUalmYEPhJeEN/sRklL6wQw5yQ7SAyT6tKGJNY0AxnyzS/L7zWQII=
DUTCTF{2u0_chu_14i_d3_5hi_h3n74i}
```



### 解法二

分析加密算法自己写出解密算法：（这是大佬教的！tql@-@!）

```python
from hashlib import md5
from Crypto.Cipher import ARC4
UC_KEY = '123456789'
key = md5(UC_KEY.encode('utf-8')).hexdigest()
cipher = '0be6770IigHXZpz9hQYR1fpl15R0z9MUalmYEPhJeEN/sRklL6wQw5yQ7SAyT6tKGJNY0AxnyzS/L7zWQII='
keyc = cipher[0:4]
cipher = cipher[4:]
cipher = cipher.decode('base64')
keya = md5(key[0:16].encode('utf-8')).hexdigest()
keyb = md5(key[16:32].encode('utf-8')).hexdigest()
cryptkey = keya + md5((keya + keyc).encode('utf-8')).hexdigest()
rc4 = ARC4.new(cryptkey)
print(rc4.decrypt(cipher))
```



**总结**：这道题不难，但是我从未遇见过反编译pyc文件，一开始有点懵，后来查到可以有`uncompyle2`这种神奇工具！Nice！学到了！然后去问大佬，大佬提供了第二种解法，直接分析加密过程，ARC4和md5，算是初步开始接触现代密码了吧！

（dalao说这东西要一眼就能看出来啊，任重而道远呀......）



## 分道扬镳

题目：[分道扬镳](http://www.shiyanbar.com/ctf/1885)

第一步，file

```text
file rev2.exe
rev2.exe: PE32 executable (console) Intel 80386, for MS Windows
```

拖进IDA看一下。

核心代码及分析如下：

```c
  v3 = 0;
  memset(&v4, 0, 0x74u);
  v5 = 0;
  v6 = 0;
  strcpy(v2, "********* *    ** * ** ** * ** ** * #* ** **** **      *********");
  v1 = &v2[9];//这里我修改了v2的类型，char v2 -> char v2[65],便于理解
  printf("Please input your key:\n");
  gets(&v3);
  if ( strlen(&v3) != 22 )                      // key长度为22
  {
    printf("Sorry you are wrong!\n");
    system("pause");
    exit(1);
  }
  v9 = 0;
  do
  {
    v8 = *(&v3 + v9);
    if ( v8 != 'k' && v8 != 'j' && v8 != 'h' && v8 != 'l' )// 应该是上下左右的意思
    {
      printf("Sorry you are wrong!\n");
      system("pause");
      exit(2);
    }
    v7 = *(&v3 + v9);
    switch ( v7 )
    {
      case 'h':                                 // 后退一步
        if ( --v1 < v2 || v1 > &v2[64] || (result = (char *)*v1, result == (char *)'*') )// 判断越界 or 撞墙
        {
          printf("Sorry you are wrong!\n");
          system("pause");
          exit(3);
        }
        if ( *v1 == '#' )                       // #应该是出口，*应该是墙
        {
LABEL_41:
          printf("Good!\n");
          system("pause");
          exit(0);
        }
        break;
      case 'j':
        v1 += 8;                                // 前进八步
        if ( v1 < v2 || v1 > &v2[64] || *v1 == '*' )
        {
          printf("Sorry you are wrong!\n");
          system("pause");
          exit(3);
        }
        result = (char *)*v1;
        if ( result == '#' )
          goto LABEL_41;
        break;
      case 'k':
        v1 -= 8;                                // 后退八步
        if ( v1 < v2 || v1 > &v2[64] || *v1 == '*' )
        {
          printf("Sorry you are wrong!\n");
          system("pause");
          exit(3);
        }
        result = v1;
        if ( *v1 == 35 )
          goto LABEL_41;
        break;
      default:
        if ( ++v1 < v2 || v1 > &v2[64] || *v1 == 42 )// 前进一步
        {
          printf("Sorry you are wrong!\n");
          system("pause");
          exit(4);
        }
        result = v1;
        if ( *v1 == '#' )
          goto LABEL_41;
        break;
    }
    ++v9;
  }
  while ( v9 < 25 );
  return result;
```

*函数说明：strcpy是一种C语言的标准库函数，strcpy把含有'\0'结束符的字符串复制到另一个地址空间，返回值的类型为char*。*

分析代码后，这里就两种方法，第一种遍历，求出所有可能性来求出最优解，但既费时又费力。

故我采取了第二种方法。

由于题目`strcpy(v2, "********* *    ** * ** ** * ** ** * #* ** **** **      *********");`已经将迷宫的大致情况给我们画了出来，那我们直接手动还原这个8*8的迷宫即可。

```text
********
* *    *
* * ** *
* * ** *
* * #* *
* **** *
*      *
********
```

而”`j`前进八步“可理解为向前，”`k`后退八步“理解为向后，”`l`前进一步“为向右，”`h`后退一步“为向左

目测解题法：jjjjjlllllkkkkkhhhjjjl

## 实验观察

题目：[实验观察](http://www.shiyanbar.com/ctf/1990)

File 一下 64位ELF。

题目叫逆向观察，那我们就直接IDA看一下，进入mian函数，代码如下：

```c
 if ( argc <= 1 )
  {
    puts("usage ./rev50 password");
  }
  else
  {
    src = 'sedecrem';//8315162673632404845LL,按R字符转换一下
    v6 = 0;
    v7 = 0;
    v8 = 0;
    memcpy(&dest, &src, 9uLL);
    for ( i = 0; i <= 999; ++i )
    {
      if ( !strcmp(argv[1], (&dict)[i]) && !strcmp(&dest, (&dict)[i]) )
      {
        puts("Good password ! ");
        goto LABEL_10;
      }
    }
    puts("Bad ! password");
  }
LABEL_10:
  puts(&byte_40252A);
```

函数说明：

*1.memcpy():C 库函数 void \*memcpy(void \*str1, const void \*str2, size_t n)* 

*从存储区 str2 复制 n 个字符到存储区 str1。*

*2.int strncmp ( const char * str1, const char * str2, size_t n );*

*若str1与str2的前n个字符相同，则返回0；若s1大于s2，则返回大于0的值；若s1 小于s2，则返回小于0的值。*

那要打印出Good password，那不就应该让`!strcmp(argv[1], (&dict)[i]) && !strcmp(&dest, (&dict)[i])`都为1嘛，那我们输入的就应该和dest一样.......真的有这么简单吗？

```linux
./rev50 sedecrem
Bad ! password
```

emmmmmm这里我就想到了，储存方式的不同，故直接把字符串反转一下：

```linux
./rev50 mercedes
Good password !
```

资料: [大端序和小端序](https://alikas.cf/misc/big_little_ending/)

## defcamp

题目：[defcamp](http://www.shiyanbar.com/ctf/2020)

File一下，64位 ELF

```text
$ file r200.bak
r200.bak: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/l, for GNU/Linux 2.6.24, BuildID[sha1]=22e68980e521b43c90688ed0693df78150b10211, stripped
```

运行一下，要求我们输入password

```text
$ ./r200.bak
Enter the password:
```

拖进IDA，追踪字符串到main函数，提取代码：

（为了便于观看，我修改了函数名）

```c
printf("Enter the password: ", a2, a3);
  if ( !fgets(&s, 7, stdin) )
    return 0LL;
  if ( (unsigned int)check_password((__int64)&s) )
  {
    puts("Incorrect password!");
    result = 1LL;
  }
  else
  {
    puts("Nice!");
    result = 0LL;
  }
```

那重点应该就在check_password()里咯，进去,，代码如下

```c
  v6 = 0LL;
  v7 = 0LL;
  v8 = 0LL;
  v9[0] = 5;
  v9[1] = 2;
  v9[2] = 7;
  v9[3] = 2;
  v9[4] = 5;
  v9[5] = 6;
  for ( i = 0; i <= 5; ++i )
  {
    v5 = qword_601080;
    v4 = 0;
    while ( v5 )
    {
      if ( *(v5 + 4) == *(i + a1) )
      {
        v4 = *v5;
        break;
      }
      v5 = *(v5 + 8);
    }
    *(&v6 + i) = v4;
  }
  for ( j = 0; j <= 5; ++j )
  {
    if ( *(&v6 + j) != v9[j] )
      return 1LL;
  }
  return 0LL;
}
```

理解一下，输入的值加密之后要等于5，2，7，2，5，6，我们理解一下解密算法就可以得到flag了。

看到v5=qword_60180,而qword_60180在主函数也出现过，过去主函数看一下:

```c
for ( i = 1; i <= 10; ++i )
  {
    v3 = malloc(0x10uLL);
    *v3 = i;
    *(v3 + 4) = *v3 + 109;
    a3 = qword_601080;
    *(v3 + 1) = qword_601080;
    qword_601080 = v3;
  }
```

我们看到表中共有10个元素，每个元素16个字节，对应的值每个都要加上109，那反过来想，我们对应的就应该是114，111，116，111，114，115,ASCII码转换一下得rotors

```text
$ ./r200.bak
Enter the password: rotors
Nice!
```

结果就很Nice！

## debug

题目：[debug32](http://www.shiyanbar.com/ctf/2044)

拿到题，先file一下

```text
debug32: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-, for GNU/Linux 2.6.32, BuildID[sha1]=46b87dea3b50c74ec8cde885ccc9a4d9e5e260f3, stripped
```

是个32位的ELF文件，运行一下`./debug32`,什么都没有？

拉进IDA查看一下，发现main函数中还真的什么都没有

```c
int __cdecl main(){
      return 0;
}
```

shift+f12(查找所有字符串)，发现`.rodata:080485F0	0000000E	C	Printing flag`
，于是跟踪找到sub_804849B();
sub_804849B()这个函数中是向屏幕中打印flag的，解题方法有二：

**一**.**直接复制sub_824849B()函数代码到VSCode，添加头文件和mian()，这里注意，在最后要使其停止，故在最后`v3[5] = 0`,代码如下：**

```c
#include<stdio.h>

int main()
{
    int i,j;
    unsigned int v3[5];
 v3[0] = 377943446;
  v3[1] = 1447490871;
  v3[2] = 1987467046;
  v3[3] = 938813270;
  v3[4] = 3334903478;
  for ( i = 0; i <= 4; ++i )
  {
    for ( j = v3[i]; j; j >>= 8 )
      putchar((char)(((unsigned __int8)j >> 4) | 16 * j));
  }
  return 0;
}
```

**二.main函数中调用这个函数，所以我们可以用GDB来使主函数调用这个打印flag的这个函数**（z这里参考了网上大佬的writeup0.0）

调试过程

- 首先加载文件

```linux
gdb debug32
```

- 然后再主函数设置断点

```linux
gdb-peda$ break __libc_start_main
Breakpoint 1 at 0x8048370
```

- 然后运行该程序

```text
gdb-peda$ run
Starting program: /root/file/debug32 

[----------------------------------registers-----------------------------------]
EAX: 0xf7ffd918 --> 0x0 
EBX: 0xf7ffd000 --> 0x22f3c 
ECX: 0xffffd154 --> 0xffffd32b ("/root/file/debug32")
EDX: 0xf7fe9880 (push   ebp)
ESI: 0x1 
EDI: 0x80483a0 (xor    ebp,ebp)
EBP: 0x0 
ESP: 0xffffd12c --> 0x80483c1 (hlt)
EIP: 0xf7e1c540 (<__libc_start_main>:	call   0xf7f21289)
EFLAGS: 0x292 (carry parity ADJUST zero SIGN trap INTERRUPT direction overflow)
[-------------------------------------code-------------------------------------]
   0xf7e1c53a:	xchg   ax,ax
   0xf7e1c53c:	xchg   ax,ax
   0xf7e1c53e:	xchg   ax,ax
=> 0xf7e1c540 <__libc_start_main>:	call   0xf7f21289
   0xf7e1c545 <__libc_start_main+5>:	add    eax,0x197abb
   0xf7e1c54a <__libc_start_main+10>:	push   ebp
   0xf7e1c54b <__libc_start_main+11>:	push   edi
   0xf7e1c54c <__libc_start_main+12>:	push   esi
No argument
[------------------------------------stack-------------------------------------]
0000| 0xffffd12c --> 0x80483c1 (hlt)
0004| 0xffffd130 --> 0x804855a (push   ebp)
0008| 0xffffd134 --> 0x1 
0012| 0xffffd138 --> 0xffffd154 --> 0xffffd32b ("/root/file/debug32")
0016| 0xffffd13c --> 0x8048570 (push   ebp)
0020| 0xffffd140 --> 0x80485d0 (repz ret)
0024| 0xffffd144 --> 0xf7fe9880 (push   ebp)
0028| 0xffffd148 --> 0xffffd14c --> 0xf7ffd918 --> 0x0 
[------------------------------------------------------------------------------]
Legend: code, data, rodata, value

Breakpoint 1, 0xf7e1c540 in __libc_start_main () from /lib32/libc.so.6
gdb-peda$
```

修改EIP的值为这个函数的起地址

```text
gdb-peda$ set $eip = 0x804849b
```

- 然后继续运行程序即得flag

```text
gdb-peda$ c
Continuing.
Printing flag
i_has_debugger_skill

Program received signal SIGSEGV, Segmentation fault.

[----------------------------------registers-----------------------------------]
EAX: 0x0 
EBX: 0xf7ffd000 --> 0x22f3c 
ECX: 0xf7fb5870 --> 0x0 
EDX: 0xa ('\n')
ESI: 0x1 
EDI: 0x80483a0 (xor    ebp,ebp)
EBP: 0x0 
ESP: 0xffffd130 --> 0x804855a (push   ebp)
EIP: 0x80483c1 (hlt)
EFLAGS: 0x10246 (carry PARITY adjust ZERO sign trap INTERRUPT direction overflow)
[-------------------------------------code-------------------------------------]
   0x80483b6:	push   esi
   0x80483b7:	push   0x804855a
   0x80483bc:	call   0x8048370 <__libc_start_main@plt>
=> 0x80483c1:	hlt    
   0x80483c2:	xchg   ax,ax
   0x80483c4:	xchg   ax,ax
   0x80483c6:	xchg   ax,ax
   0x80483c8:	xchg   ax,ax
[------------------------------------stack-------------------------------------]
0000| 0xffffd130 --> 0x804855a (push   ebp)
0004| 0xffffd134 --> 0x1 
0008| 0xffffd138 --> 0xffffd154 --> 0xffffd32b ("/root/file/debug32")
0012| 0xffffd13c --> 0x8048570 (push   ebp)
0016| 0xffffd140 --> 0x80485d0 (repz ret)
0020| 0xffffd144 --> 0xf7fe9880 (push   ebp)
0024| 0xffffd148 --> 0xffffd14c --> 0xf7ffd918 --> 0x0 
0028| 0xffffd14c --> 0xf7ffd918 --> 0x0 
[------------------------------------------------------------------------------]
Legend: code, data, rodata, value
Stopped reason: SIGSEGV
0x080483c1 in ?? ()
```

flag：flag{i_has_debugger_skill}

总结：第一种方法稍微有点没有技术含量，出题人意思应该是希望我们运用第二种方式来解决，这里我也学到一种新的工具`GDB`。