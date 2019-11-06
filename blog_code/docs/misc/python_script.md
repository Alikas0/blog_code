

整理一些用过的python脚本

## 计算文件md5

```python
import hashlib
fp = open("file.txt","rb")
md5_obj = hashlib.md5()
md5_obj.update(fp.read())
hash_code = md5_obj.hexdigest()
md5 = str(hash_code).lower()
fp.close()
```

## 计算字符串md5

```python
def md5(str):
    import hashlib
    m = hashlib.md5()   
    m.update(str)
    return m.hexdigest()
```

## C语言rand()

- Windows版

```python
def rand():
        global seed
        seed = seed * 0x343FD + 0x269EC3
        return (seed >> 16) & 0x7FFF
```

## 凯撒密码26次

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

## 二进制转字符串

```python3
import libum
s = ''
s = int(s,2)
print(libunm.n2s(s))
```

## 扩展欧几里得算法

```python
def egcd(a, b):
    x, lastX = 0, 1
    y, lastY = 1, 0
    while b != 0:
        q = a // b
        a, b = b, a % b
        x, lastX = lastX - q * x, x
        y, lastY = lastY - q * y, y
    return lastX, lastY
```

## base64隐写解密脚本
```python
def get_base64_diff_value(s1, s2):
    base64chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/'
    res = 0
    for i in xrange(len(s2)):
        if s1[i] != s2[i]:
            return abs(base64chars.index(s1[i]) - base64chars.index(s2[i]))
    return res


def solve_stego():
    with open('stego.txt', 'rb') as f:
        file_lines = f.readlines()
        bin_str = ''
        for line in file_lines:
            steg_line = line.replace('\n', '')
            norm_line = line.replace('\n', '').decode('base64').encode('base64').replace('\n', '')
            diff = get_base64_diff_value(steg_line, norm_line)
            print diff
            pads_num = steg_line.count('=')
            if diff:
                bin_str += bin(diff)[2:].zfill(pads_num * 2)
            else:
                bin_str += '0' * pads_num * 2
            print goflag(bin_str)


def goflag(bin_str):
    res_str = ''
    for i in xrange(0, len(bin_str), 8):
        res_str += chr(int(bin_str[i:i + 8], 2))
    return res_str


if __name__ == '__main__':
    solve_stego()
```