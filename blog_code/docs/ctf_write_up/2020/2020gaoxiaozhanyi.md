## 天津垓

首先题目要求我们输入用户名，解密脚本:

```python
cipher = [17, 8, 6, 10, 15, 20, 42, 59, 47, 3, 47, 4, 16, 72, 62, 0, 7, 16]
key = list(b"Rising_Hopper!")
plain = []
for i in range(18):
    plain.append(cipher[i]^key[i%14])
print(bytes(plain))
```

得`Caucasus@s_ability`

但输入后就直接退出了，再次查看发现如果在运行过程中发现这几个窗口就会运行错误代码，此处直接在调用处patch为nop即可

```c
signed __int64 __fastcall EnumFunc(HWND a1, _DWORD *a2)
{
  __int64 v3; // [rsp+0h] [rbp-80h]
  CHAR String; // [rsp+20h] [rbp-60h]
  _DWORD *v5; // [rsp+438h] [rbp+3B8h]

  v5 = a2;
  GetWindowTextA(a1, (LPSTR)&v3 + 32, 1023);
  if ( strstr(&String, "WinDbg")
    || strstr(&String, "IDA")
    || strstr(&String, "x64_dbg")
    || strstr(&String, "OllyICE")
    || strstr(&String, "OllyDBG")
    || strstr(&String, "Immunity") )
  {
    *v5 = 1;
  }
  return 1i64;
}
```

在调试过程中还发现SMC(代码自修改)，用IDA脚本patch一下:

```python
import ida_bytes
xor = map(ord, "Caucasus@s_ability")
buf = map(ord, ida_bytes.get_bytes(0x00000010040164D,1045))
for i in range(len(buf)):
	buf[i] ^= xor[i % 18]
ida_bytes.patch_bytes(0x00000010040164D, str(bytearray(buf)))
```

得到函数:

```c
int sub_10040164D()
{
  char Str[74]; // [rsp+20h] [rbp-60h]
  char v2[8]; // [rsp+6Ah] [rbp-16h]
  __int16 v3; // [rsp+78h] [rbp-8h]
  char v4; // [rsp+7Ah] [rbp-6h]
  char v5[4]; // [rsp+7Bh] [rbp-5h]
  char v6[8]; // [rsp+80h] [rbp+0h]
  char v7[8]; // [rsp+A0h] [rbp+20h]
  char Format[8]; // [rsp+D0h] [rbp+50h]
  int v9; // [rsp+110h] [rbp+90h]
  int v10; // [rsp+114h] [rbp+94h]
  int v11; // [rsp+118h] [rbp+98h]
  int v12; // [rsp+11Ch] [rbp+9Ch]
  int v13; // [rsp+120h] [rbp+A0h]
  int v14; // [rsp+124h] [rbp+A4h]
  int v15; // [rsp+128h] [rbp+A8h]
  int v16; // [rsp+12Ch] [rbp+ACh]
  int v17; // [rsp+130h] [rbp+B0h]
  int v18; // [rsp+134h] [rbp+B4h]
  int v19; // [rsp+138h] [rbp+B8h]
  int v20; // [rsp+13Ch] [rbp+BCh]
  int v21; // [rsp+140h] [rbp+C0h]
  int v22; // [rsp+144h] [rbp+C4h]
  int v23; // [rsp+148h] [rbp+C8h]
  int v24; // [rsp+14Ch] [rbp+CCh]
  int v25; // [rsp+150h] [rbp+D0h]
  int v26; // [rsp+154h] [rbp+D4h]
  int v27; // [rsp+158h] [rbp+D8h]
  int v28; // [rsp+15Ch] [rbp+DCh]
  int v29; // [rsp+160h] [rbp+E0h]
  int v30; // [rsp+164h] [rbp+E4h]
  int v31; // [rsp+168h] [rbp+E8h]
  int v32; // [rsp+16Ch] [rbp+ECh]
  int v33; // [rsp+170h] [rbp+F0h]
  int v34; // [rsp+174h] [rbp+F4h]
  int v35; // [rsp+178h] [rbp+F8h]
  int v36; // [rsp+17Ch] [rbp+FCh]
  int v37; // [rsp+180h] [rbp+100h]
  int v38; // [rsp+184h] [rbp+104h]
  int v39; // [rsp+188h] [rbp+108h]
  int v40; // [rsp+18Ch] [rbp+10Ch]
  int v41; // [rsp+190h] [rbp+110h]
  int v42; // [rsp+194h] [rbp+114h]
  int v43; // [rsp+198h] [rbp+118h]
  int v44; // [rsp+19Ch] [rbp+11Ch]
  int v45; // [rsp+1A0h] [rbp+120h]
  int v46; // [rsp+1A4h] [rbp+124h]
  int v47; // [rsp+1A8h] [rbp+128h]
  int v48; // [rsp+1ACh] [rbp+12Ch]
  int v49; // [rsp+1B0h] [rbp+130h]
  int v50; // [rsp+1B4h] [rbp+134h]
  int v51; // [rsp+1B8h] [rbp+138h]
  int v52; // [rsp+1BCh] [rbp+13Ch]
  int v53; // [rsp+1C0h] [rbp+140h]
  int v54; // [rsp+1C4h] [rbp+144h]
  int v55; // [rsp+1C8h] [rbp+148h]
  int v56; // [rsp+1CCh] [rbp+14Ch]
  int v57; // [rsp+1D0h] [rbp+150h]
  int v58; // [rsp+1D4h] [rbp+154h]
  int v59; // [rsp+1D8h] [rbp+158h]
  unsigned int v60; // [rsp+1E0h] [rbp+160h]
  int v61; // [rsp+1E4h] [rbp+164h]
  unsigned int v62; // [rsp+1E8h] [rbp+168h]
  unsigned int i; // [rsp+1ECh] [rbp+16Ch]

  v9 = 2007666;
  v10 = 2125764;
  v11 = 1909251;
  v12 = 2027349;
  v13 = 2421009;
  v14 = 1653372;
  v15 = 2047032;
  v16 = 2184813;
  v17 = 2302911;
  v18 = 2263545;
  v19 = 1909251;
  v20 = 2165130;
  v21 = 1968300;
  v22 = 2243862;
  v23 = 2066715;
  v24 = 2322594;
  v25 = 1987983;
  v26 = 2243862;
  v27 = 1869885;
  v28 = 2066715;
  v29 = 2263545;
  v30 = 1869885;
  v31 = 964467;
  v32 = 944784;
  v33 = 944784;
  v34 = 944784;
  v35 = 728271;
  v36 = 1869885;
  v37 = 2263545;
  v38 = 2283228;
  v39 = 2243862;
  v40 = 2184813;
  v41 = 2165130;
  v42 = 2027349;
  v43 = 1987983;
  v44 = 2243862;
  v45 = 1869885;
  v46 = 2283228;
  v47 = 2047032;
  v48 = 1909251;
  v49 = 2165130;
  v50 = 1869885;
  v51 = 2401326;
  v52 = 1987983;
  v53 = 2243862;
  v54 = 2184813;
  v55 = 885735;
  v56 = 2184813;
  v57 = 2165130;
  v58 = 1987983;
  v59 = 2460375;
  strcpy(Format, "Input the flag to hijack the ability of Hiden Intelligence:");
  strcpy(v7, "Progrise Key confirmed. Ready to break.\n");
  strcpy(v6, "Jacking Break! Zaia Enterprise.");
  strcpy(v5, "%59s");
  v3 = 29477;
  v4 = 0;
  strcpy(v2, "Not verified!");
  v62 = -2147483637;
  printf(Format);
  scanf(v5, Str);
  printf(v7);
  if ( strlen(Str) != 51 )
  {
    printf(v2);
    exit(0);
  }
  v61 = 19683;
  for ( i = 0; i <= 0x32; ++i )
  {
    v60 = v61 * (unsigned int)(unsigned __int8)Str[i] % v62;
    if ( v60 != *(&v9 + i) )
    {
      printf(v2);
      exit(0);
    }
  }
  printf(v6);
  getchar();
  return getchar();
}
```

加密比较简单：

```c
res = [0x1EA272, 0x206FC4, 0x1D2203, 0x1EEF55, 2421009, 0x193A7C, 0x1F3C38, 2184813, 2302911, 2263545, 1909251, 2165130, 1968300, 2243862, 2066715, 2322594, 1987983, 2243862, 1869885, 2066715, 2263545, 1869885, 964467, 944784, 944784, 944784, 728271, 1869885, 2263545, 2283228, 2243862, 2184813, 2165130, 2027349, 1987983, 2243862, 1869885, 2283228, 2047032, 1909251, 2165130, 1869885, 2401326, 1987983, 2243862, 2184813, 885735, 2184813, 2165130, 1987983, 2460375]

mul = 19683
for i in range(51):
    res[i] //= mul
print(bytes(res))
```

最终flag为:
`flag{Thousandriver_is_1000%_stronger_than_zero-one}`

## fxck!

```c
s = (char *)malloc(0x2BuLL);
  check_array = (char *)malloc(0x3CuLL);
  std::operator>><char,std::char_traits<char>>(&std::cin, s);
  input_len = strlen(s);
  if ( input_len <= 42 && input_len )
  {
    operate(s, input_len, check_array);
    if ( (unsigned int)check(check_array) == 0 )
```

主逻辑为接受一个长度小于42的字符串对其进行操作后校验

在`check()`函数中由于` return strcmp(a4vyhutqrfyfnq8, check_array) == 0;`可以在调试过程中直接得到校验字符串为`4VyhuTqRfYFnQ85Bcw5XcDr3ScNBjf5CzwUdWKVM7SSVqBrkvYGt7SSUJe`

操作过程为对输入进行base58加密,tab表为自修改后出现

最终解密脚本为:

```python
from binascii import *
from Crypto.Util.number import *

tab = b'ABCDEFGHJKLMNPQRSTUVWXYZ123456789abcdefghijkmnopqrstuvwxyz'

def b58decode(s):
    global tab
    tmp = b""
    for i in s[::-1]:
        tmp += bytes([tab.index(i)])
    pow = 1
    res = 0
    for i in range(len(tmp)):
        res += pow*tmp[i]
        pow *= 58
    return long_to_bytes(res)[:]

print(b58decode(b"4VyhuTqRfYFnQ85Bcw5XcDr3ScNBjf5CzwUdWKVM7SSVqBrkvYGt7SSUJe"))
```

flag为:`flag{63510cf7-2b80-45e1-a186-21234897e5cd}`

以下是Apeng大佬wp原话:

```
base58，之后用brainfuck语言加密，但是代码可能写错了，每次加密的都是上一位的结果。修改之后的版本改为直接对比编码结果，解base58即可。

base58编码部分也有bug，存放结果的位置没有初始化为0，导致存放了一些垃圾数据，体现在解密后为\x06开头。位数短的话这里会是\xE0。
```

学到了0.0

## easyparser

VM题，init和finit还有另外几部分虚拟机，主要用于输出题目和初始化res数组，加密比较简单:

```python
res = [0x90, 0x14C, 0x1C, 0xF0, 0x84, 0x3C, 0x18, 0x40, 0x40, 0xF0, 0xD0, 0x58, 0x2C, 0x8, 0x34, 0xF0, 0x114, 0xF0, 0x80, 0x2C, 0x28, 0x34, 0x8, 0xF0, 0x90, 0x44, 0x30, 0x50, 0x5C, 0x2C, 0x108, 0xF0]
for i in range(len(res)):
    res[i] >>= 2
    res[i] ^= 0x63
print(bytes(res))
```

flag:`flag{G0d_Bless_Wuhan_&_China_Growth!}`

## cyclegraph

图算法，用深搜和可见字符进行过滤爆破，且题目对最后一位进行了校验，避免多解:

```python
# graph = [0x34,0x0F13398,0x0F1338C,0x2,0x0F13398,0x0F133E0,0x2C,0x0F1338C,0x0F133D4,0x2A,0x0F13458,0x0F13494,0x6,0x0F133D4,0x0F133EC,0x2A,0x0F13398,0x0F13464,0x2F,0x0F134B8,0x0F134F4,0x2A,0x0F1341C,0x0F13494,0x33,0x0F133B0,0x0F133EC,0x3,0x0F133F8,0x0F1341C,0x2,0x0F133B0,0x0F13410,0x32,0x0F1347C,0x0F134DC,0x32,0x0F13428,0x0F133F8,0x32,0x0F1338C,0x0F134A0,0x30,0x0F13380,0x0F133EC,0x3,0x0F13428,0x0F134A0,0x1,0x0F133BC,0x0F134AC,0x32,0x0F133D4,0x0F133EC,0x2B,0x0F134D0,0x0F134B8,0x2,0x0F13410,0x0F133A4,0x2E,0x0F134D0,0x0F13488,0x1,0x0F13434,0x0F133C8,0x2,0x0F13434,0x0F1344C,0x2D,0x0F13398,0x0F1341C,0x32,0x0F13440,0x0F133D4,0x4,0x0F13494,0x0F13434,0x2D,0x0F134E8,0x0F13470,0x30,0x0F13494,0x0F1338C,0x31,0x0F13464,0x0F13440,0x2F,0x0F133EC,0x0F133B0,0x33,0x0F13488,0x0F13404,0x5,0x0F134F4,0x0F134F4]
# graph1 = []
# for i in graph:
# 	if i > 0x00F13380:
# 		graph1.append((i - 0x0F13380) / 4)
# 	else:
# 		graph1.append(i)
# print(graph1) 
# 96

start = 0x30
graph = [52, 6, 3, 2, 6, 24, 44, 3, 21, 42, 54, 69, 6, 21, 27, 42, 6, 57, 47, 78, 93, 42, 39, 69, 51, 12, 27, 3, 30, 39, 2, 12, 36, 50, 63, 87, 50, 42, 30, 50, 3, 72, 48, 15807360, 27, 3, 42, 72, 1, 15, 75, 50, 21, 27, 43, 84, 78, 2, 36, 9, 46, 84, 66, 1, 45, 18, 2, 45, 51, 45, 6, 39, 50, 48, 21, 4, 69, 45, 45, 90, 60, 48, 69, 3, 49, 57, 48, 47, 27, 12, 51, 66, 33, 5, 93, 93]


list = "0123456789abcdefghijklmnopqrstuvwxyz_@"
list = map(ord,list)
def dfs(index, i, flag, s):
	global graph
	if i == 16:
		if graph[index] == 5:
			print("flag{" + flag + "}")
		return 0
	else:
		if s + graph[index] in list:
			dfs(graph[index + 1], i + 1, flag + chr(s + graph[index]), s + graph[index])
		if s - graph[index] in list:
			dfs(graph[index + 2], i + 1, flag + chr(s - graph[index]), s - graph[index])
		return 0

dfs(0, 0, "", start)
```

flag: `flag{d8b0bc97a6c0ba27}`

