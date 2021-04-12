# 拆炸弹作业

[![https://www.hualigs.cn/image/60729f8db0863.jpg](https://www.hualigs.cn/image/60729f8db0863.jpg)](https://www.hualigs.cn/image/60729f8db0863.jpg)

运行之后进行输入，发现关键词爆炸。

我直接ida打开，f5一波。

[![https://ae01.alicdn.com/kf/Ud877368ce58044babc5e3a44e43ac022I.jpg](https://ae01.alicdn.com/kf/Ud877368ce58044babc5e3a44e43ac022I.jpg)](https://ae01.alicdn.com/kf/Ud877368ce58044babc5e3a44e43ac022I.jpg)

可以发现有六个炸弹需要进行拆除，点进炸弹一，

[![https://ae01.alicdn.com/kf/U1ef054d6051c4aa5a81214330b56c144o.jpg](https://ae01.alicdn.com/kf/U1ef054d6051c4aa5a81214330b56c144o.jpg)](https://ae01.alicdn.com/kf/U1ef054d6051c4aa5a81214330b56c144o.jpg)

有一个关键函数是进行比较判断的，应该是输入与字符串不同时爆炸。

[![https://ae01.alicdn.com/kf/U136c35f9a73f4821a9f7442a857cbe2aW.jpg](https://ae01.alicdn.com/kf/U136c35f9a73f4821a9f7442a857cbe2aW.jpg)](https://ae01.alicdn.com/kf/U136c35f9a73f4821a9f7442a857cbe2aW.jpg)

第一个炸弹拆除成功。师傅们说用ida不讲武德，于是按照别人的教程学习了一下gdb

将bomb反汇编之后查看源码。

[![https://ae01.alicdn.com/kf/U9b72547dbf6c4252a98134d77f012d94k.jpg](https://ae01.alicdn.com/kf/U9b72547dbf6c4252a98134d77f012d94k.jpg)](https://ae01.alicdn.com/kf/U9b72547dbf6c4252a98134d77f012d94k.jpg)

经过静态分析可以看出，程序一开始调用一个函数是读入六个数字。gdb分析函数。

[![https://ae01.alicdn.com/kf/U7d9f9a7f2ddc481a882efdb1164e05fcO.jpg](https://ae01.alicdn.com/kf/U7d9f9a7f2ddc481a882efdb1164e05fcO.jpg)](https://ae01.alicdn.com/kf/U7d9f9a7f2ddc481a882efdb1164e05fcO.jpg)

函数read_six_numbers是通过函数sscanf从我们输入中获得的六个数字。通过mov $0x4025c3,%esi

知道字符串储存在地址0x4025c3处，查看之后发现是“%d,%d,%d,%d,%d,%d”,（好吧这个是我在ida看的）

[![https://ae01.alicdn.com/kf/U5fa8b72913ba4ee78ced4b7db62012fft.jpg](https://ae01.alicdn.com/kf/U5fa8b72913ba4ee78ced4b7db62012fft.jpg)](https://ae01.alicdn.com/kf/U5fa8b72913ba4ee78ced4b7db62012fft.jpg)

根据调用约定，前四个元素的地址应该用寄存器传递，分别是%rdx、%rcx、%r8、%r9,最后两个通过栈来传递。

[![https://ae01.alicdn.com/kf/U26ebe05beac440d7b81ee82c2f1cd7a98.jpg](https://ae01.alicdn.com/kf/U26ebe05beac440d7b81ee82c2f1cd7a98.jpg)](https://ae01.alicdn.com/kf/U26ebe05beac440d7b81ee82c2f1cd7a98.jpg)

返回查看phase_2函数，

mov %rsp,%rsi 将栈顶的的地址送给指针寄存器,rsp为字符串首地址

cmpl $0x1,(%rsp) 把第一个数字和1比较，如果相等则跳转到400f30.

[![https://ae01.alicdn.com/kf/U801ace6261744561adc3678d8090faccn.jpg](https://ae01.alicdn.com/kf/U801ace6261744561adc3678d8090faccn.jpg)](https://ae01.alicdn.com/kf/U801ace6261744561adc3678d8090faccn.jpg)

黄色部分可以看出是一段循环，这段循环是将输入与数组里的数进行比较，而数的变化通过add %eax,%eax可以看出下一位数字是上一位数字的二倍，第一个数字为1。

[![https://ae01.alicdn.com/kf/U5568f73eb3434bccab5f49cd16622463C.jpg](https://ae01.alicdn.com/kf/U5568f73eb3434bccab5f49cd16622463C.jpg)](https://ae01.alicdn.com/kf/U5568f73eb3434bccab5f49cd16622463C.jpg)

拆弹成功。

查看汇编直接进行静态分析。经过一堆压栈操作之后发现调用了sanf函数，进入函数发现是输入两个整型变量。首先对函数返回值进行判断，如果返回值为1则继续运行，不为1则炸弹爆炸。随后让输入的第一个数与7进行比较，如果比7大则炸弹爆炸。

[![https://ae01.alicdn.com/kf/Ude4dbd8bf25d46d3888910963ca6242aX.jpg](https://ae01.alicdn.com/kf/Ude4dbd8bf25d46d3888910963ca6242aX.jpg)](https://ae01.alicdn.com/kf/Ude4dbd8bf25d46d3888910963ca6242aX.jpg)

上面这两条函数是重点，第一条指令是将第一次传入的值储存到寄存器%eax中，第二条指令看不懂，经过查询发现是AT&T的汇编指令，将他转换成intel形式的指令为jmpQWORDPTR[rax\*8+0x402470]这下就比较容易看了，是取出rax*8+0x402470地址出的值并跳转到这个值指示的内存地址出继续执行。用指令x/8xg 0x402470查看这个地址存放的值。

[![https://ae01.alicdn.com/kf/Ua32018a8eedc4f7bb5e9af121988cc753.jpg](https://ae01.alicdn.com/kf/Ua32018a8eedc4f7bb5e9af121988cc753.jpg)](https://ae01.alicdn.com/kf/Ua32018a8eedc4f7bb5e9af121988cc753.jpg)

那么这个炸弹的逻辑出来了，输入两个整型数，如果第一个数是0则第二个数必须是0xcf否则炸弹爆炸，与之对应的数还有七组，分别是（1，0x137）(2，0x2c3)（3，0x100）（4，0x185）（5，0xce）（6，0x2aa）（7，0x147）。

第四个实验继续静态分析，进入输入函数发现是传入两个整型数。

[![https://ae01.alicdn.com/kf/U5a6892736fc64bb3a5f86edc3717976d3.jpg](https://ae01.alicdn.com/kf/U5a6892736fc64bb3a5f86edc3717976d3.jpg)](https://ae01.alicdn.com/kf/U5a6892736fc64bb3a5f86edc3717976d3.jpg)

根据下面的汇编代码可以看出，第一个传入的参数不能大于14，第二个传入的参数必须为0。然后分析func4

[![https://ae01.alicdn.com/kf/U8b3409a065f1481a9896902161d3774eO.jpg](https://ae01.alicdn.com/kf/U8b3409a065f1481a9896902161d3774eO.jpg)](https://ae01.alicdn.com/kf/U8b3409a065f1481a9896902161d3774eO.jpg)

[![https://ae01.alicdn.com/kf/Ubd669835de324e4cad6ee1642434a736W.jpg](https://ae01.alicdn.com/kf/Ubd669835de324e4cad6ee1642434a736W.jpg)](https://ae01.alicdn.com/kf/Ubd669835de324e4cad6ee1642434a736W.jpg)

往func4中传入了三个参数

[![https://ae01.alicdn.com/kf/U3faf384468674bf9b78133e83e7c2210J.jpg](https://ae01.alicdn.com/kf/U3faf384468674bf9b78133e83e7c2210J.jpg)](https://ae01.alicdn.com/kf/U3faf384468674bf9b78133e83e7c2210J.jpg)

上面代码的操作为x=(x>>31+x)>>1，lea (%rax,%rsi,1),%ecx，这句意思是%ecx = %rax + %rsi * 1，其中%rax就是我们刚刚求得的x，%rsi是参数b。因此%ecx = (x>>31 + x) >> 1 + b;将寄存器%ecx定义一个局部变量，名为tmp，即int tmp = (x>>31 + x) >> 1 +  b；将x带进去就是int tmp = ((c - b) >> 31 + (c -b)) >> 1 + b;当tmp小于等于输入的第一个参数时跳转到0x400ff2,当tmp大于等于输入的第一个参数的时候跳转到0x401007即相等的时候成立。分析可得到如下C语言代码


```
static int func4(int a, int b, int c)
{
    int tmp = (((c - b) + ((c - b) >> 31)) >> 1) + b;

    if (tmp <= a) {
        if (tmp == a) {
            return (0);
        } else {
            return func4(a, tmp + 1, c) * 2 + 1;
        }
    } else {
        return func4(a, b, tmp - 1) * 2;
    }

}
```

写一个程序获得答案

```
#include <stdio.h>
#include <stdlib.h>
//                %edi    %esi   %edx
static int func4(int a, int b, int c)
{
    int tmp = (((c - b) + ((c - b) >> 31)) >> 1) + b;
 
    if (tmp <= a) {
        if (tmp == a) {
            return (0);
        } else {
            return func4(a, tmp + 1, c) * 2 + 1;
        }
    } else {
        return func4(a, b, tmp - 1) * 2;
    }
}
 
int main(int argc, const char *argv[])
{
    int i, result;
 
    for (i = 0; i < 14; ++i) {
        result = func4(i, 0, 14);
        if (result == 0) {
            printf("%d\n", i);
        }
    }
    return 0;
}
```

程序输出0 1 3 7，所以答案分别为（0,0）（1,0）（3,0）（7,0）