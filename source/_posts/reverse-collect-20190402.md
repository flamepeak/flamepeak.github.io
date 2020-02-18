---
title: 逆向收集
date: 2019-04-02 12:28:06
tags: [逆向]
categories: [Cryptography]
---

---


[利用repne scas byte ptr es:[edi]计算字符串长度](https://www.cnblogs.com/Viwilla/p/5033588.html)

edi:存放字符串 
al：存放字符x 
repne scas byte ptr es:[edi] ：遍历字符串，每循环一次ecx-1，遇到字符x则停止 

汇编中一个很经典的计算字符串长度的方法便是利用了这条指令。


```
 |.  89FA          mov edx,edi   
 |.  89C7          mov edi,eax         ;edi里为存放的字符串
 |.  B9 FFFFFFFF   mov ecx,-0x1      ;ecx放入-1
 |.  30C0          xor al,al                 ;al=0，将遍历所有字符串
 |.  F2:AE         repne scas byte ptr es:[edi]    ;以字节为单位遍历字符串，每循环一次cx-1
 |.  B8 FEFFFFFF   mov eax,-0x2     
 |.  29C8          sub eax,ecx        ;由于cx末尾多减了一次，因此要用-2来减去得到真正的长度
 |.  89D7          mov edi,edx
```

```C
#include<stdio.h>

int main()
{
    char str[] = "123456789";
    int strCount=0;
    int c = 0;
    _asm
    {
        lea edi,str
        mov ecx,0xFFFFFFFF
        xor al,al
        repne scas byte ptr es:[esi]
        mov eax,0xFFFFFFFE
        sub eax,ecx
        mov c,ecx

        mov strCount,eax
    }
    printf("ecx=%d  count = %d",c,strCount);
    return 0;
}
```


---


---

[汇编浮点数运算指令大全](https://blog.csdn.net/runninghui/article/details/9094123)


---


### VB

### 常见函数
[VB程序逆向反汇编常见的函数](https://www.cnblogs.com/bbdxf/p/3780187.html)


bp rtcMsgBox  弹出信息框


