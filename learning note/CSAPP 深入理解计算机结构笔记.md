# CSAPP 深入理解计算机结构笔记

http://www.cs.cmu.edu/afs/cs/academic/class/15213-f15/www/lectures/

https://wdxtub.com/csapp/thin-csapp-0/2016/04/16/

## Questions:

导向编程[6]的堆栈溢出攻击？





## 数据在机器中的存储形式

### 整形Integer

![image-20210511171923045](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210511171923045.png)

观察可以得知两个很重要的特性

- |TMin| = TMax + 1 (范围并不是对称的)
- UMax = 2*TMax + 1
- ![image-20210511172456501](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210511172456501.png)

### 浮点数的表示方法IEEE

### 

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

但是，除了缓冲区溢出，还有另一种攻击的方式，称为**返回导向编程**。可以利用修改已有的代码，来绕过系统和编译器的保护机制，攻击者控制堆栈调用以劫持程序控制流并执行针对性的机器语言指令序列（称为Gadgets）。每一段 gadget 通常结束于 return 指令，并位于共享库代码中的子程序。系列调用这些代码，攻击者可以在拥有更简单攻击防范的程序内执行任意操作。



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

![image-20210514164232711](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210514164232711.png)

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

第一条命令中，在编译链接的时候，如果在 libtest.o 中发现了外部引用，就会在 -lmine 中查找，但是如果反过来，在第二条语句中 libtest.o 后面没有东西，就会出现找不到引用的错误。从中我们可以得到一个写编译命令的技巧：

**把静态库都放到后面去**



##### 动态共享库（dynamic library）

动态链接可以在首次载入的时候执行(load-time linking)，这是 Linux 的标准做法，会由动态链接器 `ld-linux.so` 完成，比方标准 C 库(libc.so) 通常就是动态链接的，这样所有的程序可以共享同一个库，而不用分别进行封装。

动态链接也可以在程序开始执行的时候完成(run-time linking)，在 Linux 中使用 `dlopen()` 接口来完成（会使用函数指针），通常用于分布式软件，高性能服务器上。而且共享库也可以在多个进程间共享，这在后面学习到虚拟内存的时候会介绍。

![image-20210514173615175](E:\Design Pattern\Learn-Web-Hacking\learning note\CSAPP 深入理解计算机结构笔记.assets\image-20210514173615175.png)

http://jayconrod.com/posts/23/tutorial-function-interposition-in-linux  Linux修改动态库函数指向