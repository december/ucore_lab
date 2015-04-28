### 练习一
---

1、分配并初始化一个进程控制块

只需要在alloc_proc函数中把proc_struct结构的所有成员变量进行初始化，初始化的值按照实验指导书上的确定。state设置为PROC_UNINIT，pid设置为-1，其余数值设置为0，指针设置为NULL。

2、请说明proc_struct中struct context context和struct trapframe *tf成员变量含义和在本实验中的作用是啥？

context保存当前进程的上下文，从proc.h中context结构的成员变量即可看出，该结构通过保存eip、esp、ebx、ecx、edx、esi、edi、ebp等寄存器的值来保存当前进程的运行状态。当需要进程切换时，通过恢复该结构中保存的寄存器的值来切换到新进程。

trapframe保存发生中断时进程的上下文，即进程中断时对应的中断帧。当需要创建进程时，设置好相应信息后将运行环境切换到trapframe中，再切换到kernel_thread_entry中完成创建。

---
### 练习二
---

1、为新创建的内核线程分配资源

按照注释中的提示，首先调用alloc_proc获得一块空间，然后为进程分配栈空间，复制父进程的栈数据和上下文到子进程，将子进程添加到进程链表，将状态设置为唤醒状态，最后返回进程号。

2、请说明ucore是否做到给每个新fork的线程一个唯一的id？请说明你的分析和理由。 

是，通过get_pid()函数为每个进程分配一个唯一的pid。当新进程创建需要分配id时，get_pid()函数首先将上一个分配出去的id加1得到新id，若该id超过max限制则置为1。然后遍历所有线程组成的链表，寻找next_safe（比上个分配出去的id大的最小id）并将其与新id比较，若next_safe大于新id，则说明新id未被占用可以分配，直接分配之；否则将新id加1，重复上述过程，直到找到一个合法的id。因此，这样分配出去的所有id都是唯一的。

---
### 练习三
---

1、阅读代码，理解 proc_run 函数和它调用的函数如何完成进程切换的

分析如下：

```
if (proc != current) {
    bool intr_flag;
    struct proc_struct *prev = current, *next = proc;
    local_intr_save(intr_flag);                        //关闭中断
    {
        current = proc;                                //设置proc为当前线程
        load_esp0(next->kstack + KSTACKSIZE);          //载入新的esp，完成栈切换
        lcr3(next->cr3);                               //载入新的CR3，完成页表基址切换
        switch_to(&(prev->context), &(next->context)); //完成上下文切换
    }
    local_intr_restore(intr_flag);                     //打开中断
}
```

2、在本实验的执行过程中，创建且运行了几个内核线程？。

两个，分别是idleproc和init_main。

3、语句local_intr_save(intr_flag);....local_intr_restore(intr_flag);在这里有何作用?请说明理由

分别是起到关闭中断和打开中断的作用，能够保护这两条命令中间的语句正常执行不被打断，确保栈、页表基址、上下文切换正确无误，不出异常。

---
### Others
---

1、与标准答案的区别

本次实验的两个代码任务的实质分别是初始化一个结构和按注释提示为线程分配资源，我的代码与标准答案最大的不同是在练习2的代码中没有使用local_intr_save(intr_flag);....local_intr_restore(intr_flag)（开中断和关中断），虽然在本次实验中没有影响，但显然不使用开关中断命令是不安全的，语句执行有被打断的可能。

2、涉及的重要知识点

线程、进程、状态切换、资源分配、fork等。

3、未在实验中体现的知识点

挂起。



















