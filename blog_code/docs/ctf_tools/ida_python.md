
IDApython常见函数

介绍转载自https://www.cnblogs.com/0xHack/p/9399321.html,例子会持续更新补充。

IDA Python API::https://www.hex-rays.com/products/ida/support/idapython_docs/



- 2019.7.25 更新例子: *CTF 2019 Matr1x

## IDApython

因为网上对于IDApython的介绍太少，所以在这里列举了一些常用函数：

>ScreenEA()

获取 IDA 调试窗口中，光标指向代码的地址。通过这个函数，我们就能够从一个已知 的点运行我们的脚本。

>GetInputFileMD5()

返回 IDA 加载的二进制文件的 MD5 值，通过这个值能够判断一个文件的不同版本是否 有改变。

>FirstSeg()

访问程序中的第一个段。

>NextSeg()

访问下一个段，如果没有就返回 BADADDR。

>SegByName( string SegmentName )

通过段名字返回段基址，举个例子，如果调用.text 作为参数，就会返回程序中代码段的开始位置。

>SegEnd( long Address )

通过段内的某个地址，获得段尾的地址。

>SegStart( long Address )

通过段内的某个地址，获得段头的地址。

>SegName( long Address )

通过段内的某个地址，获得段名。

>Segments()

返回目标程序中的所有段的开始地址。

>Functions( long StartAddress, long EndAddress )

返回一个列表，包含了从 StartAddress 到 EndAddress 之间的所有函数。

>Chunks( long FunctionAddress )

返回一个列表，包含了函数片段。每个列表项都是一个元组（chunk start, chunk end）

>LocByName( string FunctionName )

通过函数名返回函数的地址。

>GetFuncOffset( long Address )

通过任意一个地址，然后得到这个地址所属的函数名，以及给定地址和函数的相对位移。 然后把这些信息组成字符串以"名字+位移"的形式返回。

>GetFunctionName( long Address )

通过一个地址，返回这个地址所属的函数。

>CodeRefsTo( long Address, bool Flow )

返回一个列表，告诉我们 Address 处代码被什么地方引用了，Flow 告诉 IDAPython 是否要 跟踪这些代码。

>CodeRefsFrom( long Address, bool Flow )

返回一个列表，告诉我们 Address 地址上的代码引用何处的代码。

>DataRefsTo( long Address )

返回一个列表，告诉我们 Address 处数据被什么地方引用了。常用于跟踪全局变量。

>DataRefsFrom( long Address )

返回一个列表，告诉我们 Address 地址上的代码引用何处的数据。

>Heads(start=None, end=None)

得到两个地址之间所有的元素

>GetDisasm(addr)

得到addr的反汇编语句

>GetMnem(addr)

得到addr地址的操作码

>BADADDR

验证是不是错误地址

>GetOpnd(addr，long n)

第一个参数是地址，第二个long n是操作数索引。第一个操作数是0和第二个是1。

>idaapi.decode_insn(ea) 

得到当前地址指令的长度

>idc.FindFuncEnd(ea) 

找到当前地址的函数结束地址

>Entries() 

入口点信息

>Structs() 

遍历结构体

>StructMembers(sid) 

遍历结构体成员

>DecodePrecedingInstruction(ea) 

>DecodePreviousInstruction(ea) 

>DecodeInstruction(ea)

获取指令结构

>Strings(object) 

获取字符串

>GetIdbDir()

获取idb目录

>GetRegisterList()

获取寄存器名表

>GetInstructionList

获取汇编指令表

>atoa(ea)

获取所在段

>Jump(ea)

移动光标

>Eval(expr)

计算表达式

>Exec(command) 

执行命令行

>MakeCode(ea) 

分析代码区

>MakeNameEx(ea, name, flags)

重命名地址

>MakeArray(ea, nitems) 

创建数组

>MakeStr(ea, endea) 

创建字符串

>MakeData(ea, flags, size, tid)

创建数据

>MakeByte(ea)
>
>MakeWord(ea)
>
>MakeDWord(ea)
>
>MakeQWord(ea)
>
>MakeOWord(ea)
>
>MakeYWord(ea)
>
>MakeFlot(ea)
>
>MakeDouble(ea)
>
>MakePackReal(ea)
>
>MakeTbyte(ea)
>
>MakeStructEx(ea)
>
>MakeCustomDataEx(ea)

>PatchByte(ea, value)
>
>PatchWord(ea, value)
>
>PatchDword(ea, value)
>
>PatchByte(ea, value)
>
>PatchByte(ea, value)
>

修改程序字节

>Byte(ea) 

将地址解释为Byte

>Word(ea)
>
>DWord(ea)
>
>QWord(ea)

>GetFloat(ea)
>
>GetDouble(ea)
>
>GetString(ea, length = -1, strtype = ASCSTR_C) 

获取字符串

>GetCurrentLine() 

获取光标所在行反汇编

>ItemSize(ea) 

获取指令或数据长度

>FindText(ea, flag, y, x, searchstr)

查找文本

>FindBinary(ea, flag, searchstr, radix=16) 

查找16进制

>GetEntryPointQty() 

获取入口点个数

>GetEntryOrdinal(index) 

获取入口点地址

>GetEntryName(ordinal) 

获得入口名

!!!note "得到当前地址所在函数的数据"
    ```python
    idc.GetFunctionAttr(ea, attr)
    
    FUNCATTR_START = 0 # function start address
    FUNCATTR_END = 4 # function end address
    FUNCATTR_FLAGS = 8 # function flags
    FUNCATTR_FRAME = 10 # function frame id
    FUNCATTR_FRSIZE = 14 # size of local variables
    FUNCATTR_FRREGS = 18 # size of saved registers area
    FUNCATTR_ARGSIZE = 20 # number of bytes purged from the stack
    FUNCATTR_FPD = 24 # frame pointer delta
    FUNCATTR_COLOR = 28 # function color code
    FUNCATTR_OWNER = 10 # chunk owner (valid only for tail chunks)
    FUNCATTR_REFQTY = 14 # number of chunk parents (valid only for tail chunks)
    ```


!!!note

	这个类包含了我们在创建调试脚本时，会经常用到的几个调试事件处理函数

    ```python
    class DbgHook(DBG_Hooks):
    \# Event handler for when the process starts
    def dbg_process_start(self, pid, tid, ea, name, base, size) 
        return
    \# Event handler for process exit
    def dbg_process_exit(self, pid, tid, ea, code): 
        return
    \# Event handler for when a shared library gets loaded def dbg_library_load(self, pid, tid, ea, name, base, size):
        return
    \# Breakpoint handler
    def dbg_bpt(self, tid, ea): 
        return
    ```
    安装 hook 的方式如下:
    ```python2
	debugger = DbgHook() 
	debugger.hook()
    ```
    现在运行调试器，hook 会捕捉所有的调试事件，这样就能非常精确的控制 IDA 调试器。 下面的函数在调试的时候非常有用:

    > AddBpt( long Address )
    
    在指定的地点设置软件断点。
    
    > GetBptQty()
    
    返回当前设置的断点数量。
    
    >GetRegValue( string Register )
    
    通过寄存器名获得寄存器值。
    
    >SetRegValue( long Value, string Register )
    
    通过寄存器名设置寄存器值

## 例子

### Xman-2018-re0

```python
import ida_bytes
buf = map(ord, ida_bytes.get_bytes(0x600B00, 182))
buf = map(lambda x: x ^ 0xC, buf)
ida_bytes.patch_bytes(0x600B00, str(bytearray(buf)))
```

### KCTF 2019 Q1 第七题

```python
bg = 0x00401000
end = 0x004BBE00
addr = bg
 
def patch_nop(begin,end):
    while(end>begin):
        PatchByte(begin,0x90)
        begin=begin+1
def next_instr(addr):
    return addr+ItemSize(addr)
while(addr<end):
    next =next_instr(addr)
    MakeCode(next)
    if 'j' in GetMnem(addr) and 'j' in GetMnem(next) :
        if GetOperandValue(addr,0) == GetOperandValue(next,0):
            print 'jmp %08x'%addr
            dest_addr = GetOperandValue(addr,0)
            patch_nop(addr,dest_addr)
            addr=dest_addr
            MakeCode(addr)
    if 'clc' == GetMnem(addr) and 'jnb' in GetMnem(next) :
            print 'clc %08x'%addr
            dest_addr = GetOperandValue(next,0)
            patch_nop(addr,dest_addr)
            addr=dest_addr
            MakeCode(addr)
    if 'stc' == GetMnem(addr) and 'jb' in GetMnem(next) :
            print 'clc %08x'%addr
            dest_addr = GetOperandValue(next,0)
            patch_nop(addr,dest_addr)
            addr=dest_addr
            MakeCode(addr)
    if 'call' in GetMnem(addr):
        dest_addr = GetOperandValue(addr,0)
        idc.del_items(next_instr(addr))
        MakeCode(dest_addr)
        if "add     esp, 4" == GetDisasm(dest_addr):
            print 'call %08x'%addr
            dest_addr=next_instr(dest_addr)
            patch_nop(addr,dest_addr)
            addr=dest_addr
            MakeCode(addr)
    addr = next_instr(addr)
    MakeCode(addr)
```

### *CTF 2019 Matr1x

题目：[Matr1x](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/StarCTF-2019/Matr1x.zip)

```python
bg = 0x00002620
end = 0x00011320
addr = bg
index = 0


def patch_nop(begin, end):
	while end > begin:
		PatchByte(begin, 0x90)
		begin += 1


def next_instr(addr):
	return addr + ItemSize(addr)

def patch_global(addr, eax):
	flag = GetMnem(addr)
	ebx = GetOperandValue(addr, 1)
	if flag == "xor":
		eax ^= ebx
	elif flag == "shl":
		eax <<= ebx
	elif flag == "sub":
		eax -= ebx
	elif flag == "add":
		eax += ebx
	elif flag == "and":
		eax &= ebx
	elif flag == "shr":
		eax >>= ebx
	elif flag == "or":
		eax |= ebx

	return eax & 0xFFFFFFFF

OperandValue = ['or','shr','and','add','sub','shl','xor']
Register = ['ebx','ecx','edx','eax']
Patch_jnz = ['xor','sub']

dest_addr = 0
_addr = 0
while addr < end:
	next = next_instr(addr)
	MakeCode(next)
	if GetMnem(addr) in Patch_jnz and 'jnz' in GetMnem(next):
		if GetOperandValue(addr, 0) == GetOperandValue(addr, 1):
			print("sucess_patch_jnz: %x" % addr)
			dest_addr = next_instr(next)
			patch_nop(next, dest_addr)
			

	if "call    $+5" == GetDisasm(addr):
		if "pop     eax" == GetDisasm(next):
			dest_addr = addr
			for i in range(4):
				dest_addr = next_instr(dest_addr)
				MakeCode(dest_addr)	
			if "jmp" in GetMnem(dest_addr):
				print("sucess_patch_call: %x" % addr)
				patch_nop(addr, dest_addr)
				PatchByte(dest_addr, 0xE8)
			

	if "xchg" in GetMnem(addr):
		if GetOperandValue(addr, 1) == GetOperandValue(addr, 0):
			print("sucess_patch_xchg: %x" % addr)
			patch_nop(addr, next)
			

	if "jmp     $+5" == GetDisasm(addr):
		if "leave" in GetMnem(next):
			patch_nop(addr, next)
			print("sucess_patch_jmp: %x" % addr)
			next = next_instr(next)
			addr = next
			next = next_instr(next)
			next = next_instr(next)
			patch_nop(addr,next)
			PatchByte(addr,0xC3)

	if "[ebp" in GetDisasm(addr) and GetOperandValue(addr, 1) > 0x10000 and GetOperandValue(addr,1) < (0xFFFFFFFF - 0x10000):
		patch_nop(addr, next)
		print("sucess_patch_ebp: %x" % addr)

	if 0x0001329C >= GetOperandValue(addr, 1) >= 0x00013280:
		print("sucess: %x" % addr)
		eax = Dword(GetOperandValue(addr, 1))	
		_addr = addr
		Reg = GetOperandValue(addr,0)
		while GetMnem(next) in OperandValue:
			eax = patch_global(next, eax)
			_addr = next
			next = next_instr(next)
			MakeCode(_addr)
			MakeCode(next)
			if GetOperandValue(_addr,0) != GetOperandValue(next,0):
				break
			elif GetOpnd(next,1) in Register:
				break
		if GetOpnd(_addr, 0) == GetOpnd(_addr, 1):
			Reg = -1
		if Reg != -1:
			addr = next_instr(addr)
			patch_nop(addr,next)
		if Reg == 3:
			PatchByte(addr,0xBB)
			PatchDword(addr + 1,eax)
		elif Reg == 2:
			PatchByte(addr,0xBA)
			PatchDword(addr + 1,eax)
		elif Reg == 1:
			PatchByte(addr,0xB9)
			PatchDword(addr + 1,eax)
		elif Reg == 0:
			PatchByte(addr,0xB8)
			PatchDword(addr + 1,eax)
	addr = next_instr(addr)
	MakeCode(addr)
```

