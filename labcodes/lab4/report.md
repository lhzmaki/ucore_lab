```
练习一：
在do_fork中调用了wakeup去唤醒子进程，所以进程的初始state为PROC_UNINIT
pid，run，kstack，need_resched，mm，context，flags及name都在创建后被重新定义，所以可以将他们都设置成0
cr3：base addr of Page Directroy Table(PDT)，ucore中定义了boot_cr3，所以直接赋值

context为上下文，在proc.h中可以看到其中存了8个寄存器，用于进程切换时保存现场和恢复现场
struct trapframe *tf是一个中断结构，记录了内核的代码段与数据段等参数
```

```
练习二：
按照提示首先初始化子进程，将其parent设为当前进程，然后设置堆栈，拷贝内存，获取pid，将其添加到hash表和进程链表中，最后将其唤醒

做到了pid唯一：
在get_pid函数中从last_pid = 1开始，遍历proc_list，若已有进程的pid与last_pid相同则last_pid+1，若超过MAX_PID，则
为1，重新遍历proc_list，以此保证pid不重复，所以做到了pid唯一
```

```
练习三：
创建运行了两个内核线程，分别为：initproc和idelproc
local_intr_save(intr_flag);....local_intr_restore(intr_flag); 分别用于关闭中断和开启中断，以此保证进程切换的正确进行

```
