---
title: new vs malloc
tags: C++
---

前段时间面试的时候，面试官问到了 new 与 malloc 的区别，以及 new 跟 malloc 在内存不足的情况下的处理。
没能答上来，有点尴尬，下面是事后查阅了资料，得到的一些结论。

## new/delete

分配与释放内存

1. new/delete 是 C++ 的运算符
2. 从 "free store" 中分配内存
3. 返回一个完全类型指针，如 `int *`
4. 在分配失败的情况下，与 malloc 不同，new 不会返回空指针 `NULL`，而是抛出 bad_alloc 类型的异常
5. new 运算符分配内存后，会调用相应类型的构造函数，从而进行相应的初始化，delete 调用析构函数然后释放内存
6. new 申请内存时，不需要指定内存大小，具体大小由编译器计算得出
7. 对数组的支持不同，new[] 可以直接申请数组，如 `new int[5]()`
8. 可以重写 `operator new` / `operator delete`

## malloc/free

分配与释放内存

1. malloc/free 是 C/C++ 的标准库函数
2. 从 "heap" 中分配内存
3. 返回 `void *`，需要自行转换指针类型
4. 在分配失败的情况下，会返回空指针 `NULL`
5. malloc 和 free 都不会调用构造函数或者是析构函数，只分配或者释放内存
6. malloc 申请内存时，需要人为的指定申请内存的大小
7. 申请数组时，需要手动的控制数组长度
8. malloc 与 free 不能够合法的重写

## malloc() 的更详细介绍

malloc() 其实是 C 库里用于动态分配内存的库函数，并不是系统调用。

malloc() 在向系统申请内存时，有两种方式向操作系统申请堆内存：

- 通过 brk() 系统调用从堆分配内存。
- 通过 mmap() 系统调用在文件映射区分配内存。

一般来说，在用户申请的内存小于 128 KB时，使用 brk() 来申请内存；
当申请的内存大于 128 KB 时，则通过 mmap() 申请内存。

下面通过一些代码来查看内存的分配情况

```cpp
// 申请 1 空间的内存
void *addr = malloc(1);
printf("curr address: %p\n", addr);
printf("curr pid: %d\n", getpid());
getchar();
// 释放刚才申请的内存
printf("call free\n");
free(addr);
getchar();
```

通过 cat /proc/`pid`/maps 得到类似于下面的输出

```sh
# 通过 malloc(1)，调用 brk() 申请的堆空间
55a75adca000-55a75adeb000 rw-p 00000000 00:00 0                          [heap]
# 调用 free 后的堆空间
55a75adca000-55a75adeb000 rw-p 00000000 00:00 0                          [heap]
```

通过上面的例子可以发现，在申请内存小于 128 KB 时，malloc 通过 brk 申请堆空间，
并且不是只申请了用户需要的空间，而是预分配了 132 KB的内存。
在 free 之后，并不是一下子就将申请的堆空间归还给操作系统，
而是将释放的内存放回 malloc 的内存池当中，
当下次申请时，就可以直接从内存池中取，就不需要经过系统调用了。

```cpp
// 申请 130 KB 空间的内存
void *addr = malloc(130 * 1024);
printf("curr address: %p\n", addr);
printf("curr pid: %d\n", getpid());
getchar();
// 释放刚才申请的内存
printf("call free\n");
free(addr);
getchar();
```

```sh
# 通过 malloc(130 KB)，调用 mmap() 以匿名映射的方式从文件映射区分配的匿名内存
7f8c3bda3000-7f8c3bdc8000 rw-p 00000000 00:00 0
# free 后
7f8c3bdc4000-7f8c3bdc8000 rw-p 00000000 00:00 0
```

可以发现，在申请 130KB 空间时，右边没有了 `[heap]` 的字样，
在调用 free 之后，会将申请来的内存归还给操作系统。
下次申请 130 KB 内存空间时，仍然需要通过系统调用。

此外，brk 因为是从堆空间分配的内存，并在释放的时候不会归还给 os，所以堆内会出现内存的碎片；
mmap 申请来空间之后，释放时会归还给操作系统，这就导致下次申请时需要通过系统调用的方式申请内存，
这就导致 mmap 每次分配的虚拟地址都是缺页的状态，在访问时会导致缺页中断，所以相对的会消耗更多的 CPU。

值得一提的是，malloc 申请的内存是虚拟内存，只有在真正的使用这一段虚拟内存时，操作系统才会分配相应的物理内存。
通过 malloc() 申请了一段内存之后，使用这段虚拟内存时出现了缺页，产生缺页中断，然后内核会对缺页中断进行处理，
在物理内存足够的情况下，内核为进程分配相应的物理内存；
在物理内存不足的情况下，看是否支持 swap 页面，不支持就直接 oom，kill 掉，
支持的话，会将一些用的少的内存放入磁盘中（注意写回），以腾出一定量的内存，如果仍然不够进程需要的内存，oom，kill。

## 参考

<https://stackoverflow.com/questions/240212/what-is-the-difference-between-new-delete-and-malloc-free>
<https://xiaolincoding.com/os/3_memory/malloc.html#malloc-%E6%98%AF%E5%A6%82%E4%BD%95%E5%88%86%E9%85%8D%E5%86%85%E5%AD%98%E7%9A%84>
<https://xiaolincoding.com/os/3_memory/alloc_mem.html#swap-%E6%9C%BA%E5%88%B6%E7%9A%84%E4%BD%9C%E7%94%A8>
