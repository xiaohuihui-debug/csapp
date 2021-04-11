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