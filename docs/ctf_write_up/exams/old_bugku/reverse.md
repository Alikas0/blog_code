## **0x00 入门逆向**

题目：[Bugku|入门逆向](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/Bugku-RE/%E5%85%A5%E9%97%A8%E9%80%86%E5%90%91/baby.zip)

解压文件，将baby.exe用ida打开，主函数中`"Hi~this is a babyre"`后就`call _printf`且下面为一些十六进制字符则猜测为flag。对着这些十六进制一顿R键就能看到了。**注意字符**，是0不是o等。

```assembly
.text:0040146E                 mov     dword ptr [esp], offset aHiThisIsABabyr ; "Hi~ this is a babyre" 
.text:00401475                 call    _printf
.text:0040147A                 mov     byte ptr [esp+2Fh], 66h
.text:0040147F                 mov     byte ptr [esp+2Eh], 6Ch
.text:00401484                 mov     byte ptr [esp+2Dh], 61h
.text:00401489                 mov     byte ptr [esp+2Ch], 67h
.text:0040148E                 mov     byte ptr [esp+2Bh], 7Bh
.text:00401493                 mov     byte ptr [esp+2Ah], 52h
.text:00401498                 mov     byte ptr [esp+29h], 65h
.text:0040149D                 mov     byte ptr [esp+28h], 5Fh
.text:004014A2                 mov     byte ptr [esp+27h], 31h
.text:004014A7                 mov     byte ptr [esp+26h], 73h
.text:004014AC                 mov     byte ptr [esp+25h], 5Fh
.text:004014B1                 mov     byte ptr [esp+24h], 53h
.text:004014B6                 mov     byte ptr [esp+23h], 30h
.text:004014BB                 mov     byte ptr [esp+22h], 5Fh
.text:004014C0                 mov     byte ptr [esp+21h], 43h
.text:004014C5                 mov     byte ptr [esp+20h], 30h
.text:004014CA                 mov     byte ptr [esp+1Fh], 4Fh
.text:004014CF                 mov     byte ptr [esp+1Eh], 4Ch
.text:004014D4                 mov     byte ptr [esp+1Dh], 7Dh
.text:004014D9                 mov     eax, 0
```

```assembly
.text:0040147A                 mov     byte ptr [esp+2Fh], 'f'
.text:0040147F                 mov     byte ptr [esp+2Eh], 'l'
.text:00401484                 mov     byte ptr [esp+2Dh], 'a'
.text:00401489                 mov     byte ptr [esp+2Ch], 'g'
.text:0040148E                 mov     byte ptr [esp+2Bh], '{'
.text:00401493                 mov     byte ptr [esp+2Ah], 'R'
.text:00401498                 mov     byte ptr [esp+29h], 'e'
.text:0040149D                 mov     byte ptr [esp+28h], '_'
.text:004014A2                 mov     byte ptr [esp+27h], '1'
.text:004014A7                 mov     byte ptr [esp+26h], 's'
.text:004014AC                 mov     byte ptr [esp+25h], '_'
.text:004014B1                 mov     byte ptr [esp+24h], 'S'
.text:004014B6                 mov     byte ptr [esp+23h], '0'
.text:004014BB                 mov     byte ptr [esp+22h], '_'
.text:004014C0                 mov     byte ptr [esp+21h], 'C'
.text:004014C5                 mov     byte ptr [esp+20h], '0'
.text:004014CA                 mov     byte ptr [esp+1Fh], 'O'
.text:004014CF                 mov     byte ptr [esp+1Eh], 'L'
.text:004014D4                 mov     byte ptr [esp+1Dh], '}'
```

p.s. R键：字符转换快捷键

## **0x01 Easy_vb**

题目：[Bugku|Easy_vb](https://ctf.bugku.com/challenges#Easy_vb)

下载文件，拉进IDA查看，发现只有一个函数。观察，向下继续浏览，发现flag隐藏其中！

```assembly
.text:00401A5C aMctfN3tRev1sE4:                        ; DATA XREF: .text:004023A9↓o
.text:00401A5C                 text "UTF-16LE", 'MCTF{_N3t_Rev_1s_E4ay_}',0
.text:00401A8C                 dd 14h
```

## **0x02 Easy_Re**

题目：[Bugku|Easy_RE](https://ctf.bugku.com/challenges#Easy_Re)

ida加载文件，找到main函数。F5转换成伪c代码。

strcmp函数 比较v5和v9。输入的是v9，故查看v5相关

```c
_mm_storeu_si128((__m128i *)&v5, _mm_loadu_si128((const __m128i *)&xmmword_413E34));
```

则点进去到xmmword_413E34所在位置

```assembly
.rdata:00413E34 xmmword_413E34  xmmword3074656D30633165577B465443545544h
.rdata:00413E44 qword_413E44    dq7D465443545544h
```

对着数据右键直接`Undefined`,快捷键A一下即得flag。

```assembly
.rdata:00413E34 unk_413E34      db  44h ; D             ; DATA XREF: _main+10↑r
.rdata:00413E35                 db  55h ; U
.rdata:00413E36                 db  54h ; T
.rdata:00413E37                 db  43h ; C
.rdata:00413E38                 db  54h ; T
.rdata:00413E39                 db  46h ; F
.rdata:00413E3A                 db  7Bh ; {
.rdata:00413E3B                 db  57h ; W
.rdata:00413E3C                 db  65h ; e
.rdata:00413E3D                 db  31h ; 1
.rdata:00413E3E                 db  63h ; c
.rdata:00413E3F                 db  30h ; 0
.rdata:00413E40                 db  6Dh ; m
.rdata:00413E41                 db  65h ; e
.rdata:00413E42                 db  74h ; t
.rdata:00413E43                 db  30h ; 0
.rdata:00413E44 unk_413E44      db  44h ; D             ; DATA XREF: _main+27↑r
.rdata:00413E45                 db  55h ; U
.rdata:00413E46                 db  54h ; T
.rdata:00413E47                 db  43h ; C
.rdata:00413E48                 db  54h ; T
.rdata:00413E49                 db  46h ; F
.rdata:00413E4A                 db  7Dh ; }
```

P.S一开始只`undefined xmmword_413E34`发现大括号不成对，才发现下面还有一块0.0....

## 0x03 游戏过关

题目：[Bugku|游戏过关](https://ctf.bugku.com/challenges#%E6%B8%B8%E6%88%8F%E8%BF%87%E5%85%B3)

运行了一下，发现是个开关游戏，输入数字会将对应的编码的开关，以及相邻的开关转化开关状态（即由开变关 or 由关变开），当所有开关为关时，得到flag。

```text
                     |------------／ --------△--------|
                     |------------／ --------○--------|
                     |------------／ --------◇--------|
                     |------------／ --------□--------|
|--------------------|------------／ --------☆--------|
|                    |------------／ --------▽--------|
|                    |------------／ -----(￣▽￣)／---|
|                    |------------／ -----(;°Д°)----|
二                                                     |
|              by 0x61                                 |
|                                                      |
|------------------------------------------------------|
Play a game
The n is the serial number of the lamp,and m is the state of the lamp
If m of the Nth lamp is 1,it's on ,if not it's off
At first all the lights were closed
Now you can input n to change its state
But you should pay attention to one thing,if you change the state of the Nth lamp,the state of (N-1)th and (N+1)th will be changed too
When all lamps are on,flag will appear
Now,input n
input n,n(1-8)
1.△ 2.○ 3.◇ 4.□ 5.☆ 6.▽ 7.(￣▽￣)／ 8.(;°Д°) 0.restart
n=
```

然后....观察了一下......

### 解法一

数字从1输到8即可得到flag.....

这也太简单了吧.....接下来拖进IDA看看。

### 解法二

shift+F12，根据输出找到主要代码位置：

```c
void __cdecl main_0()
{
  signed int i; // [esp+DCh] [ebp-20h]
  int v1; // [esp+F4h] [ebp-8h]

  printf((int)&unk_50B110);
  printf((int)&unk_50B158);
  printf((int)&unk_50B1A0);
  printf((int)&unk_50B1E8);
  printf((int)&unk_50B230);
  printf((int)&unk_50B278);
  printf((int)&unk_50B2C0);
  printf((int)&unk_50B308);
  printf((int)"二                                                     |\n");
  printf((int)"|              by 0x61                                 |\n");
  printf((int)"|                                                      |\n");
  printf((int)"|------------------------------------------------------|\n");
  printf((int)"Play a game\n"
              "The n is the serial number of the lamp,and m is the state of the lamp\n"
              "If m of the Nth lamp is 1,it's on ,if not it's off\n"
              "At first all the lights were closed\n");
  printf((int)"Now you can input n to change its state\n");
  printf((int)"But you should pay attention to one thing,if you change the state of the Nth lamp,the state of (N-1)th and"
              " (N+1)th will be changed too\n");
  printf((int)"When all lamps are on,flag will appear\n");
  printf((int)"Now,input n \n");
  while ( 1 )
  {
    while ( 1 )
    {
      printf((int)"input n,n(1-8)\n");
      sub_459418();
      printf((int)"n=");
      scanf("%d", &v1);
      printf((int)"\n");
      if ( v1 >= 0 && v1 <= 8 )
        break;
      printf((int)"sorry,n error,try again\n");
    }
    if ( v1 )
    {
      sub_4576D6(v1 - 1);
    }
    else
    {
      for ( i = 0; i < 8; ++i )
      {
        if ( (unsigned int)i >= 9 )
          j____report_rangecheckfailure();
        byte_532E28[i] = 0;
      }
    }
    j__system("CLS");
    sub_458054();
    if ( byte_532E28[0] == 1
      && byte_532E28[1] == 1
      && byte_532E28[2] == 1
      && byte_532E28[3] == 1
      && byte_532E28[4] == 1
      && byte_532E28[5] == 1
      && byte_532E28[6] == 1
      && byte_532E28[7] == 1 )
    {
      sub_457AB4();
    }
  }
}
```

最后那一连串的if语句看着很可疑，进去`sub_457AB4()`看一下

```
int sub_457AB4(void)
{
  return sub_45E940();
}

```

```
int sub_45E940()
{
  signed int i; // [esp+D0h] [ebp-94h]
  char v2[125]; // [esp+DCh] [ebp-88h]

  printf((int)"done!!! the flag is ");
  v2[0] = 18;
  v2[69] = 64;
  v2[70] = 98;
  v2[71] = 5;
  v2[72] = 2;
  v2[73] = 4;
  v2[74] = 6;
  v2[75] = 3;
  v2[76] = 6;
  v2[77] = 48;
  v2[78] = 49;
  v2[79] = 65;
  v2[80] = 32;
  v2[81] = 12;
  v2[82] = 48;
  v2[83] = 65;
  v2[84] = 31;
  v2[85] = 78;
  v2[86] = 62;
  v2[87] = 32;
  v2[88] = 49;
  v2[89] = 32;
  v2[90] = 1;
  v2[91] = 57;
  v2[92] = 96;
  v2[93] = 3;
  v2[94] = 21;
  v2[95] = 9;
  v2[96] = 4;
  v2[97] = 62;
  v2[98] = 3;
  v2[99] = 5;
  v2[100] = 4;
  v2[101] = 1;
  v2[102] = 2;
  v2[103] = 3;
  v2[104] = 44;
  v2[105] = 65;
  v2[106] = 78;
  v2[107] = 32;
  v2[108] = 16;
  v2[109] = 97;
  v2[110] = 54;
  v2[111] = 16;
  v2[112] = 44;
  v2[113] = 52;
  v2[114] = 32;
  v2[115] = 64;
  v2[116] = 89;
  v2[117] = 45;
  v2[118] = 32;
  v2[119] = 65;
  v2[120] = 15;
  v2[121] = 34;
  v2[122] = 18;
  v2[123] = 16;
  v2[124] = 0;
  v2[0] = 123;
  v2[1] = 32;
  v2[2] = 18;
  v2[3] = 98;
  v2[4] = 119;
  v2[5] = 108;
  v2[6] = 65;
  v2[7] = 41;
  v2[8] = 124;
  v2[9] = 80;
  v2[10] = 125;
  v2[11] = 38;
  v2[12] = 124;
  v2[13] = 111;
  v2[14] = 74;
  v2[15] = 49;
  v2[16] = 83;
  v2[17] = 108;
  v2[18] = 94;
  v2[19] = 108;
  v2[20] = 84;
  v2[21] = 6;
  v2[22] = 96;
  v2[23] = 83;
  v2[24] = 44;
  v2[25] = 121;
  v2[26] = 104;
  v2[27] = 110;
  v2[28] = 32;
  v2[29] = 95;
  v2[30] = 117;
  v2[31] = 101;
  v2[32] = 99;
  v2[33] = 123;
  v2[34] = 127;
  v2[35] = 119;
  v2[36] = 96;
  v2[37] = 48;
  v2[38] = 107;
  v2[39] = 71;
  v2[40] = 92;
  v2[41] = 29;
  v2[42] = 81;
  v2[43] = 107;
  v2[44] = 90;
  v2[45] = 85;
  v2[46] = 64;
  v2[47] = 12;
  v2[48] = 43;
  v2[49] = 76;
  v2[50] = 86;
  v2[51] = 13;
  v2[52] = 114;
  v2[53] = 1;
  v2[54] = 117;
  v2[55] = 126;
  v2[56] = 0;
  for ( i = 0; i < 56; ++i )
  {
    v2[i] ^= v2[i + 68];
    v2[i] ^= 0x13u;
  }
  return printf((int)"%s\n");
}

```

在其中发现`printf((int)"done!!! the flag is ");`,那flag就在这咯。

连解密算法都写好了，直接复制粘贴运行就得到flag[p.s.为了方便观看，我将其修改为长度为125的数组。]

`flag：zsctf{T9is_tOpic_1s_v5ry_int7resting_b6t_others_are_n0t}`

## 0x04 逆向入门

题目：[Bugku|逆向入门](https://ctf.bugku.com/challenges#%E9%80%86%E5%90%91%E5%85%A5%E9%97%A8)

下载下来是个exe文件，运行时发现无法运行，那就file一下：

```
file admin.exe
admin.exe: ASCII text, with very long lines, with no line terminators

```

ASCII text,emmmmm，用010 editor看一下，发现其文件头为：

```
data:image/png;base64

```

那可能是个图片转成了base64编码，将010 editor那一大串全部扔到在线工具里解一下，得到一个二维码：

![](https://raw.githubusercontent.com/Alikas0/files/master/img/0x04%E9%80%86%E5%90%91%E5%85%A5%E9%97%A8.png)

扫一扫，得到flag：`bugku{inde_9882ihsd8-0}`

## 0x05 love

题目：[Bugku|love](https://ctf.bugku.com/challenges#love)

打开，要求我们输入flag，随便输一个，错误会显示`wrong flag!`

拖进IDA看一下，找到主函数

```c
__int64 main_0()
{
  int v0; // eax
  const char *v1; // eax
  size_t v2; // eax
  int v3; // edx
  __int64 v4; // ST08_8
  signed int j; // [esp+DCh] [ebp-ACh]
  signed int i; // [esp+E8h] [ebp-A0h]
  signed int v8; // [esp+E8h] [ebp-A0h]
  char Dest[108]; // [esp+F4h] [ebp-94h]
  char Str; // [esp+160h] [ebp-28h]
  char v11; // [esp+17Ch] [ebp-Ch]

  for ( i = 0; i < 100; ++i )
  {
    if ( (unsigned int)i >= 0x64 )
      j____report_rangecheckfailure();
    Dest[i] = 0;
  }
  sub_41132F("please enter the flag:");
  sub_411375("%20s", &Str); //输入，相当于scanf（）
  v0 = j_strlen(&Str);
  v1 = (const char *)sub_4110BE((int)&Str, v0, (int)&v11); //对Str进行base64加密后返回给v1
  strncpy(Dest, v1, 40u);//将v1复制给Dest
  v8 = j_strlen(Dest);
  for ( j = 0; j < v8; ++j )//再进行一次加密运算
    Dest[j] += j;
  v2 = j_strlen(Dest);
  if ( !strncmp(Dest, Str2, v2) )//比较Dest和Str2,相等返回0,输出right flag！
    sub_41132F("rigth flag!\n");
  else
    sub_41132F("wrong flag!\n");
  HIDWORD(v4) = v3;
  LODWORD(v4) = 0;
  return v4;
}

```

Str2:

```
.data:0041A034 Str2            db 'e3nifIH9b_C@n@dH',0 ; DATA XREF: _main_0+142↑o

```

sub_4110BE函数：

```c
void *__cdecl sub_4110BE(int a1, int a2, int a3)
{
  return sub_411AB0((char *)a1, a2, (int *)a3);
}

```

sub_411AB0函数：

```c
  v11 = a2;
  v7 = 0;
  while ( v11 > 0 )
  {
    byte_41A144[2] = 0;
    byte_41A144[1] = 0;
    byte_41A144[0] = 0;
    for ( i = 0; i < 3 && v11 >= 1; ++i )
    {
      byte_41A144[i] = *v13;
      --v11;
      ++v13;
    }
    if ( !i )
      break;
    switch ( i )
    {
      case 1:
        *((_BYTE *)Dst + v7) = aAbcdefghijklmn[(signed int)(unsigned __int8)byte_41A144[0] >> 2];
        v4 = v7 + 1;
        *((_BYTE *)Dst + v4++) = aAbcdefghijklmn[((byte_41A144[1] & 0xF0) >> 4) | 16 * (byte_41A144[0] & 3)];
        *((_BYTE *)Dst + v4++) = aAbcdefghijklmn[64];
        *((_BYTE *)Dst + v4) = aAbcdefghijklmn[64];
        v7 = v4 + 1;
        break;
      case 2:
        *((_BYTE *)Dst + v7) = aAbcdefghijklmn[(signed int)(unsigned __int8)byte_41A144[0] >> 2];
        v5 = v7 + 1;
        *((_BYTE *)Dst + v5++) = aAbcdefghijklmn[((byte_41A144[1] & 0xF0) >> 4) | 16 * (byte_41A144[0] & 3)];
        *((_BYTE *)Dst + v5++) = aAbcdefghijklmn[((byte_41A144[2] & 0xC0) >> 6) | 4 * (byte_41A144[1] & 0xF)];
        *((_BYTE *)Dst + v5) = aAbcdefghijklmn[64];
        v7 = v5 + 1;
        break;
      case 3:
        *((_BYTE *)Dst + v7) = aAbcdefghijklmn[(signed int)(unsigned __int8)byte_41A144[0] >> 2];
        v6 = v7 + 1;
        *((_BYTE *)Dst + v6++) = aAbcdefghijklmn[((byte_41A144[1] & 0xF0) >> 4) | 16 * (byte_41A144[0] & 3)];
        *((_BYTE *)Dst + v6++) = aAbcdefghijklmn[((byte_41A144[2] & 0xC0) >> 6) | 4 * (byte_41A144[1] & 0xF)];
        *((_BYTE *)Dst + v6) = aAbcdefghijklmn[byte_41A144[2] & 0x3F];
        v7 = v6 + 1;
        break;
    }
  }
  *((_BYTE *)Dst + v7) = 0;
  return Dst;
}

```

数组aAbcdefghijklmn点进去，标准的base64 table

```
.rdata:00417B30 aAbcdefghijklmn db 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/='

```

解密代码如下（python实现）：

```python
import base64
s = "e3nifIH9b_C@n@dH"
flag = ''
for i in range(len(s)):
    flag  += chr(ord(s[i]) - i)
flag = base64.b64decode(flag)
print(flag)

```

得到`flag{i_l0ve_you}`

## 0x06 Mountain climbing

题目：[Bugku|Mountain climbing](https://ctf.bugku.com/challenges#Mountain%20climbing)

运行，根据提示应该是通过输入操作求出`maximum`。

```
input your key with your operation can get the maximum:

```

用IDA打开后，只有一个start，程序用UPX加壳了，用[52pojie|UPX Unpacker](<https://down.52pojie.cn/?query=upx>)就可以脱壳，也可以手动脱壳0.0。

将脱壳后的程序载入IDA，找到主函数，开始分析：

```c
  __int64 v1; // ST04_8
  char v3; // [esp+0h] [ebp-160h]
  int v4; // [esp+D0h] [ebp-90h]
  int j; // [esp+DCh] [ebp-84h]
  int i; // [esp+E8h] [ebp-78h]
  char Str[104]; // [esp+F4h] [ebp-6Ch]

  srand(0xCu);//设置随机数种子
  j_memset(&unk_423D80, 0, 0x9C40u);
  for ( i = 1; i <= 20; ++i )
  {
    for ( j = 1; j <= i; ++j )
      dword_41A138[100 * i + j] = rand() % 100000;//产生随机数，范围0~100000，但由于前面生成随
  }												  //数的种子是定值，so，这里其实也是确定的
  ((void (__cdecl *)(const char *, char))sub_41134D)("input your key with your operation can get the maximum:", v3);
  sub_411249("%s", (unsigned int)Str);
  if ( j_strlen(Str) == 19 )//输入的操作长度为19
  {
    sub_41114F(Str);//对输入的key进行预处理，处理后为"L"or"R"
    v4 = 0;
    j = 1;
    i = 1;
    dword_423D78 += dword_41A138[101];//将第一行第一个赋值给dword_423D78(用来计算总和)
    while ( v4 < 19 )
    {
      if ( Str[v4] == 76 )  // chr(76) = 'L' 
      {
        dword_423D78 += dword_41A138[100 * ++i + j];
      }
      else
      {
        if ( Str[v4] != 82 ) // chr(82) = 'R'
        {
          ((void (__cdecl *)(const char *, char))sub_41134D)("error\n", v3);
          system("pause");
          goto LABEL_18;
        }
        dword_423D78 += dword_41A138[100 * ++i + ++j];
      }
      ++v4;
    }
    sub_41134D("your operation can get %d points\n", dword_423D78);
    system("pause");
  }
  else
  {
    ((void (__cdecl *)(const char *, char))sub_41134D)("error\n", v3);
    system("pause");
  }
LABEL_18:
  HIDWORD(v1) = v0;
  LODWORD(v1) = 0;
  return v1;
}

```

根据上述分析，可直接生成`Mountain`

![Mountain](https://raw.githubusercontent.com/Alikas0/files/master/img/0x06%20Mountain%20climbing.png)

进入`sub_41114F(Str);`

```c
int __cdecl sub_41114F()
{
  return sub_411900();
}

```

```c
int sub_411900()
{
  sub_4110A5(nullsub_1, (char *)sub_411994 - (char *)nullsub_1, 4);
  return nullsub_1();
}

```

```c
int __cdecl sub_4110A5(LPCVOID lpAddress, int a2, int a3)
{
  return sub_411750(lpAddress, a2, a3);
}

```

```c
BOOL __cdecl sub_411750(LPCVOID lpAddress, int a2, int a3)
{
  int v3; // ST1C_4
  DWORD flOldProtect; // [esp+D4h] [ebp-2Ch]
  struct _MEMORY_BASIC_INFORMATION Buffer; // [esp+E0h] [ebp-20h]

  VirtualQuery(lpAddress, &Buffer, 0x1Cu);
  VirtualProtect(Buffer.BaseAddress, Buffer.RegionSize, 0x40u, &Buffer.Protect);
  while ( 1 )
  {
    v3 = a2--;
    if ( !v3 )
      break;
    *(_BYTE *)lpAddress ^= a3;
    lpAddress = (char *)lpAddress + 1;
  }
  return VirtualProtect(Buffer.BaseAddress, Buffer.RegionSize, Buffer.Protect, &flOldProtect);
}

```

上述循环对key进行预处理，类型为byte，间隔为char，也是一个byte，那就是说对输入的key的第2,4,6.......拿去与`a3 = 4`异或，且异或后的字符必为"L"or"R"。

```python
chr(76^4) = H
chr(82^4) = V

```

则输入的为"R"or"L"or"H"or"V"

则求出`maximum`和`key`的python代码如下（*动态规划法求解*）：

```python
s = [[0 for i in xrange(20)] for i in xrange(20)] 
path = [[0 for i in xrange(20)] for i in xrange(20)]
seed = 0xC
def rand(): 										#生成随机数算法
        global seed
        seed = seed * 0x343FD + 0x269EC3
        return (seed >> 16) & 0x7FFF

for i in xrange(20):
        for j in xrange(i+1):
                s[i][j] = rand() % 100000			#生成随机数
for i in xrange(19,-1,-1):							#求解算法
     	for j in xrange(i-1,-1,-1): 
                if s[i][j] > s[i][j+1] :
                        t = 'L'
                        max = s[i][j]
                else:
                        t = 'R'          
                        max = s[i][j+1]
                path[i-1][j] = t
                s[i-1][j] += max
key = ""
i = 0
j = 0
for i in xrange(19):								 #变换
        if path[i][j] == "L":
                t = path[i][j]
                if i%2 != 0:				
                        t = chr(ord(t) ^ 4)      
                key += t
        else:
                t = path[i][j]
                if i%2 != 0:
                        t = chr(ord(t) ^ 4)  
                key += t
                j += 1
print "key = " + key
print "maximum = " + str(s[0][0])

```

得：

```
key = RVRVRHLVRVLVLVRVLVL
maxnum = 444740

```

则flag为`zsctf{RVRVRHLVRVLVLVRVLVL}`

```
求解算法说明：
maximum[i][j]表示第i行第j列的最大权值，那么maximum[i][j]包含子问题maximum[i-1][j-1]和maximum[i-1][j]的最优解。然后我们倒推,则可得到我们的状态转移方程:
maximum[i][j] = max{ maximum[i-1][j], maximum[i][j+1] }  + maximum[i][j];

```

## 0X07  Take the maze

题目：[Bugku|Take the maze]([https://ctf.bugku.com/challenges#Take%20the%20maze](https://ctf.bugku.com/challenges#Take the maze))

根据题目maze估计是个迷宫题

下载下来`ConsoleApplication1.exe`，运行

```
welcome to zsctf!
show me your key:

```

载入IDA，通过字符串找到主要代码：

```
__int64 main_0()
{
  int v0; // ecx
  int v1; // edx
  __int64 v2; // ST00_8
  signed int i; // [esp+D0h] [ebp-48h]
  char key[24]; // [esp+DCh] [ebp-3Ch] //本来这里识别的长度为16,但看到下面发现长度应该是24，就									      //修改了长度(顺便把名字给改了)

  printf("welcome to zsctf!\n");
  printf("show me your key:");
  scanf(v0, "%s", key);                         // 输入key
  if ( sub_45B1C7() )                           //这里面点进去最终是IsDebuggerPresent()!=0
    j__exit(0);									//是个反调试的函数
  if ( j__strlen(key) == 24 )                   // key的长度
  {
    key[16] ^= 1u;                              // 对输入key进行操作
    sub_45C748(key);
    for ( i = 0; i < 24; ++i )
    {
      if ( (key[i] < '0' || key[i] > '9') && (key[i] < 'a' || key[i] > 'f') )// 判断key的字符是否在0~9、a~f的范围内
      {
        key_error();
        goto LABEL_16;
      }
    }
    if ( check((int *)key) )
    {
      printf("done!!!The flag is your input\n");
      sub_45D9C7(4);
      sub_45E1B5();                             // 输出flag.png
    }
    else
    {
      key_error();
    }
  }
  else
  {
    key_error();
  }
LABEL_16:
  HIDWORD(v2) = v1;
  LODWORD(v2) = 0;
  return v2;
}

```

在字符串中还有很多`printing ticket`：

```c
int sub_463AE0()
{
  FILE *v0; // STE0_4

  printf("\noh,wait! Take your ticket\n");
  printf("printing ticket ...\n");
  sub_45D9C7(2);
  printf("printing ticket ....\n");
  sub_45D9C7(2);
  printf("printing ticket .....\n");
  sub_45D9C7(2);
  printf("printing ticket ......\n");
  sub_45D9C7(2);
  printf("printing ticket .......\n");
  sub_45D9C7(2);
  printf("printing ticket ........\n");
  sub_45D9C7(2);
  printf("printing ticket .........\n");
  sub_45D9C7(2);
  printf("printing ticket ..........\n");
  sub_45D9C7(2);
  printf("printing ticket ...........\n");
  sub_45D9C7(2);
  printf("printing ticket ............\n");
  sub_45D9C7(2);
  printf("printing ticket .............\n\n\n");
  v0 = j__fopen("flag.png", "wb");
  j__fwrite(&unk_5409C0, 1u, 0x78Au, v0);
  j__fclose(v0);
  printf("print finished,view the path to this file,you will get a png file,have a good time\n\n\n");
  return j__system("pause");
}

```

这个函数最后是一个文件操作，`&unk_5409C0`处的内存Dump出来，长度为0x78A。Dump后发现是个二维码：

![](https://raw.githubusercontent.com/Alikas0/files/master/img/0x07%20Take-the-maze-flag.png)

dump-python脚本：

```python
fp = open("ConsoleApplication1.exe","rb")
fp.seek(0xE45C0)
data = fp.read(1930)
fo = open("flag.png", "wb")
fo.write(data)
fo.close()

```

*seek()的地址用010 editor，Ctrl+F搜索后找到。当然也可以用010 editor直接复制出来修改后缀`.png`。*

扫一扫：`Congratulations! The flag is your input + “Docupa”`

还是乖乖分析check函数

check：

```c
// 修改类型，key是一个指向char类型的指针
BOOL __cdecl sub_463480(char *key)
{
  int v1; // ST18_4
  int v2; // ST18_4
  BOOL result; // eax
  int v4; // ST18_4
  int a1; // [esp+D8h] [ebp-38h]
  int v6; // [esp+E4h] [ebp-2Ch]
  int v7; // [esp+F0h] [ebp-20h]
  int v8; // [esp+FCh] [ebp-14h]
  int v9; // [esp+108h] [ebp-8h]

  v9 = 0;
  v6 = 0;
  a1 = 0;
  while ( 2 )
  {
    v1 = v9++;
    if ( v1 >= 12 )                             // 重复12次
      return a1 == 311;                         // a1 == 311 -> 1
    if ( sub_45B1C7() )                         // 反调试函数
      j__exit(0);
    v2 = key[v6++];
    switch ( v2 )                               // 判断第一个字符
    {
      case '0':
        v8 = 0;
        goto LABEL_12;
      case '1':
        v8 = 1;
        goto LABEL_12;
      case '2':
        v8 = 2;
        goto LABEL_12;
      case '3':
        v8 = 3;
        goto LABEL_12;
      case '4':
        v8 = 4;
LABEL_12:                                       // 判断第二个字符
        v4 = key[v6++];
        switch ( v4 )
        {
          case '5':
            v7 = 5;
            goto LABEL_25;
          case '6':
            v7 = 6;
            goto LABEL_25;
          case '7':
            v7 = 7;
            goto LABEL_25;
          case '8':
            v7 = 8;
            goto LABEL_25;
          case '9':
            v7 = 9;
            goto LABEL_25;
          case 'a':
            v7 = 10;
            goto LABEL_25;
          case 'b':
            v7 = 11;
            goto LABEL_25;
          case 'c':
            v7 = 12;
            goto LABEL_25;
          case 'd':
            v7 = 13;
            goto LABEL_25;
          case 'e':
            v7 = 14;
            goto LABEL_25;
          case 'f':
            v7 = 15;
LABEL_25:                                       // delru0123456789
                                                // 0,2,3,6对应路径下，左，右，上
                                                // 56789abcdef分别对应重复0123456789次
            switch ( op[v8] )                   // 四个结构都差不多，分析其中一个
            {
              case 'd':
                sub_45CC4D(&a1, op[v7] - '0');
                continue;
              case 'l':
                sub_45D0A3((int)&a1, op[v7] - '0');
                continue;
              case 'r':
                sub_45CB0D((int)&a1, op[v7] - '0');
                continue;
              case 'u':
                sub_45D0E9((int)&a1, op[v7] - '0');
                continue;
              default:
                result = 0;
                break;
            }
            break;
          default:
            result = 0;
            break;
        }
        break;
      default:
        result = 0;
        break;
    }
    return result;
  }
}

```

这里面两个字符为一组，进行判断，故可知判断12次。

```c
int __cdecl sub_462D60(int *a1, int a2)
{
  int v2; // ST0C_4
  int result; // eax
  int i; // [esp+E0h] [ebp-8h]

  for ( i = *a1; ; i += 26 )
  {
    v2 = a2--;
    if ( !v2 )                                  // 走的次数
      break;
    result = i / 26;
    if ( i / 26 > 10 )                          // 判断是否越界
      return result;
    result = i;
    if ( dword_5404E4[i + 25] ^ dword_540004[i + 25] )// 必须dword_540548[i] == dword_540068[i]才继续,猜测用于限定路径。这里修改了一下数组长度所以地址变了
      return result;
  }
  result = (int)a1;
  *a1 = i;
  return result;
}

```

分析另外三个函数可知，0 2 4 6 分别对应 下 左 右 上（其实对应的就是单词的首字母，一开始没看出来0.0），以及坐标范围：

x∈[1, 24] 
y∈[1, 10]

还可以发现，上和左完全不能用，反正我是没看懂这个异或在异或什么.........

上： `if ( dword_540004[i + 285] ^ dword_53FF98[i] )`

```assembly
.rdata:0053FF98 ; int dword_53FF98[25]
.rdata:0053FF98 dword_53FF98    dd ?                    ; DATA XREF: sub_464930+59↑r`

```

左：`if ( dword_540004[i + 310] ^ dword_53FFFC[i] )`

```assemby
.rdata:0053FFFC ; int dword_53FFFC[]
.rdata:0053FFFC dword_53FFFC    dd ?                    ; DATA XREF: sub_4633D0+59↑r
.rdata:0053FFFC _rdata          ends

```

重复12次，退出条件为a1 == [311= 11 * 26 + 25]并且另外发现是+26，-26，++，––猜测是26个一行

综上，**猜测迷宫为26*12，由两个数组异或得，相同为合法路径，不同为非法路径;我们的目标是从左上角走到右上角；输入的key第一个字符决定方向，第二字符决定步数**

**手动dump迷宫并打印：**

迷宫（IDA）：

```assembly
.data:00540000 _data           segment para public 'DATA' use32
.data:00540000                 assume cs:_data
.data:00540000                 ;org 540000h
.data:00540000                 db  2Ah ; *
.data:00540001                 db    0
.data:00540002                 db    0
.data:00540003                 db    0
.data:00540004 ; int dword_540004[311]
.data:00540004 dword_540004    dd 1D4h,14Fh,1,0AAh,0E1h,1DFh,167h,1CFh,1D1h,0CEh,92h,11Ah,148h,1CEh,1ECh
.data:00540004                                         ; DATA XREF: sub_4644D0+59↑r
.data:00540004                 dd 1F0h,1BBh,148h,1B5h,188h,69h,193h,9Ah,125h,17Fh,1A6h,0D9h,0DBh,18Ch,1C0h
.data:00540004                 dd 0E3h,110h,27h,172h,19Dh,0A8h,12Ch,24h,18Bh,0CCh,138h,143h,14Eh,0AEh,0A5h
.data:00540004                 dd 8Eh,0D4h,0FEh,171h,30h,91h,0A3h,102h,26h,168h,0E0h,0F2h,1Eh,117h,13Dh
.data:00540004                 dd 24h,0BFh,157h,121h,6Bh,29h,1BBh,109h,95h,1BFh,132h,187h,0E6h,173h,15Fh
.data:00540004                 dd 7,66h,18Ah,31h,82h,7Ch,55h,1C7h,101h,155h,1D3h,179h,1B0h,135h,1BDh
.data:00540004                 dd 1B8h,7Fh,144h,26h,27h,77h,53h,1AEh,2Ah,14Eh,74h,8Ch,9Fh,0CDh,1AFh
.data:00540004                 dd 1DEh,133h,0AEh,183h,16h,0F6h,1A9h,49h,10Fh,14Ah,116h,4Ah,62h,0Dh,1E7h
.data:00540004                 dd 123h,0A2h,89h,164h,10Ch,9Ch,4Bh,20h,35h,15Fh,97h,1BAh,0E1h,1D3h,1AFh
.data:00540004                 dd 6Ch,0C0h,8,152h,1CAh,120h,0FEh,180h,1BEh,19Ah,0D2h,103h,0DEh,59h,1A7h
.data:00540004                 dd 1BFh,7,1Fh,19Eh,0A9h,191h,5Ch,107h,9Ch,19Bh,168h,7Dh,26h,31h,1E4h
.data:00540004                 dd 60h,2Ah,67h,15Fh,124h,151h,177h,15h,61h,16h,15Dh,0C8h,0A9h,1E5h,11Ah
.data:00540004                 dd 0EBh,36h,1F4h,1A3h,1B7h,191h,121h,80h,1D4h,0E5h,18Ah,95h,1E4h,134h,1A6h
.data:00540004                 dd 137h,76h,13Ah,0Fh,136h,75h,1B4h,1C4h,65h,0FAh,14h,39h,12Bh,130h,0E1h
.data:00540004                 dd 9,159h,6Eh,1EAh,0CBh,0C4h,1E6h,5Eh,158h,18h,58h,13Bh,4,1C1h,0C9h
.data:00540004                 dd 1CBh,77h,51h,129h,12Bh,11Ah,5Ah,12Bh,0Ah,9Eh,1D9h,7Bh,27h,125h,27h
.data:00540004                 dd 0B4h,0BFh,9Eh,1CBh,0C0h,13Ch,185h,9Dh,0Ch,0CBh,87h,111h,38h,149h,93h
.data:00540004                 dd 16Bh,183h,178h,1B2h,172h,8Fh,159h,1A1h,17Eh,1F3h,143h,98h,16h,0C8h,3Ah
.data:00540004                 dd 1DDh,189h,186h,4Ch,0D5h,65h,0Bh,4,172h,16Ah,0BDh,192h,122h,100h,1A8h
.data:00540004                 dd 3,56h,0B7h,11Eh,59h,1ABh,76h,102h,14Dh,1B1h,0AAh,9Bh,0DEh,0BEh,1DDh
.data:00540004                 dd 14Ah,171h,0C1h,1AAh,38h,1B3h,32h,1BAh,0Dh,92h,3Dh
.data:005404E0                 db  2Ah ; *
.data:005404E1                 db    0
.data:005404E2                 db    0
.data:005404E3                 db    0
.data:005404E4 ; int dword_5404E4[311]
.data:005404E4 dword_5404E4    dd 1D5h,14Eh,1,0AAh,0E1h,1DFh,167h,1CEh,1D0h,0CFh,93h,11Bh,149h,1CFh,1EDh
.data:005404E4                                         ; DATA XREF: sub_4644D0+60↑r
.data:005404E4                 dd 1F1h,1BAh,149h,1B4h,189h,68h,192h,9Bh,124h,17Eh,1A6h,0D9h,0DAh,18Ch,1C1h
.data:005404E4                 dd 0E2h,111h,27h,173h,19Ch,0A8h,12Ch,24h,18Bh,0CCh,138h,143h,14Eh,0AEh,0A5h
.data:005404E4                 dd 8Eh,0D5h,0FFh,170h,31h,90h,0A2h,102h,26h,168h,0E1h,0F3h,1Fh,117h,13Ch
.data:005404E4                 dd 25h,0BFh,156h,120h,6Ah,28h,1BAh,108h,94h,1BEh,133h,187h,0E7h,172h,15Eh
.data:005404E4                 dd 6,67h,18Ah,31h,83h,7Dh,54h,1C6h,100h,155h,1D3h,179h,1B0h,135h,1BCh
.data:005404E4                 dd 1B9h,7Eh,145h,2 dup(27h),77h,52h,1AEh,2Bh,14Fh,75h,8Dh,9Eh,0CCh,1AFh,1DEh
.data:005404E4                 dd 132h,0AFh,182h,17h,0F7h,1A8h,48h,10Eh,14Ah,117h,4Bh,63h,0Ch,1E6h,122h
.data:005404E4                 dd 0A2h,88h,165h,10Dh,9Dh,4Ah,21h,34h,15Eh,96h,1BAh,0E1h,1D3h,1AFh,6Ch
.data:005404E4                 dd 0C0h,8,153h,1CBh,120h,0FEh,180h,1BEh,19Ah,0D2h,103h,0DEh,59h,1A7h,1BEh
.data:005404E4                 dd 6,1Eh,19Fh,0A8h,190h,5Dh,106h,9Dh,19Bh,169h,7Ch,27h,31h,1E5h,61h
.data:005404E4                 dd 2Bh,66h,15Eh,125h,150h,176h,14h,60h,17h,15Dh,0C9h,0A8h,1E4h,11Bh,0EAh
.data:005404E4                 dd 37h,1F5h,1A2h,1B6h,191h,120h,81h,1D5h,0E5h,18Bh,94h,1E5h,135h,1A7h,136h
.data:005404E4                 dd 77h,13Bh,0Eh,137h,74h,1B4h,1C5h,64h,0FBh,15h,38h,12Ah,131h,0E0h,8
.data:005404E4                 dd 159h,6Fh,1EBh,0CAh,0C4h,1E6h,5Eh,158h,18h,59h,13Ah,5,1C0h,0C8h,1CAh
.data:005404E4                 dd 77h,51h,128h,12Ah,11Bh,5Bh,12Ah,0Bh,9Fh,1D8h,7Ah,27h,124h,26h,0B5h
.data:005404E4                 dd 0BEh,9Fh,1CAh,0C1h,13Ch,184h,9Ch,0Dh,0CAh,86h,110h,39h,148h,92h,16Ah
.data:005404E4                 dd 182h,179h,1B3h,173h,8Eh,158h,1A0h,17Eh,1F2h,142h,99h,17h,0C9h,3Bh,1DCh
.data:005404E4                 dd 189h,186h,4Ch,0D5h,65h,0Bh,4,172h,16Ah,0BDh,193h,123h,101h,1A9h,2
.data:005404E4                 dd 57h,0B6h,11Fh,58h,1AAh,77h,103h,14Ch,1B0h,0ABh,9Ah,0DFh,0BFh,1DCh,14Bh
.data:005404E4                 dd 170h,0C0h,1ABh,39h,1B2h,32h,1BAh,0Dh,92h,3Dh

```

由于猜测迷宫为12*26=312，所以猜测前面2A也应该是在迷宫之中（不然看不懂两个311中间插着一个2A是干嘛用的.....）

```assembly
.data:005404E0                 db  2Ah ; *
.data:005404E1                 db    0
.data:005404E2                 db    0
.data:005404E3                 db    0

```

python脚本：

```python
maze_0 = [42,468,335,1,170,225,479,359,463,465,206,146,282,328,462,492,496,443,328,437,392,105,403,154,293,383,422,217,219,396,448,227,272,39,370,413,168,300,36,395,204,312,323,334,174,165,142,212,254,369,48,145,163,258,38,360,224,242,30,279,317,36,191,343,289,107,41,443,265,149,447,306,391,230,371,351,7,102,394,49,130,124,85,455,257,341,467,377,432,309,445,440,127,324,38,39,119,83,430,42,334,116,140,159,205,431,478,307,174,387,22,246,425,73,271,330,278,74,98,13,487,291,162,137,356,268,156,75,32,53,351,151,442,225,467,431,108,192,8,338,458,288,254,384,446,410,210,259,222,89,423,447,7,31,414,169,401,92,263,156,411,360,125,38,49,484,96,42,103,351,292,337,375,21,97,22,349,200,169,485,282,235,54,500,419,439,401,289,128,468,229,394,149,484,308,422,311,118,314,15,310,117,436,452,101,250,20,57,299,304,225,9,345,110,490,203,196,486,94,344,24,88,315,4,449,201,459,119,81,297,299,282,90,299,10,158,473,123,39,293,39,180,191,158,459,192,316,389,157,12,203,135,273,56,329,147,363,387,376,434,370,143,345,417,382,499,323,152,22,200,58,477,393,390,76,213,101,11,4,370,362,189,402,290,256,424,3,86,183,286,89,427,118,258,333,433,170,155,222,190,477,330,369,193,426,56,435,50,442,13,146,61]
maze_1 = [42,469,334,1,170,225,479,359,462,464,207,147,283,329,463,493,497,442,329,436,393,104,402,155,292,382,422,217,218,396,449,226,273,39,371,412,168,300,36,395,204,312,323,334,174,165,142,213,255,368,49,144,162,258,38,360,225,243,31,279,316,37,191,342,288,106,40,442,264,148,446,307,391,231,370,350,6,103,394,49,131,125,84,454,256,341,467,377,432,309,444,441,126,325,39,39,119,82,430,43,335,117,141,158,204,431,478,306,175,386,23,247,424,72,270,330,279,75,99,12,486,290,162,136,357,269,157,74,33,52,350,150,442,225,467,431,108,192,8,339,459,288,254,384,446,410,210,259,222,89,423,446,6,30,415,168,400,93,262,157,411,361,124,39,49,485,97,43,102,350,293,336,374,20,96,23,349,201,168,484,283,234,55,501,418,438,401,288,129,469,229,395,148,485,309,423,310,119,315,14,311,116,436,453,100,251,21,56,298,305,224,8,345,111,491,202,196,486,94,344,24,89,314,5,448,200,458,119,81,296,298,283,91,298,11,159,472,122,39,292,38,181,190,159,458,193,316,388,156,13,202,134,272,57,328,146,362,386,377,435,371,142,344,416,382,498,322,153,23,201,59,476,393,390,76,213,101,11,4,370,362,189,403,291,257,425,2,87,182,287,88,426,119,259,332,432,171,154,223,191,476,331,368,192,427,57,434,50,442,13,146,61]

maze = ""
for i in xrange(len(maze_0)):
    if i%26 == 0:
        print maze
        maze = ""
    if maze_0[i] == maze_1[i] :
        maze += "1 "
    else:
        maze += "0 "
print maze

```

迷宫：

```text
1 0 0 1 1 1 1 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
1 1 0 1 0 0 0 1 0 0 1 1 1 1 1 1 1 1 1 1 1 0 0 0 0 0
0 1 1 1 0 0 0 1 0 0 1 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0
1 1 0 0 0 0 0 1 1 1 1 1 0 0 0 0 0 1 1 0 1 0 0 0 0 0
0 1 1 0 0 0 0 0 0 0 0 1 0 0 0 0 0 0 1 0 0 0 0 0 0 0
0 0 1 1 1 1 1 1 1 0 0 1 1 1 1 1 1 1 1 1 1 0 0 0 0 0
0 0 0 0 1 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0
0 0 0 0 1 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0
0 0 0 0 1 0 0 0 1 1 1 1 1 0 0 0 0 0 0 1 1 0 0 0 0 0
0 0 0 0 1 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0
0 0 0 0 1 0 0 0 0 0 0 0 1 1 1 1 1 1 1 1 1 1 0 0 0 0
0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1 1 1 1
```

手动走迷宫，得`06360836063b0839073e0639`

最后来分析一下主函数里的：

```c
 key[16] ^= 1u;                              // 对输入字符进行操作
 sub_45C748(key);

```

这个操作函数就是个VM：

```c
void __cdecl sub_4649E0(void *a1)
{
  void *v1; // ST100_4
  char *v2; // STF4_4
  void *v3; // STE8_4
  _DWORD *v4; // STDC_4

  v1 = j__malloc(0x40u);
  v2 = (char *)j__malloc(0x40u);
  v3 = j__malloc(0x20u);
  v4 = j__malloc(0x10u);
  j__memset(v2, 0, 0x40u);
  j__memset(v1, 0, 0x40u);
  j__memset(v3, 0, 0x20u);
  j__memmove(v1, a1, 24u);
  *v4 = &byte_541178;
  v4[1] = v3;
  v4[2] = v2 + 128;
  v4[3] = v1;
  sub_45DCD3((int)v4);
  j__memmove(a1, v1, 24u);
  j__free(v1);
  j__free(v2);
  j__free(v3);
  j__free(v4);
}

```

直接动态调试猜结果吧....(后面再来静态分析一下)

输入测试字符24个1时发现这个函数的操作就是输入异或位数

解密脚本：

```python
key = list("06360836063b0839073e0639")
key[16] = chr(ord(key[16]) ^ 1)
flag = ""
for i in range(24):
	flag +=chr(ord(key[i])^i)
print flag
```

得：`07154=518?9i<5=6!&!v$#%.`

最后在字符串后面加上前面的`Docupa`

得flag： `zsctf{07154=518?9i<5=6!&!v$#%.Docupa}`

## 0x08 file

无壳，载入IDA，分析主函数

```c
int __cdecl main(int argc, const char **argv, const char **envp)
{
  const char *v3; // rsi
  const char **v5; // [rsp+0h] [rbp-1A0h]
  int i; // [rsp+1Ch] [rbp-184h]
  int v7; // [rsp+20h] [rbp-180h]
  signed int j; // [rsp+24h] [rbp-17Ch]
  int k; // [rsp+28h] [rbp-178h]
  FILE *v10; // [rsp+30h] [rbp-170h]
  _BYTE *v11; // [rsp+40h] [rbp-160h]
  _BYTE *v12; // [rsp+48h] [rbp-158h]
  int v13[64]; // [rsp+50h] [rbp-150h]
  char file[72]; // [rsp+150h] [rbp-50h]
  unsigned __int64 v15; // [rsp+198h] [rbp-8h]

  v5 = argv;
  v15 = __readfsqword(0x28u);
  v10 = fopen(argv[1], "r");
  if ( !v10 )
    __assert_fail("file!=NULL", "file.c", 0x8Fu, "main");
  for ( i = 0; ; ++i )
  {
    v3 = "%c";
    if ( (unsigned int)__isoc99_fscanf(v10, "%c", &file[i]) == -1 )// 读取文件
      break;
  }
  v7 = 0;
  v11 = (_BYTE *)encode(flllag);                // base64加密，但后面没用到，感觉是用来混淆的
  v12 = (_BYTE *)encode(sttr_home);             // sttr_home = "664e06226625425d562e766e042d422c072c45692d125c7e6552606954646643"
                                                // flllag = "flag{hello_player_come_on_hahah}"
  for ( j = 0; j <= 63; j += 2 )
  {
    v3 = (const char *)(unsigned int)sttr_home[j + 1];
    v13[v7++] = sub_400EB9((unsigned int)sttr_home[j], v3);// 对sttr_home进行操作后存储在v13中
  }
  *v11 = encode(flllag);
  for ( k = 0; k < v7; ++k )
  {
    if ( *flllag != (k ^ file[k] ^ v13[k]) )    // 判断，可知file[k] = v13[k] ^ flllag[k] ^ k
    {
      printf("Your file is wrong!! try again", v5);
      return 0;
    }
    ++flllag;
  }
  *v12 = encode(sttr_home);
  printf("the flag is file's MD5 Congratulations!", v3, v5);// flag最后为输入文件的MD5值
  return 0;
}

```

运行程序时传递的第一个参数为一个文件名，并打开该文件。

判断语句`if ( *flllag != (k ^ file[k] ^ v13[k]) )`可知`file[k] = v13[k] ^ flllag[k] ^ k`

flllag[]已知，接下来解决v13[]

```
for ( j = 0; j <= 63; j += 2 )
  {
    v3 = (const char *)(unsigned int)sttr_home[j + 1];
    v13[v7++] = sub_400EB9((unsigned int)sttr_home[j], v3);
  }

```

```
__int64 __fastcall sub_400EB9(char a1, char a2)
{
  int v2; // ebx

  v2 = 16 * (unsigned __int64)sub_400E6A((unsigned int)a1);
  return v2 + (unsigned int)sub_400E6A((unsigned int)a2);
}

```

```
signed __int64 __fastcall sub_400E6A(char a1)
{
  if ( a1 > '/' && a1 <= '9' )                  // 0 ~ 9
    return (unsigned int)(a1 - 48);             // A ~ F
  if ( a1 > '@' && a1 <= 'F' )
    return (unsigned int)(a1 - 55);             // 不在a ~ f
  if ( a1 <= '`' || a1 > 'f' )
    return 0xFFFFFFFFLL;
  return (unsigned int)(a1 - 87);               // a ~ f
}

```

这里把传入的字符判断在哪个区间，然后分别返回不同的值。

则v13[]解密代码：

```python
sttr_home = "664e06226625425d562e766e042d422c072c45692d125c7e6552606954646643"
v13 = []
def fuction2(a):
    if a > 47 and a <= 57:
        return a - 48
    if a > 64 and a <= 70:
        return a - 55
    if a <= 96 or a > 102:
        return 0xFFFFFFFF
    return a - 87
def fuction1(a,b):
    return 16 * fuction2(ord(a)) + fuction2(ord(b))

for i in xrange(0,len(sttr_home),2):
    v13.append(fuction1(sttr_home[i],sttr_home[i+1]))

```

则可解出file[]，并写入文件file.txt:

```
sttr_home = "664e06226625425d562e766e042d422c072c45692d125c7e6552606954646643"
flllag = "flag{hello_player_come_on_hahah}"
v13 = []
file = []
def fuction2(a):
    if a > 47 and a <= 57:
        return a - 48
    if a > 64 and a <= 70:
        return a - 55
    if a <= 96 or a > 102:
        return 0xFFFFFFFF
    return a - 87
def fuction1(a,b):
    return 16 * fuction2(ord(a)) + fuction2(ord(b))

for i in xrange(0,len(sttr_home),2):
    v13.append(fuction1(sttr_home[i],sttr_home[i+1]))
fp = open("file.txt", "w")
for i in xrange(len(flllag)):
    file.append(chr(i ^ v13[i] ^ ord(flllag[i])))

fp.writelines(file)
fp.close()

```

```
./file file.txt
the flag is file's MD5 Congratulations!

```

顺便把MD5值也算出来：

```python
fp = open("file.txt","rb")
md5_obj = hashlib.md5()
md5_obj.update(fp.read())
hash_code = md5_obj.hexdigest()
md5 = str(hash_code).lower()
fp.close()

print "flag{" + md5 + "}"

```

则最终flag为`flag{914a7b9df69eab5b74b9edb7070e53e8}`