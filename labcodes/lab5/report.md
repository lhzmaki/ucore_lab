```
练习一：
按照注释给tf中的参数赋值，将cs，ds，es，ss设置为用户态，则可进行内核态到用户态的切换

proc_run函数会载入下一个进程的kstack和cr3，并执行switch_to，保存当前进程的eip，push下一个进程的eip，并用ret跳转到eip执行。而在load_icode中我们已经设置了eip为用户程序入口，所以switch_to返回的即为用户程序入口，又我们设置了cs，ds等为用户态对应值，之后就可以开始执行用户程序了
```

```
练习二：
在进程fork时，为共用相同内存块，可将子进程的cr3设置为和父进程相同，同时为了防止对某共享资源的修改，可将该资源对应的页设置为只读，当需要修改时，就会发生page_fault，在处理该page_fault时判断时候为COW，若是则调用copy_range进行拷贝，并将PTE_W设置为1
```

```
练习三：
do_fork:
产生一个子进程，并将唤醒子进程（即将其状态设为PROC_RUNNABLE）
do_execve：
加载一个可执行程序到进程中，但并未改变进程状态
do_wait：
检查当前进程的子进程，若有子进程，则将当前进程状态设为PROC_SLEEPING，并将子进程中状态为PROC_ZOMBIE的进程释放，若无子进程，则直接结束这个进程释放资源
do_exit：
将当前进程状态改为PROC_ZOMBIE，若当前进程的父进程的wait_state为WT_CHILD，则将父进程唤醒，若此进程含有子进程，则将子进程交由内核线程initproc处理

状态图：
		wakeup_proc                               do_wait
PROC_UNINIT_------------------------->                ----------------------> 
					PROC_RUNNABLE <---------------------   PROC_SLEEPING                         
						|		do_exit		|			
						|				|
						|do_exit			|
						|				|
						|		do_exit		|		
					PROC_ZOMBIE <---------------------------|		
                            
```
