```
练习一：
1.
采用gcc –o –c 编译现有的.c文件生成一系列.o文件，采用ld指令将这些目标文件转化成一个执行程序bootblock.out，
最后用dd指令生成一个虚拟空间，输出到ucore.img

2.
从sign.c中的代码
buf[510] = 0x55;
buf[511] = 0xAA;
可知，引导扇区要以0x55AA结尾
```

```
练习二：
在tools的gdbinit文件中，添加b *0x7c00即在0x7c00处设置了一个断点
运行make gdb，发现其确实停在了0x7c00处，输入x /2i $pc，输出为：
0x7c00 <start>:  cli
0x7c01 <start+1>:  cld
参考bootasm.s文件发现确实是这两个指令
```

```
练习三：
汇编代码：
movl  %cr0  %eax
orl  $CR0_EN_ON  %eax
movl  %eax  %cr0
其中CR0_EN_ON为1，所以该代码将cr0寄存器的值设置为1，即开启了保护模式
```

```
练习四：
readsect函数实现硬盘扇区的读取，其内部使用内联汇编实现outb，采用IO寻址方式将外设的数据读入到内存。
Bootmain函数实现了ELF格式的判断和读取：
首先通过ELFHDR的e_magic成员判断该文件是否为ELF格式的
当其为ELF格式时，从其头部的描述ELF文件应存到内存中什么位置的描述表的头地址（ELFHDR+ELFHDR->e_phoff）开始,
使用readseg函数依次将其中内容加载到内存，最后根据ELFHDR->e_entry找到内核的入口

```

```
练习五：
按照已有的注释写代码，注意ebp的判断，当ebp为0时，说明堆栈已超过0x7c00
所以加一个判断条件:
if (ebp == 0)
	break;
最后一行输出如下：
ebp : 0x00007bf8, eip : 0x00007d68, args: 0xc031fcfa0xc08ed88e0x64e4d08e0xfa7502a8
    <unknow>: -- 0x00007d67 --
此时到达了栈底，其对应第一个函数bootmain，此时的ebp应为bootmain的ebp
```

```
练习六：
1. 
根据gatedesc定义可以知道每一个表項为64位即8字节
其中1-2字节为和7-8字节为段偏移：unsigned gd_off_15_0 : 16; unsigned gd_off_31_16 : 16;
3-4字节为段选择：unsigned gd_ss : 16; 
5-6字节则定义了该中断的类型等内容

2. 
在调用SETGATE函数时，参数的设置需要看memalyout.h文件中的定义，找到其中的GD_KTEXT和DPL_KERNEL作为其段选择子和优先级

3. 
见代码
```
