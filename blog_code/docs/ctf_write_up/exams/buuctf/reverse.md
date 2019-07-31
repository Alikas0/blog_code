## 0x00 easyre

题目：[easyre](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/RE/easyre.zip)

IDA打开，main函数：

```
int __cdecl main(int argc, const char **argv, const char **envp)
{
  int b; // [rsp+28h] [rbp-8h]
  int a; // [rsp+2Ch] [rbp-4h]

  _main();
  scanf("%d%d", &a, &b);
  if ( a == b )
    printf("flag{this_Is_a_EaSyRe}");
  else
    printf("sorry,you can't get flag");
  return 0;
}
```

## 0x01 helloword

题目：[helloworld](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/RE/helloword.zip)

jeb3打开，MainActivity：

```
.method protected onCreate(Bundle)V
          .registers 6
00000000  invoke-super        ActionBarActivity->onCreate(Bundle)V, p0, p1
00000006  const               v3, 0x7F030018
0000000C  invoke-virtual      MainActivity->setContentView(I)V, p0, v3
00000012  const-string        v0, "flag{7631a988259a00816deda84afb29430a}"
00000016  const-string        v1, "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
0000001A  invoke-virtual      String->compareTo(String)I, v0, v1
00000020  move-result         v2
00000022  return-void
.end method
```

## 0x02 reverse1

题目：[reverse1](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/RE/reverse1.zip)

IDA打开，追踪字符串到主要函数：

```
__int64 sub_1400118C0()
{
  char *v0; // rdi
  signed __int64 i; // rcx
  size_t v2; // rax
  size_t v3; // rax
  char v5; // [rsp+0h] [rbp-20h]
  int j; // [rsp+24h] [rbp+4h]
  char Str1; // [rsp+48h] [rbp+28h]
  unsigned __int64 v8; // [rsp+128h] [rbp+108h]

  v0 = &v5;
  for ( i = 82i64; i; --i )
  {
    *(_DWORD *)v0 = -858993460;
    v0 += 4;
  }
  for ( j = 0; ; ++j )
  {
    v8 = j;
    v2 = j_strlen(Str2);
    if ( v8 > v2 )
      break;
    if ( Str2[j] == 111 )
      Str2[j] = 48;
  }
  sub_1400111D1("input the flag:");
  sub_14001128F("%20s", &Str1);
  v3 = j_strlen(Str2);
  if ( !strncmp(&Str1, Str2, v3) )
    sub_1400111D1("this is the right flag!\n");
  else
    sub_1400111D1("wrong flag\n");
  sub_14001113B(&v5, &unk_140019D00);
  return 0i64;
}
```

查看Str2:

```
.data:000000014001C000 Str2            db '{hello_world}',0 
```

flag{hello_world}

## 0x03 reverse2

题目 ：[reverse2](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/RE/reverse2.zip)

IDA打开，主函数：

```
int __cdecl main(int argc, const char **argv, const char **envp)
{
  int result; // eax
  int stat_loc; // [rsp+4h] [rbp-3Ch]
  int i; // [rsp+8h] [rbp-38h]
  __pid_t pid; // [rsp+Ch] [rbp-34h]
  char s2; // [rsp+10h] [rbp-30h]
  unsigned __int64 v8; // [rsp+28h] [rbp-18h]

  v8 = __readfsqword(0x28u);
  pid = fork();
  if ( pid )
  {
    argv = (const char **)&stat_loc;
    waitpid(pid, &stat_loc, 0);
  }
  else
  {
    for ( i = 0; i <= strlen(flag); ++i )
    {
      if ( flag[i] == 'i' || flag[i] == 'r' )
        flag[i] = '1';
    }
  }
  printf("input the flag:", argv);
  __isoc99_scanf("%20s", &s2);
  if ( !strcmp(flag, &s2) )
    result = puts("this is the right flag!");
  else
    result = puts("wrong flag!");
  return result;
}
```

查看flag：

```
.data:0000000000601080 flag            db '{'                  ; DATA XREF: main+34↑r
.data:0000000000601080                                         ; main+44↑r ...
.data:0000000000601081 aHackingForFun  db 'hacking_for_fun}',0
```

由于前面：

```
for ( i = 0; i <= strlen(flag); ++i )
    {
      if ( flag[i] == 'i' || flag[i] == 'r' )
        flag[i] = '1';
    }
```

so，最终flag为:

flag{hack1ng_fo1_fun}

## 0x04 刮开有奖

题目：[刮开有奖](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/RE/刮开有奖.zip)

IDA打开，查看字符串发现base64 table表

```
.rdata:00407830 byte_407830     db 41h                  ; DATA XREF: sub_401000+C0↑r
.rdata:00407831 aBcdefghijklmno db 'BCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=',0
```

整理一下：

```
.rdata:00407830 aAbcdefghijklmn db 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/='

```

追到判断函数,根据经验修改类型和大小：

```
BOOL __stdcall DialogFunc(HWND hDlg, UINT a2, WPARAM a3, LPARAM a4)
{
  const char *v4; // esi
  const char *v5; // edi
  int key[11]; // [esp+8h] [ebp-20030h]
  CHAR input[8]; // [esp+34h] [ebp-20004h]
  CHAR v9; // [esp+10034h] [ebp-10004h]
  CHAR v10; // [esp+10035h] [ebp-10003h]
  CHAR v11; // [esp+10036h] [ebp-10002h]

  if ( a2 == 272 )
    return 1;
  if ( a2 != 273 )
    return 0;
  if ( (_WORD)a3 == 1001 )
  {
    memset(input, 0, 0xFFFFu);
    GetDlgItemTextA(hDlg, 1000, input, 0xFFFF);
    if ( strlen(input) == 8 )
    {
      key[0] = 90;
      key[1] = 74;
      key[2] = 83;
      key[3] = 69;
      key[4] = 67;
      key[5] = 97;
      key[6] = 78;
      key[7] = 72;
      key[8] = 51;
      key[9] = 110;
      key[10] = 103;
      sub_4010F0(key, 0, 10);
      memset(&v9, 0, 0xFFFFu);
      v9 = input[5];
      v11 = input[7];
      v10 = input[6];
      v4 = base64((int)&v9, strlen(&v9));
      memset(&v9, 0, 0xFFFFu);
      v10 = input[3];
      v9 = input[2];
      v11 = input[4];
      v5 = base64((int)&v9, strlen(&v9));
      if ( input[0] == key[0] + 34
        && input[1] == key[4]
        && 4 * input[2] - 141 == 3 * key[2]
        && input[3] / 4 == 2 * (key[7] / 9)
        && !strcmp(v4, "ak1w")
        && !strcmp(v5, "V1Ax") )
      {
        MessageBoxA(hDlg, "U g3t 1T!", "@_@", 0);
      }
    }
    return 0;
  }
  if ( (_WORD)a3 != 1 && (_WORD)a3 != 2 )
    return 0;
  EndDialog(hDlg, (unsigned __int16)a3);
  return 1;
}

```

sub_4010F0(key, 0, 10):

```
int __cdecl sub_4010F0(int *a1, int a2, int num_10)
{
  int result; // eax
  int i; // esi
  int v5; // ecx
  int v6; // edx

  result = num_10;
  for ( i = a2; i <= num_10; a2 = i )
  {
    v5 = i;
    v6 = a1[i];
    if ( a2 < result && i < result )
    {
      do
      {
        if ( v6 > a1[result] )
        {
          if ( i >= result )
            break;
          ++i;
          a1[v5] = a1[result];
          if ( i >= result )
            break;
          while ( a1[i] <= v6 )
          {
            if ( ++i >= result )
              goto LABEL_13;
          }
          if ( i >= result )
            break;
          v5 = i;
          a1[result] = a1[i];
        }
        --result;
      }
      while ( i < result );
    }
LABEL_13:
    a1[result] = v6;
    sub_4010F0(a1, a2, i - 1);
    result = num_10;
    ++i;
  }
  return result;
}

```

研究一下就会发现是个从小到大的排序。

则解密脚本如下：

```
import base64

s2 = base64.b64decode("ak1w")
s1 = base64.b64decode("V1Ax")

key = [ 0 for i in xrange(11) ] 

key[0] = 90
key[1] = 74
key[2] = 83
key[3] = 69
key[4] = 67
key[5] = 97
key[6] = 78
key[7] = 72
key[8] = 51
key[9] = 110
key[10] = 103

key.sort()

flag = chr(key[0] + 34) + chr(key[4]) + s1 + s2

print flag

```

flag{UJWP1jMp}

## 0x05 rsa

题目：[rsa](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/RE/rsa.zip)

打开来两个文件,公钥密文都有，解密脚本如下：

```
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_v1_5
from Crypto.Util.number import inverse
import base64

message = open('flag.enc',"r")
cipher_text = message.read()
f = open('pub.key', "r")
publickey = f.read()
publickey = RSA.importKey(publickey)
# print(publickey.n)
# print(publickey.e)
n = 86934482296048119190666062003494800588905656017203025617216654058378322103517
e = 65537
p = 285960468890451637935629440372639283459
q = 304008741604601924494328155975272418463
r = (p - 1) * (q - 1)
d = inverse(e,r)
# print d
privatekey = RSA.construct((long(n), long(e), long(d), long(p), long(q)))
key = PKCS1_v1_5.new(privatekey)
msg = key.decrypt(cipher_text, e)

print msg

```

flag{decrypt_256}

参考：

[RSA-ctf-wiki](https://wiki.x10sec.org/crypto/asymmetric/rsa/rsa_theory/#pycrypto)

[整数分解](http://factordb.com/)

## 0x06 CrackRTF

题目：[CrackRTF](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/RE/CrackRTF.zip)

IDA打开，追踪字符串到主要函数：

```
int main_0()
{
  DWORD v0; // eax
  DWORD v1; // eax
  CHAR String; // [esp+4Ch] [ebp-310h]
  int v4; // [esp+150h] [ebp-20Ch]
  CHAR String1; // [esp+154h] [ebp-208h]
  char input[6]; // [esp+258h] [ebp-104h]

  memset(input, 0, 0x104u);
  memset(&String1, 0, 0x104u);
  v4 = 0;
  printf("pls input the first passwd(1): ");
  scanf("%s", input);
  if ( strlen(input) != 6 )
  {
    printf("Must be 6 characters!\n");
    ExitProcess(0);
  }
  v4 = atoi(input);
  if ( v4 < 100000 )
    ExitProcess(0);
  strcat(input, "@DBApp");
  v0 = strlen(input);
  sub_40100A((BYTE *)input, v0, &String1);
  if ( !_strcmpi(&String1, "6E32D0943418C2C33385BC35A1470250DD8923A9") )
  {
    printf("continue...\n\n");
    printf("pls input the first passwd(2): ");
    memset(&String, 0, 0x104u);
    scanf("%s", &String);
    if ( strlen(&String) != 6 )
    {
      printf("Must be 6 characters!\n");
      ExitProcess(0);
    }
    strcat(&String, input);
    memset(&String1, 0, 0x104u);
    v1 = strlen(&String);
    sub_401019((BYTE *)&String, v1, &String1);
    if ( !_strcmpi("27019e688a4e62a649fd99cadaafdb4e", &String1) )
    {
      if ( !(unsigned __int8)sub_40100F(&String) )
      {
        printf("Error!!\n");
        ExitProcess(0);
      }
      printf("bye ~~\n");
    }
  }
  return 0;
}

```

输入两次验证flag。

### 第一次

hash值在线爆破，直接得第一部分123321，进入第二部分

```
D:\CTF\Games\Exam\BUUCTF\CrackRTF>CrackRTF.exe
pls input the first passwd(1): 123321
continue...

pls input the first passwd(2):
```

### 第二次

无法爆破，观察到sub_40100F函数中：

```
char __cdecl sub_4014D0(LPCSTR lpString)
{
  LPCVOID lpBuffer; // [esp+50h] [ebp-1Ch]
  DWORD NumberOfBytesWritten; // [esp+58h] [ebp-14h]
  DWORD nNumberOfBytesToWrite; // [esp+5Ch] [ebp-10h]
  HGLOBAL hResData; // [esp+60h] [ebp-Ch]
  HRSRC hResInfo; // [esp+64h] [ebp-8h]
  HANDLE hFile; // [esp+68h] [ebp-4h]

  hFile = 0;
  hResData = 0;
  nNumberOfBytesToWrite = 0;
  NumberOfBytesWritten = 0;
  hResInfo = FindResourceA(0, (LPCSTR)0x65, "AAA");
  if ( !hResInfo )
    return 0;
  nNumberOfBytesToWrite = SizeofResource(0, hResInfo);
  hResData = LoadResource(0, hResInfo);
  if ( !hResData )
    return 0;
  lpBuffer = LockResource(hResData);
  sub_401005(lpString, (int)lpBuffer, nNumberOfBytesToWrite);
  hFile = CreateFileA("dbapp.rtf", 0x10000000u, 0, 0, 2u, 0x80u, 0);
  if ( hFile == (HANDLE)-1 )
    return 0;
  if ( !WriteFile(hFile, lpBuffer, nNumberOfBytesToWrite, &NumberOfBytesWritten, 0) )
    return 0;
  CloseHandle(hFile);
  return 1;
}

```

会生成一个dbapp.rtf文件。

随便输入123321得，此处用010editor打开：

![](https://raw.githubusercontent.com/Alikas0/files/master/img/20190707134714.png)

在生成文件前sub_401005进行了异或操作：

```
unsigned int __cdecl sub_401420(LPCSTR lpString, int a2, int a3)
{
  unsigned int result; // eax
  unsigned int i; // [esp+4Ch] [ebp-Ch]
  unsigned int v5; // [esp+54h] [ebp-4h]

  v5 = lstrlenA(lpString);
  for ( i = 0; ; ++i )
  {
    result = i;
    if ( i >= a3 )
      break;
    *(_BYTE *)(i + a2) ^= lpString[i % v5];
  }
  return result;
}

```

则可以逆解出异或因子:

```
s = [0x34,0x4f,0x72,0x26,0x14,0x30,0x5c,0x61,0x6e,0x73,0x69,0x5c,0x61,0x6e,0x73,0x69,0x63,0x70,0x28,0x2a,0x33,0x64,0x2e,0x65,0x65,0x66,0x66,0x30,0x5c,0x64,0x65,0x66,0x6c,0x61,0x6e,0x67,0x7e,0x23,0x33,0x61,0x2e,0x65,0x65,0x66,0x6c,0x61,0x6e,0x67,0x66,0x65,0x32,0x30,0x35,0x32,0x34,0x4f,0x66,0x3d,0x1c,0x75,0x74,0x62,0x6c,0x7b,0x5c,0x66,0x30,0x5c,0x66,0x6d,0x6f,0x64,0x2a,0x61,0x6e,0x0e,0x14,0x71,0x72,0x71,0x36,0x5c,0x66,0x63,0x68,0x61,0x72,0x73,0x65,0x74,0x7e,0x20,0x34,0x72,0x2e,0x26,0x63,0x62,0x5c,0x27,0x63,0x65,0x5c,0x27,0x63,0x63,0x5c,0x27,0x2a,0x26,0x3b,0x2f,0x0f,0x0c,0x0a,0x7b,0x5c,0x2a,0x5c,0x67,0x65,0x6e,0x65,0x72,0x61,0x74,0x20,0x61,0x20,0x1f,0x01,0x67,0x74,0x65,0x64,0x69,0x74,0x20,0x35,0x2e,0x34,0x31,0x2e,0x31,0x7a,0x3d,0x31,0x67,0x43,0x34,0x3b,0x7d,0x5c,0x76,0x69,0x65,0x77,0x6b,0x69,0x6e,0x64,0x34,0x13,0x66,0x63,0x63,0x2e,0x71,0x61,0x72,0x64,0x5c,0x6c,0x61,0x6e,0x67,0x32,0x30,0x35,0x32,0x13,0x75,0x30,0x0e,0x14,0x72,0x32,0x30,0x20,0x46,0x6c,0x61,0x67,0x5c,0x7b,0x4e,0x30,0x5f,0x02,0x23,0x72,0x37,0x2d,0x47,0x72,0x65,0x65,0x5f,0x42,0x75,0x67,0x73,0x5c,0x7d,0x5c,0x70,0x2e,0x61,0x0d,0x58,0x0f,0x0c,0x0a,0x00]

key = map(ord, "123321123321@DBApp")

for i in xrange(len(s)):
    s[i] ^= key[i % len(key)]

```

在本地新建RTF文件，可知文件头6位应该为`”{\\rtf1“`，则可解出第二次输入的字符：

```
key = map(ord, "{\\rtf1")
for i in xrange(len(key)):
    s[i] ^= key[i]

print "".join(map(chr,s[0:6]))    

```

为：`~!3a@0`

输入得最终rtf文件，里面就有flag，为：

Flag{N0_M0re_Free_Bugs}（`提交时要将F改为f`）

## 0x07 crackMe

题目：[crackMe](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/RE/crackMe.zip)

解压得crackMe.exe,打开：

```
Come one! Crack Me~~~
user(6-16 letters or numbers):

```

user已经给出，为`welcomebeijing`

输入后：

```
password(6-16 letters or numbers):

```

IDA打开，跟踪字符串到主要函数，根据经验稍做修改：

```
int __usercall wmain@<eax>(int a1@<ebx>)
{
  FILE *v1; // eax
  FILE *v2; // eax
  char v4; // [esp+3h] [ebp-405h]
  char v5; // [esp+4h] [ebp-404h]
  char v6; // [esp+5h] [ebp-403h]
  char v7; // [esp+104h] [ebp-304h]
  char v8; // [esp+105h] [ebp-303h]
  char password[16]; // [esp+204h] [ebp-204h]
  char user[16]; // [esp+304h] [ebp-104h]

  printf("Come one! Crack Me~~~\n");
  user[0] = 0;
  memset(&user[1], 0, 0xFFu);
  password[0] = 0;
  memset(&password[1], 0, 0xFFu);
  while ( 1 )
  {
    do
    {
      do
      {
        printf("user(6-16 letters or numbers):");
        scanf("%s", user);
        v1 = (FILE *)sub_3E24BE();
        fflush(v1);
      }
      while ( !(unsigned __int8)sub_3E1000(user) );
      printf("password(6-16 letters or numbers):");
      scanf("%s", password);
      v2 = (FILE *)sub_3E24BE();
      fflush(v2);
    }
    while ( !(unsigned __int8)sub_3E1000(password) );
    init_box(user);
    v7 = 0;
    memset(&v8, 0, 0xFFu);
    v5 = 0;
    memset(&v6, 0, 0xFFu);
    v4 = init_print(&v7, &v5);
    if ( check(a1, user, password) )
    {
      if ( v4 )
        break;
    }
    printf(&v5);
  }
  printf(&v7);
  return 0;
}

```

由调试分析得：

init_box函数会将user进行一波操作生成一个box用于后面的异或操作。

可以直接导出，但后面其实没必要用到。

init_print函数会初始化两个输出字符串，一个成功一个失败，但由于有异常花指令导致IDA无法识别为函数，根据分析，直接将异常nop掉就可以了

```
.text:003E11EA 0               nop
.text:003E11EB 0               nop
.text:003E11EC 0               nop
.text:003E11ED 0               nop
.text:003E11EE 0               nop
.text:003E11EF 0               nop
.text:003E11F0 0               aaa

```

接着就重要的check函数：

```
bool __cdecl check(char *user, const char *password)
{
  int v3; // [esp+18h] [ebp-22Ch]
  unsigned int v4; // [esp+1Ch] [ebp-228h]
  unsigned int v5; // [esp+28h] [ebp-21Ch]
  unsigned int v6; // [esp+30h] [ebp-214h]
  char v7; // [esp+36h] [ebp-20Eh]
  char v8; // [esp+37h] [ebp-20Dh]
  char v9; // [esp+38h] [ebp-20Ch]
  unsigned __int8 v10; // [esp+39h] [ebp-20Bh]
  unsigned __int8 v11; // [esp+3Ah] [ebp-20Ah]
  char v12; // [esp+3Bh] [ebp-209h]
  int v13; // [esp+3Ch] [ebp-208h]
  char v14; // [esp+40h] [ebp-204h]
  char v15; // [esp+41h] [ebp-203h]
  _BYTE key[256]; // [esp+140h] [ebp-104h]

  v4 = 0;
  v5 = 0;
  v11 = 0;
  v10 = 0;
  key[0] = 0;
  memset(&check_num[1], 0, 0xFFu);
  v14 = 0;
  memset(&v15, 0, 0xFFu);
  v9 = 0;
  v6 = 0;
  v3 = 0;
  while ( v6 < strlen(password) )
  {
    if ( isdigit(password[v6]) )
    {
      v8 = password[v6] - 48;
    }
    else if ( isxdigit(password[v6]) )
    {
      if ( *(_DWORD *)(*(_DWORD *)(__readfsdword(0x30u) + 24) + 12) != 2 )
        password[v6] = 34;
      v8 = (password[v6] | 0x20) - 87;
    }
    else
    {
      v8 = ((password[v6] | 0x20) - 97) % 6 + 10;
    }
    v9 = v8 + 16 * v9;
    if ( !((signed int)(v6 + 1) % 2) )
    {
      *(&v14 + v3++) = v9;
      v9 = 0;
    }
    ++v6;
  }
  while ( (signed int)v5 < 8 )
  {
    v10 += box[++v11];
    v12 = box[v11];
    v7 = box[v10];
    box[v10] = v12;
    box[v11] = v7;
    if ( *(_DWORD *)(__readfsdword(0x30u) + 104) & 0x70 )
      v12 = v10 + v11;
    key[v5] = box[(unsigned __int8)(v7 + v12)] ^ *(&v14 + v4);
    if ( *(_DWORD *)(__readfsdword(0x30u) + 2) & 0xFF )
    {
      v10 = -83;
      v11 = 43;
    }
    xor(check_num, user, v5++);
    v4 = v5;
    if ( v5 >= &v14 + strlen(&v14) + 1 - &v15 )
      v4 = 0;
  }
  v13 = 0;
  check2(key, &check_num);
  return check_num == 43924;
}

```

根据调试，输入的password会每两个为一组(第一个while就是在将输入转为两个为一组的十六进制字符)，来与box中的特定位置的数异或，得到一组8个key,后进入check2进行验证，最终得到`check_num == 43924`则成功。

check2:

```
_DWORD *__usercall check2@<eax>(int a1@<ebx>, _BYTE *key, _DWORD *a3)
{
  int v3; // ST28_4
  int v4; // ecx
  int v6; // edx
  int v8; // ST20_4
  int v9; // eax
  int v10; // edi
  int v11; // ST1C_4
  int v12; // edx
  char v13; // di
  int v14; // ST18_4
  int v15; // eax
  int v16; // ST14_4
  int v17; // edx
  char v18; // al
  int v19; // ST10_4
  int v20; // ecx
  int v23; // ST0C_4
  int v24; // eax
  _DWORD *result; // eax
  int v26; // edx

  if ( *key == 100 )
  {
    *a3 |= 4u;
    v4 = *a3;
  }
  else
  {
    *a3 ^= 3u;
  }
  v3 = *a3;
  if ( key[1] == 98 )
  {
    _EAX = a3;
    *a3 |= 0x14u;
    v6 = *a3;
  }
  else
  {
    *a3 &= 0x61u;
    _EAX = (_DWORD *)*a3;
  }
  __asm { aam }
  if ( key[2] == 97 )
  {
    *a3 |= 0x84u;
    v9 = *a3;
  }
  else
  {
    *a3 &= 0xAu;
  }
  v8 = *a3;
  v10 = ~(a1 >> -91);
  if ( key[3] == 112 )
  {
    *a3 |= 0x114u;
    v12 = *a3;
  }
  else
  {
    *a3 >>= 7;
  }
  v11 = *a3;
  v13 = v10 - 1;
  if ( key[4] == 112 )
  {
    *a3 |= 0x380u;
    v15 = *a3;
  }
  else
  {
    *a3 *= 2;
  }
  v14 = *a3;
  if ( *(_DWORD *)(*(_DWORD *)(__readfsdword(0x30u) + 24) + 12) != 2 )
  {
    if ( key[5] == 102 )
    {
      *a3 |= 0x2DCu;
      v17 = *a3;
    }
    else
    {
      *a3 |= 0x21u;
    }
    v16 = *a3;
  }
  if ( key[5] == 115 )
  {
    *a3 |= 0xA04u;
    v18 = (char)a3;
    v20 = *a3;
  }
  else
  {
    v18 = (char)a3;
    *a3 ^= 0x1ADu;
  }
  v19 = *a3;
  _AL = v18 - v13;
  __asm { daa }
  if ( key[6] == 101 )
  {
    *a3 |= 0x2310u;
    v24 = *a3;
  }
  else
  {
    *a3 |= 0x4Au;
  }
  v23 = *a3;
  if ( key[7] == 99 )
  {
    result = a3;
    *a3 |= 0x8A10u;
    v26 = *a3;
  }
  else
  {
    *a3 &= 0x3A3u;
    result = (_DWORD *)*a3;
  }
  return result;
}

```

由分析可知,key应该为：`key = [100, 98, 97, 112, 112, 115, 101, 99]`，即`dbappsec`（本来key[5]可能值为115或102但前面由做过以dbappsec为密钥的题，便猜测为dbappsec）

接着，根据调试可知，xor函数是将key和user每位对应异或。

接着在调试中将box参与异或的位数提取出来，即可，但解出来本地输入就错误，故猜测下面这两处地方将异或操作进行了类似于反调试的操作，故将这两处直接nop掉

```
if ( *(_DWORD *)(__readfsdword(0x30u) + 104) & 0x70 )
      v12 = v10 + v11;

```

```
if ( *(_DWORD *)(__readfsdword(0x30u) + 2) & 0xFF )
    {
      v10 = -83;
      v11 = 43;
    }

```

最终得到解密脚本：

```
key = [100, 98, 97, 112, 112, 115, 101, 99]
print "".join(map(chr,key))
xor = [0x2A, 0xD7, 0x92, 0xE9, 0x53, 0xE2, 0xC4, 0xCD]

user = "welcomebeijing"

for i in xrange(len(key)):
	key[i] ^= ord(user[i])
	key[i] ^= xor[i]
	print hex(key[i])[2:]

```

得到：`39d09ffa4cfcc4cc`

本地输入不会出现`Please try again`则猜测通过，但md532位小写hash加密后提交却不对，陷入自闭。

后用cmd打开crackMe.exe发现`39d09ffa4cfcc4cc`是错误的！

故用cmd直接打开，用IDA attach上去调试，xor数组不变但原程序中xor函数中不执行异或操作，实际毫无效果。

![](https://raw.githubusercontent.com/Alikas0/files/master/img/20190709205034.png)

因为调试直接跳过，就没有注意到下面会因为窗口的不同而不同，还是tcl.....

```
StartupInfo.dwX
    || StartupInfo.dwY
    || StartupInfo.dwXCountChars
    || StartupInfo.dwYCountChars
    || StartupInfo.dwFillAttribute
    || StartupInfo.dwXSize
    || StartupInfo.dwYSize

```

最终解密脚本为：

```
key = [100, 98, 97, 112, 112, 115, 101, 99]
print "".join(map(chr,key))
xor = [0x2A, 0xD7, 0x92, 0xE9, 0x53, 0xE2, 0xC4, 0xCD]

user = "welcomebeijing"

for i in xrange(len(key)):
	key[i] ^= xor[i]
	print hex(key[i])[2:]

```

得到第二个密码`4EB5F3992391A1AE`

```
user(6-16 letters or numbers):welcomebeijing
password(6-16 letters or numbers):4EB5F3992391A1AE
Congratulations:

```

之后进行md5加密后提交，得正确flag:

flag{d2be2981b84f2a905669995873d6a36c}

## 0x08 findit

题目：[findint](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/RE/findit.zip)

下载下来是个apk文件，就直接jeb打开，根据字符串找到主要函数：

```java
protected void onCreate(Bundle arg8) {
        super.onCreate(arg8);
        this.setContentView(0x7F030018);
        this.findViewById(0x7F05003D).setOnClickListener(new View$OnClickListener(new char[]{'T', 'h', 'i', 's', 'I', 's', 'T', 'h', 'e', 'F', 'l', 'a', 'g', 'H', 'o', 'm', 'e'}, this.findViewById(0x7F05003E), new char[]{'p', 'v', 'k', 'q', '{', 'm', '1', '6', '4', '6', '7', '5', '2', '6', '2', '0', '3', '3', 'l', '4', 'm', '4', '9', 'l', 'n', 'p', '7', 'p', '9', 'm', 'n', 'k', '2', '8', 'k', '7', '5', '}'}, this.findViewById(0x7F05003F)) {
            public void onClick(View arg13) {
                int v11 = 17;
                int v10 = 0x7A;
                int v9 = 90;
                int v8 = 65;
                int v7 = 97;
                char[] v3 = new char[v11];
                char[] v4 = new char[38];
                int v0;
                for(v0 = 0; v0 < v11; ++v0) {
                    if(this.val$a[v0] >= 73 || this.val$a[v0] < v8) {
                        if(this.val$a[v0] < 105 && this.val$a[v0] >= v7) {
                        label_39:
                            v3[v0] = ((char)(this.val$a[v0] + 18));
                            goto label_44;
                        }

                        if(this.val$a[v0] >= v8 && this.val$a[v0] <= v9 || this.val$a[v0] >= v7 && this.val$a[v0] <= v10) {
                            v3[v0] = ((char)(this.val$a[v0] - 8));
                            goto label_44;
                        }

                        v3[v0] = this.val$a[v0];
                    }
                    else {
                        goto label_39;
                    }

                label_44:
                }

                if(String.valueOf(v3).equals(this.val$edit.getText().toString())) {
                    v0 = 0;
                    goto label_18;
                }
                else {
                    this.val$text.setText("答案错了肿么办。。。不给你又不好意思。。。哎呀好纠结啊~~~");
                    return;
                label_18:
                    while(v0 < 38) {
                        if(this.val$b[v0] < v8 || this.val$b[v0] > v9) {
                            if(this.val$b[v0] >= v7 && this.val$b[v0] <= v10) {
                            label_80:
                                v4[v0] = ((char)(this.val$b[v0] + 16));
                                if((v4[v0] <= v9 || v4[v0] >= v7) && v4[v0] < v10) {
                                    goto label_95;
                                }

                                v4[v0] = ((char)(v4[v0] - 26));
                                goto label_95;
                            }

                            v4[v0] = this.val$b[v0];
                        }
                        else {
                            goto label_80;
                        }

                    label_95:
                        ++v0;
                    }

                    this.val$text.setText(String.valueOf(v4));
                }
            }
        });
    }

```

观察后发现解题方法有2：

1.逆推出输入

2.复现flag生成过程

我这里采用第2种，脚本如下：

```
flag = "pvkq{m164675262033l4m49lnp7p9mnk28k75}"
flag = map(ord,flag)

v11 = 17
v10 = 0x7A
v9 = 90
v8 = 65
v7 = 97

for i in xrange(len(flag)):
    if flag[i] < v8 or flag[i] > v9:
        if flag[i] >= v7 and flag[i] <= v10 :
            flag[i] += 16
            if (flag[i] <= v9 or flag[i] >= v7) and flag[i] < v10:
                continue
            flag[i] = flag[i] - 26
            continue
    else:
        flag[i] += 16
        if (flag[i] <= v9 or flag[i] >= v7) and flag[i] < v10:
            continue
        flag[i] = flag[i] - 26
        continue

print "".join(map(chr,flag))


```

得flag：
flag{c164675262033b4c49bdf7f9cda28a75}

## 0x09 Dig the way

题目：[Dig the way](https://raw.githubusercontent.com/Alikas0/CTF/master/challenges/BUUCTF/RE/Dig the way.zip)

解压得到一个ida数据库文件，打开：

```
int __cdecl main(int argc, const char **argv, const char **envp)
{
  int result; // eax
  int v4; // ebx
  size_t v5; // eax
  int v6; // ebx
  char v7_20[20]; // [esp+1Ch] [ebp-48h]
  int v8; // [esp+30h] [ebp-34h]
  int v9; // [esp+34h] [ebp-30h]
  int v10; // [esp+38h] [ebp-2Ch]
  int v11; // [esp+3Ch] [ebp-28h]
  int v12; // [esp+40h] [ebp-24h]
  int v13; // [esp+44h] [ebp-20h]
  signed int (__cdecl *func_ptr)(int, int, int); // [esp+48h] [ebp-1Ch]
  int (__cdecl *v15)(int, int, int); // [esp+4Ch] [ebp-18h]
  int (__cdecl *v16)(int, int, int); // [esp+50h] [ebp-14h]
  int v17; // [esp+54h] [ebp-10h]
  int v18; // [esp+58h] [ebp-Ch]
  FILE *file_ptr; // [esp+5Ch] [ebp-8h]

  __main();
  func_ptr = func0;
  v15 = func1;
  v16 = func2;
  v8 = 0;
  v9 = 1;
  v10 = 2;
  v11 = 3;
  v12 = 3;
  v13 = 4;
  file_ptr = fopen("data", "rb");
  if ( !file_ptr )
    return -1;
  fseek(file_ptr, 0, 2);
  v18 = ftell(file_ptr);
  fseek(file_ptr, 0, 0);
  v17 = ftell(file_ptr);
  if ( v17 )
  {
    puts("something wrong");
    result = 0;
  }
  else
  {
    for ( i = 0; i < v18; ++i )
    {
      v4 = i;
      v7_20[v4] = fgetc(file_ptr);
    }
    v5 = strlen(v7_20);
    if ( v5 <= v18 )
    {
      v18 = v11;
      i = 0;
      v17 = v13;
      while ( i <= 2 )
      {
        v6 = i + 1;
        *(&v8 + v6) = (*(&func_ptr + i))((int)&v8, v12, v13);// get v9,v10,v11
        v12 = ++i;
        v13 = i + 1;
      }
      if ( v11 )
      {
        result = -1;
      }
      else
      {
        get_key(v18, v17);
        system("PAUSE");
        result = 0;
      }
    }
    else
    {
      result = -1;
    }
  }
  return result;
}

```

分析可知最终可直接简化为get_key(3,4)，分析get_key:

```
int __cdecl get_key(unsigned int a1, unsigned int a2)
{
  long double v2; // fst7
  unsigned int v4; // [esp+70h] [ebp+8h]

  v4 = (signed __int64)pow((long double)a1, 0.9);
  v2 = pow((long double)a2, 9.800000000000001);
  return printf(
           "flag: %x%x%x%x%x%x%x%x\n",
           (unsigned __int16)v4,
           v4 >> 16,
           (unsigned __int16)(signed __int64)v2,
           (unsigned int)(signed __int64)v2 >> 16,
           (unsigned int)(signed __int64)v2 >> 16,
           (unsigned __int16)(signed __int64)v2,
           v4 >> 16,
           (unsigned __int16)v4);
}

```

直接复现：

```
int get_key(unsigned int a1,unsigned int a2){
    long double v2;
    unsigned int v4;
    v4 = (signed long int)pow((long double)a1, 0.9);
    v2 = pow((long double)a2, 9.800000000000001);
    return printf(
        "flag: %x%x%x%x%x%x%x%x\n",
        (unsigned __int16)v4,
        v4 >> 16,
        (unsigned __int16)(signed __int64)v2,
        (unsigned int)(signed __int64)v2 >> 16,
        (unsigned int)(signed __int64)v2 >> 16,
        (unsigned __int16)(signed __int64)v2,
        v4 >> 16,
        (unsigned __int16)v4);
}

```

`get_key(3,4)`,解出来`flag:202030cc203002`是错误的，随即陷入自闭，待以后再吧.....如果有师傅能予以指导，我将不胜感激！
