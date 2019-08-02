

整理一些用过的python脚本

##计算文件md5

```python
fp = open("file.txt","rb")
md5_obj = hashlib.md5()
md5_obj.update(fp.read())
hash_code = md5_obj.hexdigest()
md5 = str(hash_code).lower()
fp.close()
```

##计算字符串md5

```python
def md5(str):
    import hashlib
    m = hashlib.md5()   
    m.update(str)
    return m.hexdigest()
```

##C语言rand()

```python
def rand():
        global seed
        seed = seed * 0x343FD + 0x269EC3
        return (seed >> 16) & 0x7FFF
```

##凯撒密码26次

```python
#coding:utf-8

upperDict = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L',
             'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z']
lowerDict = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l',
             'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z']


def cesarWithLetter(ciphertext, offset):
    '''
    凯撒密码 :
        只转换字母(包括大写小写)
    参数 : 
        ciphertext : 明文
        offset : 偏移量
    '''
    result = ""
    for ch in ciphertext:
        if ch.isupper():
            result = result+upperDict[((upperDict.index(ch)+offset) % 26)]
        elif ch.islower():
            result = result+lowerDict[((lowerDict.index(ch)+offset) % 26)]
        elif ch.isdigit():
            result = result+ch
        else:
            result = result+ch
    return result


def printAllResult(ciphertext):
    '''
    打印所有偏移结果
    '''
    for i in range(len(upperDict)):
        print cesarWithLetter(ciphertext, i)


ciphertext = raw_input("Please input the words :")
printAllResult(ciphertext)
```

##二进制转字符串

```python3
import libum
s = ''
s = int(s,2)
print(libunm.n2s(s))
```

## 扩展欧几里得算法

```python
#-*-coding:utf-8-*- 
# 扩展欧几里得算法
# 输入m n 
# 输出 m n的最大公约数 还有s,t
# 
# 默认 m > n
 
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
ans = exgcd(m,n,0,0)
 
print("gcd(%d,%d) = %d"%(m,n,ans[0]))
print("s = %d, t = %d"%(ans[1],ans[2]))
```