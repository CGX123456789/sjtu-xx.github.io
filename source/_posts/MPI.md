---
title: MPI
toc: true
mathjax: true
date: 2020-09-07 20:23:56
tags:
categories:
- 高性能计算
---

MPI（Message-Passing-Interface 消息传递接口）实现并行是进程级别的，通过通信在进程之间进行消息传递。MPI并不是一种新的开发语言，它是一个定义了可以被C、C++和Fortran程序调用的函数库。这些函数库里面主要涉及的是两个进程之间通信的函数。MPI可以在Windows和linux环境中都有相应的库，本篇以Windows10作为演示开发环境。
<!--more-->
https://mpitutorial.com/tutorials/
## 一些概念
MPI：跨语言的通信协议。是一个信息传递应用程序接口，包括协议和语义说明。

OpenMP(Open Multi-Processing)：共享存储并行编程。用于共享内存并行系统的对处理程序设计的一套指导性编译处理方案。(一般单主机)

OpenMPI：一种高性能消息传递库，它是MPI-2标准的一个开源实现，有一些科研机构和企业一起开发和维护。OpenMPI来创建最好的MPI库。易于使用，并运行本身在各种各样的操作系统。


## windows与MPI
Windows为了兼容MPI，自己做了一套基于一般个人电脑的MPI实现。如果要安装正真意义上的MPI的话，请直接去www.mpich.org下载，里面根据对应的系统下载相应的版本。

## 一个简单的MPI程序
```c
#include "mpi.h"  
#include <stdio.h>  

int main(int argc, char* argv[])
{
    int rank, numproces;
    int namelen;
    char processor_name[MPI_MAX_PROCESSOR_NAME];

    MPI_Init(&argc, &argv);
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);//获得进程号
    MPI_Comm_size(MPI_COMM_WORLD, &numproces);//返回通信子的进程数

    MPI_Get_processor_name(processor_name, &namelen);
    fprintf(stderr, "hello world! process %d of %d on %s\n", rank, numproces, processor_name);
    MPI_Finalize();

    return 0;
}
```

## MPI的使用
<b>MPI_Init</b>：告知MPI系统进行所有必要的初始化设置。它是写在启动MPI并行计算的最前面的。具体的语法结构为:
```c
MPI_Init(
     int* argc_p,
     char*** argv_p
    );
```

   参数argc_p和argv_p分别指向main函数中的指针参数，为了弄明白这部分，还得从main函数的参数说起：Ｃ语言规定main函数的参数只能有两个，习惯上这两个参数写为argc和argv。因此，main函数的函数头可写为： main (argc,argv)。Ｃ语言还规定argc(第一个形参)必须是整型变量,argv( 第二个形参)必须是指向字符串的指针数组。其中argc参数表示了命令行中参数的个数(注意：文件名本身也 算一个参数)，argc的值是在输入命令行时由系统按实际参数的个数自动赋予的。例如有命令行为： C:">E6 24 BASIC dbase FORTRAN由于文件名E6 24本身也算一个参数，所以共有4个参数，因此argc取得的值为4。argv参数是字符串指针数组，其各元素值为命令行中各字符串(参数均按字符串处 理)的首地址。

然而在MPI_Init函数中，并不一定都需要设置argc_p和argv_p这两个参数的，不需要的时候，将它们设置为NULL即可。

通讯子（communicator）：MPI_COMM_WORLD表示一组可以互相发送消息的进程集合。

MPI_Comm_rank:用来获取正在调用进程的通信子中的进程号的函数。

MPI_Comm_size:用来得到通信子的进程数的函数。

这两个函数的具体结构如下：

```c
int MPIAPI MPI_Comm_rank(
    __in MPI_Comm comm,
    __out int* rank
    );

int MPIAPI MPI_Comm_size(
    __in MPI_Comm comm,
    __out int* size
    );
```

MPI_Finalize：告知MPI系统MPI已经使用完毕。它总是放到做并行计算的功能块的最后面，在此函数之后就不能再出现任何有关MPI相关的东西了。

以上只是表达了作为一个MPI并行计算的基本结构，并没有真正涉及进程之间的通信，为了更好的进行并行，必然需要进程间的通信，下面介绍两个进程间通信的函数，它们就是MPI_Send和MPI_Recv，分别用于消息的发送和接收。

MPI_Send：阻塞型消息发送。

MPI_Recv：阻塞型消息接收。

MPI_Status：返回消息传递的完成情况。


