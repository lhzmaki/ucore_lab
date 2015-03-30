```
练习一：
default_init_memmap函数中添加对property设置的判断
	p->property = 0;
        if (p == base)
        	p->property = n;
并将每一个page添加到free_list中list_add_before(&free_list, &(p->page_link));

default_alloc_pages函数中当找到可分配的page时，对n个page分别clear property，并将其从free_list中删除
当page的property>n时，第n个page变成了新的表头，所以其property设置为p->property - n;

default_free_pages函数只需将list_add改成list_add_before即可
```

```
练习二：
找到页目录表对应項pgdir + PDX(la) (基址+index)
若不存在，则新建一个Page
得到线性地址 : uintptr_t pa = page2pa(page)
并将目录的表項更新：*pdep = pa | PTE_U | PTE_W | PTE_P;(地址+标记位)
返回二级页表对应表項：ptep + PTX(la)
```

```
练习三：
页表项存在时，得到对应页，将该页的reference - 1，当reference为0时释放该Page
清除二级页表只需将ptep指针清空
```
