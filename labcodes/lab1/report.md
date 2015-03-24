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
