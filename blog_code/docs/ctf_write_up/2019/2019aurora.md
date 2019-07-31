记录一次内部赛的writeup0.0

## Climb

题目: [Climb](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/2019/Aurora/Climb/Climb)

可以直接找到main函数：

```c
__int64 __fastcall main(__int64 a1, char **a2, char **a3)
{
  __int64 result; // rax
  char v4; // [rsp+Fh] [rbp-91h]
  int i; // [rsp+10h] [rbp-90h]
  signed int j; // [rsp+14h] [rbp-8Ch]
  signed int k; // [rsp+18h] [rbp-88h]
  __int64 v8; // [rsp+1Ch] [rbp-84h]
  void *dest; // [rsp+20h] [rbp-80h]
  void *s1; // [rsp+28h] [rbp-78h]
  char s; // [rsp+30h] [rbp-70h]
  int v12; // [rsp+90h] [rbp-10h]
  unsigned __int64 v13; // [rsp+98h] [rbp-8h]

  v13 = __readfsqword(0x28u);
  memset(&s, 0, 0x60uLL);
  v12 = 0;
  puts("Check up:");
  __isoc99_scanf("%s", &s);
  v8 = (unsigned int)strlen(&s);
  if ( (signed int)v8 <= 100 )
  {
    if ( (signed int)v8 % 7 )
    {
      puts("Invalid lenth!");
      result = 0LL;
    }
    else
    {
      dest = malloc((signed int)v8);
      s1 = malloc((signed int)v8);
      memcpy(dest, &s, (signed int)v8);
      memset(s1, 0, (signed int)v8);
      for ( i = 0; i < (signed int)v8 / 7; ++i )
      {
        for ( j = 0; j <= 6; ++j )
        {
          v4 = 0;
          for ( k = 0; k <= 6; ++k )
            v4 += *((_BYTE *)dest + 7 * i + k) * climb[7 * j + k];// 输入的每7个为一组，每个和key异或
                                                // 类似线性代数的矩阵相乘
          *((_BYTE *)s1 + 7 * i + j) = v4;
        }
      }
      if ( !memcmp(s1, &key, 70uLL) )           // 输入长度猜测为70
        puts("OK!");
      else
        puts("Nope.");
      free(dest);
      free(s1);
      result = 0LL;
    }
  }
  else
  {
    puts("Too long!");
    result = 0LL;
  }
  return result;
}
```

那首先将climb和key转化为两个矩阵。然后用扩展欧几里得算法求出，climb行列式的值的逆元，以求出climb的逆矩阵，相乘即得答案。

[希尔密码求解方法]

脚本如下：

```python
from numpy import *
import numpy

key = [[18, 245, 75, 32, 157, 232, 99],
[165, 54, 146, 18, 221, 244, 196],
[157, 89, 115, 88, 100, 184, 103],
[91, 38, 206, 47, 46, 82, 135],
[200, 200, 64, 21, 82, 20, 156],
[202, 165, 27, 33, 69, 217, 235],
[49, 48, 196, 231, 173, 145, 174],
[250, 104, 42, 189, 118, 127, 243],
[134, 197, 174, 127, 20, 104, 70],
[170, 221, 232, 219, 251, 132, 231]]

climb = [[65, 108, 109, 111, 115, 116, 32],
		[104, 101, 97, 118, 101, 110, 32],
		[119, 101, 115, 116, 32, 118, 105],
		[114, 103, 105, 110, 105, 97, 44],
		[32, 98, 108, 117, 101, 32, 114],
		[105, 100, 103, 101, 32, 109, 111],
		[117, 110, 116, 97, 105, 110, 115]]

climb = numpy.array(climb)
key = mat(key)

#扩展欧几里得算法
def exgcd(m,n,x,y):
	if n == 0:
		x = 1
		y = 0
		return (m,x,y)
	a1 = b = 1
	a = b1 = 0
	c = m
	d = n
	q = int(c/d)
	r = c%d
	while r:
		c = d
		d = r
		t = a1
		a1 = a
		a = t-q*a
		t = b1
		b1 = b
		b = t-q*b
		q = int(c/d)
		r = c%d
	x = a
	y = b
	return (d,x,y)

#求出逆元x
#print numpy.linalg.det(climb)
#print exgcd(-512070636337,256,0,0)


x = 47
a = numpy.dot(numpy.linalg.det(climb),numpy.linalg.inv(climb))#求出逆矩阵

#约等于取整数
for i in xrange(7):
	for j in xrange(7):
		a[i][j] = round(a[i][j])

a = a + 0x100 * 1000000000

#print a % 256

#print ((((47 * a) % 256) * key.T )% 256).T #求解出下方的s

s =  [65,117,114, 111, 114,  97, 123,
 55,104, 51, 110,  95, 102,  52,
114, 51, 95, 117,  95, 119,  51,
108,108, 95,  53, 119,  51,  51,
 55, 95, 99, 114,  52, 103,  49,
 51, 95, 72,  73,  76,  76,  95,
119,104, 51, 114,  51,  95,  48,
102, 51,110,  95,  55,  49, 109,
 51, 53, 95,  49,  95, 118,  51,
 95,114, 48, 118,  51, 100, 125]

flag = ""

for j in xrange(70):
	flag += chr(s[j])

print flag
```

flag : Aurora{7h3n_f4r3_u_w3ll_5w337_cr4g13_HILL_wh3r3_0f3n_71m35_1_v3_r0v3d}

## baby_transform

题目：[baby_transform](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/2019/Aurora/baby_transform/baby_transform.zip)

main函数：

```c
int __cdecl main(int argc, const char **argv, const char **envp)
{
  unsigned int n; // ST0C_4
  void *s; // ST20_8
  const void *v6; // ST28_8
  FILE *n_4; // [rsp+10h] [rbp-90h]
  FILE *v8; // [rsp+18h] [rbp-88h]
  char ptr; // [rsp+30h] [rbp-70h]
  int v10; // [rsp+90h] [rbp-10h]
  unsigned __int64 v11; // [rsp+98h] [rbp-8h]

  v11 = __readfsqword(0x28u);
  memset(&ptr, 0, 0x60uLL);
  v10 = 0;
  n_4 = fopen("./flag", "rb");
  v8 = fopen("./enc", "wb");
  if ( n_4 )
  {
    fread(&ptr, 1uLL, 0x64uLL, n_4);
    n = strlen(&ptr);
    s = malloc(n + 1);
    memset(s, 0, n + 1);
    memcpy(s, &ptr, n);
    v6 = malloc(16 * n);
    Fourier_transform((__int64)s, (__int64)v6, n);//大佬忘记去符号表了
    fwrite(v6, 0x10uLL, n, v8);
    puts("Transfrom completed!");
  }
  else
  {
    puts("Cannot open the file! Please put the file \"flag\" in the current directory.");
  }
  return 0;
}
```

分析可得过程为：读取flag文件->傅里叶变换->输出到enc文件

Fourier_transform函数：

```c
__int64 __fastcall Fourier_transform(__int64 a1, __int64 enc, signed int a3)
{
  double v3; // ST10_8
  double *v4; // rax
  __int64 result; // rax
  signed int v6; // [rsp+1Ch] [rbp-44h]
  unsigned int i; // [rsp+38h] [rbp-28h]
  signed int v8; // [rsp+3Ch] [rbp-24h]
  double v9; // [rsp+40h] [rbp-20h]
  double v10; // [rsp+48h] [rbp-18h]

  v6 = a3;
  for ( i = 0; ; ++i )
  {
    result = i;
    if ( i >= v6 )
      break;
    v8 = 0;
    v10 = 0.0;
    v9 = 0.0;
    while ( v8 < (unsigned int)v6 )
    {
      v3 = (double)*(unsigned __int8 *)((unsigned int)v8 + a1);
      cexp();
      v9 = -0.0 * (double)(signed int)i * (double)v8 / (double)v6 * v3 + v9;
      v10 = v3 * ((double)(signed int)i * -6.283185307179586 * (double)v8++ / (double)v6) + v10;
    }
    v4 = (double *)(16LL * i + enc);
    *v4 = v9;
    v4[1] = v10;
  }
  return result;
}
```

解密脚本如下：

将enc中数据转化为复数表达形式：

```c
#include<stdio.h>
#include<stdlib.h>
#include<math.h>
double HexToDouble(const unsigned char* buf);

int main()
{
    long long int n[] = {0x40b2400000000000, 0x0000000000000000, 0x404f14f61c1d8082, 0xc04745c5d3974762, 0x405dbbb4eed9353f, 0xc0661492505818d6, 0x40637853935dff27, 0xc072ad04d63916c6, 0x40627964dd51b260, 0xc06ad4140051d3ca, 0xbfff086d44939f40, 0x404e81b48b3ed997, 0xc052349e55c8ee6c, 0xc041f7be0a48876a, 0x40569865a98b702a, 0xc0556a972993328d, 0xc05b35f7edc25b1f, 0x4050a5114652c156, 0x404086e0bd49eb72, 0x40703e0c8dd77064, 0xc05194822ef54a76, 0x4050f21dc0ae9df8, 0x404883219d48105b, 0xc071ce21c901b4d2, 0x40540064b3432d32, 0x40403b07b4d9833e, 0xc078a662057caa2e, 0xc06a3e337c31fc3f, 0xc0437ffffffffeb4, 0x404d800000000064, 0x406f521c1a036884, 0x406b1d468d5aa047, 0xc033bbc2d0f22fac, 0xc058e4382458343f, 0xc046713c446616d8, 0x4068bb2f9708025b, 0x405188584417c0f1, 0xc040b6b095bd67a8, 0xc06c47d2cb1f4146, 0xc06806b49d729f9c, 0x4050ecd192196d91, 0x3fd2812924060600, 0x406833cd2b3a48f8, 0x40608ab46b36670a, 0xc059b2169dff4b86, 0x40685d70be3f0920, 0xc064f6d60ace941c, 0x40515023314bbeab, 0xc072bec5d78045d3, 0x406385e730b6f7f6, 0x4057449810fd4454, 0x4047572706b145bf, 0xc071a235840cdc89, 0x400a2c344742e698, 0x4052e2a5f1fbdd8e, 0x40425611700b4c34, 0xc075a00000000000, 0xbd734621b3c001e4, 0x4052e2a5f1fbdc91, 0xc0425611700b4a36, 0xc071a235840cdca5, 0xc00a2c344742d9c0, 0x4057449810fd4589, 0xc047572706b14adb, 0xc072bec5d78045ca, 0xc06385e730b6f8fb, 0xc064f6d60ace9424, 0xc0515023314bbe12, 0xc059b2169dff4c45, 0xc0685d70be3f0856, 0x406833cd2b3a488a, 0xc0608ab46b36666d, 0x4050ecd192196be2, 0xbfd2812924056700, 0xc06c47d2cb1f41c0, 0x406806b49d729f86, 0x405188584417c1ca, 0x4040b6b095bd67d0, 0xc046713c446617a5, 0xc068bb2f97080274, 0xc033bbc2d0f230ed, 0x4058e4382458336d, 0x406f521c1a036a9e, 0xc06b1d468d5a9f0e, 0xc0437ffffffff87a, 0xc04d7ffffffffde4, 0xc078a662057caa54, 0x406a3e337c31fb61, 0x40540064b3432ed8, 0xc0403b07b4d9823e, 0x404883219d48130a, 0x4071ce21c901b534, 0xc05194822ef5490c, 0xc050f21dc0ae9df2, 0x404086e0bd49e9e7, 0xc0703e0c8dd770b0, 0xc05b35f7edc259b4, 0xc050a5114652c116, 0x40569865a98b7096, 0x40556a97299332c8, 0xc052349e55c8ef33, 0x4041f7be0a4886b8, 0xbfff086d4493c3c0, 0xc04e81b48b3ed8b6, 0x40627964dd51b301, 0x406ad4140051d2de, 0x40637853935dfed6, 0x4072ad04d63916fe, 0x405dbbb4eed93b0e, 0x40661492505819c1, 0x404f14f61c1d7ecc, 0x404745c5d3974575};
    // long long int n = 0x4050400000000000;
    // printf("%llf", *(double *)&n);
    for (int i = 0; i < 56; i++)
       printf("%llf+%llfj,", *(double *)&n[2 * i], *(double *)&n[2 * i + 1]);
    return 0;
}
```

快速傅里叶变化：

```
# -*- coding: UTF-8 -*-

import numpy as np
from ctypes import *

import struct
import binascii
ciphertext = open("data.txt","rb")
key = [4672.000000+0.000000j
,62.163761+-46.545100j
,118.932918+-176.642861j
,155.760202+-298.813681j
,147.793563+-214.627442j
,-1.939557+61.013322j
,-72.822164+-35.935487j
,90.381205+-85.665476j
,-108.843257+66.579179j
,33.053734+259.878065j
,-70.320446+67.783066j
,49.024463+-284.883248j
,80.006146+32.461173j
,-394.398931+-209.943785j
,-39.000000+59.000000j
,250.565930+216.914862j
,-19.733441+-99.565927j
,-44.884652+197.849559j
,70.130387+-33.427264j
,-226.244482+-192.209548j
,67.700291+0.289133j
,193.618795+132.334524j
,-102.782630+194.920013j
,-167.713628+69.252148j
,-299.923301+156.184471j
,93.071781+46.680878j
,-282.138065+3.271584j
,75.541378+36.672407j
,-346.000000+-0.000000j
,75.541378+-36.672407j
,-282.138065+-3.271584j
,93.071781+-46.680878j
,-299.923301+-156.184471j
,-167.713628+-69.252148j
,-102.782630+-194.920013j
,193.618795+-132.334524j
,67.700291+-0.289133j
,-226.244482+192.209548j
,70.130387+33.427264j
,-44.884652+-197.849559j
,-19.733441+99.565927j
,250.565930+-216.914862j
,-39.000000+-59.000000j
,-394.398931+209.943785j
,80.006146+-32.461173j
,49.024463+284.883248j
,-70.320446+-67.783066j
,33.053734+-259.878065j
,-108.843257+-66.579179j
,90.381205+85.665476j
,-72.822164+35.935487j
,-1.939557+-61.013322j
,147.793563+214.627442j
,155.760202+298.813681j
,118.932918+176.642861j
,62.163761+46.545100j]

a = np.fft.ifft(key)
#print a.real 取实部

s = [65.,117.0000001,114.00000002,111.00000002,114.00000001,97.00000009,122.99999989,100.00000012,49.00000005,52.99999998,99.00000002,113.99999994,51.00000007,55.00000007,51.00000007,95.00000007,101.99999993,48.00000004,117.,113.99999996,48.99999996,51.,113.99999996,94.99999997,55.00000002,113.99999996,51.99999998,109.99999992,53.00000007,101.99999992,48.00000004,114.00000002,109.00000003,94.99999991,48.99999997,53.00000002,95.00000002,113.99999998,50.99999998,52.,107.99999996,107.99999994,121.,95.00000011,48.99999999,110.00000008,55.,50.99999992,114.00000001,51.,53.,54.99999994,48.99999988,109.99999994,54.00000006,124.99999998]

s = map(int,map(round,s))#取整
print ''.join(map(chr,s))

```

flag：Aurora{d15cr373_f0ur13r_7r4n5f0rm_15_r34lly_1n73r3571n6}

## babypyobf

题目：[babypyobf](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/2019/Aurora/babypyobf/babypyobf.cpython-37.pyc)

打开来是个.pyc文件，用uncompyle6转成py文件。

```python
# uncompyle6 version 3.3.3
# Python bytecode 3.7 (3394)
# Decompiled from: Python 3.6.8 (default, Jan 14 2019, 11:02:34) 
# [GCC 8.0.1 20180414 (experimental) [trunk revision 259383]]
# Embedded file name: pyobf.py
# Size of source mod 2**32: 11907 bytes
𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉 = []
𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𢉌 = list
𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤنجم = map
𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𐠧 = ord
𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ犮 = print
𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𐼖 = input
𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤࢳ = len
𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𐮏 = True
𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݐ = 'the_flag_is_not_here'
𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𐬝 = [199, 191, 168, 72, 189, 125, 226, 235, 210, 126, 156, 247, 93, 137, 42, 138, 76, 23, 139, 151, 29, 39, 31, 136, 143, 129, 0, 242, 73, 19, 236, 61, 235, 70, 18, 27, 250, 135, 60, 112, 48]
𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݐ = 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𢉌(𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤنجم(𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𐠧, 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𢉌(𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݐ)))
𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ犮('Input:', end='')
𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤق = 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𐼖()
𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤق = 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𢉌(𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤنجم(𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𐠧, 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𢉌(𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤق)))
𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ绤 = 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤࢳ(𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤق)
𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 = -1479559293
𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𥞝 = 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤࢳ(𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݐ)
𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸 = 0
while 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𐮏:
    while 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𐮏:
        while 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𐮏:
            while 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𐮏:
                while 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𐮏:
                    while 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𐮏:
                        while 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𐮏:
                            while 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𐮏:
                                while 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 == -2118257528:
                                    𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ햣 = 1939365939
                                    if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤسم < 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ绤:
                                        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ햣 = 1644783123
                                    𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 = 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ햣

                                if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 != -1889341384:
                                    break
                                𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸] = 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸
                                𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 = -798623831

                            if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 != -1717721974:
                                break
                            𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸 = 0
                            𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݜ = 0
                            𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤسم = 0
                            𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 = -2118257528

                        if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 != -1479559293:
                            break
                        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦘢 = 1698955189
                        if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸 < 256:
                            𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦘢 = 1974196793
                        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 = 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦘢

                    if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 != -1443714681:
                        break
                    𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݜ = 0
                    𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸 = 0
                    𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 = 2110390670

                if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 != -1288645142:
                    break
                𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𘔌 = 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݐ[(𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸 % 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𥞝)] + 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸] + 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݜ
                𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݜ = 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𘔌 & (𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𘔌 ^ 3840)
                𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸] = (𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݜ] & 235 | ~𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݜ] & 20) ^ (𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸] & 235 | ~𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸] & 20)
                𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݜ] = ~𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸] & 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݜ] | ~𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݜ] & 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸]
                𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸] = (𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݜ] & 212 | ~𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݜ] & 43) ^ (𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸] & 212 | ~𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸] & 43)
                𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 = -367188513

            if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 != -798623831:
                break
            𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸 += 1
            𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 = 1405424855

        if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 != -606860395:
            break
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤسم += 1
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 = -2118257528

    if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 == -605967056:
        break
    if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 == -586318370:
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 = -605967056
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ犮('Bad')
    if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 == -367188513:
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸 += 1
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 = 2110390670
    if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 == 833744747:
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸 += 1
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 = -1479559293
    if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 == 1405424855:
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𬸟 = -1443714681
        if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸 < 256:
            𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𬸟 = -1889341384
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 = 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𬸟
    if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 == 1513289402:
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 = -605967056
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ犮('Good!')
    if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 == 1644783123:
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸 = 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸 + 1 & 255
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݜ = 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸] + 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݜ & 255
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸] = ~𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݜ] & 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸] | 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݜ] & ~𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸]
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݜ] = (𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸] & 212 | ~𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸] & 43) ^ (𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݜ] & 212 | ~𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݜ] & 43)
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸] = ~𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݜ] & 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸] | 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݜ] & ~𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸]
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤق[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤسم] ^= 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[(𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤݜ] + 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉[𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸] & 255)]
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 = -606860395
    if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 == 1698955189:
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸 = 0
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 = 1405424855
    if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 == 1939365939:
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤٿ = 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤق == 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𐬝
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𮏷 = 1513289402
        if not 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤٿ:
            𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𮏷 = -586318370
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 = 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𮏷
    if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 == 1974196793:
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𡣉.append(0)
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 = 833744747
    if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 == 2110390670:
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤبخ = -1717721974
        if 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ뉸 < 256:
            𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤبخ = -1288645142
        𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤ𦗴 = 𤐕يم絎𩸨ۅ𧁆תּط푊𬵝نحممحج荜ࡤبخ

```

手工去混淆：

```python
o = []
s = 'the_flag_is_not_here'
b = [199, 191, 168, 72, 189, 125, 226, 235, 210, 126, 156, 247, 93, 137, 42, 138, 76, 23, 139, 151, 29, 39, 31, 136, 143, 129, 0, 242, 73, 19, 236, 61, 235, 70, 18, 27, 250, 135, 60, 112, 48]
s = list(map(ord, list(s)))
a = "123456789"
a = list(map(ord, list(a)))
length_flag = len(a)
d = -1479559293
f = len(s)
g = 0
while True:
    while True:
        while True:
            while True:
                while True:
                    while True:
                        while True:
                            while True:
                                while d == -2118257528:
                                    h = 1939365939
                                    if i < length_flag:
                                        h = 1644783123
                                    d = h

                                if d != -1889341384:
                                    break
                                o[g] = g 
                                d = -798623831

                            if d != -1717721974:
                                break
                            g = 0
                            p = 0
                            i = 0
                            d = -2118257528

                        if d != -1479559293:
                            break
                        q = 1698955189
                        if g < 256:
                            q = 1974196793
                        d = q

                    if d != -1443714681:
                        break
                    p = 0
                    g = 0
                    d = 2110390670

                if d != -1288645142:
                    break
                r = s[(g % f)] + o[g] + p
                p = r & (r ^ 3840)
                o[g] = (o[p] & 235 | ~o[p] & 20) ^ (o[g] & 235 | ~o[g] & 20)
                o[p] = ~o[g] & o[p] | ~o[p] & o[g]
                o[g] = (o[p] & 212 | ~o[p] & 43) ^ (o[g] & 212 | ~o[g] & 43)
                d = -367188513

            if d != -798623831:
                break
            g += 1
            d = 1405424855

        if d != -606860395:
            break
        i += 1
        d = -2118257528

    if d == -605967056:
        break
    if d == -586318370:
        d = -605967056
        print('Bad')
    if d == -367188513:
        g += 1
        d = 2110390670
    if d == 833744747:
        g += 1
        d = -1479559293
    if d == 1405424855:
        s = -1443714681
        if g < 256:
            s = -1889341384
        d = s
    if d == 1513289402:
        d = -605967056
        print('Good!')
    if d == 1644783123:
        g = g + 1 & 255
        p = o[g] + p & 255
        o[g] = ~o[p] & o[g] | o[p] & ~o[g]
        o[p] = (o[g] & 212 | ~o[g] & 43) ^ (o[p] & 212 | ~o[p] & 43)
        o[g] = ~o[p] & o[g] | o[p] & ~o[g]
        a[i] ^= o[(o[p] + o[g] & 255)]
        d = -606860395
    if d == 1698955189:
        g = 0
        d = 1405424855
    if d == 1939365939:
        t = a == b
        u = 1513289402
        if not t:
            u = -586318370
        d = u
    if d == 1974196793:
        o.append(0)
        d = 833744747
    if d == 2110390670:
        w = -1717721974
        if g < 256:
            w = -1288645142
        d = w

```

RC4加密，s_box可以直接导出，最终解密脚本如下：

```python
b = [199, 191, 168, 72, 189, 125, 226, 235, 210, 126, 156, 247, 93, 137, 42, 138, 76, 23, 139, 151, 29, 39, 31, 136, 143, 129, 0, 242, 73, 19, 236, 61, 235, 70, 18, 27, 250, 135, 60, 112, 48]

flag = ""

output = [0 for  i in xrange(len(b))]
s_box = [134,202,218,39,207,28,153,156,225,18,255,199,48,186,117,189,124,72,188,255,46,120,104,184,253,237,100,173,121,117,179,94,219,40,116,110,207,182,12,30,77]

for i in xrange(len(b)):
	output[i] = s_box[i] ^ b[i]
print "".join(map(chr,output))

```

flag：Aurora{w3lc0m3_70_7h3_w0rld_0f_c0nfu510n}

## cryyyyyyypto

题目：[cryyyyyyypto](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/2019/Aurora/cryyyyyyypto/cryyyyyypto.exe)

根据字符串找到主要函数，带了6个加密算法........

```python
int __cdecl main(int argc, const char **argv, const char **envp)
{
  char *v3; // rdi
  signed __int64 i; // rcx
  char v6; // [rsp+0h] [rbp-30h]
  char Str; // [rsp+40h] [rbp+10h]
  __int64 *Dst; // [rsp+C8h] [rbp+98h]
  void *Memory; // [rsp+E8h] [rbp+B8h]
  unsigned __int64 j; // [rsp+108h] [rbp+D8h]

  v3 = &v6;
  for ( i = 122i64; i; --i )
  {
    *(_DWORD *)v3 = -858993460;
    v3 += 4;
  }
  un_use((__int64)&unk_7FF6D92E4002);
  memset(&Str, 0, 0x64ui64);
  Dst = (__int64 *)malloc(0x100ui64);
  Memory = malloc(0x100ui64);
  printf("SEIN CHECK:");
  scanf("%s", &Str);
  LODWORD(Size) = j_strlen(&Str);
  j_memset(Dst, 0, 0x100ui64);
  j_memset(Memory, 0, 0x100ui64);
  j_memcpy(Dst, &Str, (unsigned int)Size);
  j_memcpy(Memory, aABoringCryptoM, 32ui64);
  RC5((__int64)Dst, (__int64)Memory);
  j_RC4((__int64)Dst, (__int64)Memory);
  AES((__int64)Dst, (__int64)Memory);
  RubyDES((__int64)Dst, (__int64)Memory);
  TEA((__int64)Dst, (__int64)Memory);
  blowfish((__int64)Dst, (__int64)Memory);
  if ( !j_memcmp(Dst, cmp_str, (unsigned int)Size) )//判断函数
  {
    printf("GOOD!\n");
  }
  else
  {
    CreateThread(0i64, 0i64, StartAddress, 0i64, 0, 0i64);
    WinExec(CmdLine, 0);
    for ( j = 0i64; j < 0xBB8; ++j )
    {
      printf("NOPE!!! YOU HAVE BEEN HACKED BY DECADE!!!\n");
      Sleep(0x14u);
    }
  }
  free(Dst);
  free(Memory);
  system("pause");
  sub_7FF6D8931447((__int64)&v6, (__int64)&unk_7FF6D893F8F0);
  return 0;
}
```

### RC5

一开始没认出来，人工逆向破解0.0...

### RC4

```c
void __fastcall RC4(unsigned __int8 *plaing, __int64 a2)
{
  __int64 *v2; // rdi
  signed __int64 i; // rcx
  int v4; // edx
  __int64 v5; // [rsp+0h] [rbp-20h]
  _BYTE *cipher; // [rsp+28h] [rbp+8h]
  unsigned int v7; // [rsp+44h] [rbp+24h]
  unsigned int v8; // [rsp+64h] [rbp+44h]
  unsigned int j; // [rsp+84h] [rbp+64h]
  int v10; // [rsp+154h] [rbp+134h]
  unsigned __int8 *flag; // [rsp+180h] [rbp+160h]
  __int64 key; // [rsp+188h] [rbp+168h]

  key = a2;
  flag = plaing;
  v2 = &v5;
  for ( i = 90i64; i; --i )
  {
    *(_DWORD *)v2 = -858993460;
    v2 = (__int64 *)((char *)v2 + 4);
  }
  un_use((__int64)&unk_7FF6D92E4002);
  init_Sbox(key);
  cipher = malloc((unsigned int)Size);
  j_memset(cipher, 0, (unsigned int)Size);
  v7 = 0;
  v8 = 0;
  for ( j = 0; j < (unsigned int)Size; ++j )
  {
    v7 = (v7 + 1) % 256;
    v8 = (S[v7] + v8) % 256;
    S[v7] ^= S[v8];
    S[v8] ^= S[v7];
    S[v7] ^= S[v8];
    v10 = flag[j];
    v4 = (S[v8] + S[v7]) >> 31;
    cipher[j] = S[(unsigned __int8)(v4 + S[v8] + S[v7]) - (unsigned __int8)v4] ^ v10;
  }
  j_memcpy(flag, cipher, (unsigned int)Size);
  free(cipher);
}
```

特征是init_Sbox()里:

```c
size_t __fastcall sub_7FF6D89332B0(char *a1)
{
  __int64 *v1; // rdi
  signed __int64 i; // rcx
  size_t result; // rax
  int v4; // edx
  __int64 v5; // [rsp+0h] [rbp-20h]
  int j; // [rsp+24h] [rbp+4h]
  int v7; // [rsp+44h] [rbp+24h]
  char v8; // [rsp+64h] [rbp+44h]
  unsigned int v9; // [rsp+84h] [rbp+64h]
  int v10; // [rsp+154h] [rbp+134h]
  const char *Str; // [rsp+180h] [rbp+160h]

  Str = a1;
  v1 = &v5;
  for ( i = 90i64; i; --i )
  {
    *(_DWORD *)v1 = 0xCCCCCCCC;
    v1 = (__int64 *)((char *)v1 + 4);
  }
  un_use((__int64)&unk_7FF6D92E4002);
  j = 0;
  v7 = 0;
  v8 = 0;
  result = j_strlen(Str);
  v9 = result;
  for ( j = 0; j < 256; ++j )
  {
    S[j] = j;//赋值位数
    result = (unsigned int)(j + 1);
  }
  for ( j = 0; j < 256; ++j )//密钥扩展
  {
    v10 = S[j] + v7;
    v4 = ((unsigned __int8)Str[j % v9] + v10) >> 31;
    v7 = (unsigned __int8)(v4 + Str[j % v9] + v10) - (unsigned __int8)v4;
    S[j] ^= S[v7];
    S[v7] ^= S[j];
    S[j] ^= S[v7];
    result = (unsigned int)(j + 1);
  }
  return result;
}
```

### AES

```c
__int64 __fastcall sub_7FF6D8931970(__int64 *a1, __int64 a2)
{
  __int64 *v2; // rdi
  signed __int64 i; // rcx
  __int64 result; // rax
  __int64 v5; // [rsp+0h] [rbp-30h]
  unsigned int j; // [rsp+34h] [rbp+4h]
  __int64 *input; // [rsp+130h] [rbp+100h]
  __int64 key; // [rsp+138h] [rbp+108h]

  key = a2;
  input = a1;
  v2 = &v5;
  for ( i = 70i64; i; --i )
  {
    *(_DWORD *)v2 = -858993460;
    v2 = (__int64 *)((char *)v2 + 4);
  }
  un_use((__int64)&unk_7FF6D92E4002);
  pad((__int64)input, (unsigned int)Size, 16i64);
  for ( j = 0; ; ++j )
  {
    result = (unsigned int)Size / 16;
    if ( j >= (unsigned int)result )
      break;
    AES(key, 16u, &input[2 * j], &input[2 * j], 16u);
  }
  return result;
}
```

进去AES：

```c
__int64 __fastcall sub_7FF6D8934210(void *key, unsigned int num_16_1, __int64 *a3, __int64 *a4, unsigned int num_16)
{
  char *v5; // rdi
  signed __int64 i; // rcx
  char v8; // [rsp+0h] [rbp-20h]
  char v9; // [rsp+30h] [rbp+10h]
  __int64 *v10; // [rsp+1B8h] [rbp+198h]
  char *v11; // [rsp+1D8h] [rbp+1B8h]
  char v12; // [rsp+1F8h] [rbp+1D8h]
  char Dst; // [rsp+228h] [rbp+208h]
  char v14; // [rsp+258h] [rbp+238h]
  unsigned int j; // [rsp+284h] [rbp+264h]
  int k; // [rsp+2A4h] [rbp+284h]
  const void *Src; // [rsp+460h] [rbp+440h]
  unsigned int Size; // [rsp+468h] [rbp+448h]
  __int64 *input1; // [rsp+470h] [rbp+450h]
  __int64 *input2; // [rsp+478h] [rbp+458h]

  input2 = a4;
  input1 = a3;
  Size = num_16_1;
  Src = key;
  v5 = &v8;
  for ( i = 274i64; i; --i )
  {
    *(_DWORD *)v5 = -858993460;
    v5 += 4;
  }
  un_use((__int64)&unk_7FF6D92E4002);
  v10 = input2;
  v11 = &v9;
  memset(&v12, 0, 0x10ui64);
  memset(&Dst, 0, 0x10ui64);
  memset(&v14, 0, 0x10ui64);
  j_memcpy(&Dst, Src, Size);
  sub_7FF6D8931244((__int64)&Dst, 16i64, (__int64)&v9);//初始置换
  for ( j = 0; j < num_16; j += 16 )
  {
    sub_7FF6D893129E((__int64)&v14, (__int64)input1);
    sub_7FF6D8931037((__int64)&v14, (__int64)v11);
    for ( k = 1; k < 10; ++k )
    {
      v11 += 16;
      sub_7FF6D8931456((__int64)&v14);
      sub_7FF6D89313B6((__int64)&v14);
      sub_7FF6D8931438(&v14);
      sub_7FF6D8931037((__int64)&v14, (__int64)v11);
    }
    sub_7FF6D8931456((__int64)&v14);
    sub_7FF6D89313B6((__int64)&v14);
    sub_7FF6D8931037((__int64)&v14, (__int64)(v11 + 16));
    sub_7FF6D8931460((__int64)&v14, (__int64)v10);
    v10 += 2;
    input1 += 2;
    v11 = &v9;
  }
  sub_7FF6D8931447((__int64)&v8, (__int64)&unk_7FF6D893EEC8);
  return 0i64;
}
```

初始置换：

```c
__int64 __fastcall sub_7FF6D89349E0(__int64 a1, __int64 a2, _DWORD *a3)
{
  __int64 *v3; // rdi
  signed __int64 i; // rcx
  __int64 v6; // [rsp+0h] [rbp-20h]
  _DWORD *v7; // [rsp+28h] [rbp+8h]
  _DWORD *v8; // [rsp+48h] [rbp+28h]
  int j; // [rsp+64h] [rbp+44h]
  int k; // [rsp+84h] [rbp+64h]
  int l; // [rsp+A4h] [rbp+84h]
  int m; // [rsp+C4h] [rbp+A4h]
  __int64 v13; // [rsp+1C0h] [rbp+1A0h]
  _DWORD *v14; // [rsp+1D0h] [rbp+1B0h]

  v14 = a3;
  v13 = a1;
  v3 = &v6;
  for ( i = 106i64; i; --i )
  {
    *(_DWORD *)v3 = -858993460;
    v3 = (__int64 *)((char *)v3 + 4);
  }
  un_use((__int64)&unk_7FF6D92E4002);
  v7 = v14;
  v8 = v14 + 44;
  for ( j = 0; j < 4; ++j )
    v7[j] = _byteswap_ulong(*(_DWORD *)(4 * j + v13));
  for ( k = 0; k < 10; ++k )
  {
    v7[4] = dword_7FF6D893EBB0[k] ^ S_box[(unsigned __int16)(v7[3] >> 16) >> 16] ^ (S_box[v7[3] & 0xFF] << 8) & 0xFF00 ^ (S_box[(v7[3] >> 8) & 0xFF] << 16) & 0xFF0000 ^ (S_box[(v7[3] >> 16) & 0xFF] << 24) & 0xFF000000 ^ *v7;
    v7[5] = v7[4] ^ v7[1];
    v7[6] = v7[5] ^ v7[2];
    v7[7] = v7[6] ^ v7[3];
    v7 += 4;
  }
  v7 = v14 + 40;
  for ( l = 0; l < 11; ++l )
  {
    for ( m = 0; m < 4; ++m )
      v8[m] = v7[m];
    v7 -= 4;
    v8 += 4;
  }
  return 0i64;
}
```

特征：AES的S_box：

```assembly
.data:00007FF6D8942000 S_box           db 63h, 7Ch, 77h, 7Bh, 0F2h, 6Bh, 6Fh, 0C5h, 30h, 1, 67h
.data:00007FF6D8942000                                         ; DATA XREF: sub_7FF6D89349E0+180↑o
.data:00007FF6D8942000                                         ; sub_7FF6D89349E0+1B0↑o ...
.data:00007FF6D8942000                 db 2Bh, 0FEh, 0D7h, 0ABh, 76h, 0CAh, 82h, 0C9h, 7Dh, 0FAh
.data:00007FF6D8942000                 db 59h, 47h, 0F0h, 0ADh, 0D4h, 0A2h, 0AFh, 9Ch, 0A4h, 72h
.data:00007FF6D8942000                 db 0C0h, 0B7h, 0FDh, 93h, 26h, 36h, 3Fh, 0F7h, 0CCh, 34h
.data:00007FF6D8942000                 db 0A5h, 0E5h, 0F1h, 71h, 0D8h, 31h, 15h, 4, 0C7h, 23h
.data:00007FF6D8942000                 db 0C3h, 18h, 96h, 5, 9Ah, 7, 12h, 80h, 0E2h, 0EBh, 27h
.data:00007FF6D8942000                 db 0B2h, 75h, 9, 83h, 2Ch, 1Ah, 1Bh, 6Eh, 5Ah, 0A0h, 52h
.data:00007FF6D8942000                 db 3Bh, 0D6h, 0B3h, 29h, 0E3h, 2Fh, 84h, 53h, 0D1h, 0
.data:00007FF6D8942000                 db 0EDh, 20h, 0FCh, 0B1h, 5Bh, 6Ah, 0CBh, 0BEh, 39h, 4Ah
.data:00007FF6D8942000                 db 4Ch, 58h, 0CFh, 0D0h, 0EFh, 0AAh, 0FBh, 43h, 4Dh, 33h
.data:00007FF6D8942000                 db 85h, 45h, 0F9h, 2, 7Fh, 50h, 3Ch, 9Fh, 0A8h, 51h, 0A3h
.data:00007FF6D8942000                 db 40h, 8Fh, 92h, 9Dh, 38h, 0F5h, 0BCh, 0B6h, 0DAh, 21h
.data:00007FF6D8942000                 db 10h, 0FFh, 0F3h, 0D2h, 0CDh, 0Ch, 13h, 0ECh, 5Fh, 97h
.data:00007FF6D8942000                 db 44h, 17h, 0C4h, 0A7h, 7Eh, 3Dh, 64h, 5Dh, 19h, 73h
.data:00007FF6D8942000                 db 60h, 81h, 4Fh, 0DCh, 22h, 2Ah, 90h, 88h, 46h, 0EEh
.data:00007FF6D8942000                 db 0B8h, 14h, 0DEh, 5Eh, 0Bh, 0DBh, 0E0h, 32h, 3Ah, 0Ah
.data:00007FF6D8942000                 db 49h, 6, 24h, 5Ch, 0C2h, 0D3h, 0ACh, 62h, 91h, 95h, 0E4h
.data:00007FF6D8942000                 db 79h, 0E7h, 0C8h, 37h, 6Dh, 8Dh, 0D5h, 4Eh, 0A9h, 6Ch
.data:00007FF6D8942000                 db 56h, 0F4h, 0EAh, 65h, 7Ah, 0AEh, 8, 0BAh, 78h, 25h
.data:00007FF6D8942000                 db 2Eh, 1Ch, 0A6h, 0B4h, 0C6h, 0E8h, 0DDh, 74h, 1Fh, 4Bh
.data:00007FF6D8942000                 db 0BDh, 8Bh, 8Ah, 70h, 3Eh, 0B5h, 66h, 48h, 3, 0F6h, 0Eh
.data:00007FF6D8942000                 db 61h, 35h, 57h, 0B9h, 86h, 0C1h, 1Dh, 9Eh, 0E1h, 0F8h
.data:00007FF6D8942000                 db 98h, 11h, 69h, 0D9h, 8Eh, 94h, 9Bh, 1Eh, 87h, 0E9h
.data:00007FF6D8942000                 db 0CEh, 55h, 28h, 0DFh, 8Ch, 0A1h, 89h, 0Dh, 0BFh, 0E6h
.data:00007FF6D8942000                 db 42h, 68h, 41h, 99h, 2Dh, 0Fh, 0B0h, 54h, 0BBh, 16h
```

### DES

```c
__int64 __fastcall sub_7FF6D89324E0(__int64 a1, __int64 a2)
{
  char *v2; // rdi
  signed __int64 i; // rcx
  char v5; // [rsp+0h] [rbp-20h]
  unsigned int j; // [rsp+24h] [rbp+4h]
  char Dst; // [rsp+48h] [rbp+28h]
  __int64 *input; // [rsp+140h] [rbp+120h]
  __int64 key; // [rsp+148h] [rbp+128h]

  key = a2;
  input = (__int64 *)a1;
  v2 = &v5;
  for ( i = 74i64; i; --i )
  {
    *(_DWORD *)v2 = -858993460;
    v2 += 4;
  }
  un_use((__int64)&unk_7FF6D92E4002);
  memset(&Dst, 0, 8ui64);
  pad((__int64)input, (unsigned int)Size, 8i64);
  for ( j = 0; j < (unsigned int)Size / 8; ++j )
  {
    j_memset(&Dst, 0, 8ui64);
    DES((__int64)&input[j], key, (__int64)&Dst);
    j_memcpy(&input[j], &Dst, 8ui64);
  }
  return sub_7FF6D8931447((__int64)&v5, (__int64)&unk_7FF6D893F6B0);
}
```

进入DES：

```
__int64 __fastcall DES(__int64 input_0, __int64 key_0, __int64 src)
{
  char *v3; // rdi
  signed __int64 i; // rcx
  char v6; // [rsp+0h] [rbp-20h]
  int v7[72]; // [rsp+30h] [rbp+10h]
  int v8[72]; // [rsp+150h] [rbp+130h]
  char v9[3104]; // [rsp+270h] [rbp+250h]
  char v10; // [rsp+E90h] [rbp+E70h]
  char v11; // [rsp+FB0h] [rbp+F90h]
  char v12[2208]; // [rsp+10D0h] [rbp+10B0h]
  char v13[2208]; // [rsp+1970h] [rbp+1950h]
  int v14[69]; // [rsp+2210h] [rbp+21F0h]
  int j; // [rsp+2324h] [rbp+2304h]
  int k; // [rsp+2344h] [rbp+2324h]
  int l; // [rsp+2364h] [rbp+2344h]
  int m; // [rsp+2384h] [rbp+2364h]
  int n; // [rsp+23A4h] [rbp+2384h]
  int ii; // [rsp+23C4h] [rbp+23A4h]
  char *v21; // [rsp+29D8h] [rbp+29B8h]
  __int64 input; // [rsp+2A10h] [rbp+29F0h]
  __int64 key; // [rsp+2A18h] [rbp+29F8h]
  __int64 v24; // [rsp+2A20h] [rbp+2A00h]

  v24 = src;
  key = key_0;
  input = input_0;
  v3 = &v6;
  for ( i = 2686i64; i; --i )
  {
    *(_DWORD *)v3 = -858993460;
    v3 += 4;
  }
  un_use((__int64)&unk_7FF6D92E4002);
  memset(v7, 0, 0x100ui64);
  memset(v8, 0, 0x100ui64);
  memset(&v10, 0, 0x100ui64);
  memset(v14, 0, 0x100ui64);
  sub_7FF6D8931087(input, (__int64)&v10, 8i64);
  sub_7FF6D8931096((__int64)&v10, (__int64)v7, (__int64)DES_Sbox);
  sub_7FF6D8931087(key, (__int64)&v11, 8i64);
  sub_7FF6D893140B((__int64)&v11, (__int64)v9);
  for ( j = 0; j < 32; ++j )
  {
    *(_DWORD *)&v12[4 * j] = v7[j];
    *(_DWORD *)&v13[4 * j] = v7[j + 32];
  }
  for ( k = 1; k < 16; ++k )
  {
    for ( l = 0; l < 32; ++l )
      *(_DWORD *)&v12[128 * (signed __int64)k + 4 * l] = *(_DWORD *)&v13[128 * (signed __int64)(k - 1) + 4 * l];
    v21 = &v13[128 * (signed __int64)(k - 1)];
    sub_7FF6D89311D1(
      (__int64)&v13[128 * (signed __int64)(k - 1)],
      (__int64)&v13[128 * (signed __int64)k],
      (__int64)&v9[192 * (k - 1)]);
    sub_7FF6D893107D(&v13[128 * (signed __int64)k], &v12[128 * (signed __int64)(k - 1)], 32i64);
  }
  for ( m = 0; m < 32; ++m )
    *(_DWORD *)&v13[4 * m + 2048] = *(_DWORD *)&v13[4 * m + 1920];
  v21 = &v13[1920];
  sub_7FF6D89311D1((__int64)&v13[1920], (__int64)&v12[2048], (__int64)&v9[2880]);
  sub_7FF6D893107D(&v12[2048], &v12[1920], 32i64);
  for ( m = 0; m < 32; ++m )
  {
    v8[m] = *(_DWORD *)&v12[4 * m + 2048];
    v8[m + 32] = *(_DWORD *)&v13[4 * m + 2048];
  }
  sub_7FF6D89313C0(v8, v14, &unk_7FF6D8942340);
  for ( n = 0; n < 8; ++n )
  {
    for ( ii = 0; ii < 8; ++ii )
    {
      v21 = (char *)(ii + 8 * n);
      *(_BYTE *)(v24 + n) |= LOBYTE(v14[(_QWORD)v21]) << (7 - ii);
    }
  }
  return sub_7FF6D8931447((__int64)&v6, (__int64)&unk_7FF6D893F5D0);
}
```

DES置换规则：

```assembly
.data:00007FF6D8942100 DES_Sbox        dd 58, 50, 42, 34, 26, 18, 10, 2, 60, 52, 44, 36, 28, 20
.data:00007FF6D8942100                                         ; DATA XREF: DES+B9↑o
.data:00007FF6D8942100                 dd 12, 4, 62, 54, 46, 38, 30, 22, 14, 6, 64, 56, 48, 40
.data:00007FF6D8942100                 dd 32, 24, 16, 8, 57, 49, 41, 33, 25, 17, 9, 1, 59, 51
.data:00007FF6D8942100                 dd 43, 35, 27, 19, 11, 3, 61, 53, 45, 37, 29, 21, 13, 5
.data:00007FF6D8942100                 dd 63, 55, 47, 39, 31, 23, 15, 7
```

### TEA

```c
__int64 __fastcall sub_7FF6D8934070(__int64 input_0, __int64 key_1)
{
  __int64 *v2; // rdi
  signed __int64 i; // rcx
  __int64 result; // rax
  __int64 v5; // [rsp+0h] [rbp-20h]
  unsigned int j; // [rsp+24h] [rbp+4h]
  __int64 *input; // [rsp+120h] [rbp+100h]
  __int64 key; // [rsp+128h] [rbp+108h]

  key = key_1;
  input = (__int64 *)input_0;
  v2 = &v5;
  for ( i = 66i64; i; --i )
  {
    *(_DWORD *)v2 = -858993460;
    v2 = (__int64 *)((char *)v2 + 4);
  }
  un_use((__int64)&unk_7FF6D92E4002);
  pad((__int64)input, (unsigned int)Size, 8i64);
  for ( j = 0; ; ++j )
  {
    result = (unsigned int)Size / 8;
    if ( j >= (unsigned int)result )
      break;
    TEA_0((__int64)&input[j], key);
  }
  return result;
}
```

进入TEA_0:

```c
signed __int64 __fastcall sub_7FF6D8934650(int *input_0, int *key_0)
{
  __int64 *v2; // rdi
  signed __int64 i; // rcx
  double v4; // xmm0_8
  signed __int64 result; // rax
  __int64 v6; // [rsp+0h] [rbp-20h]
  unsigned int v7; // [rsp+24h] [rbp+4h]
  unsigned int v8; // [rsp+44h] [rbp+24h]
  int sum; // [rsp+64h] [rbp+44h]
  unsigned int j; // [rsp+84h] [rbp+64h]
  int delta; // [rsp+A4h] [rbp+84h]
  double v12; // [rsp+C8h] [rbp+A8h]
  int k_0; // [rsp+E4h] [rbp+C4h]
  int k_1; // [rsp+104h] [rbp+E4h]
  int k_2; // [rsp+124h] [rbp+104h]
  int k_3; // [rsp+144h] [rbp+124h]
  int *input; // [rsp+240h] [rbp+220h]
  int *key; // [rsp+248h] [rbp+228h]

  key = key_0;
  input = input_0;
  v2 = &v6;
  for ( i = 138i64; i; --i )
  {
    *(_DWORD *)v2 = -858993460;
    v2 = (__int64 *)((char *)v2 + 4);
  }
  un_use((__int64)&unk_7FF6D92E4002);
  v7 = *input;
  v8 = input[1];
  sum = 0;
  v4 = j_sqrt(5.0);
  v12 = floor((v4 - 1.0) * 2147483648.0);       // 特征1：2654435769
  delta = (signed int)v12;
  k_0 = *key;
  k_1 = key[1];
  k_2 = key[2];
  k_3 = key[3];
  for ( j = 0; j < 32; ++j ) //特征2
  {
    sum += delta;
    v7 += (k_1 + (v8 >> 5)) ^ (sum + v8) ^ (k_0 + 16 * v8);
    v8 += (k_3 + (v7 >> 5)) ^ (sum + v7) ^ (k_2 + 16 * v7);
  }
  *input = v7;
  result = 4i64;
  input[1] = v8;
  return result;
}
```

### blowfish

```c
__int64 __fastcall blowfish_0(unsigned int *input_0, __int64 key_1)
{
  char *v2; // rdi
  signed __int64 i; // rcx
  unsigned int v4; // eax
  unsigned int v5; // eax
  char v7; // [rsp+0h] [rbp-20h]
  int j; // [rsp+24h] [rbp+4h]
  int v9; // [rsp+44h] [rbp+24h]
  unsigned int *v10; // [rsp+68h] [rbp+48h]
  unsigned int Long; // [rsp+84h] [rbp+64h]
  unsigned int v12; // [rsp+A4h] [rbp+84h]
  unsigned int *input; // [rsp+1A0h] [rbp+180h]
  __int64 key; // [rsp+1A8h] [rbp+188h]

  key = key_1;
  input = input_0;
  v2 = &v7;
  for ( i = 98i64; i; --i )
  {
    *(_DWORD *)v2 = -858993460;
    v2 += 4;
  }
  un_use((__int64)&unk_7FF6D92E4002);
  v9 = (unsigned int)Size / 8;
  v10 = input;
  sub_7FF6D893132A(key, (unsigned int)Size % 8ui64);//密钥扩展
  for ( j = 0; j < v9; ++j )
  {
    Long = j__byteswap_ulong(*v10);
    v12 = j__byteswap_ulong(v10[1]);
    sub_7FF6D893145B((__int64)&Long, (__int64)&v12);
    v4 = j__byteswap_ulong(Long);
    *v10 = v4;
    v5 = j__byteswap_ulong(v12);
    v10[1] = v5;
    v10 += 2;
  }
  return sub_7FF6D8931447((__int64)&v7, (__int64)&unk_7FF6D893F860);
}
```

```python
__int64 __fastcall sub_7FF6D8932710(char *a1)
{
  char *v1; // rdi
  signed __int64 i; // rcx
  unsigned int v3; // eax
  char v5; // [rsp+0h] [rbp-20h]
  int j; // [rsp+24h] [rbp+4h]
  int k; // [rsp+44h] [rbp+24h]
  int v8; // [rsp+64h] [rbp+44h]
  void *Dst; // [rsp+88h] [rbp+68h]
  int v10; // [rsp+A4h] [rbp+84h]
  int v11; // [rsp+C4h] [rbp+A4h]
  __int64 v12; // [rsp+198h] [rbp+178h]
  const char *Str; // [rsp+1D0h] [rbp+1B0h]

  Str = a1;
  v1 = &v5;
  for ( i = 110i64; i; --i )
  {
    *(_DWORD *)v1 = -858993460;
    v1 += 4;
  }
  un_use((__int64)&unk_7FF6D92E4002);
  v8 = j_strlen(Str);
  Dst = 0i64;
  v10 = 0;
  v11 = 0;
  Dst = malloc(v8);
  j_memset(Dst, 0, v8);
  v8 /= 4ui64;
  for ( j = 0; j < v8; ++j )
  {
    v3 = j__byteswap_ulong(*(_DWORD *)&Str[4 * j]);
    *((_DWORD *)Dst + j) = v3;
  }
  for ( j = 0; j < 18; ++j )
  {
    v12 = j;
    dword_7FF6D8942DE0[j] ^= *((_DWORD *)Dst + j % v8);
  }
  v11 = 0;
  v10 = 0;
  for ( j = 0; j < 18; j += 2 )
  {
    sub_7FF6D893145B((__int64)&v10, (__int64)&v11);
    dword_7FF6D8942DE0[j] = v10;
    dword_7FF6D8942DE0[j + 1] = v11;
  }
  for ( j = 0; j < 4; ++j )
  {
    for ( k = 0; k < 256; k += 2 )
    {
      sub_7FF6D893145B((__int64)&v10, (__int64)&v11);
      dword_7FF6D8942E50[256 * (signed __int64)j + k] = v10;
      dword_7FF6D8942E50[256 * (signed __int64)j + k + 1] = v11;
    }
  }
  free(Dst);
  return sub_7FF6D8931447((__int64)&v5, (__int64)&unk_7FF6D893F780);
}
```

特征：

```assembly
.data:00007FF6D8942DE0 dword_7FF6D8942DE0 dd 243F6A88h, 85A308D3h, 13198A2Eh, 3707344h, 0A4093822h
.data:00007FF6D8942DE0                                         ; DATA XREF: sub_7FF6D8931A70+5B↑o
.data:00007FF6D8942DE0                                         ; sub_7FF6D8931A70+A6↑o ...
.data:00007FF6D8942DE0                 dd 299F31D0h, 82EFA98h, 0EC4E6C89h, 452821E6h, 38D01377h
.data:00007FF6D8942DE0                 dd 0BE5466CFh, 34E90C6Ch, 0C0AC29B7h, 0C97C50DDh, 3F84D5B5h
.data:00007FF6D8942DE0                 dd 0B5470917h, 9216D5D9h, 8979FB1Bh
```

### TEA解密

```c
#include<stdio.h>
#include<math.h>
#include <stdint.h>

void decrypt(uint32_t *v, uint32_t *k)
{
    uint32_t v0 = v[0], v1 = v[1], sum = 0xC6EF3720, i;  /* set up */
    uint32_t delta = 0x9e3779b9;                          /* a key schedule constant */
    uint32_t k0 = k[0], k1 = k[1], k2 = k[2], k3 = k[3];   /* cache key */
    for (i = 0; i < 32; i++)
    { /* basic cycle start */
        v1 -= ((v0 << 4) + k2) ^ (v0 + sum) ^ ((v0 >> 5) + k3);
        v0 -= ((v1 << 4) + k0) ^ (v1 + sum) ^ ((v1 >> 5) + k1);
        sum -= delta;
    } /* end cycle */
    v[0] = v0;
    v[1] = v1;
}

void encrypt(uint32_t *v, uint32_t *k)
{
    uint32_t v0 = v[0], v1 = v[1], sum = 0, i;           /* set up */
    uint32_t delta = 0x9e3779b9;                         /* a key schedule constant */
    uint32_t k0 = k[0], k1 = k[1], k2 = k[2], k3 = k[3]; /* cache key */
    for (i = 0; i < 32; i++)
    { /* basic cycle start */
        sum += delta;
        v0 += ((v1 << 4) + k0) ^ (v1 + sum) ^ ((v1 >> 5) + k1);
        v1 += ((v0 << 4) + k2) ^ (v0 + sum) ^ ((v0 >> 5) + k3);
    } /* end cycle */
    v[0] = v0;
    v[1] = v1;
}

int main()
{
    unsigned char cipher[80] = {18, 186, 235, 205, 57, 16, 228, 62, 74, 108, 147, 138, 39, 186, 150, 201, 206, 20, 106, 221, 216, 160, 180, 220, 41, 108, 186, 108, 86, 153, 213, 90, 223, 207, 252, 29, 148, 7, 95, 39, 91, 45, 186, 81, 191, 92, 213, 122, 220, 94, 89, 90, 115, 152, 46, 27, 231, 217, 246, 48, 166, 166, 248, 229, 85, 214, 98, 93, 157, 113, 16, 14, 237, 206, 174, 168, 121, 77, 166, 173};
    uint8_t key[] = "A_boring_crypto_";

    uint32_t b = 72;
    for (int i = 0;i <= 80; i += 8)
    {
        decrypt((uint32_t*)&cipher[i], (uint32_t*)key);
    }
        
    for(int i = 0;i < 80; i += 1)
    {
        printf("\\x%02x",cipher[i]);
    }
    return 0;
}

```

### 最终解密脚本

```python
from Crypto.Cipher import Blowfish
from Crypto.Cipher import DES
from Crypto.Cipher import AES, ARC4

cmp_str = [  0x37, 0x2A, 0x2F, 0x28, 0x5A, 0xEF, 0x17, 0x3F, 0xE7, 0xD5, 
  0x7C, 0xE5, 0xA5, 0xF7, 0xA9, 0xDA, 0xFF, 0xBC, 0x3B, 0xA9, 
  0x68, 0xDF, 0xDF, 0xF8, 0x7C, 0x20, 0x76, 0x74, 0x8F, 0xCC, 
  0xC4, 0xD4, 0xF2, 0xAC, 0x52, 0x4B, 0xD4, 0xC6, 0x87, 0xE8, 
  0x40, 0xD6, 0x9C, 0x3C, 0xCE, 0x05, 0x09, 0x7C, 0xD4, 0xB1, 
  0x6A, 0xA9, 0x65, 0xA3, 0xC7, 0xE7, 0x23, 0xD0, 0x50, 0xA7, 
  0x7E, 0x40, 0x71, 0x30, 0x7C, 0x59, 0x21, 0xCE, 0x9F, 0xE3, 
  0x91, 0xE0, 0x0E, 0xF1, 0x06, 0x29, 0xC7, 0x94, 0xCE, 0x7C, 
  0x21, 0x20, 0xD6, 0x99, 0xB0, 0x6D, 0x2B, 0x71, 0x21, 0x20, 
  0xD6, 0x99, 0xB0, 0x6D, 0x2B, 0x71]

key = b'A_boring_crypto_makes_you_angry!'

#Crypto6() = Blowfish
obj = Blowfish.new(key)
cipher = b""
for i in cmp_str:
	cipher += bytes([i])
plain = obj.decrypt(cipher[0:8])

plain = [18,186,235,205,57,16,228,62,74,108,147,138,39,186,150,201,206,20,106,221,216,160,180,220,41,108,186,108,86,153,213,90,223,207,252,29,148,7,95,39,91,45,186,81,191,92,213,122,220,94,89,90,115,152,46,27,231,217,246,48,166,166,248,229,85,214,98,93,157,113,16,14,237,206,174,168,121,77,166,173]

#Crypto5() = TEA

plain = b'\xaa\x77\x9f\x62\x8a\x27\x1c\x36\x81\x7d\xaa\x1c\x62\xaa\x03\x51\x97\x57\xde\xf1\x77\x55\x00\x9d\xbd\xa6\x28\xab\x12\x9b\xfc\xfb\x75\x6e\x0f\x4d\xaa\xa0\x97\xa0\x8c\xf9\x8a\xdb\xa8\x7f\xd0\xe9\x09\x97\x62\xfa\xa1\xf1\xed\xd1\x5b\xb6\x42\x37\xaf\xab\x9f\x29\x86\x19\x78\x3c\xda\x46\xc3\xd1'


#Crypto4() = DES
obj = DES.new(key[0:8])
plain = obj.decrypt(plain)[0:-8]
# print(plain)

#Crypto3() = AES
obj = AES.new(key[0:16])
plain = obj.decrypt(plain)[0:-16]
#print(plain)

#Crypto2() = ARC4
obj = ARC4.new(key)
plain = obj.decrypt(plain)
#print(plain)

#Crypto1() = RC5
def _ROL4_(x, y):
	y &= 0xFF
	y %= 32
	return ((x << y) & 0xFFFFFFFF) | (x >> (32 - y))


def _ROR4_(x, y):
	y &= 0xFF
	y %= 32
	return (x >> y) | ((x << (32 - y)) & 0xFFFFFFFF)

def crypto1(flag_0, num):

	keybox_0 = [0xE4,0x6A,0xED,0x19,0xB6,0x5F,0x79,0x82,0x68,0xFA,0x1F,0x4E,0x4E,0x7A,0x64,0x18,0x2A,0xE1,0x69,0xFB,0xFE,0xE3,0x0D,0xD2,0xB0,0x0E,0x9C,0x61,0xB5,0xF8,0x7C,0x8E,0xFF,0xEC,0x70,0xC4,0xA1,0x3A,0xF6,0x87,0x10,0x5F,0xD0,0xCC,0xD6,0x02,0x24,0xA1,0x97,0xB8,0xF3,0x9E,0x87,0x1E,0x85,0xF8,0x51,0x66,0x7E,0xBB,0x17,0xF1,0x3B,0xC6,0x84,0x6C,0x00,0x85,0xBE,0x42,0xEB,0x75,0xA4,0x4F,0x73,0xEF,0x6F,0xED,0x62,0xDC,0xD1,0xA0,0xAA,0x0F,0x7E,0x05,0xD5,0xF7,0xCF,0xFA,0x81,0xAC,0xA0,0x7D,0x7A,0xE8,0xFB,0xF4,0xC7,0x76,0x1C,0xCF,0x66,0x8F]

	keybox = [0 for i in range(26)]

	for i in range(len(keybox_0) // 4):
		 keybox[i] = (keybox_0[i * 4 + 3] << 24) + (keybox_0[i * 4 + 2] << 16) + (keybox_0[i * 4 + 1] << 8) + keybox_0[i * 4 + 0] 
	
	if num == 1:
		size = len(flag_0)
		for i in range(8 - size % 8):
			flag_0.append(8 - size % 8)

		size = len(flag_0)	
		flag = [0 for i in range(len(flag_0) // 4)]

		for i in range(len(flag_0) // 4):
			flag[i] = (flag_0[i * 4 + 3] << 24) + (flag_0[i * 4 + 2] << 16) + (flag_0[i * 4 + 1] << 8) + flag_0[i * 4 + 0] 

		for i in range(size // 8):
			v7 = keybox[0] + flag[2 * i]
			v8 = keybox[1] + flag[2 * i + 1]
			for k in range(1, 13):
				v7 = (keybox[2 * k] + _ROL4_(v8 ^ v7, v8)) & 0xFFFFFFFF
				v8 = (keybox[2 * k + 1] + _ROL4_(v7 ^ v8, v7)) & 0xFFFFFFFF
			flag[2 * i] = v7
			flag[2 * i + 1] = v8
	else :
		size = len(flag_0)
		flag = [0 for i in range(len(flag_0) // 4)]
		for i in range(len(flag_0) // 4):
			flag[i] = (flag_0[i * 4 + 3] << 24) + (flag_0[i * 4 + 2] << 16) + (flag_0[i * 4 + 1] << 8) + flag_0[i * 4 + 0]

		for i in range(size // 8 - 1,-1, -1):
			v7 = flag[2 * i]
			v8 = flag[2 * i + 1]
			for k in range(12,0,-1):
				tmp = (v8 - keybox[2 * k + 1]) & 0xFFFFFFFF
				v8 = _ROR4_(tmp,v7) ^ v7
				tmp = (v7 - keybox[2 * k]) & 0xFFFFFFFF
				v7 = _ROR4_(tmp,v8) ^ v8
			flag[2 * i] = (v7 - keybox[0]) & 0xFFFFFFFF
			flag[2 * i + 1] = (v8 - keybox[1]) & 0xFFFFFFFF
		
	
	for i in range(len(flag_0) // 4):
		flag_0[i * 4 + 3] = (flag[i] >> 24) & 0xFF
		flag_0[i * 4 + 2] = (flag[i] >> 16) & 0xFF
		flag_0[i * 4 + 1] = (flag[i] >> 8) & 0xFF		
		flag_0[i * 4 + 0] = flag[i] & 0xFF
	return(flag_0)

flag = []

for i in plain:
    flag.append(i)
flag = crypto1(map(ord,flag),0)
flag = "".join(list(map(chr,flag)))
print(flag)

```

flag：Aurora{wh47_d035n7_k1ll_y0u_m4k35_y0u_57r0n63r}