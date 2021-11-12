# CSAPP 深入理解计算机结构笔记

http://www.cs.cmu.edu/afs/cs/academic/class/15213-f15/www/lectures/

https://wdxtub.com/csapp/thin-csapp-0/2016/04/16/

## Questions:





## 数据在机器中的存储形式

### 整形Integer

![image-20210511171923045](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210511171923045.png)

观察可以得知两个很重要的特性

- |TMin| = TMax + 1 (范围并不是对称的)
- UMax = 2*TMax + 1
- ![image-20210511172456501](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210511172456501.png)

### 浮点数的表示方法IEEE

### todo

## CPU

![image-20210512142623807](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210512142623807.png)

### 汇编知识

![image-20210512151525034](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210512151525034.png)

![image-20210512160422063](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210512160422063.png)

#### 寄存器

x86-64 架构中的整型寄存器如下图所示（暂时不考虑浮点数的部分）

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14611562175522.jpg)

![image-20210512152408594](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210512152408594.png)

仔细看看寄存器的分布，我们可以发现有不同的颜色以及不同的寄存器名称，黄色部分是 16 位寄存器，也就是 16 位处理器 8086 的设计，然后绿色部分是 32 位寄存器（这里我是按照比例画的），给 32 位处理器使用，而蓝色部分是为 64 位处理器设计的。这样的设计保证了令人震惊的向下兼容性，几十年前的 x86 代码现在仍然可以运行！

前六个寄存器(%rax, %rbx, %rcx, %rdx, %rsi, %rdi)称为通用寄存器，有其『特定』的用途：

- %rax(%eax) 用于做累加
- %rcx(%ecx) 用于计数
- %rdx(%edx) 用于保存数据
- %rbx(%ebx) 用于做内存查找的基础地址
- %rsi(%esi) 用于保存源索引值
- %rdi(%edi) 用于保存目标索引值
- 临时数据存放在 (%rax, …)
- **运行时栈的地址存储在 (%rsp) 中**
- **目前的代码控制点存储在 (%rip, …) 中**
- 状态标志位：
  - CF: Carry Flag, 进位标志位(针对无符号数)
  - ZF: Zero Flag, 为零标志位
  - SF: Sign Flag, 正负标志位(针对有符号数)
  - OF： Overflow Flag, 溢出标志位(针对有符号数)（溢出的情况是 `(a>0 && b > 0 && t <0) || (a<0 && b<0 && t>=0)`）
  - leaq命令不会设置上述标志位，因为它本意用于地址计算
- **而 %rsp(%esp) 和 %rbp(%ebp) 则是作为栈指针和基指针来使用的**。
- [如果参数没有超过六个，那么会放在：%rdi, %rsi, %rdx, %rcx, %r8, %r9 中。如果超过了，会另外放在一个栈中。而返回值会放在 %rax 中。]

#### MOV指令

而 %rsp(%esp) 和 %rbp(%ebp) 则是作为栈指针和基指针来使用的。下面我们通过 `movq` 这个指令来了解操作数的三种基本类型：立即数(Imm)、寄存器值(Reg)和内存值(Mem)。

对于 `movq` 指令来说，需要源操作数和目标操作数，源操作数可以是立即数、寄存器值或内存值的任意一种，但目标操作数只能是寄存器值或内存值。指令的具体格式可以这样写 `movq [Imm|Reg|Mem], [Reg|Mem]`，第一个是源操作数，第二个是目标操作数，例如：

- `movq Imm, Reg` -> `mov $0x5, %rax` -> `temp = 0x5;`
- `movq Imm, Mem` -> `mov $0x5, (%rax)` -> `*p = 0x5;`
- `movq Reg, Reg` -> `mov %rax, %rdx` -> `temp2 = temp1;`
- `movq Reg, Mem` -> `mov %rax, (%rdx)` -> `*p = temp;`
- `movq Mem, Reg` -> `mov (%rax), %rdx` -> `temp = *p;`

这里有一种情况是不存在的，没有 `movq Mem, Mem` 这个方式，也就是说，我们没有办法用一条指令完成内存间的数据交换。

#### 寻址

上面的例子中有些操作数是带括号的，括号的意思就是寻址，这也分两种情况：

- 普通模式，(R)，相当于 `Mem[Reg[R]]`，也就是说寄存器 R 指定内存地址，类似于 C 语言中的指针，语法为：`movq (%rcx), %rax` 也就是说以 %rcx 寄存器中存储的地址去内存里找对应的数据，存到寄存器 %rax 中
- 移位模式，D(R)，相当于 `Mem[Reg[R]+D]`，寄存器 R 给出起始的内存地址，然后 D 是偏移量，语法为：`movq 8(%rbp),%rdx` 也就是说以 %rbp 寄存器中存储的地址再加上 8 个偏移量去内存里找对应的数据，存到寄存器 %rdx 中

因为寻址这个内容比较重要，所以多说两句，不然之后接触指针会比较吃力。对于寻址来说，**比较通用的格式是 `D(Rb, Ri, S)` -> `Mem[Reg[Rb]+S*Reg[Ri]+D]`**，其中：

- `D` - 常数偏移量
- `Rb` - 基寄存器
- `Ri` - 索引寄存器，不能是 %rsp
- `S` - 系数

除此之外，还有如下三种特殊情况

- `(Rb, Ri)` -> `Mem[Reg[Rb]+Reg[Ri]]`
- `D(Rb, Ri)` -> `Mem[Reg[Rb]+Reg[Ri]+D]`
- `(Rb, Ri, S)` -> `Mem[Reg[Rb]+S*Reg[Ri]]`

**多维数组&寻址**

```c
int get_a_digit(int index, int dig)
{
    return A[index][dig];
}
```

对应的汇编代码为，这里假设 C = 5

```assembly
leaq    (%rdi, %rdi, 4), %rax   # 5 * index
addl    %rax, %rsi              # 5 * index + dig
movl    A(, %rsi, 4), %eax      # M[A + 4*(5*index+dig)]
```

#### 运算操作

需要两个操作数的指令

- `addq Src, Dest` -> `Dest = Dest + Src`
- `subq Src, Dest` -> `Dest = Dest - Src`
- `imulq Src, Dest` -> `Dest = Dest * Src`
- `salq Src, Dest` -> `Dest = Dest << Src`
- `sarq Src, Dest` -> `Dest = Dest >> Src`
- `shrq Src, Dest` -> `Dest = Dest >> Src`
- `xorq Src, Dest` -> `Dest = Dest ^ Src`
- `andq Src, Dest` -> `Dest = Dest & Src`
- `orq Src, Dest` -> `Dest = Dest | Src`

需要一个操作数的指令

- `incq Dest` -> `Dest = Dest + 1`
- `decq Dest` -> `Dest = Dest - 1`
- `negq Dest` -> `Dest = -Dest`
- `notq Dest` -> `Dest = ~Dest`

#### 过程调用

在过程调用中主要涉及三个重要的方面：

1. 传递控制：包括如何开始执行过程代码，以及如何返回到开始的地方
2. **传递数据**：包括过程需要的参数以及过程的返回值 [如果参数没有超过六个，那么会放在：%rdi, %rsi, %rdx, %rcx, %r8, %r9 中。如果超过了，会另外放在一个栈中。而返回值会放在 %rax 中。]
3. 内存管理：如何在过程执行的时候分配内存，以及在返回之后释放内存

#### 栈结构

![image-20210512174214770](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210512174214770.png)

```asm
0000000000400540 <multstore>:
    # x 在 %rdi 中，y 在 %rsi 中，dest 在 %rdx 中
    400540: push    %rbx            # 通过压栈保存 %rbx
    400541: mov     %rdx, %rbx      # 保存 dest
    400544: callq   400550 <mult2>  # 调用 mult2(x, y)
    # t 在 %rax 中
    400549: mov     %rax, (%rbx)    # 结果保存到 dest 中
    40054c: pop     %rbx            # 通过出栈恢复原来的 %rbx
    40054d: retq                    # 返回

0000000000400550 <mult2>:
    # a 在 %rdi 中，b 在 %rsi 中
    400550: mov     %rdi, %rax      # 得到 a 的值
    400553: imul    %rsi, %rax      # a * b
    # s 在 %rax 中
    400557: retq                    # 返回
```

![image-20210512174849490](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210512174849490.png)

![image-20210513095414590](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210513095414590.png)

#### 数据的内存结构

内存对齐

具体对齐的原则是，如果数据类型需要 K 个字节，那么地址都必须是 K 的倍数”——这只是windows的原则，而Linux中的对齐策略是“2字节数据类型的地址必须为2的倍数，较大的数据类型（int,double,float）的地址必须是4的倍数



#### 缓冲区溢出

![image-20210513103225124](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210513103225124.png)

最上面是运行时栈，有 8MB 的大小限制，一般用来保存局部变量。然后是堆，动态的内存分配会在这里处理，例如 `malloc()`, `calloc()`, `new()` 等。然后是数据，指的是静态分配的数据，比如说全局变量，静态变量，常量字符串。最后是共享库等可执行的机器指令，这一部分是只读的。

##### todo

https://wdxtub.com/csapp/thick-csapp-lab-3/2016/04/16/

那么我们现在来看看，怎么处理缓冲区溢出攻击，有几种方式：

1. 好好写代码，尽量不让缓冲区异常
2. 程序容易出问题，那么提供系统层级的保护
3. 编译器也可以来个认证(stack canaries)

第一种，避免缓冲区溢出，我们用更安全的方法，如：`fgets`, `strncpy` 等等。

第二种，栈的位置不确定，让缓冲区溢出没办法影响到，并且每次位置都不一样，就不怕被暴力破解。并且也可以把一段内存标记为只读，那么就避免因为缓冲区溢出而导致的重写。

第三种，使用认证机制(Stack Canaries)。简单来说，就是在超出缓冲区的位置加一个特殊的值，如果发现这个值变化了，那么就知道出问题了。

但是，除了缓冲区溢出，还有另一种攻击的方式，称为**返回导向编程** (ROP全称为Return-oriented Programming)。可以利用修改已有的代码，来绕过系统和编译器的保护机制，攻击者控制堆栈调用以劫持程序控制流并执行针对性的机器语言指令序列（称为Gadgets）。每一段 gadget 通常结束于 return 指令，并位于共享库代码中的子程序。系列调用这些代码，攻击者可以在拥有更简单攻击防范的程序内执行任意操作。



#### 代码优化

前面了解了许多机器代码以及程序执行机制的相关知识，这一节我们来学习如何利用这些性质来优化代码。

- 用好编译器的不同参数设定
- 写对编译器友好的代码，尤其是过程调用和内存引用，时刻注意内层循环
- 根据机器来优化代码，包括利用指令级并行、避免不可以预测的分支以及有效利用缓存

##### 通用技巧

即使是常数项系数的操作，同样可能影响性能。性能的优化是一个多层级的过程：算法、数据表示、过程和循环，都是需要考虑的层次。于是，这就要求我们需要对系统有一定的了解，例如：

- 程序是如何编译和执行的
- 现代处理器和内存是如何工作的
- 如何衡量程序的性能以及找出瓶颈
- 如何保持代码模块化的前提下，提高程序性能

最根源的优化是对编译器的优化，比方说再寄存器分配、代码排序和选择、死代码消除、效率提升等方面，都可以由编译器做一定的辅助工作。

但是因为这毕竟是一个自动的过程，而代码本身可以非常多样，在不能改变程序行为的前提下，很多时候编译器的优化策略是趋于保守的。并且大部分用来优化的信息来自于过程和静态信息，很难充分进行动态优化。

1. 数据结构的优化，考虑内存对齐问题，将大size的放前面，占用更少的内存；

2. 程序过程优化：将重复的过程、计算放在循环外面；

3. 减少计算强度：乘法转为移位、加法等；

4. 汇编内存优化：减少对数组取值次数，使用临时变量暂存后再存入数组；

   

##### 处理条件分支

这个问题，如果不是对处理器执行指令的机制有一定了解的话，可能会难以理解。

现代处理器普遍采用超标量设计，也就是基于流水线来进行指令的处理，也就是说，当执行当前指令时，接下来要执行的几条指令已经进入流水线的处理流程了。

这个很重要，对于顺序执行来说，不会有任何问题，但是对于条件分支来说，在跳转指令时可能会改变程序的走向，也就是说，之前载入的指令可能是无效的。这个时候就只能清空流水线，然后重新进行载入。为了减少清空流水线所带来的性能损失，处理器内部会采用称为『分支预测』的技术。

比方说在一个循环中，根据预测，可能除了最后一次跳出循环的时候会判断错误之外，其他都是没有问题的。这就可以接受，但是如果处理器不停判断错误的话（比方说代码逻辑写得很奇怪），性能就会得到极大的拖累。

分支问题有些时候会成为最主要的影响性能的因素，但有的时候其实很难避免。



### 内存与缓存

#### 局部性原理 Locality

局部性的思路很简单[2]：

- 时间局部性(Temporal Locality): 如果一个信息项正在被访问，那么在近期它很可能还会被再次访问。程序循环、堆栈等是产生时间局部性的原因。
- 空间局部性(Spatial Locality): 在最近的将来将用到的信息很可能与现在正在使用的信息在空间地址上是临近的
- 顺序局部性(Order Locality): 在典型程序中，除转移类指令外，大部分指令是顺序进行的。顺序执行和非顺序执行的比例大致是5:1。此外，对大型数组访问也是顺序的。指令的顺序执行、数组的连续存放等是产生顺序局部性的原因。

![image-20210513170134873](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210513170134873.png)

![image-20210513170203094](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210513170203094.png)

#### 缓存未命中的类型

缓存未命中有三种，这里进行简要介绍

- 强制性失效(Cold/compulsory Miss): CPU 第一次访问相应缓存块，缓存中肯定没有对应数据，这是不可避免的
- 冲突失效(Conflict Miss): 在直接相联或组相联的缓存中，不同的缓存块由于索引相同相互替换，引起的失效叫做冲突失效
  - 假设这里有 32KB 直接相联的缓存
  - 如果有两个 8KB 的数据需要来回访问，但是这两个数组都映射到相同的地址，缓存大小足够存储全部的数据，但是因为相同地址发生了冲突需要来回替换，发生的失效则全都是冲突失效（第一次访问失效依旧是强制性失效），这时缓存并没有存满
- 容量失效(Capacity Miss): 有限的缓存容量导致缓存放不下而被替换，被替换出去的缓存块再被访问，引起的失效叫做容量失效
  - 假设这里有 32KB 直接相联的缓存
  - 如果有一个 64KB 的数组需要重复访问，数组的大小远远大于缓存大小，没办法全部放入缓存。第一次访问数组发生的失效全都是强制性失效。之后再访问数组，再发生的失效则全都是容量失效，这时缓存已经存满，容量不足以存储全部数据

## 链接

![image-20210514112018598](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210514112018598.png)

#### 预处理阶段：

1. 宏定义指令，如 `#define a b` 这种伪指令，预编译所要做的是将程序中的所有 `a` 用 `b` 替换，但作为字符串常量的 `a` 则不被替换。还有 `#undef`，则将取消对某个宏的定义，使以后该串的出现不再被替换
2. 条件编译指令，如 `#ifdef`, `#ifndef`, `#else`, `#elif`, `#endif`等。这些伪指令的引入使得程序员可以通过定义不同的宏来决定编译程序对哪些代码进行处理。预编译程序将根据有关的文件，将那些不必要的代码过滤掉
3. 头文件包含指令，如 `#include "FileName"` 。该指令将头文件中的定义统统都加入到它所产生的输出文件中，以供编译程序对之进行处理
4. 特殊符号，预编译程序可以识别一些特殊的符号。例如在源程序中出现的 `LINE` 标识将被解释为当前行号（十进制数），`FILE` 则被解释为当前被编译的C源程序的名称。预编译程序对于在源程序中出现的这些串将用合适的值进行替换

头文件搜索规则如下：

1. 所有头文件的搜寻会从 `-I` 开始
2. 然后找环境变量 `C_INCLUDE_PATH`, `CPLUS_INCLUDE_PATH`, `OBJC_INCLUDE_PATH` 指定的路径
3. 再找默认目录(`/usr/include`, `/usr/local/include`, `/usr/lib/gcc-lib/i386-linux/2.95.2/include` 等等)

1. 编译、优化阶段：
   1. 词法和语法分析
   2. 翻译为等价的中间代码或者汇编代码

#### 汇编阶段：

1. 汇编语言代码转为目标机器指令， .o文件（可重定位目标文件 Relocatable object file (`.o` file)）
2. .o文件由段组成，通常一个.o文件至少有两个段：
   1. 代码段：该段中所包含的主要是程序的指令。该段一般是可读和可执行的，但一般却不可写
   2. 数据段：主要存放程序中要用到的各种全局变量或静态的数据。一般数据段都是可读，可写，可执行的

#### 链接阶段：

##### 符号解析 Symbol Resolution：

我们在代码中会声明变量及函数，之后会调用变量及函数，所有的符号声明都会被保存在符号表(symbol table)中，而符号表会保存在由汇编器生成的 object 文件中（也就是 `.o` 文件）。符号表实际上是一个结构体数组，每一个元素包含名称、大小和符号的位置。

在 symbol resolution 阶段，链接器会给每个符号应用一个唯一的符号定义，用作寻找对应符号的标志。

##### 重定位 Relocation：

这一步所做的工作是把原先分开的代码和数据片段汇总成一个文件，会把原先在 `.o` 文件中的相对位置转换成在可执行程序的绝对位置，并且据此更新对应的引用符号（才能找到新的位置）



通过链接的方式，可以使编程更模块化、效率化

##### **三种对象文件：**

1. 可重定位目标文件 Relocatable object file (.o file)
   - 每个 `.o` 文件都是由对应的 `.c` 文件通过编译器和汇编器生成，包含代码和数据，可以与其他可重定位目标文件合并创建一个可执行或共享的目标文件
2. 可执行目标文件 Executable object file (.out file)
   - 由链接器生成，可以直接通过加载器加载到内存中充当进程执行的文件，包含代码和数据
3. 共享目标文件 Shared object file (.so file)
   - 在 windows 中被称为 Dynamic Link Libraries(DLLs)，是类特殊的可重定位目标文件，可以在链接(静态共享库)时加入目标文件或加载时或运行时(动态共享库)被动态的加载到内存并执行

##### 对象文件格式 Executable and Linkable Format(ELF)：

![image-20210514142017472](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210514142017472.png)

1. ELF header
   - 包含 word size, byte ordering, file type (.o, exec, .so), machine type, etc.
2. Segment header table
   - 包含 page size, virtual addresses memory segments(sections), segment sizes
3. .text section
   - 代码部分
4. .rodata section
   - 只读数据部分，例如跳转表
5. .data section
   - 初始化的全局变量、局部静态变量
6. .bss section
   - 未初始化的全局变量、局部静态变量
7. .symtab section
   - 包含 symbol table, procedure 和 static variable names 以及 section names 和 location
8. .rel.txt section
   - .text section 的重定位信息
9. .rel.data section
   - .data section 的重定位信息
10. .debug section
    - 包含 symbolic debugging (`gcc -g`) 的信息
11. Section header table
    - 每个 section 的大小和偏移量

##### 链接符号

链接器实际上会处理三种不同的符号，对应于代码中不同写法的部分：

- 全局符号 Global symbols
  - 在当前模块中定义，且可以被其他代码引用的符号，例如非静态 C 函数和非静态全局变量
- 外部符号 External symbols
  - 同样是全局符号，但是是在其他模块（也就是其他的源代码）中定义的，但是可以在当前模块中引用
- 本地符号 Local symbols
  - 在当前模块中定义，只能被当前模块引用的符号，例如静态函数和静态全局变量
  - 注意，Local linker symbol 并不是 local program variables

强符号：函数和初始化的全局变量

弱符号：未初始化的全局变量

链接器在处理强弱符号的时候遵守以下规则：

1. 不能出现多个同名的强符号，不然就会出现链接错误
2. 如果有同名的强符号和弱符号，选择强符号，也就意味着弱符号是『无效』
3. **如果有多个弱符号，随便选择一个；会出现同名的弱符号，指向同一个未初始化的地址**

1. 由于链接中，对于全局变量存在多个弱符号指向同一地址，导致奇特的行为，编程建议：

   **如果可能，尽量避免使用全局变量**

   如果一定要用的话，注意下面几点：

   - 使用静态变量
   - **定义全局变量的时候初始化**--转为强符号
   - 注意使用 `extern` 关键字

##### 重定位过程：

![image-20210514144229577](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210514144229577.png)

![image-20210705151130545](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210705151130545.png)

##### 静态库（static library）

链接器是如何解析外部引用的呢？详细的步骤为：

- 扫描当前命令中的 `.o` 和 `.a` 文件
- 扫描过程中，维护一个当前未解析引用的列表
- 扫描到新的 `.o` 或 `.a` 文件时，试图去寻找未解析引用
- 如果扫描结束时仍旧有为解析的引用，则报错

因为是按顺序查找，所以实际上是有引用依赖问题的，也就是说写编译命令的时候，顺序是很重要的！我们看下面这个例子，这里 `libtest.o` 中引用了 `lmine` 库中的 `libfun` 函数，仔细比较两个的顺序：

```bash
unix> gcc -L. libtest.o -lmine
# 上面这句不会出错，但是下面的会
unix> gcc -L. -lmine libtest.o
libtest.o: In function `main`:
libtest.o(.text+0x4): Undefined reference to `libfun`
```

第一条命令中，在编译链接的时候，如果在 libtest.o 中发现了外部引用，就会在 -lmine 中查找，但是如果反过来，在第二条语句中 libtest.o 后面没有东西，就会出现找不到引用的错误。从中我们可以得到一个写编译命令的技巧：**把静态库都放到后面去**.

##### 动态共享库（dynamic library）

动态链接可以在首次载入的时候执行(load-time linking)，这是 Linux 的标准做法，会由动态链接器 `ld-linux.so` 完成，比方标准 C 库(libc.so) 通常就是动态链接的，这样所有的程序可以共享同一个库，而不用分别进行封装。

动态链接也可以在程序开始执行的时候完成(run-time linking)，在 Linux 中使用 `dlopen()` 接口来完成（会使用函数指针），通常用于分布式软件，高性能服务器上。而且共享库也可以在多个进程间共享，这在后面学习到虚拟内存的时候会介绍。

![image-20210514173615175](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210514173615175.png)

http://jayconrod.com/posts/23/tutorial-function-interposition-in-linux  Linux修改动态库函数指向

因为这相当于是某种链接技术，所以同样可以在不同的时候发生，如：

- 编译时
- 链接时
- 载入/运行时

这个技术可以应用在

- 安全方面
  - 沙盒机制
  - 加密
- 调试方面
  - 可以找到隐藏比较深的 bug
- 监控和查看性能
  - 统计函数调用的次数
  - 检测内存泄露
  - 生成地址记录



## 异常控制流

异常控制流存在于系统的每个层级，最底层的机制称为**异常(Exception)**，用以改变控制流以响应系统事件，通常是由硬件的操作系统共同实现的。更高层次的异常控制流包括**进程切换(Process Context Switch)**、**信号(Signal)**和**非本地跳转(Nonlocal Jumps)**，也可以看做是一个从硬件过渡到操作系统，再从操作系统过渡到语言库的过程。进程切换是由硬件计时器和操作系统共同实现的，而信号则只是操作系统层面的概念了，到了非本地跳转就已经是在 C 运行时库中实现的了。

### 异常 Exception

这里的异常指的是把控制交给系统内核来响应某些事件（例如处理器状态的变化），其中内核是操作系统常驻内存的一部分，而这类事件包括除以零、数学运算溢出、页错误、I/O 请求完成或用户按下了 ctrl+c 等等系统级别的事件。

具体的过程可以用下图表示：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14613541138958.jpg)

系统会通过异常表(Exception Table)来确定跳转的位置，每种事件都有对应的唯一的异常编号，发生对应异常时就会调用对应的异常处理代码

#### 异步异常（中断）

**异步异常(Asynchronous Exception)称之为中断(Interrupt)，是由处理器外面发生的事情引起的。**对于执行程序来说，这种“中断”的发生完全是异步的，因为不知道什么时候会发生。CPU对其的响应也完全是被动的，但是可以屏蔽掉[1]。这种情况下：

- 需要设置处理器的中断指针(interrupt pin)
- 处理完成后会返回之前控制流中的『下一条』指令

比较常见的中断有两种：计时器中断和 I/O 中断。计时器中断是由计时器芯片每隔几毫秒触发的，内核用计时器终端来从用户程序手上拿回控制权。I/O 中断类型比较多样，比方说键盘输入了 ctrl-c，网络中一个包接收完毕，都会触发这样的中断。

#### 同步异常

**同步异常(Synchronous Exception)是因为执行某条指令所导致的事件，分为陷阱(Trap)、故障(Fault)和终止(Abort)三种情况。**

| 类型 |       原因       |       行为       |        示例         |
| :--: | :--------------: | :--------------: | :-----------------: |
| 陷阱 |    有意的异常    | 返回到下一条指令 |   系统调用，断点    |
| 故障 | 潜在可恢复的错误 |  返回到当前指令  | 页故障(page faults) |
| 终止 |  不可恢复的错误  |   终止当前程序   |      非法指令       |

这里需要注意三种不同类型的处理方式，比方说陷阱和中断一样，会返回执行『下一条』指令；而故障会重新执行之前触发事件的指令；终止则是直接退出当前的程序。

#### 系统调用示例

系统调用看起来像是函数调用，但其实是走异常控制流的，在 x86-64 系统中，每个系统调用都有一个唯一的 ID，如

| 编号 |   名称   |      描述      |
| :--: | :------: | :------------: |
|  0   |  `read`  |    读取文件    |
|  1   | `write`  |    写入文件    |
|  2   |  `open`  |    打开文件    |
|  3   | `close`  |    关闭文件    |
|  4   |  `stat`  |  获取文件信息  |
|  57  |  `fork`  |    创建进程    |
|  59  | `execve` |  执行一个程序  |
|  60  | `_exit`  |    关闭进程    |
|  62  |  `kill`  | 向进程发送信号 |

举个例子，假设用户调用了 `open(filename, options)`，系统实际上会执行 `__open` 函数，也就是进行系统调用 `syscall`，如果返回值是负数，则是出错，汇编代码如下：

```assembly
00000000000e5d70 <__open>:
    ...
    e5d79: b8 02 00 00 00     mov $0x2, %eax    # open 是编号 2 的系统调用
    e5d7e: 0f 05              syscall           # 调用的返回值会在 %rax 中
    e5d80: 48 3d 01 f0 ff ff  cmp $0xfffffffffffff001, %rax
    ...
    e5dfa: c3                 retq
```

对应的示意图是：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14613688255926.jpg)

#### 故障示例

这里我们以 Page Fault 为例，来说明 Fault 的机制。Page Fault 发生的条件是：

- 用户写入内存位置
- 但该位置目前还不在内存中

比如：

```c++
int a[1000];
main()
{
    a[500] = 13;
}
```

那么系统会通过 Page Fault 把对应的部分载入到内存中，然后重新执行赋值语句：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14613689402121.jpg)

但是如果代码改为这样：

```c++
int a[1000];
main()
{
    a[5000] = 13;
}
```

也就是引用非法地址的时候，整个流程就会变成：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14613690660319.jpg)

具体来说会像用户进程发送 `SIGSEGV` 信号，用户进程会以 segmentation fault 的标记退出。

从上面我们就可以看到**异常的具体实现是依靠在用户代码和内核代码间切换而实现的，是非常底层的机制。**

### 进程

进程是计算机科学中最为重要的思想之一，进程才是程序（指令和数据）的真正运行实例。之所以重要，是因为进程给每个应用提供了**两个非常关键的抽象**：一是**逻辑控制流**，二是**私有地址空间**。逻辑控制流通过称为上下文切换(context switching)的内核机制让每个程序都感觉自己在独占处理器。私有地址空间则是通过称为虚拟内存(virtual memory)的机制让每个程序都感觉自己在独占内存。这样的抽象使得具体的进程不需要操心处理器和内存的相关适宜，也保证了在不同情况下运行同样的程序能得到相同的结果。

#### 进程切换 Process Context Switch

这么多进程，具体是如何工作的呢？我们来看看下面的示意图：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14613707308133.jpg)

左边是单进程的模型，内存中保存着进程所需的各种信息，因为该进程独占 CPU，所以并不需要保存寄存器值。而在右边的单核多进程模型中，虚线部分可以认为是当前正在执行的进程，**因为我们可能会切换到其他进程，所以内存中需要另一块区域来保存当前的寄存器值**，以便下次执行的时候进行恢复（也就是所谓的上下文切换）。整个过程中，CPU 交替执行不同的进程，虚拟内存系统会负责管理地址空间，而没有执行的进程的寄存器值会被保存在内存中。切换到另一个进程的时候，会载入已保存的对应于将要执行的进程的寄存器值。

而现代处理器一般有多个核心，所以可以真正同时执行多个进程。这些进程会共享主存以及一部分缓存，具体的调度是由内核控制的，示意图如下：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14613708880333.jpg)

切换进程时，内核会负责具体的调度，如下图所示

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14613717282590.jpg)

#### 进程控制 Process Control

##### **系统调用的错误处理**

在遇到错误的时候，Linux 系统级函数通常会返回 -1 并且设置 `errno` 这个全局变量来表示错误的原因。使用的时候记住两个规则：

1. 对于每个系统调用都应该检查返回值
2. 当然有一些系统调用的返回值为 void，在这里就不适用

```c++
void unix_error(char *msg) /* Unix-style error */
{
    fprintf(stderr, "%s: %s\n", msg, strerror(errno));
    exit(0);
}

// 上面的片段可以写为
if ((pid = fork()) < 0)
    unix_error("fork error");
```

```c++
pid_t Fork(void)
{
    pid_t pid;
    if ((pid = fork()) < 0)
        unix_error("Fork error");
    return pid;
}
```

调用的时候直接使用 `pid = Fork();` 即可（注意这里是大写的 F）

##### **获取进程信息**

我们可以用下面两个函数获取进程的相关信息：

- `pid_t getpid(void)` - 返回当前进程的 PID
- `pid_t getppid(void)` - 返回当前进程的父进程的 PID

![image-20210705152334297](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210705152334297.png)

**终止进程**

在下面三种情况时，进程会被终止：

1. ##### 接收到一个终止信号

2. 返回到 `main`

3. 调用了 `exit` 函数

`exit` 函数会被调用一次，但从不返回，具体的函数原型是

```c++
// 以 status 状态终止进程，0 表示正常结束，非零则是出现了错误
void exit(int status)
```

**创建进程**

调用 `fork` 来创造新进程。这个函数很有趣，执行一次，但是会返回两次，具体的函数原型为

```c++
// 对于子进程，返回 0
// 对于父进程，返回子进程的 PID
int fork(void)
```

子进程几乎和父进程一模一样，会有相同且独立的虚拟地址空间，也会得到父进程已经打开的文件描述符(file descriptor)。比较明显的不同之处就是进程 PID 了。

看一个简单的例子

```c++
int main()
{
    pid_t pid;
    int x = 1;
    
    pid = Fork();
    if (pid == 0) 
    {   // Child
        printf("I'm the child!  x = %d\n", ++x);
        exit(0);
    }
    
    // Parent
    printf("I'm the parent! x = %d\n", --x);
    exit(0);
}
```

输出是

```c++
linux> ./forkdemo
I'm the parent! x = 0
I'm the child!  x = 2
```

有以下几点需要注意：

- 调用一次，但是会有两个返回值
- 并行执行，不能预计父进程和子进程的执行顺序
- 拥有自己独立的地址空间（也就是变量都是独立的），除此之外其他都相同
- 在父进程和子进程中 `stdout` 是一样的（都会发送到标准输出）

#### 进程图

进程图是一个很好的帮助我们理解进程执行的工具：

- 每个节点代表一条执行的语句
- a -> b 表示 a 在 b 前面执行
- 边可以用当前变量的值来标记
- `printf` 节点可以用输出来进行标记
- 每个图由一个入度为 0 的节点作为起始

对于进程图来说，只要满足拓扑排序，就是可能的输出。我们还是用刚才的例子来简单示意一下：

```c++
int main()
{
    pid_t pid;
    int x = 1;
    
    pid = Fork();
    if (pid == 0) 
    {   // Child
        printf("child! x = %d\n", --x);
        exit(0);
    }
    
    // Parent
    printf("parent! x = %d\n", x);
    exit(0);
}
```

对应的进程图为

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14625029984869.jpg)

#### 回收子进程

即使主进程已经终止，子进程也还在消耗系统资源，我们称之为『僵尸』。为了『打僵尸』，就可以采用『收割』(Reaping) 的方法。父进程利用 `wait` 或 `waitpid` 回收已终止的子进程，然后给系统提供相关信息，kernel 就会把 zombie child process 给删除。

如果父进程不回收子进程的话，通常来说会被 `init` 进程(pid == 1)回收，所以一般不必显式回收。但是在长期运行的进程中，就需要显式回收（例如 shell 和 server）。

如果想在子进程载入其他的程序，就需要使用 `execve` 函数，具体可以查看对应的 man page，这里不再深入。

### 信号 Signal

Linux 的进程树，可以通过 `pstree` 命令查看，如下图所示：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14613761276198.jpg)

对于前台进程来说，我们可以在其执行完成后进行回收，而对于后台进程来说，因为不能确定具体执行完成的时间，所以终止之后就成为了僵尸进程，无法被回收并因此造成内存泄露。

这怎么办呢？同样可以利用异常控制流，当后台进程完成时，内核会中断常规执行并通知我们，具体的通知机制就是『信号』(signal)。

信号是 Unix、类 Unix 以及其他 POSIX 兼容的操作系统中进程间通讯的一种有限制的方式。它是一种异步的通知机制，用来提醒进程一个事件已经发生。当一个信号发送给一个进程，操作系统中断了进程正常的控制流程，此时，任何非原子操作都将被中断。如果进程定义了信号的处理函数，那么它将被执行，否则就执行默认的处理函数。

这样看来，信号其实是类似于异常和中断的，是由内核（在其他进程的请求下）向当前进程发出的。信号的类型由 1-30 的整数定义，信号所能携带的信息极少，一是对应的编号，二就是信号到达这个事实。**下面是几个比较常用的信号的编号及简介：**

| 编号 |  名称   |  默认动作   |           对应事件            |
| :--: | :-----: | :---------: | :---------------------------: |
|  2   | SIGINT  |    终止     |        用户输入 ctrl+c        |
|  9   | SIGKILL |    终止     |  终止程序（不能重写或忽略）   |
|  11  | SIGSEGV | 终止且 Dump | 段冲突 Segmentation violation |
|  14  | SIGALRM |    终止     |           时间信号            |
|  17  | SIGCHLD |    忽略     |       子进程停止或终止        |

内核通过给目标进程发送信号，来更新目标进程的状态，具体的场景为：

- 内核检测到了如除以零(SIGFPE)或子进程终止(SIGCHLD)的系统事件
- 另一个进程调用了 `kill` 指令来请求内核发送信号给指定的进程

目标进程接收到信号后，内核会强制要求进程对于信号做出响应，可以有几种不同的操作(如果没有设置对signal的action, 则按默认action执行)：

- **忽略**这个
- **终止**进程
- **捕获**信号，执行信号处理器(signal handler)，类似于异步中断中的异常处理器(exception handler)

具体的过程如下：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14614121439266.jpg)

如果信号已被发送但是未被接收，那么处于等待状态(pending)，同类型的信号至多只会有一个待处理信号(pending signal)，一定要注意这个特性，因为内部实现机制不可能提供较复杂的数据结构，所以信号的接收并不是一个队列。比如说进程有一个 `SIGCHLD` 信号处于等待状态，那么之后进来的 `SIGCHLD` 信号都会被直接扔掉。

当然，进程也可以阻塞特定信号的接收，但信号的发送并不受控制，所以被阻塞的信号仍然可以被发送，不过直到进程取消阻塞该信号之后才会被接收。内核用等待(pending)位向量和阻塞(blocked)位向量来维护每个进程的信号相关状态。

#### 进程组

每个进程都只属于一个进程组，从前面的进程树状图中我们也能大概了解一二，想要了解相关信息，一般使用如下函数：

- `getpgrp()` - 返回当前进程的进程组
- `setpgid()` - 设置一个进程的进程组

我们可以据此指定一个进程组或者一个单独的进程，比方说可以通过 `kill` 应用来发送信号，流入

```assembly
# 创建子进程
linux> ./forks 16
Child1: pid=24818 pgrp=24817
Child2: pid=24819 pgrp=24817
# 查看进程
linux> ps
  PID TTY      TIME  CMD
24788 pts/2 00:00:00 tcsh
24818 pts/2 00:00:02 forks
24819 pts/2 00:00:02 forks
24820 pts/2 00:00:00 ps

# 可以选择关闭某个进程
linux> /bin/kill -9 24818
# 也可以关闭某个进程组，会关闭该组中所有进程
linux> /bin/kill -9 -24817
# 查看进程
linux> ps
  PID TTY      TIME  CMD
24788 pts/2 00:00:00 tcsh
24820 pts/2 00:00:00 ps
```

这里可以看到，第一个命令只会杀掉编号为 24818 的进程，但是第二个命令，因为有两个进程都属于进程组 24817，所以会杀掉进程组中的每个进程。

我们也可以通过键盘让内核向每个前台进程发送 SIGINT(SIGTSTP) 信号

- SIGINT - `ctrl+c` 默认终止进程
- SIGTSTP - `ctrl+z` 默认挂起进程

下面是一个简单的例子

```assembly
linux> ./forks 17
Child: pid=28108 pgrp=28107
Parent: pid=28107 pgrp=28107
# 按下 ctrl+z
Suspended # 进程被挂起
linux> ps w
  PID TTY   STAT  TIME  COMMAND
27699 pts/8 Ss    00:00 -tcsh
28107 pts/8 T     00:02 ./forks 17
28108 pts/8 T     00:02 ./forks 17
28109 pts/8 R+    00:00 ps w
linux> fg
./forks 17
# 按下 ctrl+c，进程被终止
linux> ps w
  PID TTY   STAT  TIME  COMMAND
27699 pts/8 Ss    00:00 -tcsh
28109 pts/8 R+    00:00 ps w
```

STAT 部分的第一个字母的意思

- S: 睡眠 sleeping
- T: 停止 stopped
- R: 运行 running

第二个字母的意思：

- s: 会话管理者 session leader
- +: 前台进程组

更多信息可以查看 `man ps`

如果想要发送信号，可以使用 `kill` 函数，下面是一个简单的示例，父进程通过发送 `SIGINT` 信号来终止正在无限循环的子进程。

```c++
void forkandkill()
{
    pid_t pid[N];
    int i;
    int child_status;
    
    for (i = 0; i < N; i++)
        if ((pid[i] = fork()) == 0)
            while(1) ;  // 死循环
    
    for (i = 0; i < N; i++)
    {
        printf("Killing process %d\n", pid[i]);
        kill(pid[i], SIGINT);
    }
    
    for (i = 0; i < N; i++)
    {
        pid_t wpid = wait(&child_status);
        if (WIFEXITED(child_status))
            printf("Child %d terminated with exit status %d\n",
                    wpid, WEXITSTATUS(child_status));
        else
            printf("Child %d terminated abnormally\n", wpid);
    }
}
```

#### 接收信号

所有的上下文切换都是通过调用某个异常处理器(exception handler)完成的，内核会计算对易于某个进程 p 的 pnb 值：`pnb = pending & ~blocked`

- 如果 `pnb == 0`，那么就把控制交给进程 p 的逻辑流中的下一条指令

- 如果

  ```
pnb != 0
  ```
  
  - 选择 `pnb` 中最小的非零位 k，并强制进程 p 接收信号 k
- 接收到信号之后，进程 p 会执行对应的动作
  - 对 `pnb` 中所有的非零位进行这个操作
  - 最后把控制交给进程 p 的逻辑流中的下一条指令

每个信号类型都有一个预定义的『默认动作』，可能是以下的情况：

- 终止进程
- 终止进程并 dump core
- 停止进程，收到 `SIGCONT` 信号之后重启
- 忽略信号

`signal` 函数可以修改默认的动作，函数原型为 `handler_t *signal(int signum, handler_t *handler)`。我们通过一个简单的例子来感受下，这里我们屏蔽了 `SIGINT` 函数，即使按下 `ctrl+c` 也不会终止

```c++
void sigint_handler(int sig) // SIGINT 处理器
{
    printf("想通过 ctrl+c 来关闭我？\n");
    sleep(2);
    fflush(stdout);
    sleep(1);
    printf("OK. :-)\n");
    exit(0);
}

int main()
{
    // 设定 SIGINT 处理器
    if (signal(SIGINT, sigint_handler) == SIG_ERR)
        unix_error("signal error");
        
    // 等待接收信号
    pause();
    return 0;
}
```

信号处理器的工作流程可以认为是和当前用户进程『并发』的同一个『伪』进程，示意如下：

注：并行与并发的区别

计算机虽然只有一个 CPU，但操作系统能够将程序的执行单位细化，然后分开执行，从而实现伪并行执行。这种**伪并行执行称为并发(concurrent)**。**使用多个 CPU 真的同时执行称为并行(parallel)**

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14614181710603.jpg)

还有一个需要注意的是，信号处理器也可以被其他的信号处理器中断，控制流如下图所示：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14614185201475.jpg)

#### 阻塞信号

我们知道，**内核会阻塞与当前在处理的信号同类型的其他正待等待的信号**，也就是说，一个 SIGINT 信号处理器是不能被另一个 SIGINT 信号中断的。

如果想要显式阻塞，就需要使用 **`sigprocmask` 函数**了，以及其他一些辅助函数：

- `sigemptyset` - 创建空集
- `sigfillset` - 把所有的信号都添加到集合中（因为信号数目不多）
- `sigaddset` - 添加指定信号到集合中
- `sigdelset` - 删除集合中的指定信号

我们可以用下面这段代码来临时阻塞特定的信号：

```assembly
sigset_t mask, prev_mask;

Sigemptyset(&mask); // 创建空集
Sigaddset(&mask, SIGINT); // 把 SIGINT 信号加入屏蔽列表中

// 阻塞对应信号，并保存之前的集合作为备份
Sigprocmask(SIG_BLOCK, &mask, &prev_mask);
...
... // 这部分代码不会被 SIGINT 中断
...
// 取消阻塞信号，恢复原来的状态
Sigprocmask(SIG_SETMASK, &prev_mask, NULL);
```

#### 安全处理信号

**信号处理器的设计并不简单，因为它们和主程序并行且共享相同的全局数据结构，尤其要注意因为并行访问可能导致的数据损坏的问题**，这里提供一些基本的指南（后面的课程会详细介绍）

- 规则 1：信号处理器越简单越好

  - 例如：设置一个全局的标记，并返回

- 规则 2：**信号处理器中只调用异步且信号安全(async-signal-safe)的函数**

  - 诸如 `printf`, `sprintf`, `malloc` 和 `exit` 都是不安全的！

- 规则 3：在进入和退出的时候保存和恢复errno

  - 这样信号处理器就不会覆盖原有的 `errno` 值

- 规则 4：**临时阻塞所有的信号以保证对于共享数据结构的访问**

  - 防止可能出现的数据损坏

- 规则 5：**用 volatile关键字声明全局变量**

  - 这样编译器就不会把它们保存在寄存器中，保证一致性

- 规则 6：**用 volatile sig_atomic_t来声明全局标识符(flag)**

  - 这样可以防止出现访问异常

这里提到的**异步信号安全(async-signal-safety)**指的是如下两类函数：

1. **所有的变量都保存在栈帧中的函数**
2. **不会被信号中断的函数**

Posix 标准指定了 117 个异步信号安全(async-signal-safe)的函数（可以通过 `man 7 signal` 查看）

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14614222627451.jpg)

[异步信号安全函数](./signal_async_safe_funcs.txt)

### 非本地跳转 Non local Jump

所谓的本地跳转，指的是在一个程序中通过 goto 语句进行流程跳转，尽管不推荐使用goto语句，但在嵌入式系统中为了提高程序的效率，goto语句还是可以使用的。**本地跳转(goto)的限制在于，我们不能从一个函数跳转到另一个函数中。如果想突破函数的限制，就要使用 `setjmp` 或 `longjmp` 来进行非本地跳转了。**

`setjmp` 保存当前程序的堆栈上下文环境(stack context)，注意，这个保存的堆栈上下文环境仅在调用 `setjmp` 的函数内有效，如果调用 `setjmp` 的函数返回了，这个保存的堆栈上下文环境就失效了。调用 `setjmp` 的直接返回值为 0。

`longjmp` 将会恢复由 `setjmp` 保存的程序堆栈上下文，即程序从调用 `setjmp` 处重新开始执行，不过此时的 `setjmp` 的返回值将是由 `longjmp` 指定的值。注意`longjmp` 不能指定0为返回值，即使指定了 0，`longjmp` 也会使 `setjmp` 返回 1。

```c++
#include <stdio.h>
#include <setjmp.h>
 
static jmp_buf buf;
 
void second(void) {
    printf("second\n");         // 打印
    longjmp(buf,1);             // 跳回setjmp的调用处 - 使得setjmp返回值为1
}
 
void first(void) {
    second();
    printf("first\n");          // 不可能执行到此行
}
 
int main() {   
    if ( ! setjmp(buf) ) {
        first();                // 进入此行前，setjmp返回0
    } else {                    // 当longjmp跳转回，setjmp返回1，因此进入此行
        printf("main\n");       // 打印
    }
 
    return 0;
}
```

上述程序将输出:

second
main
注意到虽然first()子程序被调用，"first"不可能被打印。"main"被打印，因为条件语句if ( ! setjmp(buf) )被执行第二次。　

使用setjmp和longjmp要注意以下几点：

1、setjmp与longjmp结合使用时，它们必须有严格的先后执行顺序，也即先调用setjmp函数，之后再调用longjmp函数，以恢复到先前被保存的“程序执行点”。否则，如果在setjmp调用之前，执行longjmp函数，将导致程序的执行流变的不可预测，很容易导致程序崩溃而退出

2.  longjmp必须在setjmp调用之后，而且longjmp必须在setjmp的作用域之内。具体来说，在一个函数中使用setjmp来初始化一个全局标号，然后只要该函数未曾返回，那么在其它任何地方都可以通过longjmp调用来跳转到 setjmp的下一条语句执行。实际上setjmp函数将发生调用处的局部环境保存在了一个jmp_buf的结构当中，只要主调函数中对应的内存未曾释放 （函数返回时局部内存就失效了），那么在调用longjmp的时候就可以根据已保存的jmp_buf参数恢复到setjmp的地方执行。
————————————————
版权声明：本文为CSDN博主「大米粒ing」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/chenyiming_1990/article/details/8683413



## 系统输入输出（linux下）

### Unix I/O(所有东西都是文件)

在 Linux 中，文件实际上可以看做是字节的序列。更有意思的是，所有的 I/O 设备也是用文件来表示的，比如：

- `./dev/sda2` (`/usr` 磁盘分区)
- `/dev/tty2` (终端)

甚至连内核也是用文件来表示的：

- `/boot/vmlinuz-3.13.0-55-generic` (内核镜像)
- `/proc` (内核数据结构)

因为 I/O 设备也是文件，所以内核可以利用称为 Unix I/O 的简单接口来处理输入输出，比如使用 `open()` 和 `close()` 来打开和关闭文件，使用 `read()` 和 `write()` 来读写文件，或者利用 `lseek()` 来设定读取的偏移量等等。

为了区别不同文件的类型，会有一个 `type` 来进行区别：

| 类型       | 示例（ls -lhti）                                             | 说明                                                         |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 普通文件   | -rwxrwxr-x.  1 o365-user o365-user  66K Jan 12  2016 ctarget<br/>-rw-rw-r--.  1 o365-user o365-user 2.6K Jan 12  2016 farm.c | 普通文件（灰色）、可执行文件（绿色）、压缩文件（红色）等     |
| 目录       | drwxrwxr-x. 3 o365-user o365-user 4.0K Jul  5 19:19 cachelab-handout<br/>drwxrwxr-x. 3 o365-user o365-user 4.0K Jun 30 11:48 target1 | 目录文件（蓝色）                                             |
| 链接       | 1125476 lrwxrwxrwx. 1 o365-user o365-user   16 Jul  6 03:03 test_softLink.txt -> test_judy_csv.py 【软链接】<br/>1079476 -rw-rw-r--. 2 o365-user o365-user  45K May 10 02:09 test_hardLink.txt 【硬链接，inode一致】<br/>1079476 -rw-rw-r--. 2 o365-user o365-user  45K May 10 02:09 test_judy_csv.py 【原文件】 | 软链接（浅蓝色）<br />ln test_judy_csv.py test_hardLink.txt<br/>ln -s test_judy_csv.py test_softLink.txt |
| 设备文件   | 6944 brw-rw----. 1 root disk      8,   1 Apr  2  2020 sda1 （块设备文件）<br/>6945 brw-rw----. 1 root disk      8,   2 Apr  2  2020 sda2<br/>6491 crw-rw-rw-. 1 root tty       5,   0 Aug  6  2020 tty （字符设备文件）<br/>6571 crw-------. 1 root root      4,  64 Apr  2  2020 ttyS0 | 设备文件（黄色）<br />块设备文件：硬盘、软盘，以block为单位可以随机存取<br />字符设备：字符终端、串口、键盘，以字节流顺序读取（open、close、read、write等系统调用），通常不支持随机存取， |
| 管道文件   | 1125486 prw-rw-r--. 1 o365-user o365-user    0 Jul  6 03:15 testPipe | （有名）管道文件（橘黄色）<br />mkfifo fifo_file<br />在 FIFO 中可以很好地解决在无关进程间数据交换的要求，FIFO 的通信方式类似于在进程中使用文件来传输数据，只不过 FIFO 类型的文件同时具有管道的特性，在读取数据时，FIFO 管道中同时清除数据。 |
| 套接字文件 | 12395 srw-rw-rw-. 1 root root           0 Apr  2  2020 log<br />522792185 lrwx------. 1 o365-user o365-user 64 Jul  6 03:24 /proc/36923/fd/31 -> socket:[511707057] | 套接字文件（灰色，或者红色闪烁）<br /><br />Linux系统下通过Socket文件描述符寻找连接状态 https://blog.csdn.net/gdutliuyun827/article/details/45038595 |

https://www.cnblogs.com/tongye/p/10410098.html

#### **普通文件**

普通的文件包含任意数据，应用一般来说需要区分出文本文件和二进制文件。文本文件只包含 ASCII 或 Unicode 字符。除此之外的都是二进制文件(对象文件, JPEG 图片, 等等)。对于内核来说其实并不能区分出个中的区别。

文本文件就是一系列的文本行，每行以 `\n` 结尾，新的一行是 `0xa`，和 ASCII 码中的 line feed 字符(LF) 一样。不同系统用用判断一行结束的符号不同(End of line, EOL)，如：

- Linux & Mac OS: \n (0x0a)
  - line feed(LF)
- Windows & 网络协议:  \r\n (0x0d 0x0a)
  - Carriage return(CR) followed by line feed(LF)

#### **目录**

目录包含一个链接(link)数组，并且每个目录至少包含两条记录：

- `.`(dot) 当前目录
- `..`(dot dot) 上一层目录

用来操作目录的命令主要有 `mkdir`, `ls`, `rmdir`。目录是以树状结构组织的，根目录是 `/`(slash)。

内核会为每个进程保存当前工作目录(cwd, current working directory)，可以用 `cd` 命令来进行更改。我们通过路径名来确定文件的位置，一般分为绝对路径和相对路径。

接下来我们了解一下基本的文件操作。

#### 打开文件

在使用文件之前需要通知内核打开该文件：

```c++
int fd; // 文件描述符 file descriptor

if ((fd = open("/etc/hosts", O_RDONLY)) < 0)
{
    perror("open");
    exit(1);
}
```

返回值是一个小的整型称为文件描述符(file descriptor)，如果这个值等于 -1 则说明发生了错误。每个由 Linux shell(注：感谢网友 yybear 的勘误) 创建的进程都会默认打开三个文件（注意这里的文件概念）：

- 0: standard input(stdin)
- 1: standard output(stdout)
- 2: standar error(stderr)

#### 关闭文件

使用完毕之后同样需要通知内核关闭文件：

```c++
int fd;     // 文件描述符
int retval; // 返回值

int ((retval = close(fd)) < 0)
{
    perror("close");
    exit(1);
}
```

如果在此关闭已经关闭了的文件，会出大问题。所以一定要检查返回值，哪怕是 `close()` 函数（如上面的例子所示）

#### 读取文件

在打开和关闭之间就是读取文件，实际上就是把文件中对应的字节复制到内存中，并更新文件指针：

```c++
char buf[512];
int fd;
int nbytes;

// 打开文件描述符，并从中读取 512 字节的数据
if ((nbytes = read(fd, buf, sizeof(buf))) < 0)
{
    perror("read");
    exit(1);
}
```

返回值是读取的字节数量，是一个 `ssize_t` 类型（其实就是一个有符号整型），如果 `nbytes < 0` 那么表示出错。`nbytes < sizeof(buf)` 这种情况(short counts) 是可能发生的，而且并不是错误。

#### 写入文件

写入文件是把内存中的数据复制到文件中，并更新文件指针：

```c++
char buf[512];
int fd;
int nbytes;

// 打开文件描述符，并向其写入 512 字节的数据
if ((nbytes = write(fd, buf, sizeof(buf)) < 0)
{
    perror("write");
    exit(1);
}
```

返回值是写入的字节数量，如果 `nbytes < 0` 那么表示出错。`nbytes < sizeof(buf)` 这种情况(short counts) 是可能发生的，而且并不是错误。

综合上面的操作，我们可以来看看 Unix I/O 的例子，这里我们一个字节一个字节把标准输入复制到标准输出中：

```c++
#include "csapp.h"

int main(void)
{
    char c;
    while(read(STDIN_FILENO, &c, 1) != 0)
        write(STDOUT_FILENO, &c, 1);
    exit(0);
}
```

前面提到的 short count 会在下面的情形下发生：

- 在读取的时候遇到 EOF(end-of-file)
- 从终端中读取文本行
- 读取和写入网络 sockets

但是在下面的情况下不会发生

- 从磁盘文件中读取（除 EOF 外）
- 写入到磁盘文件中

最好总是允许 short count，这样就可以避免处理这么多不同的情况 【想起libevent的触发方式，水平触发和边沿触发：Level Triggered模式下只要某个socket处于readable/writable状态， 无论什么时候进行epoll_wait都会返回该socket；而Edge Triggered模式下只有某个socket从unreadable变为readable或 从unwritable变为writable时，epoll_wait才会返回该socket（即每次事件触发后，都需要将内容读/写完）。】

### 元数据

元数据是用来描述数据的数据，由内核维护，可以通过 `stat` 和 `fstat` 函数来访问，其结构是：

```c++
struct stat
{
    dev_t           st_dev;     // Device
    ino_t           st_ino;     // inode
    mode_t          st_mode;    // Protection & file type
    nlink_t         st_nlink;   // Number of hard links
    uid_t           st_uid;     // User ID of owner
    gid_t           st_gid;     // Group ID of owner
    dev_t           st_rdev;    // Device type (if inode device)
    off_t           st_size;    // Total size, in bytes
    unsigned long   st_blksize; // Blocksize for filesystem I/O
    unsigned long   st_blocks;  // Number of blocks allocated
    time_t          st_atime;   // Time of last access
    time_t          st_mtime;   // Time of last modification
    time_t          st_ctime;   // Time of last change
}
```

对应的访问例子：https://blog.csdn.net/yasi_xi/article/details/9226267

```c++
int main (int argc, char **argv)
{
    struct stat stat;
    char *type, *readok;
    
    stat(argv[1], &stat);
    if (S_ISREG(stat.st_mode)) // 确定文件类型
        type = "regular";
    else if (S_ISDIR(stat.st_mode))
        type = "directory";
    else
        type = "other";
    
    if ((stat.st_mode & S_IRUSR)) // 检查读权限
        readok = "yes";
    else
        readok = "no";
    
    printf("type: %s, read: %s\n", type, readok);
    exit(0);
}
```

### 重定向

了解了具体的结构之后，我们来看看内核是如何表示已打开的文件的。其实过程很简单，每个进程都有自己的描述符表(Descriptor table)，然后 Descriptor 1 指向终端，Descriptor 4 指向磁盘文件，如下图所示：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14614963360280.jpg)

这里有一个需要说明的情况，就是使用 `fork`。子进程实际上是会继承父进程打开的文件的。在 fork 之后，子进程实际上和父进程的指向是一样的，这里需要注意的是会把引用计数加 1，如下图所示

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14614966173879.jpg)

了解了这个，我们我们就可以知道所谓的重定向是怎么实现的了。其实很简单，只要调用 `dup2(oldfd, newfd)` 函数即可。我们只要改变文件描述符指向的文件，也就完成了重定向的过程，下图中我们把原来指向终端的文件描述符指向了磁盘文件，也就把终端上的输出保存在了文件中：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14614968463687.jpg)

### 标准输入输出

C 标准库中包含一系列高层的标准 IO 函数，比如

- 打开和关闭文件: `fopen`, `fclose`
- 读取和写入字节: `fread`, `fwrite`
- 读取和写入行: `fgets`, `fputs`
- 格式化读取和写入: `fscanf`, `fprintf`

标准 IO 会用流的形式打开文件，所谓流(stream)实际上是文件描述符和缓冲区(buffer)在内存中的抽象。C 程序一般以三个流开始，如下所示：

```c++
#include <stdio.h>
extern FILE *stdin;     // 标准输入 descriptor 0
extern FILE *stdout;    // 标准输出 descriptor 1
extern FILE *stderr;    // 标准错误 descriptor 2

int main()
{
    fprintf(stdout, "Hello, Da Wang\n");
}
```

接下来我们详细了解一下为什么需要使用缓冲区，程序经常会一次读入或者写入一个字符，比如 `getc`, `putc`, `ungetc`，同时也会一次读入或者写入一行，比如 `gets`, `fgets`。如果用 Unix I/O 的方式来进行调用，是非常昂贵的，比如说 `read` 和 `write` 因为需要内核调用，需要大于 10000 个时钟周期。

解决的办法就是利用 `read` 函数一次读取一块数据，然后再由高层的接口，一次从缓冲区读取一个字符（当缓冲区用完的时候需要重新填充）

### 总结

前面介绍了两种 I/O 方法，Unix I/O 是最底层的，通过系统调用来进行文件操作，在这之上是 C 的标准 I/O 库，对应的函数为：

- Unix I/O: `open`, `read`, `write`, `lseek`, `stat`, `close`
- Standard C I/O: `fopen`, `fdopen`, `fread`, `fwrite`, `fscanf`, `fprintf`, `sscanf`, `sprintf`, `fgets`, `fputs`, `fflush`, `fseek`, `fclose`

**Unix I/O 是最通用最底层的 I/O 方法，其他的 I/O 包都是在 Unix I/O 的基础上进行构建的，值得注意的一点是，Unix I/O 中的方法都是异步信号安全(async-signal-safe)的**，也就是说，可以在信号处理器中调用。因为比较底层和基础的缘故，需要处理的情况非常多，很容易出错。高效率的读写需要用到缓冲区，同样容易出错，这也就是标准 C 库着重要解决的问题。

标准 C I/O 提供了带缓存访问文件的方法，使用的时候几乎不用考虑太多，但是如果我们想要得到文件的元信息时，就还是得使用 Unix I/O 中的 `stat` 函数。另外标准 C I/O 中的函数都不是异步信号安全(async-signal-safe)的，所以并不能在信号处理器中使用。最后，标准 C I/O 不适合用于处理网络套接字。



## 虚拟内存与动态内存分配

### 从物理内存到虚拟内存

物理地址一般应用在简单的嵌入式微控制器中（汽车、电梯、电子相框等），因为应用的范围有严格的限制，不需要在内存管理中引入过多的复杂度。

但是对于计算机（以及其他智能设备）来说，虚拟地址则是必不可少的，通过 MMU(Memory management unit)把虚拟地址(Virtual Address, VA)转换为物理地址(Physical Address, PA)，再由此进行实际的数据传输。大致的过程如下图所示

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14615011037796.jpg)

**使用虚拟内存主要是基于下面三个考虑：**

1. 可以更有效率的使用内存：使用 DRAM 当做部分的虚拟地址空间的缓存
2. 简化内存管理：每个进程都有统一的线性地址空间
3. 隔离地址控件：进程之间不会相互影响；用户程序不能访问内核信息和代码

### 虚拟内存的三个角色

#### 作为缓存工具

概念上来说，虚拟内存就是存储在磁盘上的 N 个连续字节的数组。这个数组的部分内容，会缓存在 DRAM 中，在 DRAM 中的每个缓存块(cache block)就称为页(page)，如下图所示：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14615017442915.jpg)

大致的思路和之前的 cache memory 是类似的，就是利用 DRAM 比较快的特性，把最常用的数据换缓存起来。如果要访问磁盘的话，大约会比访问 DRAM 慢一万倍，所以我们的目标就是**尽可能从 DRAM 中拿数据**。为此，我们需要：

- 更大的页尺寸(page size)：通常是 4KB，有的时候可以达到 4MB
- 全相联(Fully associative)：每一个虚拟页(virual page)可以映射到任意的物理页(physical page)中，没有限制。
- 映射函数非常复杂，所以没有办法用硬件实现，通常使用 Write-back 而非 Write-through 机制
  - Write-through: 命中后更新缓存，同时写入到内存中
  - Write-back: 直到这个缓存需要被置换出去，才写入到内存中（需要额外的 dirty bit 来表示缓存中的数据是否和内存中相同，因为可能在其他的时候内存中对应地址的数据已经更新，那么重复写入就会导致原有数据丢失）

具体怎么做呢？通过页表(page table)。每个页表实际上是一个数组，数组中的每个元素称为页表项(PTE, page table entry)，每个页表项负责把虚拟页映射到物理页上。**在 DRAM 中，每个进程都有自己的页表**，具体如下

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14615030555447.jpg)

因为有一个表可以查询，就会遇到两种情况，一种是命中(Page Hit)，另一种则是未命中(Page Fault)。

命中的时候，即访问到页表中蓝色条目的地址时，因为在 DRAM 中有对应的数据，可以直接访问。

不命中的时候，即访问到 page table 中灰色条目的时候，因为在 DRAM 中并没有对应的数据，所以需要执行一系列操作（从磁盘复制到 DRAM 中），具体为：

- 触发 Page fault，也就是一个异常
- Page fault handler 会选择 DRAM 中需要被置换的 page，并把数据从磁盘复制到 DRAM 中
- 重新执行访问指令，这时候就会是 page hit

**复制过程中的等待时间称为 demand paging**。

仔细留意上面的页表，会发现有一个条目是 null，也就是没有分配。具体的分配过程（比方说声明了一个大数组），就是让该条目指向虚拟内存（在磁盘上）的某个页，但并不复制到 DRAM，只有当出现 page fault 的时候才需要拷贝数据。

看起来『多此一举』，但是由于局部性原理，虚拟内存其实是非常高效的机制，这一部分最后提到了工作集(working set)[1]的概念，比较简单，这里不再赘述。

### 作为内存管理工具

![虚拟地址空间](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\aHR0cDovL3poYW5neXV6ZWNobi5jbi93cC1jb250ZW50L3VwbG9hZHMvMjAxOS8xMS8lRTglQkYlOUIlRTclQTglOEIlRTglOTklOUElRTYlOEIlOUYlRTUlOUMlQjAlRTUlOUQlODAlRTclQTklQkElRTklOTclQjQucG5n)

前面提到，每个进程都有自己的虚拟地址空间，这样一来，对于进程来说，它们看到的就是简单的线性空间（但实际上在物理内存中可能是间隔、支离破碎的），具体的映射过程可以用下图表示：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14615040688550.jpg)

在内存分配中没有太多限制，每个虚拟页都可以被映射到任何的物理页上。这样也带来一个好处，如果两个进程间有共享的数据，那么直接指向同一个物理页即可（也就是上图 PP 6 的状况，只读数据）

虚拟内存带来的另一个好处就是可以简化链接和载入的结构（因为有了统一的抽象，不需要纠结细节）

### 作为内存保护工具

页表中的每个条目的高位部分是表示权限的位，MMU 可以通过检查这些位来进行权限控制（读、写、执行），如下图所示：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14615862225552.jpg)

## 地址翻译

开始之前先来了解以下参数：



N=2n,M=2m,P=2pN=2n,M=2m,P=2p

其中 `N` 表示虚拟地址空间中的地址数量，`M` 表示物理地址空间中的地址数量，`P` 是每一页包含的字节数(page size)。

虚拟地址(VA, Virtual Address)中的元素：

- `TLBI`: TLB 的索引值
- `TLBT`: TLB 的标签(tag)
- `VPO`: 虚拟页偏移量
- `VPN`: 虚拟页编号

物理地址(PA, physical address)中的元素：

- `PPO`: 物理页偏移量（与 `VPO` 的值相同）
- `PPN`: 物理页编号

然后我们通过一个具体的例子来说明如何进行地址翻译

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14615900211847.jpg)

具体的访问过程为：

- 通过虚拟地址找到页表(page table)中对应的条目
- 检查有效位(valid bit)，是否需要触发页错误(page fault)
- 然后根据页表中的物理页编号(physical page number)找到内存中的对应地址
- 最后把虚拟页偏移(virtual page offset)和前面的实际地址拼起来，就是最终的物理地址了

这里又分两种情况：Page Hit 和 Page Fault，我们先来看看 Page Hit 的情况

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14619333202471.jpg)

主要有 5 步，CPU 首先把虚拟地址发送给 MMU，MMU 检查缓存，并把从页表中得到对应的物理地址，接着 MMU 把物理地址发送给缓存/内存，最后从缓存/内存中得到数据。

而 Page Fault 的时候就复杂一些，第一次触发页错误会把页面载入内存/缓存，然后再以 Page Hit 的机制得到数据：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14619339362618.jpg)

这里有 7 步，前面和 Page Hit 是一致的，先把虚拟地址发给 MMU 进行检查，然后发现没有对应的页，于是触发异常，异常处理器会负责从磁盘中找到对应页面并与缓存/内存中的页进行置换，置换完成后再访问同一地址，就可以按照 Page Hit 的方式来访问了。

虽然缓存已经很快了，但是能不能更快呢，为什么不能直接在 MMU 进行一部分的工作呢？于是就有了另外一个设计：Translation Lookaside Buffer(TLB)。TLB 实际上可以认为是页表在处理芯片上的缓存，整体的机制和前面提到的缓存很像，我们通过下面的图进行讲解：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14619349692957.jpg)

这里 VPN + VPO 就是虚拟地址，同样分成三部分，分别用于匹配标签、确定集合，如果 TLB 中有对应的记录，那么直接返回对应页表项(PTE)即可，如果没有的话，就要从缓存/内存中获取，并更新 TLB 的对应集合。

### 多层页表 Multi-Level Page Table

虽然页表是一个表，但因为往往虚拟地址的位数比物理内存的位数要大得多，所以保存页表项(PTE) 所需要的空间也是一个问题。举个例子：

假设每个页的大小是 4KB（2 的 12 次方），每个地址有 48 位，一条 PTE 记录有 8 个字节，那么要全部保存下来，需要的大小是：



248×2−12×23=239bytes248×2−12×23=239bytes

整整 512 GB！所以我们采用多层页表，第一层的页表中的条目指向第二层的页表，一个一个索引下去，最终寻找具体的物理地址，整个翻译过程如下：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14619365087673.jpg)

### 地址翻译实例

来看一个简单的例子，我们的内存系统设定如下：

- 14 位的虚拟地址
- 12 位的物理地址
- 页大小为 64 字节

TLB 的配置为：

- 能够存储 16 条记录
- 每个集合有 4 条记录

系统本身缓存（对应于物理地址）：

- 16 行，每个块 4 个字节
- 直接映射（即 16 个集合）

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14619386956596.jpg)

TLB 中的数据为

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14619392574046.jpg)

页表中的数据为（一共有 256 条记录，这里列出前 16 个）

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14619394874702.jpg)

缓存中的数据为

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14619399183944.jpg)

一定要注意好不同部分的所代表的位置，这里我也会尽量写得清楚一些，来看第一个例子：

> 虚拟地址为 `0x03D4`

具体的转换过程如下图所示：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14619404450222.jpg)

具体来梳理一次：

先看 TLB 中有没有对应的条目，所以先看虚拟地址的第 6-13 位，在前面的 TLB 表中，根据 TLBI 为 3 这个信息，去看这个 set 中有没有 tag 为 3 的项目，发现有，并且对应的 PPN 是 0x0D，所以对应到物理地址，就是 PPN 加上虚拟地址的 0-5 位，而具体的物理地址又可以在缓存中找到（利用 cache memory 的机制），就可以获取到对应的数据了。

下面的例子同样可以按照这个方法来进行分析

> 虚拟地址为 `0x0020`

（物理地址已更正，感谢网友小新指出）

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14619405964352.jpg)

## 动态内存分配

前面了解了虚拟内存的相关知识，这一节我们来看看动态内存分配的基本概念，相信这之后就知道诸如 `malloc` 和 `new` 这类方法是怎么做的了。

程序员通过动态内存分配（例如 `malloc`）来让程序在运行时得到虚拟内存。动态内存分配器会管理一个虚拟内存区域，称为堆(heap)。

分配器以块为单位来维护堆，可以进行分配或释放。有两种类型的分配器：

- 显式分配器：应用分配并且回收空间（C 语言中的 `malloc` 和 `free`）
- 隐式分配器：应用只负责分配，但是不负责回收（Java 中的垃圾收集）

先来看看一个简单的使用 `malloc` 和 `free` 的例子

```c++
#include <stdio.h>
#include <stdlib.h>

void foo(int n) {
    int i, *p;
    
    /* Allocate a block of n ints */
    p = (int *) malloc(n * sizeof(int));
    if (p == NULL) {
        perror("malloc");
        exit(0);
    }
    
    /* Initialize allocated block */
    for (i=0; i<n; i++)
        p[i] = i;

    /* Return allocated block to the heap */
    free(p);
}
```

为了讲述方便，我们做如下假设：

- 内存地址按照字来编码
- 每个字的大小和整型一致

例如：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14619422087381.jpg)

程序可以用任意的顺序发送 `malloc` 和 `free` 请求，`free` 请求必须作用与已被分配的 block。

分配器有如下的限制：

- 不能控制已分配块的数量和大小
- 必须立即响应 `malloc` 请求（不能缓存或者给请求重新排序）
- 必须在未分配的内存中分配
- 不同的块需要对齐（32 位中 8 byte，64 位中 16 byte）
- 只能操作和修改未分配的内存
- 不能移动已分配的块

### 性能指标

现在我们可以来看看如何去评测具体的分配算法了。假设给定一个 `malloc` 和 `free` 的请求的序列：



R0,R1,...,Rk,...,Rn−1R0,R1,...,Rk,...,Rn−1

目标是尽可能提高吞吐量以及内存利用率（注意，这两个目标常常是冲突的）

吞吐量是在单位时间内完成的请求数量。假设在 10 秒中之内进行了 5000 次 `malloc` 和 5000 次 `free` 调用，那么吞吐量是 1000 operations/second

另外一个目标是 Peak Memory Utilization，就是最大的内存利用率。

影响内存利用率的主要因素就是『内存碎片』，分为内部碎片和外部碎片两种。

**内部碎片**

内部碎片指的是对于给定的块，如果需要存储的数据(payload)小于块大小，就会因为对齐和维护堆所需的数据结构的缘故而出现无法利用的空间，例如：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14619426495995.jpg)

内部碎片只依赖于上一个请求的具体模式，所以比较容易测量。

**外部碎片**

指的是内存中没有足够的连续空间，如下图所示，内存中有足够的空间，但是空间不连续，所以成为了碎片：

![img](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\14619429933039.jpg)

### 实现细节

我们已经知道了原理，现在就来看看怎么样能够实现一个高效的内存分配算法吧！在具体实现之前，需要考虑以下问题：

- 给定一个指针，我们如何知道需要释放多少内存？
- 如何记录未分配的块？
- 实际需要的空间比未分配的空间要小的时候，剩下的空间怎么办？
- 如果有多个区域满足条件，如何选择？
- 释放空间之后如何进行记录？

具体这部分书中提到了四种方法：

1. 隐式空闲列表 Implicit List
2. 显式空闲列表 Explicit List
3. 分离的空闲列表 Segregated Free List
4. 按照大小对块进行排序 Blocks Sorted by Size

因为涉及的细节比较多，建议是详读书本的对应章节（第二版和第三版均为第九章第九节），这里不再赘述（如果需要的话之后我在另起一篇做详细介绍）

这里提一点，就是如何确定哪部分空间合适，有三种方法：

1. First Fit: 每次都从头进行搜索，找到第一个合适的块，线性查找
2. Next Fit: 每次从上次搜索结束的位置继续搜索，速度较快，但可能会有更多碎片
3. Best Fit: 每次遍历列表，找到最合适的块，碎片较少，但是速度最慢

更详细可以参考这两篇文章：[Dynamic Memory Allocation - Basic Concept](http://wdxtub.com/vault/csapp-18.html) 和 [Dynamic Memory Allocation - Advanced Concept](http://wdxtub.com/vault/csapp-19.html)

## 垃圾回收

所谓垃圾回收，就是我们不再需要显式释放所申请内存空间了，例如：

```c++
void foo() {
    int *p = malloc(128);
    return; /* p block is now garbage*/
}
```

这种机制在许多动态语言中都有实现：Python, Ruby, Java, Perl, ML, Lisp, Mathematica。C 和 C++ 中也有类似的变种，但是需要注意的是，是不可能回收所有的垃圾的。

我们如何知道什么东西才是『垃圾』呢？简单！只要没有任何指针指向的地方，不管有没有用，因为都不可能被使用，当然可以直接清理掉啦。不过这其实是需要一些前提条件的：

- 我们可以知道哪里是指针，哪里不是指针
- 每个指针都指向 block 的开头
- 指针不能被隐藏(by coercing them to an `int`, and then back again)

相关的算法如下：

- Mark-and-sweep collection (McCarthy, 1960)
- Reference counting (Collins, 1960)
- Copying collection (Minsky, 1963)
- Generational Collectors(Lieberman and Hewitt, 1983)

大部分比较常用的算法居然都有五十多年历史了，神奇。更多相关细节在维基百科[2]中都有详细介绍（中文版本质量较差，这里给出英文版）。

## 内存陷阱

关于内存的使用需要注意避免以下问题：

- 解引用错误指针
- 读取未初始化的内存
- 覆盖内存
- 引用不存在的变量
- 多次释放同一个块
- 引用已释放的块
- 释放块失败

### Dereferencing Bad Pointers

这是非常常见的例子，没有引用对应的地址，少了 `&`

```
int val;
...
scanf("%d", val);
```

复制

### Reading Uninitialized Memory

不能假设堆中的数据会自动初始化为 0，下面的代码就会出现奇怪的问题

```
/* return y = Ax */
int *matvec(int **A, int *x) {
    int *y = malloc(N * sizeof(int));
    int i, j;
    
    for (i = 0; i < N; i++)
        for (j = 0; j < N; j++)
            y[i] += A[i][j] * x[j];
    return y;
}
```

复制

### Overwriting Memory

这里有挺多问题，第一种是分配了错误的大小，下面的例子中，一开始不能用 `sizeof(int)`，因为指针的长度不一定和 int 一样。

```
int **p;
p = malloc(N * sizeof(int));

for (i = 0; i < N; i++) 
    p[i] = malloc(M * sizeof(int));
```

复制

第二个问题是超出了分配的空间，下面代码的 for 循环中，因为使用了 `<=`，会写入到其他位置

```
int **p;

p = malloc(N * sizeof (int *));

for (i = 0; i <= N; i++)
    p[i] = malloc(M * sizeof(int));
```

复制

第三种是因为没有检查字符串的长度，超出部分就写到其他地方去了（经典的缓冲区溢出攻击也是利用相同的机制）

```
char s[8];
int i;

gets(s); /* reads "123456789" from stdin */
```

复制

第四种是没有正确理解指针的大小以及对应的操作，应该使用 `sizeof(int *)`

```
int *search(int *p, int val) {
    while (*p && *p != null)
        p += sizeof(int);
    
    return p;
}
```

复制

第五种是引用了指针，而不是其指向的对象，下面的例子中，`*size--` 一句因为 `--` 的优先级比较高，所以实际上是对指针进行了操作，正确的应该是 `(*size)--`

```
int *BinheapDelete(int **binheap, int *size) {
    int *packet;
    packet = binheap[0];
    binheap[0] = binheap[*size - 1];
    *size--;
    Heapify(binheap, *size, 0);
    return (packet);
}
```

复制

### Referencing Nonexistent Variables

下面的情况中，没有注意到局部变量会在函数返回的时候失效（所以对应的指针也会无效），这是传引用和返回引用需要注意的，传值的话则不用担心

```
int *foo() {
    int val;
    
    return &val;
}
```

复制

### Freeing Blocks Multiple Times

这个不用多说，不能重复搞两次

```
x = malloc(N * sizeof(int));
//  <manipulate x>
free(x);

y = malloc(M * sizeof(int));
//  <manipulate y>
free(x);
```

复制

### Referencing Freed Blocks

同样是很明显的错误，不要犯

```
x = malloc(N * sizeof(int));
//  <manipulate x>
free(x);
//  ....

y = malloc(M * sizeof(int));
for (i = 0; i < M; i++)
    y[i] = x[i]++;
```

复制

### Memory Leaks

用完没有释放，就是内存泄露啦

```
foo() {
    int *x = malloc(N * sizeof(int));
    // ...
    return ;
}
```

复制

或者只释放了数据结构的一部分：

```
struct list {
    int val;
    struct list *next;
};

foo() {
    struct list *head = malloc(sizeof(struct list));
    head->val = 0;
    head->next = NULL;
    //...
    free(head);
    return;
}
```

复制

## 总结

这一讲的内容比较多，再加上各种不同的映射，可能是最需要花时间去理解的一讲了。我们先了解了物理地址和虚拟地址的区别，然后在此基础上理解虚拟内存在缓存、内存管理与保护中所扮演的角色，并通过实际的例子学习虚拟内存到物理内存的翻译机制。

有了前面的基础，简要介绍了动态内存分配的基本概念和管理动态内存分配的三种算法。最后提及了垃圾回收的基本原理和内存使用中常见的错误。

尤其是动态内存分配和垃圾回收的部分，因为篇幅限制说得比较简单，一定要对照书本进行阅读和理解。





[全虚拟化和半虚拟化的区别 cpu的ring0 ring1又是什么概念?](https://www.cnblogs.com/xusongwei/archive/2012/07/30/2615592.html)

Ring0-Ring3层到底是个什么东西 https://juejin.cn/post/6844904071917207566 