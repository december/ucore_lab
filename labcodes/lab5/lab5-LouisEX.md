### 练习一
---

1、加载应用程序并执行

按照实验中的提示设置tf的成员变量，其中cs为用户代码段设为USER_CS，ds、es、ss为用户数据段设为USER_DS，esp为栈顶，eip为程序入口，eflags为中断使能标志。

2、描述当创建一个用户态进程并加载了应用程序后，CPU是如何让这个应用程序最终在用户态执行起来的。即这个用户态进程被ucore选择占用CPU执行（RUNNING态）到具体执行应用程序第一条指令的整个经过。

当一个用户态进程进入RUNNING态后，首先加载新线程的段和CR3，然后调用switch_to函数，保存上下文，将存储相应信息设置为用户态的tf作为参数调用fortret函数，将栈空间切换到新线程的内核栈，加载存储在寄存器中的参数。当所有中断和系统调用返回后，开始执行第一条指令。

---
### 练习二
---

1、父进程复制自己的内存空间给子进程

按照注释中的提示，首先利用KADDR找到src、dst地址所对应内容，再利用memset完成复制，最后用page_insert来修改页表的对应映射关系，即可复制父进程的内存空间到子进程。

2、简要说明如何设计实现”Copy on Write 机制“，给出概要设计，鼓励给出详细设计。

COW机制要求在生成新进程时，新进程与原进程会共享同一内存区；只有当其中一进程进行写操作时，系统才会为其另外分配内存页面并完成其余不变部分的复制。在ucore中，复制mm_struct的工作在dup_mmap中完成。若要实现COW机制，需修改dup_mmap，将复制mm_struct改为直接传vma的指针，相当于使两个mm_struct共享原来的内存空间，并将该vma设为只读。 当出现page_fault的时候，如果发现错误原因是在尝试写一个只读页，则说明这里有共用这块空间的进程尝试进行了写操作，这时再新建vma并复制原来的mm_struct，并将原来vma的只读取消，即实现了COW机制。

---
### 练习三
---

1、请分析fork/exec/wait/exit在实现中是如何影响进程的执行状态的？

fork：创建一个新的进程，父进程复制自己的内存空间给子进程，并将子进程状态设为RUNNABLE。

exec：将新的程序复制到内核空间并执行（即进入RUNNING态），如果加载失败则调用do_exit退出。

wait：回收进入僵尸状态的或已终止的子进程的资源，若子进程正在运行，则通过schedule进入SLEEPING状态，SLEEPING时间结束或被唤醒后次再尝试回收。

exit：回收自身进程的大部分资源并进入僵尸状态，然后查看父进程，如果在SLEEPING状态则唤醒。遍历自己的子进程，将其父进程设置为initproc，如果子进程为僵尸状态且initproc为SLEEPING状态则唤醒initproc。 


2、请给出ucore中一个用户态进程的执行状态生命周期图（包执行状态，执行状态之间的变换关系，以及产生变换的事件或函数调用）。（字符方式画即可）

```
        fork                wait
UNINIT-------->RUNNABLE------------->SLEEPING
                |      <-------------
        exit    |          wake_up
                |
                v
              ZOMBIE
```

---
### Others
---

1、与标准答案的区别

在练习2复制内存空间时没有通过void*来复制空间而是直接用的uint32，此外在修改lab1和lab4的部分代码时具体实现有一些区别。

2、涉及的重要知识点

进程状态变迁、fork、进程的选择与加载。

3、未在实验中体现的知识点

挂起及相关的变迁与处理。


















