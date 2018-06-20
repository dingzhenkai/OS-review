Linux启动流程

[系统软件](https://www.binss.me/blog/?category=2) 2017-02-21 21:14

本文发自 [http://www.binss.me/blog/boot-process-of-linux/](https://www.binss.me/blog/boot-process-of-linux/)，转载请注明出处。

最近在折腾kernel的过程中频频卡在启动过程的某个阶段，发现自己对Linux的启动过程缺乏了解，因此查阅了一些资料，先以本文作为概述，后续再进行详述。

## 启动过程

1. 通电后，寄存器reset，CPU跳转到BIOS(Basic Input/Output System)代码处开始执行

   读取并校验CMOS(可读写RAM芯片，保存计算机基本启动信息，如时间、设备信息、启动设置等。靠钮扣电池供电，断电信息不丢失)中的内容是否正确、有哪些硬件、检查某些硬件的状态。如果出错，由于VGA未激活，无法从屏幕上查看相关信息，只能通过主板的蜂鸣声进行判断。

2. BIOS进行POST(Power-On Self-Test, 硬件自检)，初始化硬件

   检查硬件设备能否正常工作，并执行硬件的ROM代码进行硬件初始化。

   自检通过后，BIOS根据设置的启动顺序(Boot Sequence)把控制权转交给排在第一位的存储设备。可以通过狂按F12打开启动顺序菜单手动选择要加载的存储设备。

3. BIOS引导程序读取位于该设备第一个扇区(512bytes)的主引导记录(MBR，Master boot record)到内存中，加载并将控制权转交给bootloader

   MBR组成如下(下文单位都为byte)

   > 0-445 bootloader
   >
   > 446-509 磁盘分区表(Disk Partition table)
   >
   > 510-511 MBR结束标志。如果值为0x55和0xAA，表明这个设备可以用于启动，否则将控制权转交给"启动顺序"中的下一个设备。

   由于分区表的长度只有64个字节，每个表项16个字节。所以最多只能定义四个分区，称为主分区。

   分区表表项组成如下：

   > 0 flag，如果为0x80，就表示该主分区是激活分区，可引导
   >
   > 1-3 分区第一个扇区的物理位置(磁头号+扇区号+柱面号)
   >
   > 4 分区类型。0B为FAT32，07为NTFS，83为ext
   >
   > 5-7 分区最后一个扇区的物理位置(磁头号+扇区号+柱面号)
   >
   > 8-11 分区第一个扇区的相对于磁盘开始的位置。
   >
   > 12-15 分区的扇区总数(4byte可以表示2^32个扇区)

   为了解决主分区不够的问题，引入了扩展分区，即通过表示为扩展分区。通过对扩展分区进一步进行划分，可以得到多个逻辑分区。逻辑分区通过维护下一个逻辑分区的起始位置，像链表式地串了起来。但需要注意的是扩展分区只能有一个。

   每个逻辑分区的第一个扇区为扩展引导记录(EBR, Extended boot record)，组成如下：

   > 0-445 未用。可能用来存放其他boot loader
   >
   > 446-461 第一个分区表项
   >
   > 462-477 第二个(下一个)分区表项
   >
   > 478-493 第三个分区表项，未用
   >
   > 494-509 第四个分区表项，未用
   >
   > 510-511 结束标志：0x55 0xAA

   分区表项结构和MBR的分区表项相同。

   BIOS引导程序检查分区表寻找一个激活的分区。一旦找到，就开始在/boot寻找bootloader，并把它加载到RAM。

4. 执行bootloader

   目前Liunx下主流的bootloader为GRUB2。它是GRUB的升级重构版，相比GRUB有以下变化：

   - 配置文件由/boot/grub/menu.lst改为/boot/grub/grub.cfg 不应该直接修改配置文件，而是修改/etc/default/grub后通过update-grub来更新之。

   - 已经模块化,不再需要'stage1.5'

   - 磁盘分区编号变更。现在第一个磁盘分区是 1 而不是 0。然而第一个设备仍然是 0

   - 引入菜单项生成脚本

     脚本存放在/etc/grub.d下，负责生成菜单项。它们按文件名前端的数字按从小到大的顺序依次执行。

     00_header负责解析/etc/default/grub配置文件。

     10_linux为/boot下的每个内核创建一个正规菜单项和一个恢复菜单项。

     20_memtest86+为memtest86+(内存测试工具)创建菜单项。

     20_linux_xen为xen创建菜单项。

     30_os-prober为其它分区里的操作系统创建菜单项。能够识别安装 Linux、 Windows、 BSD 以及 Mac OS X 。

     40_custom，供用户手动添加菜单项。

     GRUB能够将控制权转交给其它bootloader，如转交给Windows的bootloader，这时只需在40_custom加入以下内容即可：

     ```
     menuentry "Windows" {        insmod chain        insmod ntfs        set root=（hd0,1）        chainloader +1}
     ```

     (hd0,1)表示第一个磁盘设备的第一个分区(设备号从0开始数，而分区号从1开始数)。该配置会增加Windows菜单项，选中后控制权将转交给hd0的第一个分区(ntfs格式)，启动其位于boot sector的bootloader。

     还可以跳回MBR的bootloader：

     ```
     menuentry 'Go to MBR' --id 'mbr' {        insmod chain        set root=（hd0）        chainloader +1}
     ```

   由于MBR中提供给bootloader的空间有限(只有446 byte)，所以需要进行二阶段加载(stage1.5是可选的，一般没有)。

   stage1：加载存放在MBR前446个byte中的boot.img，其中硬编码了stage2要用到文件的磁盘位置。它将core.img读入内存，然后将控制权交给它

   stage2：动态加载链接的mod(/boot/grub2/i386-pc/*.mod)，执行配置文件(/boot/grub/grub.cfg)生成菜单项供用户选择(按ESE/Shift可避免菜单界面被跳过)，将控制权转交给用户选择或默认的启动项，比如说某个Linux kernel。

   我们选择的菜单项的配置如下：

   ```
   menuentry 'Ubuntu, with Linux 4.4.0-57-generic' --class ubuntu --class gnu-linux --class gnu --class os $menuentry_id_option 'gnulinux-4.4.0-57-generic-advanced-de5274e8-7fd2-47f0-b567-eed623d05ada' {    recordfail    load_video    gfxmode $linux_gfx_mode    insmod gzio    if [ x$grub_platform = xxen ]; then insmod xzio; insmod lzopio; fi    insmod part_msdos    insmod ext2    set root='hd0,msdos1'    if [ x$feature_platform_search_hint = xy ]; then      search --no-floppy --fs-uuid --set=root --hint-bios=hd0,msdos1 --hint-efi=hd0,msdos1 --hint-baremetal=ahci0,msdos1  de5274e8-7fd2-47f0-b567-eed623d05ada    else      search --no-floppy --fs-uuid --set=root de5274e8-7fd2-47f0-b567-eed623d05ada    fi    echo    'Loading Linux 4.4.0-57-generic ...'    linux   /boot/vmlinuz-4.4.0-57-generic root=UUID=de5274e8-7fd2-47f0-b567-eed623d05ada ro find_preseed=/preseed.cfg auto noprompt priority=critical locale=en_US quiet    echo    'Loading initial ramdisk ...'    initrd  /boot/initrd.img-4.4.0-57-generic}
   ```

   该配置先载入了gzio驱动(用来解压initrd)、msdos(用来支持硬盘的MS-DOS分区)和ext2驱动(为了挂载/boot目录)，然后根据initrd和linux的路径进行加载，从而进入下一阶段。

5. 加载kernel

   kernel其实就是/boot/vmlinuz-xxx。它是可引导的、用gzip压缩过Linux kernel。该文件在开头部分内嵌有gzip解压缩代码，能够进行自解压。

   ```
   $ sudo file vmlinuz-4.4.0-57-genericvmlinuz-4.4.0-57-generic: Linux kernel x86 boot executable bzImage, version 4.4.0-57-generic (buildd@lgw01-54) #78-Ubuntu SMP Fri Dec 9 23:, RO-rootFS, swap_dev 0x6, Normal VGA
   ```

   可以通过以下方式解压：

   ```
   // 寻找gzip第一个签名的位置$ od -t x1 -A d vmlinuz-4.4.0-57-generic | grep "1f 8b 08"0018864 ac fe ff ff 1f 8b 08 00 00 00 00 00 02 03 ec dd
   // 18864 + 偏移量4(ac fe ff ff) = 18868$ dd if=vmlinuz-4.4.0-57-generic bs=1 skip=18868 | zcat > vmlinuz-4.4.0-57-generic-decompress
   $ file vmlinuz-4.4.0-57-generic-decompressvmlinuz-4.4.0-57-generic-decompress: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, BuildID[sha1]=d3b00331a23bbf9d77720a19923606381623b3cb, stripped
   $ strings vmlinuz-4.4.0-57-generic-decompress | grep 'Linux version'Linux version 4.4.0-57-generic (buildd@lgw01-54) (gcc version 5.4.0 20160609 (Ubuntu 5.4.0-6ubuntu1~16.04.4) ) #78-Ubuntu SMP Fri Dec 9 23:50:32 UTC 2016 (Ubuntu 4.4.0-57.78-generic 4.4.35)
   ```

   kernel加载后，开始进行一些初始化工作，比如加载驱动等。在以往驱动类型少的时候，直接把驱动嵌入到kernel里就完事了。但随着设备的发展，文件系统的类型越来越多，可能是各种各样的存储设备(RAID/USB/MTD)，网络的文件系统，甚至是加了密的文件系统，这些文件系统都需要专有的驱动。为了能够加载这些文件系统，将一大堆驱动都打包进kernel显然不现实。

   于是我们将驱动从kernel中分离开来，放到磁盘上，kernel将磁盘挂载后再进行动态加载驱动。但这时问题来了：kernel需要加载驱动来挂载磁盘，而驱动又存于磁盘上，这不就是鸡生蛋蛋生鸡的问题吗。为了解决这个问题，Linux引入了initrd(Initial RAM Disk)，来为kernel提供初始化时所需要的文件系统环境。

   initrd是ramdisk类型的文件，本质上是块设备，只提供了块设备的读写接口。kernel将其解压后挂到`/`目录上，从而得到一系列驱动和启动脚本。

   然而initrd有以下缺点：

   - 作为块设备，kernel在使用时依然需要文件系统(ext2)的支持，意味着要把ext2驱动编译进kernel
   - 需要挂载，挂载后还要用被称为"dirty hack"的pivot_root切换根目录
   - 即使ramdisk本身就在内存中，但依然要先复制到page cache中来使用，相当于在内存中存在了两份。
   - 没有弹性，分配容量后，未用区域被浪费，而扩展需要重新格式化
   - 基于root device是块设备的假设
   - 不以pid为1运行初始化程序

   因此自Linux kernel 2.6以来，initrd被initramfs(Initial Ram File System)取代。initramfs是rootfs(ramfs的一种)类型的文件，无需挂载，由cpio解压后能够直接被加载到内存。但为了保持一致性，initramfs依然按传统以initrd为名。

   挂载initramfs后，执行/init脚本，挂载根设备到$rootmnt(默认为/root)下，然后切换根目录，运行真正的init程序。

   关于该阶段在此后将有更详细的文章进行分析。

6. 启动userspace程序

   执行对应平台汇编代码(arch/i386/boot/head.S)，进行一些硬件的初始化和环境初始化(如栈设置)，然后执行通用内核代码(init/main.c)，调用start_kernel。最后，调用kernel_thread(arch/i386/kernel/process.c)执行init。

   init是kernel初始化后第一个运行的进程`/sbin/init`(如Upstart、systemd，详情可以看下一篇文章[init之争])，pid为1。它根据配置文件启动其他进程。就绪后，提供用户登陆方式，如命令行(getty)登录，ssh(sshd)登陆，图形界面等。

至此，系统的启动过程就结束了，总结一下，启动流程如下：

[![img](https://illustration-10018028.file.myqcloud.com/20170221211235.jpg)](https://illustration-10018028.file.myqcloud.com/20170221211235.jpg)

## 其他

正文塞不下都塞到这里了。

### UEFI(Unified Extensible Firmware Interface)

Legacy BIOS由于历史原因，存在以下问题：

1. 以Real mode(16bit)启动，而目前主流CPU已是64bit
2. 寻址空间只有1MB(2^20)
3. 以中断向量的方式与硬件进行交互，需要将中断处理程序放到内存中，但空间受限
4. 界面丑陋，操作死板，实现困难，易用性低

为了避免BIOS拖后腿，英特尔牵头搞出UEFI。相比BIOS，有以下优点：

1. 取消Real mode。突破传统16位代码的寻址能力，能够在32位或64位乃至未来更多位的模式下运行。
2. 事先储存硬件信息，跳过POST阶段(出问题时还是要重新POST的)，加快启动速度
3. 取消中断服务，利用加载EFI驱动的形式来和硬件进行交互，一般驱动被存放EFI分区中。
4. 几乎全由C编写，降低了实现难度

吐槽：一切都挺好，然而微软搞了个大新闻，让市面上贴着Windows认证的带UEFI固件的机器都附带并开启了Secure Boot功能。该功能号称为了保证操作系统的安全性，程序需要有合法的签名才会被boot，从而保证恶意代码无法被执行在boot阶段不会被执行。然而真实目的你懂的，没签名的bootloader无法加载，自然系统也无法加载，系统启动的生杀大权掌握在微软手上。在饱受抨击后，微软为部分系统签了名(如Ubuntu)，并声明用户可以手动关闭Secure Boot功能。对此Liunx阵营是拒绝的，依然在玩BIOS+MBR那一套。。

UEFI一般采用了GPT作为分区表格式，其不包含bootloader。在启动时，它会读取EFI分区，如果EFI分区存在且有bootloader，则将控制权转交给它。否则退化到BIOS的启动方式(说的就是GRUB)，读取位于第一个扇区的Protective MBR到内存中，将控制权交给里面的Bootloader。

### GPT(GUID Partition Table)

相比传统分区表MBR，GPT最多支持128个分区，并且每个分区最大容量能够达到EB级。

GPT将分区信息存储在分区表项区中，但出于兼容性考虑，磁盘的第一个扇区仍然用作MBR(Protective MBR)，用来防止不支持GPT的硬盘管理工具错误识别并破坏硬盘中的数据。该MBR只有一个标识为0xEE的分区，以此来表示这块硬盘使用GPT分区表。不能识别GPT硬盘的系统通常会识别出一个未知类型的分区，然后拒绝对硬盘进行操作。而能够识别GPT的系统会加载下一个扇区，即GPT分区表头，其结构如下：

> 0-7 签名(EFI PART)
>
> 8-11 版本
>
> 12-15 分区表头的大小
>
> 16-19 分区表头(0-91)的CRC32校验，在计算时把这个字段作为0处理
>
> 20-23 保留
>
> 24-31 当前LBA(location of this header copy)
>
> 32-39 备份LBA
>
> 40-47 第一个可用于分区的LBA
>
> 48-55 最后一个可用于分区的LBA
>
> 56-71 硬盘GUID/UUID
>
> 72-79 分区表项的起始LBA(默认为2)
>
> 80-83 分区表项的数量
>
> 84-87 分区表项的大小(默认为128)
>
> 88-91 分区数组的CRC32校验
>
> 92- 保留

GPT分区表头扇区后跟着的是GPT分区表项，每个表项大小为128byte，结构如下：

> 0-15 分区类型的GUID
>
> 16-31 分区的GUID
>
> 32-39 分区的起始LBA
>
> 40-47 分区的末尾LBA
>
> 48-55 属性
>
> 56-127 分区名

可以通过parted查看当前分区表类型：

```
sudo parted -l | grep "Partition Table"Partition Table: msdos
```

msdos其实就是MBR。如果显示gpt，则分区表类型为GPT。

### 配置GRUB2

可供用户修改的配置文件：`/etc/default/grub`。常用配置如下：

- GRUB_DEFAULT

  默认选择的启动菜单项。grub默认启动最新安装的kernel，但用户可以通过该选项自定义。

  查找`/boot/grub/grub.cfg`文件中的submenu，得到一级标题，如 Advanced options for Ubuntu

  查找menuentry，得到二级标题，如：Ubuntu, with Linux 4.4.0-59-generic

  然后拼起来：`GRUB_DEFAULT="Advanced options for Ubuntu>Ubuntu, with Linux 4.4.0-59-generic"`

- GRUB_SAVEDEFAULT

  设置为true后，如果GRUB_DEFAULT为saved，则默认启动上一次选择的启动项。

- GRUB_TIMEOUT

  菜单显示时间。

- GRUB_DEVICE

  指定根设备，会覆盖自动检测得到的设备，会在配置文件menuentry的linux项中体现(root=...)。

- GRUB_DEVICE_UUID

  指定根设备的UUID，会覆盖自动检测得到的设备，会在配置文件menuentry的linux项中体现(root=...)。

- GRUB_DISABLE_RECOVERY

  不生成recovery菜单项。recovery菜单项实际上就是在linux项中加了recovery参数。

- GRUB_CMDLINE_LINUX

  传递给kernel的参数。会在配置文件menuentry的linux项中体现。

- GRUB_CMDLINE_LINUX_DEFAULT

  传递给kernel的参数。只会在non-recovery配置文件menuentry的linux项中体现。

- GRUB_TERMINAL

  启动模式。

  默认为GUI模式，可以设置为以text mode启动：`GRUB_TERMINAL=console`

尽管提供了很多选项，然而有时候我们希望能够完全自定义menuentry，可以在/etc/grub.d/40_custom自己写menuentry。

配置完成后记得通过以下命令更新最终配置文件：

```
sudo update-grub
```

可以在`/boot/grub/grub.cfg`查看最终生成配置文件，该文件不推荐修改，由update-grub自动生成。

### /boot

目录包含以下文件：

- vmlinuz

  压缩后的kernel。

- initrd.img

  压缩后的initramfs。

- System.map

  kernel自身的符号映射表。一些应用程序利用此来将地址转换为名称。

- config kernel的配置文件。该kernel编译时用的就是该配置文件，只是让你看看kernel包含了什么特性而已，修改它并不能改变什么，除非重新编译。

- grub

  grub目录。包括grub的配置文件、启动时需要加载的一些mod等。

- abi

  应用程序二进制接口，同样是符号表，向应用程序提供驱动和内核接口。 Linux内核通过地址来识别变量或函数，为了用户编程方便，维护符号(变量名/函数名)到它们地址的映射。

这个目录的文件系统为ext2，和`/`是分离开，独立被挂载。

## 参考

《鸟哥的Linux私房菜》

<http://stackoverflow.com/questions/10603104/the-difference-between-initrd-and-initramfs>

<https://en.wikipedia.org/wiki/Master_boot_record>

<https://www.ibm.com/developerworks/cn/linux/l-linuxboot/>

<https://www.ibm.com/developerworks/library/l-grub2/>

<https://wiki.gentoo.org/wiki/GRUB2>

<https://www.kernel.org/doc/Documentation/filesystems/ramfs-rootfs-initramfs.txt>