## Index
原文：[glibc内存管理ptmalloc源代码分析](https://paper.seebug.org/papers/Archive/refs/heap/glibc%e5%86%85%e5%ad%98%e7%ae%a1%e7%90%86ptmalloc%e6%ba%90%e4%bb%a3%e7%a0%81%e5%88%86%e6%9e%90.pdf)

跟着学习并补充不懂的知识

## 基础知识

### x86 平台 Linux 进程内存布局

Linux 系统在加载elf格式的程序文件时, 会调用loader把可执行文件中的各个段依次载入到某一地址开始的空间中（载入地址取决于link editor(ld) 和机器地址的位数, 在32位机器上是 0x8048000, 即 128M 处）
如图所示，以32位机器为例，首先被载入的是.text段，然后是.data段，最后是.bss段。

可以看作是程序的开始空间，程序所能访问的最后地址是0xbfffffff, 也就是到3G地址， 3G以上的1G空间是
内核使用的，应用程序不能直接访问。

应用程序的堆栈从最高地址处开始向下生长, .bss段与堆栈之间的空间是空闲的，空闲的空间被分成两部分： 一部分为heap，另一部分为mmap映射区域。

mmap映射区域一般从TASK_SIZE/3 的地方开始，但是在不同的Linux内核和机器上， mmap区域的开始位置一般是不同的。

Heap和mmap区域都可以供用户自由使用，但在开始未映射到内存空间内，是不可访问的。

在向内核请求分配该空间之前，对这个空间的访问会导致segmentation fault。

用户程序可以直接使用系统调用来管理 heap 和 mmap 映射区域， 但更多时候程序使用的都是C语言提供的 malloc() 和 free()
 函数来动态的分配和释放内存。
 
 Stack区域是唯一不需要映射，用户却可以访问的内存区域， 这也是利用堆栈溢出进行攻击的基础
 
 ### 32 位模式下进程经典内存布局
 
 ![32位模式下进程内存经典布局](https://raw.githubusercontent.com/Alikas0/files/master/img/20191116165530.png)
 
 这种布局是 Linux 内核 2.6.7 以前的默认进程内存布局形式, mmap 区域与栈区域相对增长, 这意味着堆只有1GB的[虚拟地址](https://docs.microsoft.com/zh-cn/windows-hardware/drivers/gettingstarted/virtual-address-spaces)可以使用, 继续增长就会进入mmap映射区域, 这显然不是我们想要的。
 
 这是由于32模式地址空间造成的， 所以内核引入了另一种虚拟地址空间布局的形式， 将在后面介绍。 但对于64位系统， 提供了巨大的虚拟地址空间， 这种布局就相当好。 
 
 ### 32 位模式下进程默认内存布局
 
 ![](https://raw.githubusercontent.com/Alikas0/files/master/img/20191117154156.png)
 
 这种布局是 Linux 内核 2.6.7 以后的默认进程内存布局形式，栈至顶向下扩展，并且栈是有界的。 堆至底向上扩展， mmap 映射区域至顶向下扩展， mmap 映射区域和堆相对扩展， 直到耗尽虚拟地址空间中的剩余区域， 这种结构便于 C [运行时库](https://zh.wikipedia.org/wiki/%E8%BF%90%E8%A1%8C%E6%97%B6%E5%BA%93) 使用 mmap 映射区域和堆进行内存分配。
 
 ### 64 位模式下进程内存布局
 
 对 AMD64 系统， 内存布局采用经典布局， text 的起始地址为 0x0000000000400000， 堆紧接着BSS段向上增长， mmap 映射区域开始位置一般设为 TASK_SIZE/3
 
 ```c
#define TASK_SIZE_MAX ((1UL << 47) - PAGE_SIZE)
#define TASK_SIZE (test_thread_flag(TIF_IA32) ? \
 IA32_PAGE_OFFSET : TASK_SIZE_MAX)
#define STACK_TOP TASK_SIZE
#define TASK_UNMAPPED_BASE (PAGE_ALIGN(TASK_SIZE / 3))
```  

可知，mmap开始区域为 0x00002AAAAAAAA000，栈顶地址为 0x00007FFFFFFFF000

![X86_64 下 Linux 进程的默认内存布局形式](https://raw.githubusercontent.com/Alikas0/files/master/img/20191117162202.png)

上图X86_64 下 Linux 进程的默认内存布局形式的示意图， 当前内核默认配置下， 进程的栈和mmap 映射区域 并不是从一个固定地址开始的， 并且每次启动时的值都不一样， 这是程序在启动时随机改变这些值的设置，使得使用缓冲区溢出进行攻击更加困难。
当然也可以让进程的栈和 mmap 映射区域从一个固定位置开始，只需要设置全局变量 randomize_va_space 值为0 ， 这个变量默认值为 1 。 用户可以通过设置`proc/sys/kernel/randomize_va_space`来停用该特性，也可以用如下命令：

```text
$ sudo sysctl -w kernel.randomize_va_space=0
```
或者是
```text
$ sudo echo 0 >/proc/sys/kernel/randomize_va_space 
```

!!!note 'Linux [内存地址随机化机制](https://zh.wikipedia.org/wiki/%E4%BD%8D%E5%9D%80%E7%A9%BA%E9%96%93%E9%85%8D%E7%BD%AE%E9%9A%A8%E6%A9%9F%E8%BC%89%E5%85%A5)'
    
    randomize_va_space的值有三种，分别是[0,1,2]
    0 - 表示关闭进程地址空间随机化
    1 - 表示将mmap的基址，stack和vdso页面随机化
    2 - 表示在1的基础上增加栈（heap）的随机化

## 操作系统内存分配的相关函数

- 对heap操作: 
    - 操作系统:`brk()`
    - C运行时库:`sbrk()` 

- 对mmap映射区域的操作
    - 操作系统`mmap()` 和 `munmap()`

sbrk()、 brk() 、 mmap() 都可以为进程添加额外的虚拟内存

Glibc同样是使用这些函数对操作系统申请虚拟内存

**重要概念**： 内存的延迟分配，只有在真正访问一个地址的啥时候才建立这个地址的物理映射, 这是Linux内存管理的基本思想之一。

Linux 内核在用户申请内存的时候，只是给它分配了一个线性区（也就是虚拟内存），并没有分配实际物理内存；只有当用户使用这块内存的时候，内核才会分配具体的物理页面给用户，这时候才占用宝贵的物理内存。内核释放物理页面是通过释放线性区，找到其所对应的物理页面，将其全部释放的过程。

### Heap操作相关的函数

Heap 操作函数主要有两个， brk()为系统调用， sbrk()为C库函数。 系统调用通常会提供一种最小功能， 而库函数通常提供比较复杂的功能。 Glibc的malloc族(realloc, calloc 等)就调用sbrk()函数将数据段下届移动，sbrk() 函数在内核的管理下将[虚拟地址空间](https://docs.microsoft.com/zh-cn/windows-hardware/drivers/gettingstarted/virtual-address-spaces)映射到内存，拱 malloc() 函数使用

内核数据结构 mm_struct 中的成员变量 start_code 和 end_code 是进程代码段的起始和终止地址，start_data 和 end_data 是进程数据段的起始和终止地址，start_stack 是进程堆栈段起始地址，start_brk 是进程动态内存分配起始地址（堆的起始地址），还有一个 brk（堆的当前最后地址），就是动态内存分配当前的终止地址。C 语言的动态内存分配基本函数是malloc()，在 Linux 上的实现是通过内核的 brk 系统调用。brk()是一个非常简单的系统调用，只是简单地改变 mm_struct 结构的成员变量 brk 的值。

函数定义如下:

```text
NAME
       brk, sbrk - change data segment size
SYNOPSIS
       #include <unistd.h>
       int brk(void *addr);
       void *sbrk(intptr_t increment);
```

!!!note "brk() 和 sbrk()"
    
    ```
    DESCRIPTION
       brk()  and sbrk() change the location of the program break, which defines the end of
       the process's data segment (i.e., the program break is the first location after  the
       end of the uninitialized data segment).  Increasing the program break has the effect
       of allocating memory to the process; decreasing the break deallocates memory.

       brk() sets the end of the data segment to the value specified  by  addr,  when  that
       value  is  reasonable, the system has enough memory, and the process does not exceed
       its maximum data size (see setrlimit(2)).

       sbrk() increments the program's data space by increment bytes.  Calling sbrk()  with
       an increment of 0 can be used to find the current location of the program break.

    RETURN VALUE
       On  success,  brk()  returns  zero.   On  error, -1 is returned, and errno is set to
       ENOMEM.

       On success, sbrk() returns the previous program break.  (If the break was increased,
       then this value is a pointer to the start of the newly allocated memory).  On error,
       (void *) -1 is returned, and errno is set to ENOMEM.
    ```
    
    brk() 直接修改有效访问范围的末尾地址来实现分配和回收
    sbrk() increment为正值时扩展brk值， 当increment为负值时收缩brk值， 当increment = 0时，sbrk()函数返回当前brk值
    
    ```c
    #include <stdio.h>
    #include <stdlib.h>
    #include <unistd.h>
    
    int main() {
        int *old = sbrk(0);
        int *p = sbrk(0x100);
        if (p == (void *)(-1) ) {
            perror("sbrk");
            exit(EXIT_FAILURE);
        }
        int *new = sbrk(0);
        new = sbrk(0);
        int err = brk(old); // 虽然，sbrk()与brk()均可分配回收兼职，但是我们一般用sbrk()分配内存，而用brk()回收内存; brk(old) 等价于 sbrk(-0x100)
        if (err == -1 ) {
            perror("brk");
            exit(EXIT_FAILURE);
        }
        int *free = sbrk(0);
        printf("old: %p\np: %p\nnew: %p\nfree: %p\n", old, p, new, free);
        printf("size: %lx\n", (long)new - (long)old);
        return 0;
    }
    ```
