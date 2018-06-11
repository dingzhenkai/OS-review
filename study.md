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

####DMA

**直接内存访问**（**D**irect **M**emory **A**ccess，**DMA**）是[计算机科学](https://zh.wikipedia.org/wiki/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%A7%91%E5%AD%A6)中的一种内存访问技术。它允许某些[计算机](https://zh.wikipedia.org/wiki/%E9%9B%BB%E8%85%A6)内部的硬件子系统（计算机外设），可以独立地直接读写系统[内存](https://zh.wikipedia.org/wiki/%E8%A8%98%E6%86%B6%E9%AB%94)，而不需[中央处理器](https://zh.wikipedia.org/wiki/%E4%B8%AD%E5%A4%AE%E8%99%95%E7%90%86%E5%99%A8)（CPU）介入处理 。在同等程度的处理器负担下，DMA是一种快速的数据传送方式。很多硬件的系统会使用DMA，包含[硬盘](https://zh.wikipedia.org/wiki/%E7%A1%AC%E7%A2%9F)控制器、[绘图显卡](https://zh.wikipedia.org/wiki/%E7%B9%AA%E5%9C%96%E9%A1%AF%E7%A4%BA%E5%8D%A1)、[网卡](https://zh.wikipedia.org/wiki/%E7%BD%91%E7%BB%9C%E5%8D%A1)和[声卡](https://zh.wikipedia.org/wiki/%E5%A3%B0%E5%8D%A1)。



## OS-2

**随机存取存储器**（英语：**R**andom **A**ccess **M**emory，缩写：**RAM**），也叫**主存**，是与[CPU](https://zh.wikipedia.org/wiki/CPU)直接交换数据的内部存储器。[[1\]](https://zh.wikipedia.org/wiki/%E9%9A%8F%E6%9C%BA%E5%AD%98%E5%8F%96%E5%AD%98%E5%82%A8%E5%99%A8#cite_note-1)它可以随时读写（刷新时除外，见下文），而且速度很快，通常作为[操作系统](https://zh.wikipedia.org/wiki/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F)或其他正在运行中的程序的临时数据存储媒介。

主存（Main memory）即计算机内部最主要的存储器，用来加载各式各样的程序与数据以供[CPU](https://zh.wikipedia.org/wiki/CPU)直接运行与运用。由于[DRAM](https://zh.wikipedia.org/wiki/DRAM)的[性价比](https://zh.wikipedia.org/wiki/%E6%80%A7%E5%83%B9%E6%AF%94)很高，且扩展性也不错，是现今一般计算机[主存](https://zh.wikipedia.org/wiki/%E4%B8%BB%E8%A8%98%E6%86%B6%E9%AB%94)的最主要部分。



























































## TODO

**？？？**User ISA与System ISA的区别：系统ISA中有一些特权指令

Question:Which software does ABI Emulation?



## 不懂

OS-1 23-27

chip是什么

虚拟机的IP是什么，虚拟化的原理？

【[已解答](https://blog.csdn.net/czg13548930186/article/details/52290085)】系统的存储，DRAM，SRAM，RAM，FLASH STORAGE

## 英语

voltage:电压

be prone to somethings:易于遭受某事

notorious：臭名昭著的

harness:利用，控制

faithfully:忠诚的

unified:统一的

decouple:解耦

infrastructure：基础设施