# CSAPP LAB笔记

http://csapp.cs.cmu.edu/3e/labs.html

https://wdxtub.com/csapp/thick-csapp-lab-0/2016/04/16/

## **程序和数据**

- 学习目标
  - 位操作，数学计算，汇编程序
  - C 控制流程的表示以及数据结构
  - 体系架构和编译器
- 实验介绍
  - L1(datalab)：操作 bits
  - L2(bomblab)：拆除一个二进制炸弹
  - L3(attacklab)：基本的代码注入攻击

## **存储体系**

- 学习目标
  - 内存技术，内存层级，缓存，磁盘，局部性原理
  - 体系架构和操作系统
- 实验介绍
  - L4(cachelab)：做一个缓存模拟器并且进行优化，与此同时学习如何在程序中利用局部性

## **异常控制流**

- 学习目标
  - 硬件异常，进程，进程控制，Unix 信号，非本地跳转(nonlocal jumps)
  - 编译器，操作系统和提及架构
- 实验介绍
  - L5(tshlab)：写一个自己的 Unix shell

## **虚拟内存**

- 学习目标
  - 虚拟内存，地址翻译，动态存储分配
  - 体系架构和操作系统
- 实验介绍
  - L6(malloclab)：自己实现 malloc 功能，借此了解系统层次编程

## **网络与并行**

- 主题
  - 高层和底层 IO，网络编程
  - Internet 服务，Web 服务器
  - IO 复用
  - 网络，操作系统和体系架构
- 作业
  - L7(proxylab)：写一个 web proxy，借此学习网络编程和并行/同步





## L1(datalab)：操作 bits



## L2(bomblab)：拆除一个二进制炸弹

### 汇编复习

想要完成拆弹任务，不但需要理解不同寄存器的常用方法，也要弄明白具体的操作符是什么意思：

|   类型   |     语法      |                      例子                      |             备注             |
| :------: | :-----------: | :--------------------------------------------: | :--------------------------: |
|   常量   | 符号`$` 开头  |               `$-42`, `$0x15213`               | 一定要注意十进制还是十六进制 |
|  寄存器  | 符号 `%` 开头 |                 `%esi`, `%rax`                 |     可能存的是值或者地址     |
| 内存地址 |  括号括起来   | `(%rbx)`, `0x1c(%rax)`, `0x4(%rcx, %rdi, 0x1)` |   括号实际上是去寻址的意思   |

一些汇编语句与实际命令的转换：

|            指令             |         效果         |
| :-------------------------: | :------------------: |
|      `mov %rbx, %rdx`       |     `rdx = rbx`      |
|      `add (%rdx), %r8`      | `r8 += value at rdx` |
|        `mul $3, %r8`        |      `r8 *= 3`       |
|        `sub $1, %r8`        |        `r8--`        |
| `lea (%rdx, %rbx, 2), %rdx` | `rdx = rdx + rbx*2`  |

比较与跳转是拆弹的关键，基本所有的字符判断就是通过比较来实现的，比方说 `cmp b,a` 会计算 `a-b` 的值，`test b, a` 会计算 `a&b`，注意运算符的顺序。例如

```
cmpl %r9, %r10
jg   8675309
```

等同于 `if %r10 > %r9, jump to 8675309`

各种不同的跳转：

|  指令   |         效果         | 指令 |            效果             |
| :-----: | :------------------: | :--: | :-------------------------: |
|   jmp   |     Always jump      |  ja  |  Jump if above(unsigned >)  |
|  je/jz  |  Jump if eq / zero   | jae  |    Jump if above / equal    |
| jne/jnz | Jump if !eq / !zero  |  jb  |  Jump if below(unsigned <)  |
|   jg    |   Jump if greater    | jbe  |    Jump if below / equal    |
|   jge   | Jump if greater / eq |  js  | Jump if sign bits is 1(neg) |
|   jl    |     Jump if less     | jns  | Jump if sign bit is 0 (pos) |
|   jle   |  Jump if less / eq   |  x   |              x              |

举几个例子

```assembly
cmp $0x15213, %r12
jge deadbeef
```

若 `%r12 >= 0x15213`，则跳转到 `0xdeadeef`

```assembly
cmp %rax, %rdi
jae 15213b
```

如果 `%rdi` 的无符号值大于等于 `%rax`，则跳转到 `0x15213b`

```assembly
test %r8, %r8
jnz (%rsi)
```

如果 `%r8 & %r8` 不为零，那么跳转到 `%rsi` 存着的地址中。

```assembly
# 检查符号表
# 然后可以寻找跟 bomb 有关的内容
objdump -t bomb | less 

# 反编译
# 搜索 explode_bomb
objdump -d bomb > bomb.txt

# 显示所有字符
strings bomb | less
```

### GDB 介绍

```assembly
gdb bomb

# 获取帮助
help

# 设置断点
break explode_bomb
break phase_1

# 开始运行
run

# 检查汇编 会给出对应的代码的汇编
disas 

# 查看寄存器内容
info registers

# 打印指定寄存器
print $rsp

# 每步执行
stepi

# 检查寄存器或某个地址
x/4wd $rsp

# 在汇编位置加断点
b *0x40190b
```

## L3(attacklab)：基本的代码注入攻击

### 预备知识

x86-64 架构的寄存器有一些使用习惯，比如：

- 用来传参数的寄存器：%rdi, %rsi, %rdx, %rcx, %r8, %r9
- 保存返回值的寄存器：%rax
- 被调用者保存状态：%rbx, %r12, %r13, %r14, %rbp, %rsp
- 调用者保存状态：%rdi, %rsi, %rdx, %rcx, %r8, %r9, %rax, %r10, %r11
- 栈指针：%rsp
- 指令指针：%rip

函数调用前需要把某些以后仍旧需要用到的值保存起来。

而对于 x86-64 的栈来说，栈顶的地址最小，栈底的地址最大，寄存器 `%rsp` 保存着指向栈顶的指针。栈支持两个操作：

- `push %reg`：`%rsp` 的值减去 8，把寄存器 `%reg` 中的值放到 `(%rsp)` 中
- `pop %reg`：把寄存器 `(%rsp)` 中的值放到 `%reg` 中，`%rsp` 的值加上 8

接下来需要了解的事情是，每个函数都有自己的栈帧(stack frame)，可以把它理解为每个函数的工作空间，保存着：

- 本地变量

- 调用者和被调用者保存的寄存器里的值

- 其他一些函数调用可选的值

  

x86-64 的函数调用过程，需要做的设置有：

- 调用者：
  - 为要保存的寄存器值及可选参数分配足够大控件的栈帧
  - 把所有调用者需要保存的寄存器存储在帧中
  - 把所有需要保存的可选参数按照逆序存入帧中
  - `call foo:` 会先把 `%rip` 保存到栈中，然后跳转到 label `foo`
- 被调用者
  - 把任何被调用者需要保存的寄存器值压栈减少 `%rsp` 的值以便为新的帧腾出空间

x86-64 的函数返回过程：

- 被调用者
  - 增加 `%rsp` 的计数，逆序弹出所有的被调用者保存的寄存器，执行 `ret: pop %rip`

有了上面的基础知识，我们大概就能明白，利用缓冲区溢出，实际上是通过重写返回值地址，来执行另一个代码片段，就是所谓代码注入了。比较关键的点在于

- 熟悉 x86-64 约定俗成的用法
- 使用 `objdump -d` 来了解相关的偏移量
- 使用 `gdb` 来确定栈地址

这之后，我们需要把需要注入的代码转换位字节码，这样机器才能执行，这里可以使用 `gcc` 和 `objdump` 来完成这个工作

```assembly
# 假设 foo.s 是我们想要注入的代码
vim foo.s

# 利用 gcc 生成对应的字节码 foo.o
gcc -c foo.s

# 通过 objdump 来查看其内容，可以看到对应的字节码
objdump -d foo.o | less

# 然后需要把十六进制代码转换成字符串这样我们可以写在程序里
./hex2raw -i inputfile -o outputfile
```

另一种攻击是使用 return-oriented programming 来执任意代码，这种方法在 stack 不可以执行或者位置随机的时候很有用。

这种方法主要是利用 gadgets 和 string 来组成注入的代码。具体来说是使用 `pop` 和 `mov` 指令加上某些常数来执行特定的操作。也就是说，利用程序已有的代码，重新组合成我们需要的东西，这样就绕开了系统的防御机制。

举个例子，一个代码片段如下：

```c
void foo(char *input){
    char buf[32];
    ...
    strcpy (buf, input;
    return;
}
```

假设我们这里想要把一个值 `0xBBBBBBBB` 弹出到 `%rbx` 中并且移动它到 `%rax` 中，我们找到下面两个 gadgets:

- `address1: mov %rbx, %rax; ret`
- `address2: pop %rbx; ret`

所以在这里我们其实不需要关心如何在 buffer 中运行我们的代码，而只需要知道 buffer 的 size，从而改写返回地址，即可以利用程序中原有的代码进行我们的操作。

在这个例子中，因为 address2 中的代码是把栈顶的值弹出到 `%rbx` 中，所以执行的时候，就会把 `0xBBBBBBBB` 放到 `%rbx` 中，现在程序就指向 address1 了，然后就会继续执行 address1，也就达到我们的目的，把 `0xBBBBBBBB` 放到了 `%rax` 中。

那么问题来了，我们如何能找到想要的 gadget 呢？在这个实验中，提供了一个 `farm.c`，可以从这里找到我们需要的 gadgets。

```assembly
gcc -c  farm.c
objdump -d farm.o | less
```

一些建议：

- 注意寻找 `c3` 结尾的代码，因为这可以作为每个 gadget 的最后一句（也就是正常返回）
- 画出栈的图
- 注意字节的顺序 (little endian)

这个阶段我们需要重复之前第二阶段的工作，但是因为程序的限制，只能另辟蹊径了，这里我们只需要利用下表给出的指令类型，以及前八个寄存器(`%rax - %rdi`)。表格如下：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP LAB笔记.assets\14554593555844.jpg)

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP LAB笔记.assets\14554593658395.jpg)

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP LAB笔记.assets\14554593790251.jpg)

注意这里的内容都是 16 进制。另外两个指令是：

- `ret`: 一个字节编码 `0xc3`
- `nop`: 什么都不做，只是让程序计数器加一，一个字节编码 `0x90`



对应的十六进制代码为（同样需要注意不全十六位的 0，不然会出段错误），这里还有一个需要注意的地方是偏移量，在执行第一句时，`%rsp` 已经是指向下一句了（指向的是当前的栈顶，正在执行的语句是不需要考虑的），所以可以数出来，在 cookie 之前一共有 9 条指令，每个 8 byte，所以一共的偏移量是 `0x48`（十进制的 72）。







