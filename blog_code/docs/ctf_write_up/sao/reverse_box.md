
# 很SAO的解题方法

## reverse_box

### 弱鸡的Alikas的操作

> dump出v7和v8,爆破randnum
```python
from Crypto.Util.number import long_to_bytes

max = 8

def ror(s, bit):
    global max
    return ((s >> bit) | (s << (max - bit))) & 0xff


v7 = 1
v8 = 1
v8 = [246, 82, 199, 180, 108, 36, 28, 253, 162, 151, 132, 124, 221, 75, 57, 23, 13, 242, 167, 148, 133, 138, 143, 140,
      141, 123, 41, 238, 90, 54, 18, 14, 243, 81, 198, 66, 62, 227, 168, 145, 134, 139, 121, 222, 74, 207, 69, 202, 70,
      203, 176, 153, 119, 45, 27, 9, 7, 244, 165, 99, 33, 31, 252, 84, 197, 67, 200, 177, 111, 37, 234, 175, 101, 35,
      232, 88, 193, 182, 155, 128, 137, 142, 122, 223, 188, 157, 130, 126, 42, 239, 172, 100, 213, 186, 159, 117, 218,
      191, 156, 116, 44, 237, 91, 192, 64, 201, 71, 61, 226, 94, 195, 65, 63, 21, 250, 86, 50, 231, 93, 194, 183, 109,
      210, 78, 58, 22, 251, 160, 96, 32, 233, 174, 147, 113, 47, 236, 173, 146, 135, 125, 43, 25, 254, 163, 97, 214,
      187, 105, 39, 29, 11, 240, 80, 48, 16, 249, 87, 196, 181, 154, 118, 219, 73, 206, 179, 152, 129, 127, 220, 189,
      107, 208, 185, 158, 131, 136, 120, 40, 24, 8, 241, 166, 98, 215, 77, 59, 224, 169, 103, 212, 76, 205, 178, 110,
      211, 184, 104, 209, 79, 204, 68, 60, 20, 12, 4, 245, 83, 49, 230, 171, 144, 112, 217, 190, 106, 38, 235, 89, 55,
      228, 92, 52, 229, 170, 102, 34, 30, 10, 6, 2, 247, 164, 149, 115, 216, 72, 56, 225, 95, 53, 19, 248, 161, 150,
      114, 46, 26, 255, 85, 51, 17, 15, 5, 3, 1]
v7 = [3, 5, 15, 17, 51, 85, 255, 26, 46, 114, 150, 161, 248, 19, 53, 95, 225, 56, 72, 216, 115, 149, 164, 247, 2, 6, 10,
      30, 34, 102, 170, 229, 52, 92, 228, 55, 89, 235, 38, 106, 190, 217, 112, 144, 171, 230, 49, 83, 245, 4, 12, 20,
      60, 68, 204, 79, 209, 104, 184, 211, 110, 178, 205, 76, 212, 103, 169, 224, 59, 77, 215, 98, 166, 241, 8, 24, 40,
      120, 136, 131, 158, 185, 208, 107, 189, 220, 127, 129, 152, 179, 206, 73, 219, 118, 154, 181, 196, 87, 249, 16,
      48, 80, 240, 11, 29, 39, 105, 187, 214, 97, 163, 254, 25, 43, 125, 135, 146, 173, 236, 47, 113, 147, 174, 233, 32,
      96, 160, 251, 22, 58, 78, 210, 109, 183, 194, 93, 231, 50, 86, 250, 21, 63, 65, 195, 94, 226, 61, 71, 201, 64,
      192, 91, 237, 44, 116, 156, 191, 218, 117, 159, 186, 213, 100, 172, 239, 42, 126, 130, 157, 188, 223, 122, 142,
      137, 128, 155, 182, 193, 88, 232, 35, 101, 175, 234, 37, 111, 177, 200, 67, 197, 84, 252, 31, 33, 99, 165, 244, 7,
      9, 27, 45, 119, 153, 176, 203, 70, 202, 69, 207, 74, 222, 121, 139, 134, 145, 168, 227, 62, 66, 198, 81, 243, 14,
      18, 54, 90, 238, 41, 123, 141, 140, 143, 138, 133, 148, 167, 242, 13, 23, 57, 75, 221, 124, 132, 151, 162, 253,
      28, 36, 108, 180, 199, 82, 246, 1]

# print(len(v7))
# print(len(v8))
flag = "TWCTF"
index = list(map(ord, flag))
target = [0x95, 0xee, 0xaf, 0x95, 0xef]

# print ror(246,4)

xor = []

for i in v8:
    xor.append(ror(i,4) ^ ror(i,5) ^ ror(i,6) ^ ror(i,7))
# print(xor)
for randnum in range(0xFF):
    sbox = [0 for i in range(0x100)]
    sbox[0] = randnum
    for i in range(len(v7)):
        sbox[v7[i]] = (xor[i] ^ randnum ^ v8[i]) & 0xff
    key = 1
    for i in range(len(flag)):
        if sbox[index[i]] != target[i]:
            key = 0
            break
    if key:
        print randnum
        break

target = '95eeaf95ef94234999582f722f492f72b19a7aaf72e6e776b57aee722fe77ab5ad9aaeb156729676ae7a236d99b1df4a'
key = []

for i in range(len(target) // 2):
    key.append(int(target[i * 2:i * 2+2],16))

flag = ""

for i in key:
    flag += chr(sbox.index(i))

print flag
```

### Apeng大佬的操作

> 修改文件名以及patch上randnum的generate.py

```python
buf = ""
with open("reverse_box","rb") as f:
    buf = f.read()
for i in range(0,256):
    f = open("reverse_box%02x"%i,"wb")
    f.write(buf[0:0x5B1])
    f.write(chr(i))
    f.write(buf[0x5B2:])
    f.close()
```

> 爆破出randnum的exploit.py

```python
from pwn import *
context.log_level = "error"
for i in range(0,256):
	p = process(argv = ["./reverse_box%02x"%i,"}"])
	s = p.recv().strip()
	if s == "4a":
		print(i)
		break
	p.close()
	
key = i
#key = 214
dest = "95eeaf95ef94234999582f722f492f72b19a7aaf72e6e776b57aee722fe77ab5ad9aaeb156729676ae7a236d99b1df4a"
res = ""
dic = {}
for i in range(0x20,0x7f):
	p = process(argv = ["./reverse_box%02x"%key,chr(i)])
	dic[p.recv().strip().decode("hex")] = chr(i)
	p.close()
for i in dest.decode("hex"):
	res+=dic[i]
print(res)
"""
for i in range(len(dest)/2):
		
	for j in range(0x21,0x7f):
		tmp = res+chr(j)
		
		p = process(argv = ["./reverse_box%02x"%key,tmp])
		s = p.recv().strip()
		if dest.startswith(s):
			res = tmp
			p.close()
			break
		p.close()
	print(res)
"""
```