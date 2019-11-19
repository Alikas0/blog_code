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

???note "Linux [内存地址随机化机制](https://zh.wikipedia.org/wiki/%E4%BD%8D%E5%9D%80%E7%A9%BA%E9%96%93%E9%85%8D%E7%BD%AE%E9%9A%A8%E6%A9%9F%E8%BC%89%E5%85%A5)"
    
    randomize_va_space的值有三种，分别是[0,1,2]
    0 - 表示关闭进程地址空间随机化
    1 - 表示将mmap的基址，stack和vdso页面随机化
    2 - 表示在1的基础上增加栈（heap）的随机化

### 操作系统内存分配的相关函数

- 对heap操作: 
    - 操作系统:`brk()`
    - [C运行时库](https://blog.csdn.net/wqvbjhc/article/details/6612099):`sbrk()` 

- 对mmap映射区域的操作
    - 操作系统`mmap()` 和 `munmap()`

sbrk()、 brk() 、 mmap() 都可以为进程添加额外的虚拟内存

Glibc同样是使用这些函数对操作系统申请虚拟内存

**重要概念**： 内存的延迟分配，只有在真正访问一个地址的啥时候才建立这个地址的物理映射, 这是Linux内存管理的基本思想之一。

Linux 内核在用户申请内存的时候，只是给它分配了一个线性区（也就是虚拟内存），并没有分配实际物理内存；只有当用户使用这块内存的时候，内核才会分配具体的物理页面给用户，这时候才占用宝贵的物理内存。内核释放物理页面是通过释放线性区，找到其所对应的物理页面，将其全部释放的过程。

#### Heap操作相关的函数

Heap 操作函数主要有两个， brk()为系统调用， sbrk()为C库函数。 系统调用通常会提供一种最小功能， 而库函数通常提供比较复杂的功能。 Glibc的malloc族(realloc, calloc 等)就调用sbrk()函数将数据段下届移动，sbrk() 函数在内核的管理下将[虚拟地址空间](https://docs.microsoft.com/zh-cn/windows-hardware/drivers/gettingstarted/virtual-address-spaces)映射到内存，拱 malloc() 函数使用

内核数据结构mm_struct中的成员变量start_code和end_cod是进程代码段的起始和终止地址，start_data 和 end_data 是进程数据段的起始和终止地址，start_stack 是进程堆栈段起始地址，start_brk 是进程动态内存分配起始地址（堆的起始地址），还有一个 brk（堆的当前最后地址），就是动态内存分配当前的终止地址。

C 语言的动态内存分配基本函数是malloc()，在 Linux 上的实现是通过内核的 brk 系统调用。brk()是一个非常简单的系统调用，只是简单地改变 mm_struct 结构的成员变量 brk 的值。

函数定义如下:

```text
NAME
       brk, sbrk - change data segment size
SYNOPSIS
       #include <unistd.h>
       int brk(void *addr);
       void *sbrk(intptr_t increment);
```

???note "brk() 和 sbrk()"
    
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
    ```text
    old: 0x7fffcea72000
    p: 0x7fffcea72000
    new: 0x7fffcea72100
    free: 0x7fffcea72000
    size: 100
    ```
    由于缓冲区在堆，而一旦使printf、getchar等需要使用缓冲区时，就会预先申请0x21000大小的堆块以供程序使用(wsl:ubuntu18.04;gcc (Ubuntu 7.4.0-1ubuntu1~18.04.1) 7.4.0)
    若在`sbrk(0x100)`前`getchar`结果：
    ```c
    #include <stdio.h>
    #include <stdlib.h>
    #include <unistd.h>
    
    int main() {
        int *old = sbrk(0);
        getchar();
        int *p = sbrk(0x100);
        if (p == (void *)(-1) ) {
            perror("sbrk");
            exit(EXIT_FAILURE);
        }
        int *new = sbrk(0);
        new = sbrk(0);
        printf("old: %p\np: %p\nnew: %p\n", old, p, new);
        printf("size: %lx\n", (long)new - (long)old);
        return 0;
     }
     ```
     这时如果使用`brk(old)`会导致将stdio申请的堆块缩掉导致printf访问时报错
     ```
     
#### Mmap映射区域操作相关函数

mmap() 函数将一个文件或者其他对象映射进内存。 文件被映射到多个页（[内存页](https://www.ibm.com/developerworks/cn/linux/l-memmod/index.html)）上，如果文件大小不是所有页的大小之和，最后一个页不被使用的空间将会被清零。 

???note "[怎么理解操作系统中内存管理分页和分段](https://www.zhihu.com/question/50796850)"

    1. 这两个技术都是为了利用和管理好计算机的资源————内存
        
        在分段这个技术还没有出现之前，程序运行是需要从内存中分配出足够多的连续的内存，然后把整个程序装载进去, 如果找不到连续的符合大小的空间就无法将程序装载进去，程序也就无法运行
    
    2. 问题
        
        - 地址空间不隔离：操作程序A的地址时可能会误操作到程序B
        
        - 程序运行时地址不确定： 程序装载进内存的地址是随机的
        
        - 内存利用率低下：若程序A大小10M, 程序B大小70M, 程序C大小30M, 计算机内存共100M则你无法同时运行这三个程序。若A地址0x0~0x9,B地址0x10~0x79,此时若加载C，只能换出程序B到磁盘上，保留程序A。此时有**60M内存无法利用**。
    
    3.计算机科学领域的任何问题都可以通过增加一个间接的**中间层**来解决(例如很多优秀的中间层：Nginx、Redis等等)
    
    4.分段
    
        - 把虚拟地址空间映射到了物理地址空间，并且你写的程序操作的是虚拟地址, 此时不需要连续的物理内存来存放A, 且假设程序A的虚拟地址空间是0x00000000~0x00000099, 映射到的物理地址空间是0x00000600~0x00000699,此时用程序A操作地址0x150，英文此时的地址0x00000150是虚拟的，而虚拟化的操作是在操作系统的掌控中的，所以，操作系统有能力判断，这个虚拟地址0x00000150是有问题的，然后阻止后续的操作。所以，体现出了隔离性。（另一种体现隔离性的方式就是，操作同一个虚拟地址，实际上可能操作的是不同的物理地址）
    
        - 问题
            - 分段机制，映射的是一片连续的物理内存，所以问题3得不到解决
    5.分页
        
        - 分页这个技术仍然是一种虚拟地址空间到物理地址空间映射的机制。但是，粒度更加的小了。单位不是整个程序，而是某个“页”，一段虚拟地址空间组成的某一页映射到一段物理地址空间组成的某一页
        
        - 在基本的分页概念中，我们把程序分成等长的小块。这些小块叫做“页（Page）”，同样内存也被我们分成了和页面同样大小的”页框（Frame）“，一个页可以装到一个页框里。在执行程序的时候我们根据一个页表去查找某个页面在内存的某个页框中，由此完成了逻辑到物理的映射

munmap() 执行相反的操作，删除特定地址区域的对象映射。

函数定义如下：

```text
NAME
       mmap, munmap - map or unmap files or devices into memory

SYNOPSIS
       #include <sys/mman.h>

       void *mmap(void *addr, size_t length, int prot, int flags,
                  int fd, off_t offset);
       int munmap(void *addr, size_t length);

       See NOTES for information on feature test macro requirements.
```

参数：
- start：映射区的开始地址

- length：映射区的长度

- prot：期望的内存保护标志，不能与文件的打开模式冲突。是以下的某个值，可以通过
or 运算合理地组合在一起。Ptmalloc 中主要使用了如下的几个标志:

    - PROT_EXEC //页内容可以被执行，ptmalloc 中没有使用
    
    - PROT_READ //页内容可以被读取，ptmalloc 直接用 mmap 分配内存并立即返回给用户时设置该标志
    
    - PROT_WRITE //页可以被写入，ptmalloc 直接用 mmap 分配内存并立即返回给用户时设置该标志
    
    - PROT_NONE //页不可访问，ptmalloc 用 mmap 向系统“批发”一块内存进行管理时设置该标志
    
- flags：指定映射对象的类型，映射选项和映射页是否可以共享。它的值可以是一个或者多个以下位的组合体

    - MAP_FIXED //使用指定的映射起始地址，如果由 start 和 len 参数指定的内存区重叠于现存的映射空间，重叠部分将会被丢弃。如果指定的起始地址不可用，操作将会失败。并且起始地址必须落在页的边界上。Ptmalloc 在回收从系统中“批发”的内存时设置该标志
    
    - MAP_PRIVATE //建立一个写入时拷贝的私有映射。内存区域的写入不会影响到原文件。这个标志和以上标志是互斥的，只能使用其中一个。Ptmalloc每次调用mmap都设置该标志
    
    - MAP_NORESERVE //不要为这个映射保留交换空间。当交换空间被保留，对映射区修改的可能会得到保证。当交换空间不被保留，同时内存不足，对映射区的修改会引起段违例信号。Ptmalloc 向系统“批发”内存块时设置该标志
    
    - MAP_ANONYMOUS //匿名映射，映射区不与任何文件关联。Ptmalloc 每次调用 mmap 都设置该标志
    
- fd：有效的文件描述词。如果 MAP_ANONYMOUS 被设定，为了兼容问题，其值应为-1

- offset：被映射对象内容的起点。

## 概述

### 内存管理的一般性描述

当不知道程序的每个部分将需要多少内存时， 系统内存空间有限， 而内存需求又是变化的， 这时就需要内存管理程序来负责分配和回收内存。程序的动态性越强， 内存管理就越重要， 内存分配程序的选择也就更重要

#### 内存管理的方法

可用于内存管理的方法有许多种，它们各有好处与不足，不同的内存管理方法有最适用的情形。

##### 1.C风格的内存管理程序

C风格的内存管理程序主要实现**malloc()** 和 **free()**. 内存管理程序主要通过调用brk() 或者 mmap() 进程来添加额外的虚拟内存。

Doug Lea Malloc，ptmalloc，BSD malloc，Hoard，TCMalloc 都属于这一类内存管理程序。

基于 malloc()的内存管理器仍然有很多缺点，不管使用的是哪个分配程序。

- 对于那些需要保持长期存储的程序使用 malloc()来管理内存可能会非常令人失望。

- 如果有大量的不固定的内存引用，经常难以知道它们何时被释放。

- 生存期局限于当前函数的内存非常容易管理，但是对于生存期超出该范围的内存来说，管理内存则困难得多。因为管理内存的问题，很多程序倾向于使用它们自己的内存管理规则。

##### 2.池式内存管理

内存池是一种半内存管理方法。

内存池帮助某些程序进行自动内存管理，这些程序会经历一些特定的阶段，而且每个阶段中都有分配给进程的特定阶段的内存。

例如，很多网络服务器进程都会分配很多针对每个连接的内存——内存的最大生存期限为当前连接的存在期。

Apache 使用了池式内存（pooled memory），将其连接拆分为各个阶段，每个阶段都有自己的内存池。在结束每个阶段时，会一次释放所有内存。

在池式内存管理中，每次内存分配都会指定内存池，从中分配内存。每个内存池都有不同的生存期限。

在 Apache 中，有一个持续时间为服务器存在期的内存池，还有一个持续时间为连接的存在期的内存池，以及一个持续时间为请求的存在期的池，另外还有其他一些内存池。

因此，如果一系列函数不会生成比连接持续时间更长的数据，那么就可以完全从连接池中分配内存，并知道在连接结束时，这些内存会被自动释放。另外，有一些实现允许注册清除函数（cleanup functions），在清除内存池之前，恰好可以调用它，来完成在内存被清理前需要完成的其他所有任务（类似于面向对象中的析构函数）。

使用池式内存分配的优点如下所示：

- 应用程序可以简单地管理内存。

- 内存分配和回收更快，因为每次都是在一个池中完成的。分配可以在 O(1)时间内完成，释放内存池所需时间也差不多（实际上是 O(n)时间，不过在大部分情况下会除以一个大的因数，使其变成 O(1)）。

- 可以预先分配错误处理池（Error-handling pools），以便程序在常规内存被耗尽时仍可以恢复。

- 有非常易于使用的标准实现。

池式内存的缺点是：

- 内存池只适用于操作可以分阶段的程序。

- 内存池通常不能与第三方库很好地合作。

- 如果程序的结构发生变化，则不得不修改内存池，这可能会导致内存管理系统的重新设计。

- 您必须记住需要从哪个池进行分配。另外，如果在这里出错，就很难捕获该内存池。

##### 3. 引用计数

