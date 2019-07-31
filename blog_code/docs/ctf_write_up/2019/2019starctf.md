赛后复现，学习学习！

## yy

题目：[yy](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/2019/StarCTF/yy.zip)

这题真是调和猜出来的0.0.....

根据调试和猜测，从yylex()函数中的yysec[]数组可以猜出合法字符为。

```c
yyaccept = []

for i in range(len(yyec)):
   if yyec[i] != 0x01 :
     yyaccept.append(i)
   print("".join(map(chr,yyaccept)))

yyaccept = "*0123456789CFT_abcdefghijklmnopqrstuvwxyz{}"
```

猜测为：*CTF{0123456789_abcdefghijklmnopqrstuvwxyz}

继续调试可发现,程序会根据输入字符从box[]中取出对应的字符替换buffer中的字符，而`“_”`则是调用aes_cbc_encrypt进行加密,并更新buffer。最终和cmp进行对比：

```c
 switch ( (unsigned int)&savedregs )
        {
          case 2u:
            if ( !memcmp(result, cmp, 0xA0uLL) )
              puts("Congratulations!");
            else
              puts("try again!");
            break;
          case 3u:
            pc = 0;
            buffer = *(_QWORD *)&append;
            qword_5556FE90A3E8 = qword_5556FE90A2B8;
            break;
          case 7u:
            pc = 0;
            buffer = *(_QWORD *)&append;
            qword_5556FE90A3E8 = qword_5556FE90A2B8;
            break;
          case 8u:
            aes_cbc_encrypt(&buffer);
            break;
          case 0xBu:
            aes_cbc_encrypt(&buffer);
            break;
          case 0xFu:
            v1 = pc++;
            *((_BYTE *)&buffer + v1) = box[0];
            break;
          case 0x10u:
            v2 = pc++;
            *((_BYTE *)&buffer + v2) = box[1];
            break;
          case 0x11u:
            v3 = pc++;
            *((_BYTE *)&buffer + v3) = box[2];
            break;
          case 0x12u:
            v4 = pc++;
            *((_BYTE *)&buffer + v4) = box[3];
            break;
          case 0x13u:
            v5 = pc++;
            *((_BYTE *)&buffer + v5) = box[4];
            break;
          case 0x14u:
            v6 = pc++;
            *((_BYTE *)&buffer + v6) = box[5];
            break;
          case 0x15u:
            v7 = pc++;
            *((_BYTE *)&buffer + v7) = box[6];
            break;
          case 0x16u:
            v8 = pc++;
            *((_BYTE *)&buffer + v8) = box[7];
            break;
          case 0x17u:
            v9 = pc++;
            *((_BYTE *)&buffer + v9) = box[8];
            break;
          case 0x18u:
            v10 = pc++;
            *((_BYTE *)&buffer + v10) = box[9];
            break;
          case 0x19u:
            v11 = pc++;
            *((_BYTE *)&buffer + v11) = box[10];
            break;
          case 0x1Au:
            v12 = pc++;
            *((_BYTE *)&buffer + v12) = box[11];
            break;
          case 0x1Bu:
            v13 = pc++;
            *((_BYTE *)&buffer + v13) = box[12];
            break;
          case 0x1Cu:
            v14 = pc++;
            *((_BYTE *)&buffer + v14) = box[13];
            break;
          case 0x1Du:
            v15 = pc++;
            *((_BYTE *)&buffer + v15) = box[14];
            break;
          case 0x1Eu:
            v16 = pc++;
            *((_BYTE *)&buffer + v16) = box[15];
            break;
          case 0x1Fu:
            v17 = pc++;
            *((_BYTE *)&buffer + v17) = box[16];
            break;
          case 0x20u:
            v18 = pc++;
            *((_BYTE *)&buffer + v18) = box[17];
            break;
          case 0x21u:
            v19 = pc++;
            *((_BYTE *)&buffer + v19) = box[18];
            break;
          case 0x22u:
            v20 = pc++;
            *((_BYTE *)&buffer + v20) = box[19];
            break;
          case 0x23u:
            v21 = pc++;
            *((_BYTE *)&buffer + v21) = box[20];
            break;
          case 0x24u:
            v22 = pc++;
            *((_BYTE *)&buffer + v22) = box[21];
            break;
          case 0x25u:
            v23 = pc++;
            *((_BYTE *)&buffer + v23) = box[22];
            break;
          case 0x26u:
            v24 = pc++;
            *((_BYTE *)&buffer + v24) = box[23];
            break;
          case 0x27u:
            v25 = pc++;
            *((_BYTE *)&buffer + v25) = box[24];
            break;
          case 0x28u:
            v26 = pc++;
            *((_BYTE *)&buffer + v26) = box[25];
            break;
          case 0x29u:
            v27 = pc++;
            *((_BYTE *)&buffer + v27) = box[26];
            break;
          case 0x2Au:
            v28 = pc++;
            *((_BYTE *)&buffer + v28) = box[27];
            break;
          case 0x2Bu:
            v29 = pc++;
            *((_BYTE *)&buffer + v29) = box[28];
            break;
          case 0x2Cu:
            v30 = pc++;
            *((_BYTE *)&buffer + v30) = box[29];
            break;
          case 0x2Du:
            v31 = pc++;
            *((_BYTE *)&buffer + v31) = box[30];
            break;
          case 0x2Eu:
            v32 = pc++;
            *((_BYTE *)&buffer + v32) = box[31];
            break;
          case 0x2Fu:
            v33 = pc++;
            *((_BYTE *)&buffer + v33) = box[32];
            break;
          case 0x30u:
            v34 = pc++;
            *((_BYTE *)&buffer + v34) = box[33];
            break;
          case 0x31u:
            v35 = pc++;
            *((_BYTE *)&buffer + v35) = box[34];
            break;
          case 0x32u:
            v36 = pc++;
            *((_BYTE *)&buffer + v36) = box[35];
            break;
          default:
            break;
        }
```

则解密脚本如下：

```c
from Crypto.Cipher import AES

key = b"\x2B\x7E\x15\x16\x28\xAE\xD2\xA6\xAB\xF7\x15\x88\x09\xCF\x4F\x3C"

# print(len(key))

cmp = [0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
       0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0xAE, 0x46, 0x14, 0xF8,
       0x2A, 0x40, 0xCF, 0x50, 0x31, 0xD3, 0xFE, 0x04, 0x8C, 0x06,
       0x12, 0x12, 0x23, 0xFA, 0xC7, 0x26, 0xE8, 0x61, 0xD9, 0xC3,
       0xA9, 0x3C, 0x45, 0x70, 0x1A, 0xC7, 0xF0, 0x3D, 0xDF, 0xBE,
       0xBC, 0x16, 0xAB, 0x6E, 0x37, 0xAC, 0x14, 0x8B, 0x9C, 0x94,
       0xF7, 0x5D, 0x62, 0x78, 0xFC, 0x16, 0x98, 0x1D, 0xB2, 0x31,
       0xD3, 0x5A, 0xDC, 0x3A, 0x60, 0x86, 0x9A, 0xCA, 0x7B, 0xA3,
       0xB5, 0xD5, 0xF1, 0xB2, 0xD9, 0xFF, 0xD2, 0x09, 0xD4, 0x77,
       0xD7, 0x3D, 0xC0, 0x56, 0x19, 0x02, 0xB6, 0x9B, 0x42, 0x6C,
       0xE8, 0xA2, 0x77, 0xE3, 0x99, 0xAC, 0x32, 0x40, 0x91, 0xA9,
       0x2A, 0x86, 0xF3, 0xFA, 0x47, 0x3C, 0xC3, 0x5C, 0x41, 0x9B,
       0xE8, 0x05, 0x07, 0xD0, 0xD4, 0x30, 0x5A, 0x9E, 0x8D, 0x52,
       0x9B, 0xA3, 0xFB, 0xAD, 0xB6, 0x44, 0x3F, 0x72, 0x83, 0x9C,
       0x22, 0x77, 0xFE, 0x48, 0xFE, 0x86, 0x84, 0x12, 0x00, 0x4E,
       0xED, 0xFF, 0xAC, 0x44, 0x19, 0x23, 0x84, 0x1F, 0x12, 0xCA]

append = [0x61, 0x24, 0x25, 0x86, 0x31, 0xAB, 0x6E, 0xAF, 0xB1, 0x14,
          0xFE, 0x76, 0x78, 0x3D, 0x1E, 0xFF]

box = {0x82:"a", 0x05:"b", 0x86:"c", 0x8A:"d", 0x0B:'e', 0x11:'f', 0x96:'g', 0x1D:'h', 0x27:'i', 0xA9:'j',
       0x2B:'k', 0xB1:'l', 0xF3:'m', 0x5E:'n', 0x37:'o', 0x38:'p', 0xC2:'q', 0x47:'r', 0x4E:'s', 0x4F:'t',
       0xD6:'u', 0x58:'v', 0xDE:'w', 0xE2:'x', 0xE5:'y', 0xE6:'z', 0x67:'0', 0x6B:'1', 0xEC:'2', 0xED:'3',
       0x6F:'4', 0xF2:'5', 0x73:'6', 0xF5:'7', 0x77:'8', 0x7F:'9'}

Cipher = b""

for i in cmp:
    Cipher += bytes([i])

obj = AES.new(key, AES.MODE_CBC,key)
plain = obj.decrypt(Cipher)


flag = ""
index = 0

for i in plain:
  try:
    flag += box[i]
    index += 1
  except KeyError:
  	if flag[index-1] != "_":
              flag += '_'
              index += 1

print(flag)
#yy_funct10n_1s_h4rd_and_n0_n33d_to_r3v3rs3
```

由于append中有可转化字符，所以打印出来后手动除去hhhhh........

最终flag如下：

*CTF{yy_funct10n_1s_h4rd_and_n0_n33d_to_r3v3rs3}

## fanoGo

题目：[fanoGo](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/2019/StarCTF/fanGo.zip)

本题一路连猜带蒙。。。

根据输出`Say something:`找到主要函数.......看不懂，开始调试：

输入后会经过`fano___Fano__Decode`函数进行解码(为什么是解码名字说的很清楚hhh),解码后长度为0x15A,由该语句确认：

```c
if ( e._type == (runtime__type_0 *)0x15A )
```

后进入runtime_eqstring()函数进行对比,汇编代码如下0.0：

```assembly
.text:0000000000456B83                 cmp     rbx, 40h
.text:0000000000456B87                 jb      short loc_456BC6
.text:0000000000456B89                 vmovdqu ymm0, ymmword ptr [rsi]
.text:0000000000456B8D                 vmovdqu ymm1, ymmword ptr [rdi]
.text:0000000000456B91                 vmovdqu ymm2, ymmword ptr [rsi+20h]
.text:0000000000456B96                 vmovdqu ymm3, ymmword ptr [rdi+20h]
.text:0000000000456B9B                 vpcmpeqb ymm4, ymm0, ymm1
.text:0000000000456B9F                 vpcmpeqb ymm5, ymm3, ymm2
.text:0000000000456BA3                 vpand   ymm6, ymm5, ymm4
.text:0000000000456BA7                 vpmovmskb edx, ymm6
.text:0000000000456BAB                 add     rsi, 40h
.text:0000000000456BAF                 add     rdi, 40h
.text:0000000000456BB3                 sub     rbx, 40h
.text:0000000000456BB7                 cmp     edx, 0FFFFFFFFh
.text:0000000000456BBD                 jz      short loc_456B83
```

而调试着可以发现对比对象就是这串：

```text
"If you cannot read all your books...fondle them---peer into them, let them fall open where they w"
                       "ill, read from the first sentence that arrests the eye, set them back on the shelves with your ow"
                       "n hands, arrange them on your own plan so that you at least know where they are. Let them be your"
                       " friends; let them, at any rate, be your acquaintances."
```

尝试搜索Encode函数,还真有hhhhhh，patch上去，将上面那串进行编码就得到我们的输入。

![](https://raw.githubusercontent.com/Alikas0/files/master/img/20190714132723.png)

脚本：

```python
from pwn import *

exp = process("./fanoGo")

# cmp = "\x49\x66\x20\x79\x6F\x75\x20\x63\x61\x6E\x6E\x6F\x74\x20\x72\x65\x61\x64\x20\x61\x6C\x6C\x20\x79\x6F\x75\x72\x20\x62\x6F\x6F\x6B\x73\x2E\x2E\x2E\x66\x6F\x6E\x64\x6C\x65\x20\x74\x68\x65\x6D\x2D\x2D\x2D\x70\x65\x65\x72\x20\x69\x6E\x74\x6F\x20\x74\x68\x65\x6D\x2C\x20\x6C\x65\x74\x20\x74\x68\x65\x6D\x20\x66\x61\x6C\x6C\x20\x6F\x70\x65\x6E\x20\x77\x68\x65\x72\x65\x20\x74\x68\x65\x79\x20\x77\x69\x6C\x6C\x2C\x20\x72\x65\x61\x64\x20\x66\x72\x6F\x6D\x20\x74\x68\x65\x20\x66\x69\x72\x73\x74\x20\x73\x65\x6E\x74\x65\x6E\x63\x65\x20\x74\x68\x61\x74\x20\x61\x72\x72\x65\x73\x74\x73\x20\x74\x68\x65\x20\x65\x79\x65\x2C\x20\x73\x65\x74\x20\x74\x68\x65\x6D\x20\x62\x61\x63\x6B\x20\x6F\x6E\x20\x74\x68\x65\x20\x73\x68\x65\x6C\x76\x65\x73\x20\x77\x69\x74\x68\x20\x79\x6F\x75\x72\x20\x6F\x77\x6E\x20\x68\x61\x6E\x64\x73\x2C\x20\x61\x72\x72\x61\x6E\x67\x65\x20\x74\x68\x65\x6D\x20\x6F\x6E\x20\x79\x6F\x75\x72\x20\x6F\x77\x6E\x20\x70\x6C\x61\x6E\x20\x73\x6F\x20\x74\x68\x61\x74\x20\x79\x6F\x75\x20\x61\x74\x20\x6C\x65\x61\x73\x74\x20\x6B\x6E\x6F\x77\x20\x77\x68\x65\x72\x65\x20\x74\x68\x65\x79\x20\x61\x72\x65\x2E\x20\x4C\x65\x74\x20\x74\x68\x65\x6D\x20\x62\x65\x20\x79\x6F\x75\x72\x20\x66\x72\x69\x65\x6E\x64\x73\x3B\x20\x6C\x65\x74\x20\x74\x68\x65\x6D\x2C\x20\x61\x74\x20\x61\x6E\x79\x20\x72\x61\x74\x65\x2C\x20\x62\x65\x20\x79\x6F\x75\x72\x20\x61\x63\x71\x75\x61\x69\x6E\x74\x61\x6E\x63\x65\x73\x2E\x00"

cmp = "\x2B\x60\xC3\xBE\xC2\xB7\xC2\x82\xC2\x89\xC3\x95\x5B\xC2\x87\x2A\x69\x13\xC2\x96\x51\xC3\xBD\x6F\x32\x28\x5A\xC3\x92\x74\xC2\x94\xC2\x94\xC2\x95\xC2\x96\xC2\xA4\xC3\x8A\xC2\xA3\xC3\x8E\xC2\xB3\x24\x24\x24\xC2\xBA\xC2\xAE\x46\x2B\xC2\xAC\x3C\xC3\xAB\x32\x23\x2A\xC3\xB0\xC3\xB3\xC2\xAC\xC3\x85\xC2\x87\x2C\xC2\xA3\x6B\xC2\xAD\x0F\xC3\x87\x5C\xC2\xA8\xC3\xB3\xC2\xAF\xC3\xA1\xC3\xB9\x12\xC3\x8A\x44\x72\xC2\xA6\xC2\x91\x66\x6D\x31\xC3\xA7\x51\x64\x67\x78\x75\x6B\xC2\x96\xC2\x91\x51\xC3\xA7\x3E\x13\xC3\x8E\x57\x7B\x47\xC2\x9D\x45\x7F\x29\x11\xC3\x95\xC3\xA1\xC3\xA7\x59\xC2\x8A\x06\xC2\x8C\xC2\x91\xC2\xB5\x0F\x3A\xC2\x8E\xC2\xBA\xC3\x8B\xC3\xAA\xC3\xA8\xC3\xBC\xC2\x8E\x71\xC3\xBD\x6F\x32\x36\xC3\xB9\x42\xC3\xA7\x49\xC3\x92\x22\x79\xC3\x89\xC3\x93\x54\x79\xC3\x96\x63\x6A\x1F\xC3\x96\xC3\xB3\x23\x6F\xC2\x94\x37\xC2\x94\xC3\xA8\x76\xC3\x83\xC3\x8E\x7C\x3F\xC2\xAD\xC3\xA0\xC2\x9F\x0C\xC2\xAA\x7B\xC3\x83\x26\xC2\xAD\xC3\xB0\x7E\x3A\xC3\xA5\x47\xC2\x9D\x7F\x09\xC3\xA5\x49\x44\xC2\xB0\xC2\xAF\x0F\x3A\xC3\x8C\x50\x51\xC3\xBD\x6F\x32\x2C\xC3\x8C\x2D\x27\x49\xC3\xA3\x2A\xC3\xB0\xC3\xB3\xC2\xAC\xC3\x88\xC2\x89\xC3\xB0\xC2\x9D\x7E\x1C\xC2\x9F\x29\x11\x41\x47\xC3\xB5\xC2\xBC\xC3\x88\xC2\x9A\x38\xC3\xB0\xC3\xA2\xC2\xB8\xC3\xA9\x15\xC3\x92\x50"

exp.sendafter("Say something:",cmp)

exp.interactive()
```

由于是后面做的，端口已经关了,所以只能本地打了hhhhhh

![](https://raw.githubusercontent.com/Alikas0/files/master/img/20190714132914.png)

## Matr1x

题目：[Matr1x](hhttps://raw.githubusercontent.com/Alikas0/CTF/master/challenges/2019/StarCTF/Matr1x.zip)

题目一打开，通过start找到main函数。

```assembly
.text:00002620 start           proc near               ; DATA XREF: LOAD:00000018↑o
.text:00002620                 xor     ebp, ebp
.text:00002622                 pop     esi
.text:00002623                 mov     ecx, esp
.text:00002625                 and     esp, 0FFFFFFF0h
.text:00002628                 push    eax
.text:00002629                 push    esp             ; stack_end
.text:0000262A                 push    edx             ; rtld_fini
.text:0000262B                 call    sub_2652
.text:00002630                 add     ebx, 10994h
.text:00002636                 lea     eax, (nullsub_1 - 12FC4h)[ebx]
.text:0000263C                 push    eax             ; fini
.text:0000263D                 lea     eax, (sub_11250 - 12FC4h)[ebx]
.text:00002643                 push    eax             ; init
.text:00002644                 push    ecx             ; ubp_av
.text:00002645                 push    esi             ; argc
.text:00002646                 push    ds:(off_12FF8 - 12FC4h)[ebx] ; main
.text:0000264C                 call    ___libc_start_main
.text:00002651                 hlt
.text:00002651 start           endp
.text:00002651
```

### 去花指令和冗余代码

.....一堆花指令和冗余代码。那就去掉咯。

其中，除去全局变量的代码如果之间在全局变量赋值处patch会出问题，来自Apeng大佬的指导：

!!!note
    问题出在pie的重定位上了，由于程序原本开启了pie，对全局变量引用的时候地址是会变的，所以elf运行的时候会把所有这些全局变量的引用都patch成重定位之后的。而他查找的方式肯定不是根据语句来查，可能是有个表，所以所有这些全局变量都会被patch。所以只需要把mov eax,0xxx这句话patch到其他地方就行了


所以稍微修改了一下，最终如下：

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

之后就可以F5，但是由于IDA是软件分析，所以那些函数得一个个F5.....

之后再手动去掉冗余代码，就可以很愉快的分析了：

```c
  printf(format);
  _isoc99_scanf(a255s, input);
  v8 = strlen(input);
  for ( i = 0; i < v8 / 2; ++i )
    *v7 = (input[2 * i + 1] - '0') | 16 * (input[2 * i] - '0');
  v9[v8 / 2] = 0;
  sub_39C7(v9);
  if ( sub_4638() )
  {
    memset(s, 0, 0x1Cu);
    for ( i = 0; i < 6; ++i )
    {
      v3 = 0;
      v5 = cube[i];
      v4 = mul_cube[i];
      for ( j = 0; j < 9; ++j )
        v3 += v4[j] * v5[j];
      *(_DWORD *)&s[4 * i] = v3;
    }
    printf(aHereIsYourFlag, s);
  }
  else
  {
    v1 = puts(aTryAgain) | v0;
  }
  return 0;
}

```

### 算法

```c
 for ( i = 0; i < v8 / 2; ++i )
    *v7 = (input[2 * i + 1] - '0') | 16 * (input[2 * i] - '0');
```

这个循环将输入每两位一组转成十六进制数：例如输入1234会转成0x12,0x34

主要操作为`sub_39C7(v9);`

```c
int __cdecl sub_39C7(char *s)
{
  int v1; // ecx
  signed int i; // [esp+54h] [ebp-8h]
  signed int v4; // [esp+58h] [ebp-4h]

  v4 = strlen(s);
  for ( i = 0; i < v4; ++i )
  {
    v1 = s[i];
    switch ( v1 )
    {
      case 0x10:
        sub_6D47();
        break;
      case 0x15:
        sub_76DF();
        break;
      case 0x11:
        sub_818A();
        break;
      case 0x14:
        sub_88B8();
        break;
      case 0x12:
        sub_9131();
        break;
      case 0x13:
        sub_9BE7();
        break;
      case 0x20:
        sub_A570();
        break;
      case 0x25:
        sub_AC8E();
        break;
      case 0x21:
        sub_B43B();
        break;
      case 0x24:
        sub_BC72();
        break;
      case 0x22:
        sub_C2E1();
        break;
      case 0x23:
        sub_C9C9();
        break;
      case 0x30:
        sub_D14D();
        break;
      case 0x35:
        sub_DB6D();
        break;
      case 0x31:
        sub_E5D7();
        break;
      case 0x34:
        sub_EE9E();
        break;
      case 0x32:
        sub_F7F9();
        break;
      case 0x33:
        sub_101AD();
        break;
      default:
        return 1;
    }
  }
  return 0;
}

```

根据case可以知道我们输入的范围：`10~15，20~25，30~35`

随便进去一个看一下(这里也有一些冗余代码我手动去掉了，，便于分析)：

```c
int sub_6D47()
{
  int i; // [esp+5Ch] [ebp-8h]
  int v2; // [esp+60h] [ebp-4h]

  v2 = 0;
  for ( i = 0; i < 3; ++i )
  {
    v2 = A[3 * i + 2];
    A[3 * i + 2] = F[3 * i + 2];
    F[3 * i + 2] = B[3 * i + 2];
    B[3 * i + 2] = E[3 * i + 2];
    E[3 * i + 2] = v2;
  }
  return sub_5C20(D);
}

```

应该是个轮换。

再根据经验不难推出，这应该是一个3阶魔方：`一共6个面，每个面有9个元素，共有3*6=18种操作`

判断函数sub_4638():

```c
int sub_4638()
{
  int (*v0)[9]; // ST00_4
  signed int i; // [esp+4h] [ebp-8h]
  int v3; // [esp+8h] [ebp-4h]

  v3 = 1;
  for ( i = 0; i < 6 && v3; ++i )
  {
    v0 = cube[i];
    v3 = ((*v0)[7] + (*v0)[5] + (*v0)[4] + (*v0)[3] + (*v0)[1] == cmp[2 * i + 1]) & ((*v0)[8]+ (*v0)[6]+ (*v0)[4] + (*v0)[2] + (*v0)[0] == cmp[2 * i]) & (unsigned __int8)v3;
  }
  return v3;
}

```

可以分析出，该判断为每个魔方的：

sum(center + middle)==cmp[2 * i]

sum(center + coner)==cmp[2 * i + 1]

而得到flag的循环：

```c
for ( i = 0; i < 6; ++i )
    {
      v3 = 0;
      v5 = cube[i];
      v4 = mul_cube[i];
      for ( j = 0; j < 9; ++j )
        v3 += v4[j] * v5[j];
      *(_DWORD *)&s[4 * i] = v3;
    }
    printf(aHereIsYourFlag, s);

```

### 穷举求解

由上述分析，由于corner[4\*6],middle[4\*6],center[6],数据量小，可以通过穷举的方法求解：

#### 提取

```python
mul_cube = [[0xB849CD19,0x55E00017,0x844966B,0x80C181EC,0x686C0B3C,0x55400592,0xCD42168A,0x4039E81,0xD9DE549F],
[0x2034677D,0x144ABD,0x49100D00,0xE003A0E0,0x80F0006D,0x8307ADD6,0x4CF60781,0xA0352643,0xC580C3DE],
[0xEA8C4E24,0x68603008,0x687FBFFF,0x19DE4BF9,0x271A1179,0x99791C4D,0x29CBFFC,0x2B82801E,0x3C0307FB],
[0xDAE61CD6,0x8F7B1BF0,0xC56CEF1D,0xD6493A96,0x1808018,0xF48001B9,0x3712519,0x9294F318,0x6DE20384],
[0xF3750B04,0x256A122A,0x257290B,0xC4582056,0x204E8BC0,0x79C7ADE7,0xC4C20203,0x5B961570,0x66034856],
[0x78329E3A,0x1D07C00,0x4AC240E6,0x854CFBBE,0xABFEC404,0x5BD80037,0xE94CBCD8,0x1,0xC4CA280D]]

cube =  [[0xFDFE0BA1,0x9A915052,0xC96F3527,0xF5201FCD,0xFE32ED8F,0xDB8E3EF9,0x51EF954,0xFE217F1C,0x7B33A8BB],
[0x9CF903A1,0xC381E2CD,0x22B35BE4,0x4550E6AE,0xDC9E8F3C,0xA9B44EAF,0x3372486A,0x51329F58,0x5F2F456E],
[0x9B555A08,0xEB1A8529,0x9B009084,0x9B0B7B06,0x9967F311,0x91FB13AB,0x18952236,0x6F7B9915,0xEDD9D6D1],
[0xFB67FE21,0x259911B0,0x3DC4EE74,0x98936FF0,0xDF7502CE,0xC3DF1016,0xBC1220F9,0xF54C810C,0x715A634C],
[0x3E1637A6,0x80F07B8D,0xFB9CA491,0xAD254C2E,0xFB5A012F,0x1AEF5581,0xB9CC1351,0x9A3B536D,0xBD7FAF0F],
[0xF49AD883,0x2C55324,0x83BC3205,0x43846281,0x19382448,0xFADB2B18,0x9335D185,0x94C6BF5A,0x591685AE]]

print(center)
for i in range(6):
	print(hex(mul_cube[i][4]) , end = ',')

print(corner)
for i in range(6):
	v0 = mul_cube[i]
	for j in range(0,9,2):
		if j == 4:
			continue
		print(hex(v0[j]),end = ',')

print(middle)
for i in range(6):
	v0 = mul_cube[i]
	for j in range(1,9,2):
		print(hex(v0[j]), end = ',')

```

#### 组合求值

用cmp来center的确定值以及对应corner，middle的取值

```python
import itertools
corner = [0xfdfe0ba1,0xc96f3527,0x51ef954,0x7b33a8bb,0x9cf903a1,0x22b35be4,0x3372486a,0x5f2f456e,0x9b555a08,0x9b009084,0x18952236,0xedd9d6d1,0xfb67fe21,0x3dc4ee74,0xbc1220f9,0x715a634c,0x3e1637a6,0xfb9ca491,0xb9cc1351,0xbd7faf0f,0xf49ad883,0x83bc3205,0x9335d185,0x591685ae]

middle = [0x9a915052,0xf5201fcd,0xdb8e3ef9,0xfe217f1c,0xc381e2cd,0x4550e6ae,0xa9b44eaf,0x51329f58,0xeb1a8529,0x9b0b7b06,0x91fb13ab,0x6f7b9915,0x259911b0,0x98936ff0,0xc3df1016,0xf54c810c,0x80f07b8d,0xad254c2e,0x1aef5581,0x9a3b536d,0x2c55324,0x43846281,0xfadb2b18,0x94c6bf5a]

center = [0xfe32ed8f,0xdc9e8f3c,0x9967f311,0xdf7502ce,0xfb5a012f,0x19382448]

#print(corner)
for i in range(6):
	corner_1 = itertools.combinations(corner, 4)
	for corner_0 in corner_1:
		for j in range(6):
			sum = corner_0[0] + corner_0[1] + corner_0[2] + corner_0[3]
			sum += center[j]
			sum &= 0xFFFFFFFF
			# print(i)
			if sum == cmp[i * 2]:
				print(hex(center[j]), (list(map(hex, corner_0))))

print("----------------------------")
# print(middle)
for i in range(6):
	middle_1 = itertools.combinations(middle, 4)
	for middle_0 in middle_1:
		for j in range(6):
			sum = middle_0[0] + middle_0[1] + middle_0[2] + middle_0[3]
			sum += center[j]
			sum &= 0xFFFFFFFF
			# print(i)
			if sum == cmp[i * 2 + 1]:
				print(hex(center[j]), (list(map(hex, middle_0))))

```



#### 排列组合，穷举求解

用可见字符进行过滤

```python
center = [0xdf7502ce, 0x9967f311, 0xdc9e8f3c,
          0xfe32ed8f, 0x19382448, 0xfb5a012f]
corner = [
	[0xfdfe0ba1, 0x7b33a8bb, 0x3dc4ee74, 0x3e1637a6], [0xc96f3527, 0x51ef954, 0x715a634c, 0x9335d185], [0x9b009084, 0x18952236, 0xbd7faf0f, 0x83bc3205], [0x22b35be4, 0x3372486a, 0xedd9d6d1, 0xfb9ca491], [0x9cf903a1, 0x9b555a08, 0xb9cc1351, 0x591685ae], [0x5f2f456e, 0xfb67fe21, 0xbc1220f9, 0xf49ad883]]

middle = [
	[0x51329f58, 0x6f7b9915, 0x2c55324, 0x43846281], [0xf5201fcd, 0xdb8e3ef9, 0xeb1a8529, 0x9a3b536d], [0xc381e2cd, 0x98936ff0, 0xc3df1016, 0x94c6bf5a], [0x91fb13ab, 0x80f07b8d, 0xad254c2e, 0x1aef5581], [0xfe217f1c, 0xa9b44eaf, 0x259911b0, 0xf54c810c], [0x9a915052, 0x4550e6ae, 0x9b0b7b06, 0xfadb2b18]]

mul_center = [0x686c0b3c, 0x80f0006d, 0x271a1179, 0x1808018, 0x204e8bc0, 0xabfec404]
mul_corner = [
	[0xb849cd19, 0x844966b, 0xcd42168a, 0xd9de549f], 
	[0x2034677d, 0x49100d00, 0x4cf60781, 0xc580c3de], 
	[0xea8c4e24, 0x687fbfff, 0x29cbffc, 0x3c0307fb], 
	[0xdae61cd6, 0xc56cef1d, 0x3712519, 0x6de20384], 
	[0xf3750b04,   0x257290b, 0xc4c20203, 0x66034856], 
	[0x78329e3a, 0x4ac240e6, 0xe94cbcd8, 0xc4ca280d]]
mul_middle = [
	[0x55e00017, 0x80c181ec, 0x55400592, 0x4039e81], 
	[0x144abd, 0xe003a0e0, 0x8307add6, 0xa0352643], 
	[0x68603008, 0x19de4bf9, 0x99791c4d, 0x2b82801e], 
	[0x8f7b1bf0, 0xd6493a96, 0xf48001b9, 0x9294f318], 
	[0x256a122a, 0xc4582056, 0x79c7ade7, 0x5b961570], 
	[0x1d07c00, 0x854cfbbe, 0x5bd80037, 0x1]]

for i in range(6):
	print("-------------------")
	corner_0 = list(itertools.permutations(corner[i],4))
	middle_0 = list(itertools.permutations(middle[i],4))
	for k in range(24):
		corner_1 = corner_0[k]
		for l in range(24):
			sum = center[i] * mul_center[i]
			middle_1 = middle_0[l]
			flag = ""
			# print(list(map(hex, middle_1)))
			for m in range(4):
				sum += corner_1[m] * mul_corner[i][m]
				sum += middle_1[m] * mul_middle[i][m]
			key = 0
			for m in range(4):
				char = sum & 0xFF
				if 0x30 <= char <= 0x39 or 0x41 <= char <= 0x41 + 25 or 0x61 <= char <= 0x61 + 25 or ord('_') == char or ord('{') == char or ord('}') == char or ord("*") == char:
					flag += chr(sum & 0xFF)
					sum >>= 8
				else:
					key = 1
			if key:
				continue
			print(flag)

```

得到:

```text
-------------------
*CTF
erkU
-------------------
Gj3o
{7h1
KP6o
uk6W
4gjL
-------------------
S_Cu
oeps
-------------------
63_i
ERNJ
-------------------
s_m4
-------------------
ukxG
uHnr
g1c}
4nEE
YOcm
AkT6
YctL
VRae

```

根据词义不难猜出flag为：

*CTF{7h1S_Cu63_is_m4g1c}

## Obfuscating Macros II

题目：[Obfuscating Macros II](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/StarCTF-2019/Obfuscating Macros II.zip)

根据题目混淆宏emmmmmm不懂

运行下程序

```text
./obfuscating_macros_II.out
sss
Failed
```

IDA打开找到main函数：

```c
v21 = __readfsqword(0x28u);
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::basic_string();
  v16 = 0LL;
  v17 = 0LL;
  std::operator>><char,std::char_traits<char>,std::allocator<char>>(&std::cin, &v20);
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::length();
  if ( v3 != 16 )
    goto LABEL_9;
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::c_str();
  v5 = *v4;
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::c_str();
  encrypto(v5, *(_QWORD *)(v6 + 8));
  v18 = v7;
  v19 = v8;
  sub_40465C(&v16, (__int64)&v18);
  v9 = v17;
  v10 = std::ostream::operator<<(&std::cout, v16);
  v11 = std::operator<<<std::char_traits<char>>(v10, (__int64)&byte_407D38);
  v12 = std::ostream::operator<<(v11, v9);
  std::ostream::operator<<(v12, std::endl<char,std::char_traits<char>>);
  if ( v16 != 0xA1E8895EB916B732LL || v17 != 0x50A2DCC51ED6C4A2LL )
  {
LABEL_9:
    v14 = std::operator<<<std::char_traits<char>>((__int64)&std::cout, (__int64)"Failed");
    std::ostream::operator<<(v14, std::endl<char,std::char_traits<char>>);
  }
  else
  {
    v13 = std::operator<<<std::char_traits<char>>((__int64)&std::cout, (__int64)"Congratulations!");
    std::ostream::operator<<(v13, std::endl<char,std::char_traits<char>>);
  }
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::~basic_string(&v20);
  return 0LL;
```

### 程序流程

根据调试:

首先判断输出长度是否为16：

```c
if ( v3 != 16 )
    goto LABEL_9;
```

接着进入encrypto函数进行加密,加密后判断：

```c
if ( v16 != 0xA1E8895EB916B732LL || v17 != 0x50A2DCC51ED6C4A2LL )
```

重点就是在encrypto函数咯。

### 加密函数

进去以后发现根本看不懂....调试大法好！

首先是将输入切成两段，长度为8：

![](https://raw.githubusercontent.com/Alikas0/files/master/img/20190720160232.png)

接着在栈里面看哪里对这两块地方进行了操作，这里我输入了11111111AAAAAAAA：

得到：

首先来到了v16 = 0,判断v16 <= 0x3FF，那么回到这里就是一个循环咯：

接着第一段取反

```c
v8 = ~v7;
```

接着

```c
if(v7&1)
```

为真则

```c
v8^=*v5
```

这里看到v8 指向了v6，v5 指向了v7

接下来就是

```c
v6 = v8
v7 ~= v7
```

再接着根据调试不难发现,接下来就是对整一个128位字符进行循环左移1位.

因为一开始`if(v7&1)`为真，这次我们输00000000AAAAAAAA：

得到`v8^=*v5`此时v5变成了指向~v7，而之后就都一样了。

不难写出加密算法:

```python
def Andf(str):
    return str & 0xFFFFFFFFFFFFFFFF 

def Rol(str1,str2):
    a = str1 >> 63
    b = str2 >> 63
    str2 <<= 1
    str1 <<= 1
    str2 |= a
    str1 |= b
    str1,str2 = Andf(str1),Andf(str2)
    return str1,str2
    
def Encrypto(str1,str2):
    if str1&1:
        str2 ^= str1
    else:
        str2 ^= (~str1)
    str1 = (~str1)
    str1 = Andf(str1)
    str2 = Andf(str2)
    str1,str2 = Rol(str1,str2)   
    tmp = str2
    str2 += str1
    str2 = Andf(str2)
    str1 = tmp
    str1, str2 = Rol(str1, str2)
    return str1,str2

for i in range(0x400):
   str1,str2 = Encrypto(str1,str2)
```

则解密脚本为：

```python
def Andf(str):
    return str & 0xFFFFFFFFFFFFFFFF 

def Ror(str1,str2):
    a = (str1 & 0x1) << 63
    b = (str2 & 0x1) << 63
    str2 >>= 1
    str1 >>= 1
    str1 |= b
    str2 |= a
    str1,str2 = Andf(str1),Andf(str2)
    return str1,str2
    
def Decrypto(str1,str2):
    str1, str2 = Ror(str1, str2)
    tmp = str2
    tmp -= str1
    tmp = Andf(tmp)
    str2 = str1
    str1 = tmp
    str1, str2 = Ror(str1, str2)
    str1 = (~str1)
    str1 = Andf(str1)
    if str1&1:
        str2 ^= str1
    else:
        str2 ^= (~str1)
    str2 = Andf(str2)
    return str1,str2
    
str1 = 0xA1E8895EB916B732
str2 = 0x50A2DCC51ED6C4A2

for i in range(0x400):
   str1,str2 = Decrypto(str1,str2)

# print(hex(str1),hex(str2))

str1 = [0x6e,0x55,0x66,0x7b,0x46,0x54,0x43,0x2a][::-1]
str2 = [0x7d,0x39,0x66,0x43,0x74,0x40,0x6c,0x66][::-1]
str = str1 + str2
print(''.join(map(chr,str)))
```

得到flag：

*CTF{fUnfl@tCf9}

### 验证

```text
./obfuscating_macros_II.out
*CTF{fUnfl@tCf9}
11666725874628474674 5810449208445420706
Congratulations!
```

