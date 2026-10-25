# bbxor WP

> 小 D 在参加某知名网络安全比赛时，遇到了一道名为 bbjv 的题目，可是他苦思冥想了好久都没有做出来。赛后他发誓，再也不相信任何前缀为 bb 的题目了。
>
> 那么聪明的你，能否解决这道 bbxor 呢：）

下载chall 4

在终端查看文件类型

```bash
 file chall4
```

得到

> chall4: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=be9e403dc850f59d4d7383d9fbab3a845d891f6d, for GNU/Linux 3.2.0, not stripped

分析内容

身份信息

ELF   Linux 系统下的标准可执行文件格式

64-bit  64 位程序

LSB executable  小端序存储方式

x86-64  CPU 架构

version 1 (SYSV)  版本号



运行环境

dynamically linked  动态链接，需要使用系统动态库

interpreter /lib64/ld-linux-x86-64.so.2  Linux 系统的“动态链接加载器”

for GNU/Linux 3.2.0 编译所需的linux内核最低版本





调试与逆向的关键信息

BuildID[sha1]=be9e403dc850f59d4d7383d9fbab3a845d891f6d

文件的哈希值

**not stripped**  *说明程序保留了原来的函数名和全局变量名*

开始运行程序

```bash
chmod +x chall4   # 给它加上可执行权限
./chall4          # 运行它
```

输出input 需要验证码

使用linux的objdump 查看二进制文件内部结构，进行反汇编

```bash
objdump -M intel -d chall4
```


完整输出很长，大部分是与解题无关的运行样板（`_init`、`.plt`、`deregister_tm_clones`、`__do_global_dtors_aux` 等），真正要看的只有 `main` 函数这一段，其余省略。

```
0000000000401156 <main>:
  401156:       f3 0f 1e fa             endbr64
  40115a:       55                      push   rbp
  40115b:       48 89 e5                mov    rbp,rsp
  40115e:       48 81 ec a0 00 00 00    sub    rsp,0xa0
  401165:       48 8d 05 98 0e 00 00    lea    rax,[rip+0xe98]        # 402004 <_IO_stdin_used+0x4>
  40116c:       48 89 c7                mov    rdi,rax
  40116f:       b8 00 00 00 00          mov    eax,0x0
  401174:       e8 d7 fe ff ff          call   401050 <printf@plt>
  401179:       c7 45 fc 00 00 00 00    mov    DWORD PTR [rbp-0x4],0x0
  401180:       eb 2f                   jmp    4011b1 <main+0x5b>
  401182:       48 8d 85 60 ff ff ff    lea    rax,[rbp-0xa0]
  401189:       8b 55 fc                mov    edx,DWORD PTR [rbp-0x4]
  40118c:       48 63 d2                movsxd rdx,edx
  40118f:       48 c1 e2 02             shl    rdx,0x2
  401193:       48 01 d0                add    rax,rdx
  401196:       48 89 c6                mov    rsi,rax
  401199:       48 8d 05 6c 0e 00 00    lea    rax,[rip+0xe6c]        # 40200c <_IO_stdin_used+0xc>
  4011a0:       48 89 c7                mov    rdi,rax
  4011a3:       b8 00 00 00 00          mov    eax,0x0
  4011a8:       e8 b3 fe ff ff          call   401060 <__isoc99_scanf@plt>
  4011ad:       83 45 fc 01             add    DWORD PTR [rbp-0x4],0x1
  4011b1:       83 7d fc 23             cmp    DWORD PTR [rbp-0x4],0x23
  4011b5:       7e cb                   jle    401182 <main+0x2c>
  4011b7:       c7 45 f8 00 00 00 00    mov    DWORD PTR [rbp-0x8],0x0
  4011be:       eb 4b                   jmp    40120b <main+0xb5>
  4011c0:       8b 45 f8                mov    eax,DWORD PTR [rbp-0x8]
  4011c3:       48 98                   cdqe
  4011c5:       8b 84 85 60 ff ff ff    mov    eax,DWORD PTR [rbp+rax*4-0xa0]
  4011cc:       83 f0 66                xor    eax,0x66
  4011cf:       89 c1                   mov    ecx,eax
  4011d1:       8b 45 f8                mov    eax,DWORD PTR [rbp-0x8]
  4011d4:       48 98                   cdqe
  4011d6:       48 8d 14 85 00 00 00    lea    rdx,[rax*4+0x0]
  4011dd:       00
  4011de:       48 8d 05 5b 2e 00 00    lea    rax,[rip+0x2e5b]        # 404040 <cipher>
  4011e5:       8b 04 02                mov    eax,DWORD PTR [rdx+rax*1]
  4011e8:       39 c1                   cmp    ecx,eax
  4011ea:       74 1b                   je     401207 <main+0xb1>
  4011ec:       48 8d 05 1c 0e 00 00    lea    rax,[rip+0xe1c]        # 40200f <_IO_stdin_used+0xf>
  4011f3:       48 89 c7                mov    rdi,rax
  4011f6:       b8 00 00 00 00          mov    eax,0x0
  4011fb:       e8 50 fe ff ff          call   401050 <printf@plt>
  401200:       b8 00 00 00 00          mov    eax,0x0
  401205:       eb 23                   jmp    40122a <main+0xd4>
  401207:       83 45 f8 01             add    DWORD PTR [rbp-0x8],0x1
  40120b:       83 7d f8 23             cmp    DWORD PTR [rbp-0x8],0x23
  40120f:       7e af                   jle    4011c0 <main+0x6a>
  401211:       48 8d 05 fa 0d 00 00    lea    rax,[rip+0xdfa]        # 402012 <_IO_stdin_used+0x12>
  401218:       48 89 c7                mov    rdi,rax
  40121b:       b8 00 00 00 00          mov    eax,0x0
  401220:       e8 2b fe ff ff          call   401050 <printf@plt>
  401225:       b8 00 00 00 00          mov    eax,0x0
  40122a:       c9                      leave
  40122b:       c3                      ret
```

`401179: mov DWORD PTR [rbp-0x4],0x0`：**初始化变量 i = 0**。（`[rbp-0x4]` 就是变量 `i` 的家）

`401180: jmp 4011b1`：直接跳过去**检查循环条件**。

**循环体（401182 ~ 4011a8）**：

``lea rax, [rbp-0xa0]`：取出刚才开辟的 160 字节空间的**起始地址**（也就是数组 input 的首地址）。

`mov edx, [rbp-0x4]` / `shl rdx, 0x2`：计算下标。`shl rdx, 0x2` 就是 `i * 4`，因为每个整数占 4 个字节。

`add rax, rdx`：算出数组第 `i` 个元素的**精确地址**。

`call __isoc99_scanf@plt`：**读取输入**。格式是 `%d`（地址在 `40200c`），把读到的数字存入数组。

`add [rbp-0x4], 1`：`i++`。

`4011b1: cmp [rbp-0x4], 0x23` / `jle 401182`：**循环判断**。`0x23` 换算成十进制是 **35**。因为 `i` 从 0 开始，所以这代表要读取 **36 个整数**。

`4011b7: mov DWORD PTR [rbp-0x8],0x0`：**初始化变量 j = 0**。

**循环体（4011c0 ~ 4011e8）**：

`mov eax, [rbp+rax*4-0xa0]`：**取出 input[j]**。

**4011cc: xor eax,0x66：** 把刚才取出的数字与 `0x66`（十进制 102，字符 `'f'`）做异或运算。

`mov ecx, eax`：把异或后的结果暂存在 `ecx` 里。

`lea rax, [rip+0x2e5b] # 404040 <cipher>`： 加载名为 `cipher` 的数组地址，地址是 `0x404040`。

`mov eax, [rdx+rax*1]`：取出 `cipher[j]` 的值。

`cmp ecx, eax`：**比较** `input[j] ^ 0x66` 和 `cipher[j]`。

`je 401207`：如果相等，跳过去执行 `j++`；如果不等，继续往下走打印 `Wrong!` 并退出。

`401207: add [rbp-0x8], 1`：`j++`。

`40120b: cmp [rbp-0x8], 0x23` / `jle 4011c0`：循环 36 次

`401211 ~ 401220`：如果 36 个数字全都对上了，打印 `Success!`（地址在 `402012`）。

`40122a: leave` / `40122b: ret`：打扫现场，函数返回。





因此这是一个关于异或的计算程序

密文在内存地址 `0x404040`，且名称是cipher，计算方式是与0x66进行异或运算

先去读取内存里的密文

```bash
gdb ./chall4
x/36wx 0x404040
```

得到

```
0x404040 <cipher>:      0x0000000b      0x00000009      0x00000003      0x00000005
0x404050 <cipher+16>:   0x00000012      0x00000000      0x0000001d      0x00000024
0x404060 <cipher+32>:   0x00000052      0x00000015      0x0000000f      0x00000005
0x404070 <cipher+48>:   0x00000039      0x0000001e      0x00000056      0x00000014
0x404080 <cipher+64>:   0x00000039      0x00000005      0x0000000e      0x00000007
0x404090 <cipher+80>:   0x00000057      0x0000000a      0x00000003      0x00000008
0x4040a0 <cipher+96>:   0x00000001      0x00000003      0x00000039      0x00000015
0x4040b0 <cipher+112>:  0x00000009      0x0000000a      0x00000010      0x00000003
0x4040c0 <cipher+128>:  0x00000002      0x00000047      0x00000047      0x0000001b
```

用python进行异或运算

```python
c = [0x0b, 0x09, 0x03, 0x05, 0x12, 0x00, 0x1d, 0x24, 0x52, 0x15, 0x0f, 0x05, 0x39, 0x1e, 0x56, 0x14, 0x39, 0x05, 0x0e, 0x07, 0x57, 0x0a, 0x03, 0x08, 0x01, 0x03, 0x39, 0x15, 0x09, 0x0a, 0x10, 0x03, 0x02, 0x47, 0x47, 0x1b]
print(''.join(chr(n ^ 0x66) for n in c))
```

得到

moectf{B4sic_x0r_cha1lenge_solved!!}