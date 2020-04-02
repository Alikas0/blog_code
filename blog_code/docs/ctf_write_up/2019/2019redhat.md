## RE

### XX

 https://en.wikipedia.org/wiki/XXTEA XXTEA加密

输入前四位应为小写字母+数字，取前四位为key，后面补\x00，补足16位



之后对flag进行XXTEA加密，然后按2 0 3 1 的顺序混淆错位，再谜之异或，最后和

 0x6B40BCCE, 0xC0953A7C, 0x20209BEF, 0x3502F791, 0xC8021823, 0xFA5656E7进行对比.。

求异或：

```c
# include<stdio.h>

int main() {
	unsigned char cipherX[] = { 0xCE, 0xBC, 0x40, 0x6B, 0x7C, 0x3A, 0x95, 0xC0,  0xEF, 0x9B, 0x20, 0x20, 0x91, 0xF7, 0x02, 0x35,0x23, 0x18, 0x02, 0xC8,0xE7, 0x56, 0x56, 0xFA};
	unsigned char *v21 = cipherX + 23;
	int v20 = 23;
	int v22;
	char v23;
	for (; v20 > 0; --v21) {
		v22 = 0;
    	if ( v20 / 3 > 0 )
  	  	{
      		v23 = *v21;
     		 do
     		 {
       		 	v23 ^= cipherX[v22++];
       		 	*v21 = v23;
    		  }
     		 while ( v22 < v20 / 3 );
    	}
    	--v20;
	}
	for (int i = 0; i < 24; i++) {
		printf("0x%x,", cipherX[i]);
	}
}
```

解题脚本,flag格式猜测key为flag，python3：

```python
import string
from itertools import product
from ctypes import *

def decrypt(cipher,key):
        k = [0] * 4
        for i in range(4):
            tmp = c_uint32(0)
            for j in range(4):
                tmp.value = tmp.value << 8
                tmp.value = tmp.value + key[4 * i + (3 - j)]
            k[i] = tmp
        length = len(cipher)
        n = length // 4
        v = [c_uint32()]*n
        for i in range(n):
            tmp = c_uint32(0)
            for j in range(4):
                tmp.value = tmp.value << 8
                tmp.value = tmp.value + cipher[4 * i + (3 - j)]
            v[i] = tmp
        q = 6 + 52 // n
        z = c_uint32(0)
        y = c_uint32(v[0].value)
        delta = c_uint32(0x9e3779b9)
        sum_delta = c_uint32(q * delta.value)
        for i in range(q)[::-1]:
            e = (sum_delta.value >> 2) & 3
            for p in range(1, n)[::-1]:
                z.value = v[p-1].value
                v[p].value -=  ((z.value >> 5 ^ y.value << 2) + (y.value >> 3 ^ z.value<<4)) ^ ((sum_delta.value ^ y.value) + (k[(p&3)^e].value ^ z.value))
                y.value = v[p].value
            z.value = v[n-1].value
            v[0].value -= ((z.value >> 5 ^ y.value << 2) + (y.value >> 3 ^ z.value<<4)) ^ ((sum_delta.value ^ y.value) + (k[(0&3)^e].value ^ z.value))
            y.value = v[0].value
            sum_delta.value -= delta.value
        plain = b""
        for i in range(n):
            for j in range(4):
                plain+=bytes([v[i].value&0xff])
                v[i].value = v[i].value >> 8
        return plain
# cipherX = [ 0xCE, 0xBC, 0x40, 0x6B, 0x7C, 0x3A, 0x95, 0xC0,  0xEF, 0x9B, 0x20, 0x20, 0x91, 0xF7, 0x02, 0x35,0x23, 0x18, 0x02, 0xC8,0xE7, 0x56, 0x56, 0xFA] print(len(cipherX))
cipherX = [0xce,0xbc,0x40,0xa5,0xb2,0xf4,0xe7,0xb2,0x9d,0xa9,0x12,0x12,0xc8,0xae,0x5b,0x10,0x6,0x3d,0x1d,0xd7,0xf8,0xdc,0xdc,0x70]
cipher = [0 for i in range(24)]
for i in range(6):
    cipher[i * 4 + 2] = cipherX[i * 4 + 0]
    cipher[i * 4 + 0] = cipherX[i * 4 + 1]
    cipher[i * 4 + 3] = cipherX[i * 4 + 2]
    cipher[i * 4 + 1] = cipherX[i * 4 + 3]

cipher_b = b""
for i in cipher:
	cipher_b += bytes([i])

key = b'flag'
for i in range(12):
	key += b'\x00'
plain = decrypt(cipher_b,key)
flag = "".join(list(map(chr,plain)))
print(flag)
```

### easyRE

出题挖了坑，实际逻辑在fini里，参考 https://luomuxiaoxiao.com/?p=516 

主逻辑就是个异或加密，根据提示知道异或key是flag

解密脚本：

```python
s = [0x49 ,0x6F ,0x64 ,0x6C ,0x3E ,0x51 ,0x6E ,0x62 ,0x28 ,0x6F ,0x63 ,0x79 ,0x7F ,0x79 ,0x2E ,0x69 ,0x7F ,0x64 ,0x60 ,0x33 ,0x77 ,0x7D ,0x77 ,0x65 ,0x6B ,0x39 ,0x7B ,0x69 ,0x79 ,0x3D ,0x7E ,0x79 ,0x4C ,0x40 ,0x45 ,0x43]

input = ""

for i in range(len(s)):
	s[i] ^= i
	input += chr(s[i])

print(input)

# input = "Info:The first four chars are `flag`"

cipher = [  0x40, 0x35, 0x20, 0x56, 0x5D, 0x18, 0x22, 0x45, 0x17, 0x2F, 
  0x24, 0x6E, 0x62, 0x3C, 0x27, 0x54, 0x48, 0x6C, 0x24, 0x6E, 
  0x72, 0x3C, 0x32, 0x45, 0x5B]

key = []

xor = list(map(ord,"flag"))
for i in range(4):
	key.append(cipher[i] ^ xor[i])

flag = ""
for i in range(len(cipher)):
	flag += chr(cipher[i] ^ key[i % 4])
print(flag)
```

### childRE

先把输入当成满二叉树然后后序遍历再输出进行打乱，然后UnDecorate， 然后置换加密进行对比

爆破UnDecorate之后的内容，然后进行还原得到

` private: char * __thiscall R0Pxx::My_Aut0_PWN(unsigned char *) `

根据维基百科 https://en.wikipedia.org/wiki/Name_mangling 进行

Decorate，然后还原打乱再进行md5得flag

解密脚本：

```python
# -*- coding: utf-8 -*-
# cmp1 = "(_@4620!08!6_0*0442!@186%%0@3=66!!974*3234=&0^3&1@=&0908!6_0*&"
# cmp2 = "55565653255552225565565555243466334653663544426565555525555222"
# table = [  0x31, 0x32, 0x33, 0x34, 0x35, 0x36, 0x37, 0x38, 0x39, 0x30, 
#   0x2D, 0x3D, 0x21, 0x40, 0x23, 0x24, 0x25, 0x5E, 0x26, 0x2A, 
#   0x28, 0x29, 0x5F, 0x2B, 0x71, 0x77, 0x65, 0x72, 0x74, 0x79, 
#   0x75, 0x69, 0x6F, 0x70, 0x5B, 0x5D, 0x51, 0x57, 0x45, 0x52, 
#   0x54, 0x59, 0x55, 0x49, 0x4F, 0x50, 0x7B, 0x7D, 0x61, 0x73, 
#   0x64, 0x66, 0x67, 0x68, 0x6A, 0x6B, 0x6C, 0x3B, 0x27, 0x41, 
#   0x53, 0x44, 0x46, 0x47, 0x48, 0x4A, 0x4B, 0x4C, 0x3A, 0x22, 
#   0x5A, 0x58, 0x43, 0x56, 0x42, 0x4E, 0x4D, 0x3C, 0x3E, 0x3F, 
#   0x7A, 0x78, 0x63, 0x76, 0x62, 0x6E, 0x6D, 0x2C, 0x2E, 0x2F, 
#   0x00]

# # for i in range(len(cmp1)):
# # 	for j in range(len(table)):
# # 		if ord(cmp1[i]) == table[j]:
# # 			print hex(j)+',',

# # for i in range(len(cmp2)):
# # 	for j in range(len(table)):
# # 		if ord(cmp2[i]) == table[j]:
# # 			print hex(j)+',',

# index1 = [0x14, 0x16, 0xd, 0x3, 0x5, 0x1, 0x9, 0xc, 0x9, 0x7, 0xc, 0x5, 0x16, 0x9, 0x13, 0x9, 0x3, 0x3, 0x1, 0xc, 0xd, 0x0, 0x7, 0x5, 0x10, 0x10, 0x9, 0xd, 0x2, 0xb, 0x5, 0x5, 0xc, 0xc, 0x8, 0x6, 0x3, 0x13, 0x2, 0x1, 0x2, 0x3, 0xb, 0x12, 0x9, 0x11, 0x2, 0x12, 0x0, 0xd, 0xb, 0x12, 0x9, 0x8, 0x9, 0x7, 0xc, 0x5, 0x16, 0x9, 0x13, 0x12]
# index2 = [0x4, 0x4, 0x4, 0x5, 0x4, 0x5, 0x4, 0x2, 0x1, 0x4, 0x4, 0x4, 0x4, 0x1, 0x1, 0x1, 0x4, 0x4, 0x5, 0x4, 0x4, 0x5, 0x4, 0x4, 0x4, 0x4, 0x1, 0x3, 0x2, 0x3, 0x5, 0x5, 0x2, 0x2, 0x3, 0x5, 0x4, 0x2, 0x5, 0x5, 0x2, 0x4, 0x3, 0x3, 0x3, 0x1, 0x5, 0x4, 0x5, 0x4, 0x4, 0x4, 0x4, 0x4, 0x1, 0x4, 0x4, 0x4, 0x4, 0x1, 0x1, 0x1]
# # print(len(index2))
# name = ""

# for j in range(len(index1)):
# 	for i in range(0x20,0x7e):
# 		if i % 23 == index1[j] and i // 23 == index2[j]:
# 			name += chr(i)
# 			break
# print name
# private: char * __thiscall R0Pxx::My_Aut0_PWN(unsigned char *)
# ?表示模板
# * P
# char D; unsigned char E
# private near A
# 64位编程时，唯一可用的调用协议的编码是A
# @： 形参表结束标志
# Z: 缺省的异常规范 
# PAE unsiged char *
# PAD 返回类型
# A表示private的成员函数;A表示非只读成员函数;E表示thiscall
# ?My_Aut0_PWN@R0Pxx@@AAEPADPAE@Z

s1 = "1234567890abcdefghijklmnopqrstu"
s2 = "fg8hi94jk0lma52nobpqc6rsdtue731"
dic = []
for i in range(len(s1)):
	for j in range(len(s2)):
		if s1[i] == s2[j]:
			dic.append(j)

# print dic

name = "?My_Aut0_PWN@R0Pxx@@AAEPADPAE@Z"
input = ""
for i in range(len(name)):
	input += name[dic[i]]
# print input


def md5(str):
    import hashlib
    m = hashlib.md5()   
    m.update(str)
    return m.hexdigest()

print md5(input)
```

### calc

C++写的 STL+VS 又开了Release

自己实现了大数算法，输入三个数，然后进行一波计算之后检查

三个数的大小排序为 p2 < p1 < p3

对算法进行化简，发现就是42的三立方和问题

p1\*\*3 + p2 \*\* 3 - p3 \*\* 3 == 42 

直接百度答案进行排序

```
p1 == 80435758145817515
p2 == 12602123297335631
p3 == 80538738812075974
```

### snake

先玩了一波hhhh

在`.\Snake_Data\Plugins\`找到`Interface.dll`, 分析导出函数`GameObject`

发现就是个RSA

```
def f(v):
    s = ""
    while v != 0:
        v, r = divmod(v, 255)
        s = chr(r) + s
    return s

n = 139907262641720884635250105449327463531131227516500497307311002094885245322386805049406878643982216326493527702414689439930090794753345844178528356178539094825247389836142928474607108262267087850211322640806135698076207986818086837911361480181444157057782599277473843153161174504240064610043962720953514451563
c = 79981856490856999850671700360733120831999995589421207460490185876531860518527597767905168099182891345123878966403548022646956365158864209467614850251731806682037300712511185681164865174187586907707195428804234739667769742078793162639867922056194688917569369338005327309973680573581158754297630654105882382426

for i in range(2, 100):
    r = pow(c, i, n)
    s = f(r)
    if s.startswith("flag"):
        print(s)
```

