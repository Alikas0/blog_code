## first_fit

### 代码
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main()
{
	fprintf(stderr, "This file doesn't demonstrate an attack, but shows the nature of glibc's allocator.\n");
	fprintf(stderr, "glibc uses a first-fit algorithm to select a free chunk.\n");
	fprintf(stderr, "If a chunk is free and large enough, malloc will select this chunk.\n");
	fprintf(stderr, "This can be exploited in a use-after-free situation.\n");

	fprintf(stderr, "Allocating 2 buffers. They can be large, don't have to be fastbin.\n");
	char* a = malloc(0x512);
	char* b = malloc(0x256);
	char* c;

	fprintf(stderr, "1st malloc(0x512): %p\n", a);
	fprintf(stderr, "2nd malloc(0x256): %p\n", b);
	fprintf(stderr, "we could continue mallocing here...\n");
	fprintf(stderr, "now let's put a string at a that we can read later \"this is A!\"\n");
	strcpy(a, "this is A!");
	fprintf(stderr, "first allocation %p points to %s\n", a, a);

	fprintf(stderr, "Freeing the first one...\n");
	free(a);

	fprintf(stderr, "We don't need to free anything again. As long as we allocate smaller than 0x512, it will end up at %p\n", a);

	fprintf(stderr, "So, let's allocate 0x500 bytes\n");
	c = malloc(506);
	fprintf(stderr, "3rd malloc(0x500): %p\n", c);
	fprintf(stderr, "And put a different string here, \"this is C!\"\n");
	strcpy(c, "this is C!");
	fprintf(stderr, "3rd allocation %p points to %s\n", c, c);
	fprintf(stderr, "first allocation %p points to %s\n", a, a);
	fprintf(stderr, "If we reuse the first allocation, it now holds the data from the third allocation.\n");
}
```

### 编译运行

```text
➜  uaf_first_fit gcc first_fit.c -o first_fit
➜  uaf_first_fit ./first_fit
This file doesn't demonstrate an attack, but shows the nature of glibc's allocator.
glibc uses a first-fit algorithm to select a free chunk.
If a chunk is free and large enough, malloc will select this chunk.
This can be exploited in a use-after-free situation.
Allocating 2 buffers. They can be large, don't have to be fastbin.
1st malloc(0x512): 0x7fffcf3ab260
2nd malloc(0x256): 0x7fffcf3ab780
we could continue mallocing here...
now let's put a string at a that we can read later "this is A!"
first allocation 0x7fffcf3ab260 points to this is A!
Freeing the first one...
We don't need to free anything again. As long as we allocate smaller than 0x512, it will end up at 0x7fffcf3ab260
So, let's allocate 0x500 bytes
3rd malloc(0x500): 0x7fffcf3ab260
And put a different string here, "this is C!"
3rd allocation 0x7fffcf3ab260 points to this is C!
first allocation 0x7fffcf3ab260 points to this is C!
If we reuse the first allocation, it now holds the data from the third allocation.
```

### 翻译

这个程序并不展示如何攻击, 而是展示glibc分配器的性质。

glibc使用`first-fit`算法来选择空闲的块（`free chunk`）

如果一个块是空闲的且足够大， 则`malloc`将选择该块。

这种机制就可以在`use-after-free`(简称`uaf`)的情况下使用该漏洞

先分配两个buffer， 可以分配大一点， 是不是fastbin也无所谓

```text
1st malloc(0x512): 0x7fffcf3ab260
2nd malloc(0x256): 0x7fffcf3ab780
```

我们可以继续在这里分配.....

现在将一个字符串“This is a”放在a处，以便稍后阅读

我们使第一个分配的内存空间的地址 0x662420 指向这个字符串”this is A!”.

然后free掉这块内存

我们也不需要free其他内存块了.之后只要我们用malloc申请的内存大小小于第一块的512字节,都会给我们返回第一个内存块开始的地址 0x662420.

ok,我们现在开始用malloc申请500个字节试试.

```text
3rd malloc(500): 0x662420
```

然后我们在这个地方放置一个字符串 “this is C!”

第三个返回的内存块的地址 0x662420 指向了这个字符串 “this is C!”.

第一个返回的内存块的地址也指向这个字符串!

## UAF

### uaf原理

uaf漏洞产生的主要原因是释放了一个堆块后，并没有将该指针置为NULL，这样导致该指针处于悬空的状态，同样被释放的内存如果被恶意构造数据，就有可能会被利用。

```c
#include <stdio.h>
#include <stdlib.h>

typedef void (*func_ptr)(char *);

void evil_fuc(char command[]) {
    system(command);
}

void echo(char content[]) {
    printf("%s", content);
}

int main() {
    func_ptr *p1 = (int *) malloc(4 * sizeof(int));
    printf("malloc addr: %p\n", p1);
    p1[3] = echo;
    p1[3]("hello world\n");
    free(p1);  // 在这里free了p1,但并未将p1置空,导致后续可以再使用p1指针
    p1[3]("hello again\n"); // p1指针未被置空,虽然free了,但仍可使用.
    func_ptr *p2 = (int *) malloc(4 * sizeof(int)); // malloc在free一块内存后,再次申请同样大小的指针会把刚刚释放的内存分配出来.
    printf("malloc addr: %p\n", p2);
    printf("malloc addr: %p\n", p1); // p2与p1指针指向的内存为同一地址
    p2[3] = evil_fuc; // 在这里将p1指针里面保存的echo函数指针覆盖成为了evil_func指针.
    p1[3]("whoami");
    return 0;
}
```
这段代码在32位系统下执行。通过这段代码可以大概将uaf的利用过程小结为以下过程：

1、申请一段空间，并将其释放，释放后并不将指针置为空，因此这个指针仍然可以使用，把这个指针简称为p1。

2、申请空间p2，由于malloc分配的过程使得p2指向的空间为刚刚释放的p1指针的空间，构造恶意的数据将这段内存空间布局好，即覆盖了p1中的数据。

3、利用p1，一般多有一个函数指针，由于之前已使用p2将p1中的数据给覆盖了，所以此时的数据既是我们可控制的，即可能存在劫持函数流的情况。