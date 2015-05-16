```
练习一：
内核级信号量的设计：
typedef struct {
    int value;
    wait_queue_t wait_queue;
} semaphore_t;
包含了一个value和一个等待队列
执行流程：
sem_init：初始化了value值和等待队列
up：关闭中断，如果等待队列为空，则直接给value加1，否则如果wait_state相同则调用wakeup_wait唤醒等待队列中队首的进程，最后开启中断
down：关闭中断，如果value大于0，说明没有互斥，否则将当前进程加入等待队列，调用schedule进行调度

用户态进程/线程提供信号量机制的设计方案:
添加相应的系统调用函数使其可以调用与内核信号量相同的一些内核功能，比如关闭和使能中断
异同：
执行流程和相关功能相同，但相比较而言，内核级信号量由于不需要系统调用，更为简洁快捷
```

```
练习二：
内核级条件变量的设计描述：
设计了两个结构如下：
typedef struct condvar{
    semaphore_t sem;        // the sem semaphore  is used to down the waiting proc, and the signaling proc should up the waiting proc
    int count;              // the number of waiters on condvar
    monitor_t * owner;      // the owner(monitor) of this condvar
} condvar_t;

typedef struct monitor{
    semaphore_t mutex;      // the mutex lock for going into the routines in monitor, should be initialized to 1
    semaphore_t next;       // the next semaphore is used to down the signaling proc itself, and the other OR wakeuped waiting proc should wake up the sleeped signaling proc.
    int next_count;         // the number of of sleeped signaling proc
    condvar_t *cv;          // the condvars in monitor
} monitor_t;

monitor_init：初始化了monitor中的相关变量
cond_signal：如果条件变量中计数大于0，说明有进程在等待，此时调用up唤醒在条件变量上等待的进程，将next计数加1，并调用down等待在next信号量上。若从down返回说明再次被唤醒，此时将next计数减1，便可返回。若没有进程咋等待，直接返回。
cond_wait：将条件变量中计数加1。如果next计数大于0，说明有进程因为signal而等待，调用up唤醒一个等待在next条件变量上的这种进程。否则调用up放弃mutex互斥锁，然后调用down等待在条件变量上。若从down返回，说明该进程再次被唤醒，将条件变量计数加1，即可返回

哲学家问题：
phi_take_forks_condvar：将自己的状态设为HUNGRY，然后调用phi_test_condvar(查看时候能得到叉子，如果不能，则等待在自己的条件变量上直到被别人唤醒。
phi_put_forks_condvar：将自己的状态设为THINKING，然后调用phi_test_condvar查看左右邻居是否得到叉子


用户态进程/线程提供条件变量机制的设计方案：
基于练习一中的用户态信号量，按照内核态条件变量的设计方法实现即可
```
