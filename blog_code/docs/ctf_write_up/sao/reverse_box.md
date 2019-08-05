
# 很SAO的解题方法

## reverse_box

题目: [reverse_box](https://github.com/Alikas0/CTF/blob/master/challenges/reverse_box/reverse_box.zip)

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