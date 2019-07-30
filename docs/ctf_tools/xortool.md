
## 简介
xortool.py是基于python的脚本，用于完成一些xor分析，包括:

- 猜想key的长度
- 猜想key的值
- 解密一些经过xoe加密的文件

官方:https://github.com/hellman/xortools.git

## 安装

xortool依赖于python的命令行参数解释器docopt

```
$ sudo pip isntall docopt
$ cd /data/src
$ git clone https://github.com/hellman/xortools.git
$ cd xortool
$ sudo python setup.py install
```

## Options
```
$ xortool -h
xortool
  A tool to do some xor analysis:
  - guess the key length (based on count of equal chars)
  - guess the key (base on knowledge of most frequent char)

Usage:
  xortool [-x] [-m MAX-LEN] [-f] [-t CHARSET] [FILE]
  xortool [-x] [-l LEN] [-c CHAR | -b | -o] [-f] [-t CHARSET] [FILE]
  xortool [-x] [-m MAX-LEN| -l LEN] [-c CHAR | -b | -o] [-f] [-t CHARSET] [FILE]
  xortool [-h | --help]
  xortool --version

Options:
  -x --hex                          input is hex-encoded str
  -l LEN, --key-length=LEN          length of the key
  -m MAX-LEN, --max-keylen=MAX-LEN  maximum key length to probe [default: 65]
  -c CHAR, --char=CHAR              most frequent char (one char or hex code)
  -b --brute-chars                  brute force all possible most frequent chars
  -o --brute-printable              same as -b but will only check printable chars  -f --filter-output                filter outputs based on the charset
  -t CHARSET --text-charset=CHARSET target text character set [default: printable]  -h --help                         show this help

Notes:
  Text character set:
    * Pre-defined sets: printable, base32, base64
    * Custom sets:
      - a: lowercase chars
      - A: uppercase chars
      - 1: digits
      - !: special chars
      - *: printable chars

Examples:
  xortool file.bin
  xortool -l 11 -c 20 file.bin
  xortool -x -c ' ' file.hex
  xortool -b -f -l 23 -t base64 message.enc
```

## 例题

### 攻防世界 misc 5-1

``` hl_lines="13 14 15"
$ xortool -c 20 cipher
The most probable key lengths:
   2:   12.2%
   5:   11.9%
   9:   9.8%
  13:   22.2%
  20:   6.8%
  22:   6.2%
  26:   12.8%
  30:   4.6%
  39:   7.8%
  52:   5.7%
Key-length can be 3*n
1 possible key(s) of length 13:
Good\tuckToYou
Found 1 plaintexts with 95.0%+ valid characters
See files filename-key.csv, filename-char_used-perc_valid.csv
```
