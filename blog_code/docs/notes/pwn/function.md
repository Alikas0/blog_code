

## 文件描述符

在Linux系统中一切皆可以看成是文件，文件又可分为：普通文件、目录文件、链接文件和设备文件。

文件描述符（file descriptor）是内核为了高效管理已被打开的文件所创建的索引，其是一个非负整数（通常是小整数），用于指代被打开的文件，所有执行I/O操作的系统调用都通过文件描述符。

程序刚刚启动的时候，0是标准输入，1是标准输出，2是标准错误。如果此时去打开一个新的文件，它的文件描述符会是3。

| 文件描述符 | 用途 | POSIX名称 | stdio流 |
| :---: | :---: | :---: | :---: |
| 0 | 标准输入 | STDIN_FILENO | stdin|
| 1 | 标准输出 | STDOUT_FILENO | stdout |
| 2 | 标准错误 | STDERR_FILENO | stderr|


## write

```c
#include <unistd>
ssize_t write(int filedes, void *buf, size_t nbytes);
// 返回：若成功则返回写入的字节数，若出错则返回-1
// filedes：文件描述符
// buf:待写入数据缓存区
// nbytes:要写入的字节数
```

## read

```c
#include <unistd>
ssize_t read(int filedes, void *buf, size_t nbytes);
// 返回：若成功则返回读到的字节数，若已到文件末尾则返回0，若出错则返回-1
// filedes：文件描述符
// buf:读取数据缓存区
// nbytes:要读取的字节数
```