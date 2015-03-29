### 练习一
---

1、操作系统镜像文件ucore.img是如何一步步生成的？(需要比较详细地解释Makefile中每一条相关命令和参数的含义，以及说明导致结果)
答：用make -n命令将make的过程打印出来，可以看到ucore.img在生成中经历的主要步骤。将整个过程粘贴在下面，并分段解释如下：
echo + cc kern/init/init.c

gcc -Ikern/init/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/init/init.c -o obj/kern/init/init.o
echo + cc kern/libs/readline.c
gcc -Ikern/libs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/libs/readline.c -o obj/kern/libs/readline.o
echo + cc kern/libs/stdio.c
gcc -Ikern/libs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/libs/stdio.c -o obj/kern/libs/stdio.o
echo + cc kern/debug/kdebug.c
gcc -Ikern/debug/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/debug/kdebug.c -o obj/kern/debug/kdebug.o
echo + cc kern/debug/kmonitor.c
gcc -Ikern/debug/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/debug/kmonitor.c -o obj/kern/debug/kmonitor.o
echo + cc kern/debug/panic.c
gcc -Ikern/debug/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/debug/panic.c -o obj/kern/debug/panic.o
echo + cc kern/driver/clock.c
gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/clock.c -o obj/kern/driver/clock.o
echo + cc kern/driver/console.c
gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/console.c -o obj/kern/driver/console.o
echo + cc kern/driver/intr.c
gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/intr.c -o obj/kern/driver/intr.o
echo + cc kern/driver/picirq.c
gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/picirq.c -o obj/kern/driver/picirq.o
echo + cc kern/trap/trap.c
gcc -Ikern/trap/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/trap/trap.c -o obj/kern/trap/trap.o
echo + cc kern/trap/trapentry.S
gcc -Ikern/trap/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/trap/trapentry.S -o obj/kern/trap/trapentry.o
echo + cc kern/trap/vectors.S
gcc -Ikern/trap/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/trap/vectors.S -o obj/kern/trap/vectors.o
echo + cc kern/mm/pmm.c
gcc -Ikern/mm/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/mm/pmm.c -o obj/kern/mm/pmm.o
echo + cc libs/printfmt.c
gcc -Ilibs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/  -c libs/printfmt.c -o obj/libs/printfmt.o
echo + cc libs/string.c
gcc -Ilibs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/  -c libs/string.c -o obj/libs/string.o
//这一部分是在编译kern文件夹中后缀名为.o、.c、.S的文件，供后面进行链接。其中，-fno-builtin命令禁用gcc的built，-fno-stack-protector命令取消使用多余的代码来检查栈溢出，-Wall命令打开所有警告，-ggdb命令添加公共gdb调试信息，-m32命令表示产生32位代码，-c指定源文件名，-o指定目标文件名，-nostdinc表示不检查系统默认的目录以获取头文件。

mkdir -p bin
//在当前目录创建bin文件夹

echo + ld bin/kernel
ld -m    elf_i386 -nostdlib -T tools/kernel.ld -o bin/kernel  obj/kern/init/init.o obj/kern/libs/readline.o obj/kern/libs/stdio.o obj/kern/debug/kdebug.o obj/kern/debug/kmonitor.o obj/kern/debug/panic.o obj/kern/driver/clock.o obj/kern/driver/console.o obj/kern/driver/intr.o obj/kern/driver/picirq.o obj/kern/trap/trap.o obj/kern/trap/trapentry.o obj/kern/trap/vectors.o obj/kern/mm/pmm.o  obj/libs/printfmt.o obj/libs/string.o
//连接obj/kern中所有的.o文件

objdump -S bin/kernel > obj/kernel.asm
objdump -t bin/kernel | sed '1,/SYMBOL TABLE/d; s/ .* / /; /^$/d' > obj/kernel.sym
//使用objdump进行反汇编，生成kernel.asm和kernel.sym

echo + cc boot/bootasm.S
gcc -Iboot/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Os -nostdinc -c boot/bootasm.S -o obj/boot/bootasm.o
echo + cc boot/bootmain.c
gcc -Iboot/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Os -nostdinc -c boot/bootmain.c -o obj/boot/bootmain.o
//链接编译生成bootasm.o和bootmain.o文件

echo + cc tools/sign.c
gcc -Itools/ -g -Wall -O2 -c tools/sign.c -o obj/sign/tools/sign.o
gcc -g -Wall -O2 obj/sign/tools/sign.o -o bin/sign
//链接编译生成bin/sigh文件

echo + ld bin/bootblock
ld -m    elf_i386 -nostdlib -N -e start -Ttext 0x7C00 obj/boot/bootasm.o obj/boot/bootmain.o -o obj/bootblock.o
//链接obj中的.o文件，生成目标为bin/bootblock.o文件

objdump -S obj/bootblock.o > obj/bootblock.asm
//反汇编bootblock.o生成bootblock.asm文件

objcopy -S -O binary obj/bootblock.o obj/bootblock.out
//使用objcopy命令由bootblock.o生成bootblock.out文件

bin/sign obj/bootblock.out bin/bootblock
dd if=/dev/zero of=bin/ucore.img count=10000
dd if=bin/bootblock of=bin/ucore.img conv=notrunc
dd if=bin/kernel of=bin/ucore.img seek=1 conv=notrunc
wulingling@ubuntu:~/ucore_lab/labcodes/lab1$ 

2、一个系统被认为是符合规范的硬盘主引导扇区的特征是什么？
大小为512字节，最后两个字节分别为0x55 0xAA。前446个字节为MBR，后跟64字节DPT，记录硬盘分区和分区属性。

---
### 练习二
---

为了熟悉使用qemu和gdb进行的调试工作，我们进行如下的小练习：

1、从CPU加电后执行的第一条指令开始，单步跟踪BIOS的执行。
2、在初始化位置0x7c00设置实地址断点,测试断点正常。
3、从0x7c00开始跟踪代码运行,将单步跟踪反汇编得到的代码与bootasm.S和 bootblock.asm进行比较。
4、自己找一个bootloader或内核中的代码位置，设置断点并进行测试。


1、在gdbinit中写入define hook-stop x/i $pc end 设置反汇编。删除gdbinit最后的continue，这样qemu启动后会停在加电第一条指令。执行make debug,会自动编译ucore.img，并启动qemu和gdb。qemu加电后在第一条指令暂停。在gdb命令行中执行ni,可单步并显示汇编代码。

2、输入b *0x7c00设置断点。再输入c继续执行。
屏幕显示=> 0x7C00: cli，说明已到断点处并暂停，断点正常。

3、单步执行，反汇编结果如下：
(gdb) x/20i $pc
=> 0x7c00:	cli    
   0x7c01:	cld    
   0x7c02:	xor    %eax,%eax
   0x7c04:	mov    %eax,%ds
   0x7c06:	mov    %eax,%es
   0x7c08:	mov    %eax,%ss
   0x7c0a:	in     $0x64,%al
   0x7c0c:	test   $0x2,%al
   0x7c0e:	jne    0x7c0a
   0x7c10:	mov    $0xd1,%al
   0x7c12:	out    %al,$0x64
   0x7c14:	in     $0x64,%al
   0x7c16:	test   $0x2,%al
   0x7c18:	jne    0x7c14
   0x7c1a:	mov    $0xdf,%al
   0x7c1c:	out    %al,$0x60
   0x7c1e:	mov    $0xdf,%al
   0x7c20:	out    %al,$0x60
   0x7c22:	lgdtl  (%esi)
   0x7c25:	jo     0x7ca3
代码和bootasm.S、bootblock.asm是相同的，只是代码格式上略有差别。
4、在obj文件夹中找到bootmain对应的物理地址为0x7d11，设置断点进行测试，具体结果显示如下：
(gdb) b *0x7d11
Breakpoint 1 at 0x7d11
(gdb) continue
Continuing.

Breakpoint 1, 0x00007d11 in ?? ()
(gdb) 

qemu中显示此时运行到的汇编代码：
(qemu) x/10i $pc
0x00007d11:  push   %ebp
0x00007d12:  xor    %ecx,%ecx
0x00007d14:  mov    %esp,%ebp
0x00007d16:  mov    $0x1000,%edx
0x00007d1b:  push   %esi
0x00007d1c:  mov    $0x10000,%eax
0x00007d21:  push   %ebx
0x00007d22:  call   0x7c78
0x00007d27:  cmpl   $0x464c457f,0x10000
0x00007d31:  jne    0x7d73


---
### 练习三
---

1、分析bootloader进入保护模式的过程
a.开机后，BIOS将硬盘第一个扇区中的代码载入到物理地址0x7c00的位置，开始运行代码。
b.清理环境，cli关中断，清除方向标志，设置段寄存器，将flag和段寄存器置0。
c.开启A20 Gate，使得全部地址线可用。
d.载入全局描述符表gdtdesc，将CR0的PE位置1，开启保护模式。
f.用ljmp进行长跳转，将段选择子和代码偏移载入cs、eip。
g.将数据段选择子载入各个段寄存器。在调用C函数之前，设置栈寄存器和帧寄存器，建立堆栈。
h.已进入保护模式，调用bootmain加载elf。若

---
### 练习四		
---

1、分析bootloader加载ELF格式的OS的过程。
a.读取ELF的头部，通过储存在头部的幻数判断ELF文件是否合法。
b.按照ELF文件头的描述表，调用readseg函数，将从1扇区开始的ELF文件载入内存的对应位置。
c.根据ELF头部储存的入口信息，找到内核的入口。

---
### 练习五
---

1、实现函数调用堆栈跟踪函数。
先用read_ebp()、read_eip()两个函数获取当前ebp、eip的位置。在ebp=0且计数器没有达到STACKFRAME_DEPTH之前，输出当前的ebp与eip。根据栈帧规则，uint32_t* args=((uint32_t*)ebp + 2)是第一个参数的首地址。按照这种方法，弹栈获取上级栈帧，返回地址[ebp+4]作为新的eip，ebp则更新为[ebp]，循环直到达到STACKFRAME_DEPTH，即可输出当前堆栈中的所有栈帧。

程序运行结果如下：
ebp:0x00007b28 eip:0x001009c6 args:0x00010094 0x00010094 0x00007b58 0x00100094
    kern/debug/kdebug.c:306: print_stackframe+22
ebp:0x00007b38 eip:0x00100cab args:0x00000000 0x00000000 0x00000000 0x00007ba8
    kern/debug/kmonitor.c:125: mon_backtrace+10
ebp:0x00007b58 eip:0x00100094 args:0x00000000 0x00007b80 0xffff0000 0x00007b84
    kern/init/init.c:48: grade_backtrace2+33
ebp:0x00007b78 eip:0x001000bd args:0x00000000 0xffff0000 0x00007ba4 0x00000029
    kern/init/init.c:53: grade_backtrace1+38
ebp:0x00007b98 eip:0x001000db args:0x00000000 0x00100000 0xffff0000 0x0000001d
    kern/init/init.c:58: grade_backtrace0+23
ebp:0x00007bb8 eip:0x00100100 args:0x0010365c 0x00103640 0x00001328 0x00000000
    kern/init/init.c:63: grade_backtrace+34
ebp:0x00007be8 eip:0x00100057 args:0x00000000 0x00000000 0x00000000 0x00007c53
    kern/init/init.c:28: kern_init+86
ebp:0x00007bf8 eip:0x00007d66 args:0xc031fcfa 0xc08ed88e 0x64e4d08e 0xfa7502a8
    <unknow>: -- 0x00007d65 --
ebp:0x00000000 eip:0x00007c53 args:0xf000e2c3 0xf000ff53 0xf000ff53 0xf000ff53
    <unknow>: -- 0x00007c52 --
ebp:0xf000ff53 eip:0xf000ff53 args:0x00000000 0x00000000 0x00000000 0x00000000
    <unknow>: -- 0xf000ff52 --
ebp:0x00000000 eip:0x00000000 args:0xf000e2c3 0xf000ff53 0xf000ff53 0xf000ff53
    <unknow>: -- 0xffffffff --
ebp:0xf000ff53 eip:0xf000ff53 args:0x00000000 0x00000000 0x00000000 0x00000000
    <unknow>: -- 0xf000ff52 --
ebp:0x00000000 eip:0x00000000 args:0xf000e2c3 0xf000ff53 0xf000ff53 0xf000ff53
    <unknow>: -- 0xffffffff --
ebp:0xf000ff53 eip:0xf000ff53 args:0x00000000 0x00000000 0x00000000 0x00000000
    <unknow>: -- 0xf000ff52 --
ebp:0x00000000 eip:0x00000000 args:0xf000e2c3 0xf000ff53 0xf000ff53 0xf000ff53
    <unknow>: -- 0xffffffff --
ebp:0xf000ff53 eip:0xf000ff53 args:0x00000000 0x00000000 0x00000000 0x00000000
    <unknow>: -- 0xf000ff52 --
ebp:0x00000000 eip:0x00000000 args:0xf000e2c3 0xf000ff53 0xf000ff53 0xf000ff53
    <unknow>: -- 0xffffffff --
ebp:0xf000ff53 eip:0xf000ff53 args:0x00000000 0x00000000 0x00000000 0x00000000
    <unknow>: -- 0xf000ff52 --
ebp:0x00000000 eip:0x00000000 args:0xf000e2c3 0xf000ff53 0xf000ff53 0xf000ff53
    <unknow>: -- 0xffffffff --
ebp:0xf000ff53 eip:0xf000ff53 args:0x00000000 0x00000000 0x00000000 0x00000000
    <unknow>: -- 0xf000ff52 --

---
### 练习六
---

1、中断向量表一个项占多少字节？其哪几位代表中断处理码的入口？
答：中断向量表一个项共8个字节，其中入口包括16－31位的段选择子、48-63位的段内偏移高16位，0－15位的段内偏移低16位。

2、请编程完善kern/trap/trap.c中对中断向量表进行初始化的函数idt_init。
首先声明引用变量，然后通过调用mmu.h中的SETGATE函数进行初始化。在这里参照了lab1ppt中的代码，首先是将idt向量中的所有项都关联到__vectors的对应项上，设置特权级，对于T_SYSCALL再进行单独处理。最后调用x86.h中的lidt函数嵌入lidt汇编代码。

3、请编程完善trap.c中的中断处理函数trap，在对时钟中断进行处理的部分填写trap函数。
首先要对时钟中断进行计数，使用全局变量ticks，初始为0。当trap发生时，若tr_trapno等于IRQ_OFFSET+IRQ_TIMER,则是时钟中断，计数器加1。若计数器模TICK_NUM为0，则调用print_ticks()输出。

---
### Challenge
---
独立进行尝试实现用户态与内核态



















