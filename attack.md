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