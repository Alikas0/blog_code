
## 背景

学习PWN Canary时，64位Ubuntu编译32位程序报错

```text
$ gcc -m32 ex2.c -o ex2
In file included from ex2.c:2:0:
/usr/include/stdio.h:27:10: fatal error: bits/libc-header-start.h: No such file or directory
 #include <bits/libc-header-start.h>
          ^~~~~~~~~~~~~~~~~~~~~~~~~~
compilation terminated.
```

## 解决

```text
$ sudo apt-get install build-essential module-assistant  
$ sudo apt-get install gcc-multilib g++-multilib  
```

结果:
```text
$ gcc -m32 ex2.c -o ex2
ex2.c: In function ‘vuln’:
ex2.c:18:16: warning: format not a string literal and no format arguments [-Wformat-security]
         printf(buf);
                ^~~
```

成功！