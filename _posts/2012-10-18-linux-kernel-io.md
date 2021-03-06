---
layout: post
title: Linux内核的设计与实现:块I/O层
---

### 块设备

### 扇区:

扇区是块设备中最小的可寻址的单元,大小一般是2的整数倍.扇区的大小是设备的物理属性,扇区是所有块设备的基本单元--块设备无法对比它还小的单元进行寻址和操作.

### 块:

块是自己的最小逻辑可寻址单元.块是文件系统的一种抽象--只能及愉快来范围文件系统.虽然物理磁盘寻址是按照扇区级进行的,但是内核执行的所有的磁盘操作都是按照块进行的.由于扇区是设备的最小可寻址单元,所有块不能比扇区还小,只能数倍于扇区大小.





# I/O调度程序





## I/O调度程序





 IO调度程序将磁盘I/O资源分配给系统中所有挂起的块I/O请求.具体的说这种资源分配是通过将请求队列中挂起的请求合并和排序来完成的.





 与进程调度的区别





 进程调度程序的作用是将处理器资源分配给系统中的运行进程.





 进程调度程序和I/O调度程序都是将一个资源虚拟给多个对象,对进程调度来说,处理器被虚拟并被系统中的运行进程共享.这种虚拟提供给用户的就是多任务和分时操作系统,像Unix系统.相反,I/O调度程序虚拟块设备给多个磁盘请求,以便降低磁盘寻道的时间, 确保磁盘性能的最优化





## I/O调度程序的工作





  IO调度程序的工作是管理块设备的请求队列.它觉得队列中的请求排列顺序以及在什么时候派发请求到块设备.这样做有利于减少磁盘寻址时间,从而提高全局吞吐量.





  IO调度两种方法减少寻址时间:合并和排序





  合并:将两个或多个请求结合成一个新请求.





  这种情况,文件系统提交请求到请求队列--从文件中读取一个数据区,如果这时文件中已经存在一个请求,它访问的磁盘扇区和当前请求访问的磁盘扇区相连(比如同一个文件中早些时候被读取的数据区),那么这两个请求可以合并为一个对单个和多个相邻磁盘扇区操作的新请求.通过合并请求,I/O调度程序将多次请求的开销压缩成一次请求的开销,而且只需要传递给磁盘一条寻找命令,就可以访问到合并前多次寻址才能访问完的磁盘区域.(寻址是I/O里面最费时的操作)





  排序:I/O调度程序是的整个请求队列按扇区增长方向有序排序.使所有秦秋按磁盘扇区排列的顺序尽可能有序排列.这样不只是缩短一次请求的寻址时间,更重要的,通过保存磁盘头以直线方向移动,缩短了所有请求的磁盘寻址时间.该排序算法类似于电梯调度





## Linux 电梯





  当一个请求加入到队列中是,有可能发生4中操作
   1. 如果队列中已存在一个对相连磁盘扇区操作的请求,那么新请求将和这个已存在的请求合并成一个请求
   2. 如果队列中存在一个驻留时间过长的请求,那么新请求将被插入到队列尾部,已防止其他旧的请求发生饥饿
   3. 如果队列中以扇区方向为序存在合适的插入位置, 那么新的请求将被插入到该位置,保证队列中的请求是以被访问磁盘物理位置为序进行排列
   4. 如果队列中不存在合适的请求插入位置,请求将被插入到队列尾部





## 其他4中I/O调度程序







  1. . 最终期限I/O调度程序


  2. . 预测I/O调度程序


  3. . 完全公正的排队I/O调度程序


  4. . 空操作的I/O调度程序



