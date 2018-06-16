## OS-1

ISA（Instruction Set Architecture）指令集架构，区分硬件和软件

**？？？**User ISA与System ISA的区别：系统ISA中有一些特权指令

ABI（Application Binary Layer）应用程序二进制接口，跟系统有关，定义了在相应系统上编译程序所需的规则（基本数据类型，通用寄存器的使用，参数的传递规则，以及堆栈的使用），想win10,win7,ubuntu16.04都有自己不同的ABI

让程序能接触到硬件跟系统服务，包含User ISA跟System Call

API（Application Programming Interface）程序调用库函数，read(),write()

![](D:\Courses\OS\PPT\pics\0001.JPG)

### 8 important problems

#### scale up(可扩展性)

#### Security & Privacy（安全和隐私)

#### Power Efficiency（用电的效率）

#### Mobility（流动性）

#### Write Correct Parallel Code(写正确的并行代码)

#### Scale Out(use distributed systems)分布式系统

#### Non-Volatile Storage(稳定的存储)

#### Virtualization(虚拟化)



## OS-2

**随机存取存储器**（英语：**R**andom **A**ccess **M**emory，缩写：**RAM**），也叫**主存**，是与[CPU](https://zh.wikipedia.org/wiki/CPU)直接交换数据的内部存储器。[[1\]](https://zh.wikipedia.org/wiki/%E9%9A%8F%E6%9C%BA%E5%AD%98%E5%8F%96%E5%AD%98%E5%82%A8%E5%99%A8#cite_note-1)它可以随时读写（刷新时除外，见下文），而且速度很快，通常作为[操作系统](https://zh.wikipedia.org/wiki/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F)或其他正在运行中的程序的临时数据存储媒介。

主存（Main memory）即计算机内部最主要的存储器，用来加载各式各样的程序与数据以供[CPU](https://zh.wikipedia.org/wiki/CPU)直接运行与运用。由于[DRAM](https://zh.wikipedia.org/wiki/DRAM)的[性价比](https://zh.wikipedia.org/wiki/%E6%80%A7%E5%83%B9%E6%AF%94)很高，且扩展性也不错，是现今一般计算机[主存](https://zh.wikipedia.org/wiki/%E4%B8%BB%E8%A8%98%E6%86%B6%E9%AB%94)的最主要部分。

PC架构：von Neumann Model（冯 诺伊曼模式）![](D:\course_material\os\final\pics\0003.JPG)

程序运行时，程序的指令和数据会被读到主存里面，CPU会一条一条的执行主存里面的指令。

x86运行模式：

1. 实模式：

实地址模式（real-addess mode）——该模式以扩展的方式实现了8086CPU的程序运行环境（就像切换到保护模式和系统管理模式一样）。处理器在刚刚上电或者重启后的时候，处于实地址模式。

1. 保护模式：

保护模式（protected mode）——是处理器的根本模式，在保护模式下，可以为直接运行的实地址模式程序提供保护的、多任务的环境，这种特性被称作“虚拟8086模式（virtual 8086 mode）”,尽管“虚拟8086”模式并不是一种真正的处理器模式；virtual 8086 mode实际上是保护模式的一种属性，在保护模式下，可以向任何任务提供这种属性。

1. SMM：

系统管理模式（system manangement mode，SMM）——该模式提供操作系统或者执行程序一种透明的机制去实现平台相关的特性，例如电源管理和系统安全。当来自外部的或者APIC控制器的SMM中断pin脚被触发时，处理器在下列情况进入SMM。在SMM下，处理器切换到一个独立的地址空间，同时保存当前运行的程序或任务的上下文。SMM相关的代码可透明的执行。当SMM模式返回时，处理器返回SMI（system manangement interrupt）前的工作模式。SMM模式在Intel 386 SL和Intel 486 SL处理器时被引入，在Pentium家族时成为标准的IA-32架构的特性。

[实模式保护模式SMM必读](http://ahhqlrg.blog.163.com/blog/static/10592880520156635551198/)

x86控制寄存器：

1. CR0: 

   | Bit  | Name | Full Name                                                | Description                                                  |
   | ---- | ---- | -------------------------------------------------------- | ------------------------------------------------------------ |
   | 0    | PE   | Protected Mode Enable                                    | If 1, system is in [protected mode](https://en.wikipedia.org/wiki/Protected_mode), else system is in [real mode](https://en.wikipedia.org/wiki/Real_mode) |
   | 1    | MP   | Monitor co-processor                                     | Controls interaction of WAIT/FWAIT instructions with TS flag in CR0 |
   | 2    | EM   | Emulation                                                | If set, no x87 [floating point unit](https://en.wikipedia.org/wiki/Floating_point_unit) present, if clear, x87 FPU present |
   | 3    | TS   | Task switched                                            | Allows saving x87 task context upon a task switch only after x87 instruction used |
   | 4    | ET   | Extension type                                           | On the 386, it allowed to specify whether the external math coprocessor was an [80287](https://en.wikipedia.org/wiki/80287) or [80387](https://en.wikipedia.org/wiki/80387) |
   | 5    | NE   | Numeric error                                            | Enable internal [x87](https://en.wikipedia.org/wiki/X87) floating point error reporting when set, else enables PC style x87 error detection |
   | 16   | WP   | Write protect                                            | When set, the CPU can't write to read-only pages when privilege level is 0 |
   | 18   | AM   | Alignment mask                                           | Alignment check enabled if AM set, AC flag (in [EFLAGS](https://en.wikipedia.org/wiki/FLAGS_register_(computing)) register) set, and privilege level is 3 |
   | 29   | NW   | Not-write through                                        | Globally enables/disable write-through caching               |
   | 30   | CD   | [Cache](https://en.wikipedia.org/wiki/CPU_cache) disable | Globally enables/disable the memory cache                    |
   | 31   | PG   | Paging                                                   | If 1, enable paging and use the CR3 register, else disable paging |

2. CR1:预留给之后用的，现在调用会抛exception

3. CR2:用于发生Page Fault报告出错信息。当发生页异常时，处理器把引起页异常的线性地址保存在CR2中。操作系统中的页异常处理程序可以检查CR2的内容，从而查出线性地址空间中的哪一页引起本次异常。 

4. CR3:用于保存页目录表页面的物理地址，因此被称为PDBR。由于目录是页对齐的，所以仅高20位有效，低12 位保留供更加高级的处理器使用。向CR3中装入一个新值时，低12位必须为0；但从 CR3中取值时，低12位被忽略。每当用MOV指令重置CR3的值时，会导致分页机制高速缓冲区的内容无效，用此方法，可以在启用分页机制之前，即把PG 位置1之前，预先刷新分页机制的高速缓存。CR3寄存器即使在CR0寄存器的PG位或PE位为0时也可装入，如在实模式下也可设置CR3，以便进行分页机制的初始化。在任务切换时，CR3要被改变，但是如果新任务中CR3的值与原任务中CR3的值相同，那么处理器不刷新分页高速缓存，以便当任务共享页表时有较快的执行速度。

5. CR4：Used in protected mode to control operations such as virtual-8086 support, enabling I/O breakpoints, [page size extension](https://en.wikipedia.org/wiki/Page_Size_Extension) and [machine check exceptions](https://en.wikipedia.org/wiki/Machine_Check_Exception).

   | Bit  | Name                                                         | Full Name                                                    | Description                                                  |
   | ---- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
   | 0    | VME                                                          | [Virtual 8086 Mode Extensions](https://en.wikipedia.org/wiki/Virtual_8086_Mode_Extensions) | If set, enables support for the virtual interrupt flag (VIF) in virtual-8086 mode. |
   | 1    | PVI                                                          | Protected-mode Virtual Interrupts                            | If set, enables support for the virtual interrupt flag (VIF) in protected mode. |
   | 2    | TSD                                                          | Time Stamp Disable                                           | If set, [RDTSC](https://en.wikipedia.org/wiki/Time_Stamp_Counter) instruction can only be executed when in [ring 0](https://en.wikipedia.org/wiki/Ring_(computer_security)), otherwise RDTSC can be used at any privilege level. |
   | 3    | DE                                                           | Debugging Extensions                                         | If set, enables debug register based breaks on I/O space access |
   | 4    | PSE                                                          | [Page Size Extension](https://en.wikipedia.org/wiki/Page_Size_Extension) | If unset, page size is 4 KiB, else page size is increased to 4 MiB (if PAE is enabled or the processor is in Long Mode this bit is ignored[[2\]](https://en.wikipedia.org/wiki/Control_register#cite_note-AMD64-Vol2-2)). |
   | 5    | PAE                                                          | [Physical Address Extension](https://en.wikipedia.org/wiki/Physical_Address_Extension) | If set, changes page table layout to translate 32-bit virtual addresses into extended 36-bit physical addresses. |
   | 6    | MCE                                                          | Machine Check Exception                                      | If set, enables machine check interrupts to occur.           |
   | 7    | PGE                                                          | Page Global Enabled                                          | If set, address translations (PDE or PTE records) may be shared between address spaces. |
   | 8    | PCE                                                          | Performance-Monitoring Counter enable                        | If set, RDPMC can be executed at any privilege level, else RDPMC can only be used in ring 0. |
   | 9    | OSFXSR                                                       | Operating system support for FXSAVE and FXRSTOR instructions | If set, enables [SSE](https://en.wikipedia.org/wiki/Streaming_SIMD_Extensions) instructions and fast FPU save & restore |
   | 10   | OSXMMEXCPT                                                   | Operating System Support for Unmasked SIMD Floating-Point Exceptions | If set, enables unmasked SSE exceptions.                     |
   | 11   | UMIP                                                         | User-Mode Instruction Prevention                             | If set, the SGDT, SIDT, SLDT, SMSW and STR instructions cannot be executed if CPL > 0[[1\]](https://en.wikipedia.org/wiki/Control_register#cite_note-Intel-Vol3a1-1). |
   | 12   | LA57                                                         | (none specified)                                             | If set, enables 5-Level Paging[[3\]](https://en.wikipedia.org/wiki/Control_register#cite_note-Intel-5LP-3). |
   | 13   | VMXE                                                         | Virtual Machine Extensions Enable                            | see [Intel VT-x](https://en.wikipedia.org/wiki/Intel_VT-x)   |
   | 14   | SMXE                                                         | Safer Mode Extensions Enable                                 | see [Trusted Execution Technology](https://en.wikipedia.org/wiki/Trusted_Execution_Technology) (TXT) |
   | 16   | FSGSBASE                                                     | Enables the instructions RDFSBASE, RDGSBASE, WRFSBASE, and WRGSBASE. |                                                              |
   | 17   | PCIDE                                                        | PCID Enable                                                  | If set, enables process-context identifiers (PCIDs).         |
   | 18   | OSXSAVE                                                      | XSAVE and Processor Extended States Enable                   |                                                              |
   | 20   | SMEP[[4\]](https://en.wikipedia.org/wiki/Control_register#cite_note-4) | Supervisor Mode Execution Protection Enable                  | If set, execution of code in a higher ring generates a fault |
   | 21   | SMAP                                                         | [Supervisor Mode Access Prevention](https://en.wikipedia.org/wiki/Supervisor_Mode_Access_Prevention) Enable | If set, access of data in a higher ring generates a fault[[5\]](https://en.wikipedia.org/wiki/Control_register#cite_note-5) |
   | 22   | PKE                                                          | Protection Key Enable                                        | See Intel® 64 and IA-32 Architectures Software Developer’s Manual |

Memory-management Register(内存管理寄存器)

1. GDTR（Global Descriptor Table Register）存放GDT的入口地址，**物理地址**
2. IDTR (Interrupt Descriptor Table Register)
3. TR(Task Register)

[内存管理笔记(分页，分段，逻辑地址，物理地址与地址转换方式)](https://www.cnblogs.com/felixfang/p/3420462.html)



## OS-3

系统设计要注意模块化，设计与实现分离，这样设计需要修改时，实现可以不用大改。

#### MicroKernel

1. 最小的分配单元：进程
2. 最小的执行单元：线程
3. 应用可以实现自己的CPU调度
4. IPC与线程间通信？？？
5. 对Syscall的处理：trap 到用户模式
6. 微内核把所有原来内核里面的系统服务都用进程来实现，要调用系统服务的时候，使用IPC
   ![](D:\course_material\os\final\pics\0004.JPG)
7. Redirection allows call traps to link directly to executable binaries without modifying the kernel!Just need an emulation library

#### L4 Microkernel

1. 之前的IPC的copy是先copy到kernel，然后kernel再copy到系统服务进程，在L4里面是直接copy到系统服务进程，不经过kernel
2. 异步的IPC

#### Exokernel

Exokernel微内核的核心观点是：只要内核还提供对系统资源的抽象，就不能实现性能的最大优化 -- 内核应该支持一个最小的、高度优化的原语集，而不是提供对系统资源的抽象。从这个观点上来说，IPC也是一个太高级的抽象因而不能达到最高的性能。Exokernel微内核的核心是支持一个高度优化的原语名叫保护控制转移(protected control transfer, PCT)。PCT是一个不带参数的跨地址空间的过程调用，其功能类似于一个硬件中断。在PCT的基础上，可以实现高级的IPC抽象如RPC。在MIPS R3000处理器上，一个基于PCT的RPC实现了仅10µs的开销，而在同一硬件上运行的Mach RPC为95µs。



























































## 知识点

#### hypervisor

Hypervisor——一种运行在基础物理服务器和操作系统之间的中间软件

层,可允许多个操作系统和应用共享硬件。也可叫做VMM（ virtual machine monitor ），即虚拟机监视器。

[![img](https://gss1.bdstatic.com/9vo3dSag_xI4khGkpoWK1HF6hhy/baike/s%3D220/sign=401df813b319ebc4c478719bb226cf79/10dfa9ec8a1363275bfc0545918fa0ec08fac7b7.jpg)](https://baike.baidu.com/pic/hypervisor/3353492/0/99636c0e35df258c7bcbe1d0?fr=lemma&ct=single)

Hypervisors是一种在虚拟环境中的“元”操作系统。他们可以访问服务器上包括磁盘和内存在内的所有物理设备。Hypervisors不但协调着这些硬件资源的访问，也同时在各个虚拟机之间施加防护。当服务器启动并执行Hypervisor时，它会加载所有虚拟机客户端的操作系统同时会分配给每一台虚拟机适量的内存，CPU，网络和磁盘。

#### Android Architecture

![](D:\Courses\OS\PPT\pics\0002.JPG)

#### Programmed I/O

Programmed I/O是指用特定的IO指令实现从设备到CPU的数据传输。具体表现在Intel的x86平台上有访问端口的IN和OUT指令。

PIO是 [CPU](https://zh.wikipedia.org/wiki/CPU) 与[外围设备](https://zh.wikipedia.org/wiki/%E5%A4%96%E5%9B%B4%E8%AE%BE%E5%A4%87)（如[网卡](https://zh.wikipedia.org/wiki/%E7%BD%91%E5%8D%A1)、[硬盘](https://zh.wikipedia.org/wiki/%E7%A1%AC%E7%9B%98)等）传输数据的一种方法。

#### DMA

**直接内存访问**（**D**irect **M**emory **A**ccess，**DMA**）是[计算机科学](https://zh.wikipedia.org/wiki/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%A7%91%E5%AD%A6)中的一种内存访问技术。它允许某些[计算机](https://zh.wikipedia.org/wiki/%E9%9B%BB%E8%85%A6)内部的硬件子系统（计算机外设），可以独立地直接读写系统[内存](https://zh.wikipedia.org/wiki/%E8%A8%98%E6%86%B6%E9%AB%94)，而不需[中央处理器](https://zh.wikipedia.org/wiki/%E4%B8%AD%E5%A4%AE%E8%99%95%E7%90%86%E5%99%A8)（CPU）介入处理 。在同等程度的处理器负担下，DMA是一种快速的数据传送方式。很多硬件的系统会使用DMA，包含[硬盘](https://zh.wikipedia.org/wiki/%E7%A1%AC%E7%A2%9F)控制器、[绘图显卡](https://zh.wikipedia.org/wiki/%E7%B9%AA%E5%9C%96%E9%A1%AF%E7%A4%BA%E5%8D%A1)、[网卡](https://zh.wikipedia.org/wiki/%E7%BD%91%E7%BB%9C%E5%8D%A1)和[声卡](https://zh.wikipedia.org/wiki/%E5%A3%B0%E5%8D%A1)。

#### MMU

MMU负责地址翻译，TLB是地址翻译的cache。

**内存管理单元**（英语：**memory management unit**，缩写为**MMU**），有时称作**分页内存管理单元**（英语：**paged memory management unit**，缩写为**PMMU**）。它是一种负责处理[中央处理器](https://zh.wikipedia.org/wiki/%E4%B8%AD%E5%A4%AE%E5%A4%84%E7%90%86%E5%99%A8)（CPU）的[内存](https://zh.wikipedia.org/wiki/%E5%86%85%E5%AD%98)访问请求的[计算机硬件](https://zh.wikipedia.org/wiki/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%A1%AC%E4%BB%B6)。它的功能包括[虚拟地址](https://zh.wikipedia.org/wiki/%E8%99%9A%E6%8B%9F%E5%9C%B0%E5%9D%80)到[物理地址](https://zh.wikipedia.org/wiki/%E7%89%A9%E7%90%86%E5%9C%B0%E5%9D%80)的转换（即[虚拟内存](https://zh.wikipedia.org/wiki/%E8%99%9A%E6%8B%9F%E5%86%85%E5%AD%98)管理）、[内存保护](https://zh.wikipedia.org/wiki/%E5%86%85%E5%AD%98%E4%BF%9D%E6%8A%A4)、中央处理器[高速缓存](https://zh.wikipedia.org/wiki/%E9%AB%98%E9%80%9F%E7%BC%93%E5%AD%98)的控制，在较为简单的计算机体系结构中，负责[总线](https://zh.wikipedia.org/wiki/%E6%80%BB%E7%BA%BF)的[仲裁](https://zh.wikipedia.org/w/index.php?title=%E4%BB%B2%E8%A3%81_(%E7%94%B5%E5%AD%90%E5%99%A8%E4%BB%B6)&action=edit&redlink=1)以及[存储体切换](https://zh.wikipedia.org/w/index.php?title=%E5%AD%98%E5%82%A8%E4%BD%93%E5%88%87%E6%8D%A2&action=edit&redlink=1)（bank switching，尤其是在[8位](https://zh.wikipedia.org/wiki/8%E4%BD%8D)的系统上）。

现代的内存管理单元是以页的方式，分区虚拟[地址空间](https://zh.wikipedia.org/wiki/%E5%9C%B0%E5%9D%80%E7%A9%BA%E9%97%B4)（处理器使用的地址范围）的；页的大小是2的n次方，通常为几[KB](https://zh.wikipedia.org/wiki/KB)。地址尾部的n位（页大小的2的次方数）作为页内的偏移量保持不变。其余的地址位（address）为（虚拟）页号。内存管理单元通常借助一种叫做[转译旁观缓冲器](https://zh.wikipedia.org/wiki/%E8%BD%89%E8%AD%AF%E6%97%81%E8%A7%80%E7%B7%A9%E8%A1%9D%E5%8D%80)（Translation Lookaside Buffer，缩写为TLB）的相联高速缓存（associative cache）来将虚拟页号转换为物理页号。当后备缓冲器中没有转换记录时，则使用一种较慢的机制，其中包括专用硬件（hardware-specific）的数据结构（Data structure）或软件辅助手段。这个数据结构称为[分页表](https://zh.wikipedia.org/wiki/%E5%88%86%E9%A0%81%E8%A1%A8)，页表中的数据就叫做页表项（page table entry，缩写为PTE）。物理页号结合页偏移量便提供出了完整的物理地址。

页表或转换后备缓冲器中数据项包括的信息有：一、“脏位”（页面重写标志位，dirty bit）——表示该页是否被写过。二、“访问位”（accessed bit）——表示该页最后使用于何时，以便于[最近最少使用](https://zh.wikipedia.org/w/index.php?title=%E6%9C%80%E8%BF%91%E6%9C%80%E5%B0%91%E4%BD%BF%E7%94%A8%E7%AE%97%E6%B3%95&action=edit&redlink=1)[页面置换算法](https://zh.wikipedia.org/w/index.php?title=%E9%A1%B5%E9%9D%A2%E7%BD%AE%E6%8D%A2%E7%AE%97%E6%B3%95&action=edit&redlink=1)（least recently used page replacement algorithm）的实现。三、哪种进程可以读写该页的信息，例如[用户模式](https://zh.wikipedia.org/wiki/%E7%94%A8%E6%88%B7%E6%A8%A1%E5%BC%8F)（user mode）进程还是[特权模式](https://zh.wikipedia.org/w/index.php?title=%E7%89%B9%E6%9D%83%E6%A8%A1%E5%BC%8F&action=edit&redlink=1)（supervisor mode）进程。四、该页是否应被高速缓冲的信息。

有时，TLB或PTE会禁止对虚拟页的访问，这可能是因为没有物理[随机存取存储器](https://zh.wikipedia.org/wiki/%E9%9A%8F%E6%9C%BA%E5%AD%98%E5%8F%96%E5%AD%98%E5%82%A8%E5%99%A8)（random access memory）与虚拟页相关联。如果是这种情况，MMU将向CPU发出[页错误](https://zh.wikipedia.org/wiki/%E9%A1%B5%E9%94%99%E8%AF%AF)（page fault）的信号。[操作系统](https://zh.wikipedia.org/wiki/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F)operating system）将进行处理，也许会尝试寻找RAM的空白帧，同时创建一个新的PTE将之映射到所请求的虚拟地址。如果没有空闲的RAM，可能必须关闭一个已经存在的页面，使用一些替换算法，将之保存到磁盘中（这被称之为[页面调度](https://zh.wikipedia.org/w/index.php?title=%E9%A1%B5%E9%9D%A2%E8%B0%83%E5%BA%A6&action=edit&redlink=1)（paging）。在一些MMU中，PTEs或者TLB也存在一些缺点，在这样的情况下操作系统将必须释放空间以供新的映射。

####BIOS

BIOS是英文"Basic Input Output System"的缩略语，直译过来后中文名称就是"[基本输入输出系统](https://www.baidu.com/s?wd=%E5%9F%BA%E6%9C%AC%E8%BE%93%E5%85%A5%E8%BE%93%E5%87%BA%E7%B3%BB%E7%BB%9F&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)"。其实，它是一组固化到计算机内主板上一个ROM芯片上的程序，它保存着计算机最重要的基本输入输出的程序、系统设置信息、开机后自检程序和系统自启动程序。 其主要功能是为计算机提供最底层的、最直接的硬件设置和控制。

####ROM

ROM是只读内存（Read-Only Memory）的简称，是一种只能读出事先所存数据的固态[半导体存储器](https://www.baidu.com/s?wd=%E5%8D%8A%E5%AF%BC%E4%BD%93%E5%AD%98%E5%82%A8%E5%99%A8&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)。其特性是一旦储存资料就无法再将之改变或删除。通常用在不需经常变更资料的电子或[电脑系统](https://www.baidu.com/s?wd=%E7%94%B5%E8%84%91%E7%B3%BB%E7%BB%9F&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)中，资料并且不会因为电源关闭而消失。

####RAM

RAM（random access memory）随机存储器。存储单元的内容可按需随意取出或存入，且存取的速度与存储单元的位置无关的存储器。这种存储器在断电时将丢失其存储内容，故主要用于存储短时间使用的程序。 按照存储信息的不同，随机存储器又分为[静态随机存储器](https://www.baidu.com/s?wd=%E9%9D%99%E6%80%81%E9%9A%8F%E6%9C%BA%E5%AD%98%E5%82%A8%E5%99%A8&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)（Static RAM,SRAM)和动态随机存储器（Dynamic RAM,DRAM)。

#### CMOS

CMOS（Complementary Metal Oxide Semiconductor），[互补金属氧化物半导体](https://www.baidu.com/s?wd=%E4%BA%92%E8%A1%A5%E9%87%91%E5%B1%9E%E6%B0%A7%E5%8C%96%E7%89%A9%E5%8D%8A%E5%AF%BC%E4%BD%93&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)，电压控制的一种放大器件。是组成[CMOS数字集成电路](https://www.baidu.com/s?wd=CMOS%E6%95%B0%E5%AD%97%E9%9B%86%E6%88%90%E7%94%B5%E8%B7%AF&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)的基本单元。

####BIOS与CMOS

BIOS与CMOS
区别　　由于CMOS与BIOS都跟[电脑系统](https://www.baidu.com/s?wd=%E7%94%B5%E8%84%91%E7%B3%BB%E7%BB%9F&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)设置密切相关，所以才有CMOS设置和BIOS设置的说法。也正因此，初学者常将二者混淆。CMOS 是电脑主机板上一块特殊的RAM芯片，是系统参数存放的地方，而BIOS中系统设置程序是完成参数设置的手段。因此，准确的说法应是通过BIOS设置程序对CMOS参数进行设置。而我们平常所说的CMOS设置和BIOS设置是其简化说法，也就在一定程度上造成了两个概念的混淆。事实上，BIOS程序是储存在主板上一块 EEPROM Flash 芯片中的，CMOS存储器是用来存储BIOS设定后的要保存数据的，包括一些系统的硬件配置和用户对某些参数的设定，比如传统BIOS的系统密码和设备启动顺序等等 
联系　　BIOS是一组设置硬件的电脑程序，保存在主板上的一块EPROM或EEPROM芯片中，里面装有系统的重要信息和设置系统参数的设置程序——BIOS Setup程序。而CMOS即：Complementary Metal Oxide Semiconductor——[互补金属氧化物半导体](https://www.baidu.com/s?wd=%E4%BA%92%E8%A1%A5%E9%87%91%E5%B1%9E%E6%B0%A7%E5%8C%96%E7%89%A9%E5%8D%8A%E5%AF%BC%E4%BD%93&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)，是主板上的一块可读写的RAM芯片，用来保存当前系统的硬件配置和用户对参数的设定，其内容可通过设置程序进行读写。[CMOS芯片](https://www.baidu.com/s?wd=CMOS%E8%8A%AF%E7%89%87&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)由主板上的[钮扣电池](https://www.baidu.com/s?wd=%E9%92%AE%E6%89%A3%E7%94%B5%E6%B1%A0&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)供电，即使系统断电，参数也不会丢失。[CMOS芯片](https://www.baidu.com/s?wd=CMOS%E8%8A%AF%E7%89%87&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)只有保存数据的功能，而对CMOS中各项参数的修改要通过BIOS的设定程序来实现。BIOS与CMOS既相关又不同：BIOS中的系统设置程序是完成CMOS参数设置的手段；CMOS RAM既是BIOS设定系统参数的存放场所，又是 BIOS设定系统参数的结果。因此，完整的说法应该是“通过BIOS设置程序对CMOS参数进行设置”。由于 BIOS和CMOS都跟系统设置密初相关，所以在实际使用过程中造成了BIOS设置和CMOS设置的说法，其实指的都是同一回事，但BIOS与CMOS却是两个完全不同的概念，切勿混淆。

####volatile

volatile是一个类型[修饰符](https://baike.baidu.com/item/%E4%BF%AE%E9%A5%B0%E7%AC%A6)（type specifier），就像大家更熟悉的const一样，它是被设计用来修饰被不同线程访问和修改的[变量](https://baike.baidu.com/item/%E5%8F%98%E9%87%8F/5271)。**volatile**的作用是作为指令[关键字](https://baike.baidu.com/item/%E5%85%B3%E9%94%AE%E5%AD%97)，确保本条指令不会因[编译器](https://baike.baidu.com/item/%E7%BC%96%E8%AF%91%E5%99%A8)的优化而省略，且要求每次直接读值。

volatile的变量是说这变量可能会被意想不到地改变，这样，[编译器](https://baike.baidu.com/item/%E7%BC%96%E8%AF%91%E5%99%A8)就不会去假设这个变量的值了。

#### System call

Linux 的系统调用通过 int 80h 实现，用[系统调用号](https://zh.wikipedia.org/w/index.php?title=%E7%B3%BB%E7%BB%9F%E8%B0%83%E7%94%A8%E5%8F%B7&action=edit&redlink=1)来区分入口函数。操作系统实现系统调用的基本过程是：

1. 应用程序调用库函数（API）；
2. API 将系统调用号存入 EAX，然后通过中断调用使系统进入内核态；
3. 内核中的中断处理函数根据系统调用号，调用对应的内核函数（系统调用）；
4. 系统调用完成相应功能，将返回值存入 EAX，返回到中断处理函数；
5. 中断处理函数返回到 API 中；
6. API 将 EAX 返回给应用程序。

应用程序调用系统调用的过程是：

1. 把系统调用的编号存入 EAX；
2. 把函数参数存入其它通用寄存器；
3. 触发 0x80 号中断（int 0x80）。

####

## TODO

**？？？**User ISA与System ISA的区别：系统ISA中有一些特权指令

Question:Which software does ABI Emulation?



## 不懂

【已解决】OS-1 23-27

OS-2 33-41

【已解决】OS-3 14-15(害处)  OS-17

chip是什么

虚拟机的IP是什么，虚拟化的原理？

【[已解答](https://www.zhihu.com/question/29918252)】线性地址，逻辑地址，物理地址，虚拟地址的区别？

逻辑地址：段：偏移

线性地址：段描述符+偏移（因为段描述符被设置为0）所以线性地址数值上等于虚拟地址

虚拟地址；偏移

物理地址：线性地址地址翻译

【[已解答](https://blog.csdn.net/czg13548930186/article/details/52290085)】系统的存储，DRAM，SRAM，RAM，FLASH STORAGE

GDT里面存的什么，跟代码段 数据段这些有什么关系

HW的标准答案

【[半解决](https://www.jianshu.com/p/9218692cb209)】IPC与线程间通信

什么是trap（csapp 异常控制流）

什么是IPC的port

## 英语

voltage:电压

be prone to somethings:易于遭受某事

notorious：臭名昭著的

harness:利用，控制

faithfully:忠诚的

unified:统一的

decouple:解耦

infrastructure：基础设施

resident:定居的，常驻的，存于内存中的

detriment:损害 不利

primitive: 原始的，原函数

eliminate: 消除 

Rendezvous:会合