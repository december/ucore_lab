### 练习一
---

1、给未被映射的地址映射上物理页

按照注释的提示，给do_pgfault函数补充if 1的情况。首先根据虚拟地址寻找二级页表，如果不存在则创建；然后找页表项对应的物理页，如果不存在则分配一个并将页表项指向它；最后将数据装入对应的页，并设置好相应参数即可。

2、请描述页目录项（Pag Director Entry）和页表（Page Table Entry）中组成部分对ucore实现页替换算法的潜在用处。

页目录项和页表项中的一些位用于保存页的访问和修改信息，使用clock算法实现页替换算法时，需要记录内存中每个页的访问与修改情况，这种情况下就要用到这些位。当某一页要被替换出去时，需要检查该页是否被写过，若被写过则需要将其内容在外存中更新，这时也会用到这些位。

3、如果ucore的缺页服务例程在执行过程中访问内存，出现了页访问异常，请问硬件要做哪些事情？

需要保存当前访问进程的现场，然后调用缺页中断处理服务，将控制权交给操作系统，根据中断号查找IDT，采取相应的策略处理，最后返回缺页服务例程处理缺页异常。

---
### 练习二
---

1、补充完成基于FIFO的页面替换算法

按照注释中的提示，在do_pgfault函数中补充页面替换的相应处理，首先根据出现异常的地址寻找对应页表项及其对应的物理地址，找不到则报告fail，否则将其从外存中换进来，并设置对应参数、建立映射。在_fifo_map_swappable函数中，由于pra_page_link采用双向链表结构，只需加一句list_add_after(head,entry)将新换进内存的页加到head之后即可。在_fifo_swap_out_victim函数中，找到链表最前面的项，并将替换出去的页指针的内容指向它。

2、如果要在ucore上实现"extended clock页替换算法"请给你的设计方案，现有的swap_manager框架是否足以支持在ucore中实现此算法？如果是，请给你的设计方案。如果不是，请给出你的新的扩展和基此扩展的设计方案。并需要回答如下问题：需要被换出的页的特征是什么？在ucore中如何判断具有这样特征的页？何时进行换入和换出操作？

由于页表项已经提供了记录页的访问和修改信息的位，基本能够支持实现"extended clock页替换算法"的要求。只需要修改swapout，将替换方式改为循环遍历链表，沿途按照11到10、10到00、01到00的方式修改修改位和访问位，并将遇到的第一个00的页替换掉即可。需要被换出的页的特征是修改位和访问位均为0，在ucore中可借助页表项的PTE_A和PTE_D位来判断，找到第一个00的页时进行换入和换出操作。

---
### Others
---

1、与标准答案的区别

本次实验主要调用底层函数来实现虚拟内存管理相关功能，代码量很少，因此和标准答案差别也不大。主要是我在练习1中仍然按照if 1的形式实现，在练习2中没有为了严谨而加入许多assert语句。

2、涉及的重要知识点

虚拟内存管理、页缺失的异常处理、页置换算法等。

3、未在实验中体现的知识点

其它页置换算法，如LRU、工作集置换、缺页率等。



















