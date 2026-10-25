# Assembly WP

> 什么是汇编语言（Assembly Language）？什么是 add, mov, jne, loop？什么是寄存器？

> 阅读这一段 x86_64 风格的汇编代码，学习各种指令，并找到正确的 flag！

```
; x86-64, Intel syntax
; rdi points to output buffer

start:
    lea     rdi, [buf]

    lea     rsi, [byte_404000]
    mov     ecx, 25

loc_401000:
    mov     al, byte ptr [rsi]
    mov     byte ptr [rdi], al
    inc     rsi
    inc     rdi
    loop    loc_401000

    mov     eax, 0x2a
    add     eax, 0x16
    cmp     eax, 0x40
    jne     loc_401080

    mov     ebx, 0x10
    shl     ebx, 2
    cmp     eax, ebx
    jne     loc_401080

    mov     ecx, 0x39
    sub     ecx, 0x20
    cmp     ecx, 0x18
    jg      loc_401050

    jmp     loc_401080

loc_401050:
    lea     rsi, [byte_404020]
    mov     ecx, 15

loc_401060:
    mov     al, byte ptr [rsi]
    xor     al, 0x42
    mov     byte ptr [rdi], al
    inc     rsi
    inc     rdi
    loop    loc_401060

    jmp     loc_4010b0

loc_401080:
    lea     rsi, [byte_404030]
    mov     ecx, 9

loc_401090:
    mov     al, byte ptr [rsi]
    mov     byte ptr [rdi], al
    inc     rsi
    inc     rdi
    loop    loc_401090

loc_4010b0:
    mov     byte ptr [rdi], 0
    ret


byte_404000:
    db 0x6d, 0x6f, 0x65, 0x63, 0x74, 0x66, 0x7b
    db 0x41, 0x73, 0x73, 0x65, 0x6d, 0x62, 0x31, 0x79
    db 0x5f, 0x4c, 0x34, 0x6e, 0x67, 0x75, 0x61, 0x67, 0x65, 0x5f

byte_404020:
    db 0x73, 0x11, 0x1d, 0x21, 0x2d
    db 0x72, 0x2d, 0x72, 0x0d, 0x2d
    db 0x2d, 0x2e, 0x63, 0x63, 0x3f

byte_404030:
    db 0x63, 0x6f, 0x72, 0x72, 0x65, 0x63, 0x74, 0x21, 0x7d

buf:
    db 64 dup(0)
```

分析汇编代码

第一部分

```
start:
    lea     rdi, [buf]          ; 把“输出框”的地址交给 rdi
    lea     rsi, [byte_404000]  ; 把“数据源”的地址交给 rsi
    mov     ecx, 25             ; 告诉计数器 ecx：我们要搬运 25 次

loc_401000:                     ; 开始循环搬运
    mov     al, byte ptr [rsi]  ; 从数据源拿 1 个字节，放进临时口袋 al
    mov     byte ptr [rdi], al  ; 把这个字节放进输出框
    inc     rsi                 ; 数据源地址 +1（看下一个字节）
    inc     rdi                 ; 输出框地址 +1（准备写下一个）
    loop    loc_401000          ; ecx 自动减 1，如果不为 0，就跳回上面继续搬
```

这是在把byte_404000里的十六进制数字转写成字符

我们使用python

```python
part1_hex = [0x6d, 0x6f, 0x65, 0x63, 0x74, 0x66, 0x7b, 0x41, 0x73, 0x73, 0x65, 0x6d, 0x62, 0x31, 0x79, 0x5f, 0x4c, 0x34, 0x6e, 0x67, 0x75, 0x61, 0x67, 0x65, 0x5f]


part1_str = ''.join([chr(b) for b in part1_hex])
print("第一段破解结果：", part1_str)
```

得到moectf{Assemb1y_L4nguage_

第二部分

算术题

    mov     eax, 0x2a       ; eax = 42
    add     eax, 0x16       ; eax = 42 + 22 = 64
    cmp     eax, 0x40       ; 比较 64 和 64 (0x40是十六进制的64)
    jne     loc_401080      ; jne = 如果不相等就跳走。结果相等，所以不跳。
    
    mov     ebx, 0x10       ; ebx = 16
    shl     ebx, 2          ; shl是左移，相当于乘以4。ebx = 16 * 4 = 64
    cmp     eax, ebx        ; 再次比较 64 和 64
    jne     loc_401080      ; 结果相等，所以不跳。
    
    mov     ecx, 0x39       ; ecx = 57
    sub     ecx, 0x20       ; ecx = 57 - 32 = 25
    cmp     ecx, 0x18       ; 比较 25 和 24 (0x18是24)
    jg      loc_401050      ; jg = 如果大于就跳转。25大于24，条件成立！跳到 loc_401050
    
    jmp     loc_401080      ; 如果前面没跳，就掉进陷阱 loc_401080
第三部分进行异或

```
loc_401050:
    lea     rsi, [byte_404020] ; 读第二段数据源
    mov     ecx, 15            ; 这次循环 15 次

loc_401060:
    mov     al, byte ptr [rsi]
    xor     al, 0x42           ; 把每个字节和 0x42 做“异或”运算
    mov     byte ptr [rdi], al ; 把解密后的结果塞进输出框
    inc     rsi
    inc     rdi
    loop    loc_401060
    jmp     loc_4010b0         ; 跳去收尾（加上结束符并退出）
```

```python

part2_encrypted = [0x73, 0x11, 0x1d, 0x21, 0x2d, 0x72, 0x2d, 0x72, 0x0d, 0x2d, 0x2d, 0x2e, 0x63, 0x63, 0x3f]


part2_str = ''.join([chr(b ^ 0x42) for b in part2_encrypted])
print("第二段破解结果：", part2_str)
```

得到

1S_co0o0Oool!!}

拼接到一起得到

**moectf{Assemb1y_L4nguage_1S_co0o0Oool!!}**

