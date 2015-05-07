```
练习一：
sched_class中共有5个函数指针：
    void (*init)(struct run_queue *rq);
    // put the proc into runqueue, and this function must be called with rq_lock
    void (*enqueue)(struct run_queue *rq, struct proc_struct *proc);
    // get the proc out runqueue, and this function must be called with rq_lock
    void (*dequeue)(struct run_queue *rq, struct proc_struct *proc);
    // choose the next runnable task
    struct proc_struct *(*pick_next)(struct run_queue *rq);
    // dealer of the time-tick
    void (*proc_tick)(struct run_queue *rq, struct proc_struct *proc);
init为初始化进程队列
enqueue可以将一个进程proc插入进程队列rq中
dequeue可以将进程proc从队列中删除
pick_next可以从队列中选择下一个可执行的进程
proc_tick可以为进程计时，从而知晓进程的时间片是否用完

RR调度算法：
enqueue将进程插入队列，并给time_slice赋值，在proc_tick中将time_slice-1,直至time_slice=0,此时将该进程的need_resched置1，进入run_list队列的尾部。然后pick_next选择下一个进程。

多级反馈队列调度算法：
没一级队列即为一个链表，将这些链表的头部按照优先级组成一个新的链表，每一次从高优先级到低优先级查看每一级对应的链表中时候有进程，有就执行，在进程用完固定的时间片后如果还没有结束，就将进程放置到下一级队列的尾部。
```

```
练习二：
将BIG_STRIDE 设置为0x7fffffff，这样stride的差一定在BIG_STRIDE的范围内
init：初始化队列，将lab6_run_pool设置为0
enqueue：将proc的lab6_run_pool插入队列，并检查进程的time_slice
dequeue：将进程移出队列
pick_next：直接从头部取出stride值最小的进程，并更新lab6_stride的值，当priority不为0时，stride加上BIG_STRIDE / priority，否则直接加BIG_STRIDE
proc_tick：为进程计时，从而知晓进程的时间片是否用完
```
