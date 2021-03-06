
##大端模式和小端模式的起源

关于大端小端名词的由来，有一个有趣的故事，来自于Jonathan Swift的《格利佛游记》：
Lilliput和Blefuscu这两个强国在过去的36个月中一直在苦战。战争的原因：大家都知道，
吃鸡蛋的时候，原始的方法是打破鸡蛋较大的一端，可以那时的皇帝的祖父由于小时侯吃鸡蛋，
按这种方法把手指弄破了，因此他的父亲，就下令，命令所有的子民吃鸡蛋的时候，
必须先打破鸡蛋较小的一端，违令者重罚。然后老百姓对此法令极为反感，期间发生了多次叛乱，
其中一个皇帝因此送命，另一个丢了王位，产生叛乱的原因就是另一个国家Blefuscu的国王大臣煽动起来的，叛乱平息后，就逃到这个帝国避难。据估计，先后几次有11000余人情愿死也不肯去
打破鸡蛋较小的端吃鸡蛋。这个其实讽刺当时英国和法国之间持续的冲突。

Danny Cohen一位网络协议的开创者，第一次使用这两个术语指代字节顺序，后来就被大家广泛接受。

##Big-Endian和Little-Endian的定义如下

1) Little-Endian就是低位字节排放在内存的低地址端，高位字节排放在内存的高地址端。

2) Big-Endian就是高位字节排放在内存的低地址端，低位字节排放在内存的高地址端。

举一个例子，比如数字0x12 34 56 78在内存中的表示形式为：

1)大端模式：

| 低地址 |      |      | 高地址 |
| ------ | :--- | ---- | ------ |
| 0x12   | 0x34 | 0x56 | 0x78   |

2)小端模式：

| 低地址 |      |      | 高地址 |
| ------ | ---- | ---- | ------ |
| 0x78   | 0x56 | 0x34 | 0x12   |

可见，大端模式和字符串的存储模式类似。

3)下面是两个具体例子：

16bit宽的数0x1234在Little-endian模式（以及Big-endian模式）CPU内存中的存放方式（假设从地址0x4000开始存放）为：	

| *内存地址* | 小端模式存放内容 | 大端模式存放内容 |
| :--------- | ------------------ | ------------------ |
| *0x4000*   | 0x34             | 0x12             |
| *0x4001*   | 0x12             | 0x34            |

·32bit宽的数0x12345678在Little-endian模式以及Big-endian模式）CPU内存中的存放方式（假设从地址0x4000开始存放）为：

| *内存地址* | 小端模式存放内容 | 大端模式存放内容 |
| :--------- | ------------------ | ------------------ |
| *0x400*    | 0x78             | 0x12             |
| *0x4001*   | 0x56             | 0x34             |
| *0x4002*   | 0x34             | 0x56             |
| *0x4003*   | 0x12             | 0x78             |

 4)大端小端没有谁优谁劣，各自优势便是对方劣势：

小端模式 ：强制转换数据不需要调整字节内容，1、2、4字节的存储方式一样。

大端模式 ：符号位的判定固定为第一个字节，容易判断正负。

##数组在大端小端情况下的存储

以unsigned int value = 0x12345678为例，分别看看在两种字节序下其存储情况，我们可以用unsigned char buf[4]来表示value：

　　
Big-Endian: 低地址存放高位，如下：

| 高地址        |        |
| --------------- | ------ |
| buf[3] (0x78) | 低位 |
| buf[2] (0x56) |        |
| buf[1] (0x34) |        |
| buf[0] (0x12) | 高位 |
| 低地址    |        |

Little-Endian: 低地址存放低位，如下：

| 高地址    |        |
| --------------- | ------ |
| buf[3] (0x12) | 高位 |
| buf[2] (0x34) |        |
| buf[1] (0x56) |        |
| buf[0] (0x78) | 低位 |
| 低地址    |        |

##为什么会有大小端模式之分

这是因为在计算机系统中，我们是以字节为单位的，每个地址单元都对应着一个字节，一个字节为8bit。
但是在C语言中除了8bit的char之外，还有16bit的short型，32bit的long型（要看具体的编译器），
另外，对于位数大于8位的处理器，例如16位或者32位的处理器，由于寄存器宽度大于一个字节，
那么必然存在着一个如果将多个字节安排的问题。因此就导致了大端存储模式和小端存储模式。
例如一个16bit的short型x，在内存中的地址为0x0010，x的值为0x1122，那么0x11为高字节，
0x22为低字节。对于大端模式，就将0x11放在低地址中，即0x0010中，0x22放在高地址中，
即0x0011中。小端模式，刚好相反。我们常用的X86结构是小端模式，而KEIL C51则为大端模式。
很多的ARM，DSP都为小端模式。有些ARM处理器还可以由硬件来选择是大端模式还是小端模式。


参考资料：

<https://blog.csdn.net/ce123_zhouwei/article/details/6971544>
<http://radishes.top/2018/10/07/2018-10-07-小端模式和大端模式>