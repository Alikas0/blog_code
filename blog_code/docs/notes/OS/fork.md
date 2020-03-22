## fork

### 头文件

```c
#include<unistd.h>
#include<sys/types.h>
```

### 函数原型

```c
pid_t fork(void)
```

​			程序包含位于内存的多个组成部分，执行程序的过程将根据需要来访问这些内容，包括文本段（text segment）、数据段（data segments）、栈（stack）和堆（heap）。文本段中存放CPU所执行的命令，数据段存放进程操作的所有数据变量，栈存放自动变量和函数数据，堆存放动态内存分配情况数据。当进程被创建时，子进程收到父进程的数据副本，`包括数据空间、堆、栈和进程描述符`

### 返回值 

若成功调用一次则返回两个值，子进程返回0，父进程返回子进程ID；否则，出错返回-1

### 示例

#### 单子进程创建

##### 代码

```c
/* 单子进程创建 */

#include <unistd.h>
#include <sys/wait.h>
#include <stdio.h>
#include <time.h>
#include <stdarg.h>

int tPrint (const char * fmt, ...);

int main(void) {
    pid_t pid;
    printf("Hello from Parent Process, PID is %d.\n", getpid());

    pid = fork(); //创建子进程

    if (pid == 0) {
        sleep(1);
        for (int k = 0; k < 3; ++k) {
            printf("Hello from Child Process %d. %d times\n", getpid(), k + 1);
        }
    } else if ( pid != -1) {
        tPrint("Parent process forked one child process--%d.\n", pid);
        tPrint("Parent process is waiting for child process to exit,\n");
        waitpid(pid, NULL, 0);
        tPrint("Child Process has exited.\n");
        tPrint("Parent process had exited.\n");
    } else {
        tPrint("Everything was done without error.\n");
    }

    return 0;
}

// 对输出信息进行优化
int tPrint (const char *fmt, ...) {
    va_list args;
    struct tm *tStruct;
    time_t tSec;
    tSec = time(NULL);
    tStruct = localtime(&tSec);
    printf("%02d:%02d:%02d: %5d|", tStruct->tm_hour, tStruct->tm_min, tStruct->tm_sec, getpid());
    va_start(args, fmt);
    return vprintf(fmt, args);
}

```

##### 运行结果

![UTOOLS1584867714061.png](http://yanxuan.nosdn.127.net/845385f3ccfbb0008b0cbc17d2f4dd23.png)

##### 流程图分析

![单进程.png](http://yanxuan.nosdn.127.net/1f0b501b6552710213a963c871642e7d.png)

#### 用循环创建两（多）个子进程

##### 代码1——预测错误

```c
/* 多进程创建 */
#include <unistd.h>
#include <sys/wait.h>
#include <stdio.h>
#include <time.h>
#include <stdarg.h>

int tPrint (const char * fmt, ...);

int main(void) {
    pid_t pid = 0;
    printf("Hello from Parent Process, PID is %d.\n", getpid());

    for (int i = 0; i < 2; ++i) {
        pid = fork();
        if (pid != 0 && pid != -1) {
            tPrint("Parent process forked one child process--%d.\n", pid);
            tPrint("Parent process is waiting for child process to exit,\n");
        }
    }

    if (pid == 0) {
        sleep(1);
        printf("Hello from Child Process %d.\n", getpid());
    } else if ( pid != -1) {
        waitpid(pid, NULL, 0);
        tPrint("Child Process has exited.\n");
        tPrint("Parent Process has exited.\n");
    } else {
        tPrint("Everything was done without error.\n");
    }



    return 0;
}

int tPrint (const char *fmt, ...) {
    va_list args;
    struct tm *tStruct;
    time_t tSec;
    tSec = time(NULL);
    tStruct = localtime(&tSec);
    printf("%02d:%02d:%02d: %5d|", tStruct->tm_hour, tStruct->tm_min, tStruct->tm_sec, getpid());
    va_start(args, fmt);
    return vprintf(fmt, args);
}
```

###### 运行结果

![UTOOLS1584869814528.png](http://yanxuan.nosdn.127.net/a71615608d54ab11459979e4f5cf35ce.png)

###### 分析错误原因

看似只产生了两个子进程，实际上产生了三个子进程。

fork() 函数是将父进程的数据副本进行复制，`包括数据空间、堆、栈和进程描述符`，所以第一个子进程还在循环之中，此时 `i = 0`, i++ 后 `再一次运行fork() 创建一个子进程`:

```text
PID
2342(Parent) -> 2343(Child1)
2343(Child1) -> 2344(Child3)
2142(Parent) -> 2145(Child2)
```


##### 代码2

由于fork函数会返回两次，在子进程中返回0值，因此，可以在循环中添加`if (pid == 0 || pid == -1) break; `则当第一个子进程创建后，由于`pid == 0`则退出循环

```c

#include <unistd.h>
#include <sys/wait.h>
#include <stdio.h>
#include <time.h>
#include <stdarg.h>

int tPrint (const char * fmt, ...);

int main(void) {
    pid_t pid = 0;
    printf("Hello from Parent Process, PID is %d.\n", getpid());

    for (int i = 0; i < 2; ++i) {
        pid = fork();
        if (pid != 0 && pid != -1) {
            tPrint("Parent process forked one child process--%d.\n", pid);
            tPrint("Parent process is waiting for child process to exit,\n");
        }
        if (pid == 0 || pid == -1) break;
    }

    if (pid == 0) {
        sleep(1);
        printf("Hello from Child Process %d.\n", getpid());
    } else if ( pid != -1) {
        waitpid(pid, NULL, 0);
        tPrint("Child Process has exited.\n");
        tPrint("Parent Process has exited.\n");
    } else {
        tPrint("Everything was done without error.\n");
    }



    return 0;
}

int tPrint (const char *fmt, ...) {
    va_list args;
    struct tm *tStruct;
    time_t tSec;
    tSec = time(NULL);
    tStruct = localtime(&tSec);
    printf("%02d:%02d:%02d: %5d|", tStruct->tm_hour, tStruct->tm_min, tStruct->tm_sec, getpid());
    va_start(args, fmt);
    return vprintf(fmt, args);
}

```

###### 运行结果

![UTOOLS1584869951772.png](http://yanxuan.nosdn.127.net/6f13c15c15f1cb61a05a8cd0898dcc46.png)
