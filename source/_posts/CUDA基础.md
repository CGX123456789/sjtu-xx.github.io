---
title: CUDA基础
toc: true
mathjax: true
date: 2020-09-06 17:09:47
tags:
categories:
- 高性能计算
---

# 并行编程
<!--more-->
## 并行编程模型
- 共享存储模型
- 线程模型
- 消息传递模型
- 数据并行模型

具体实例：OpenMP、MPI、Single Program Multiple Data（SPMD）、MPMD

## 设计并行处理程序和系统
- 自动和手动并行
- 理解问题和程序
- 分块分割
- 通讯
- 同步
- 数据依赖

> 指令流水线：取指，译码，执行，访存，写回。

## 名词
FLOPS:floating-point operations per second
GPU: graphic processing unit

GPU是异构、众核、处理器，针对吞吐优化。

高效GPU任务具备的条件：
- 具有成千上万的独立工作
  - 尽量利用大量的ALU单元
  - 大量的片元切换掩藏延迟
- 可以共享指令流
  - 适用于SIMD处理
- 最好是计算密集的任务
  - 通信和计算开销比例合适
  - 不要受制于访存带宽

CPU-GPU交互
- GPU、CPU拥有各自的内存空间。
- 通过PCIE总线互连（8～16GHz）。
- 交互开销大。

## CUDA编程概念
Thread，block，grid是CUDA编程上的概念，为了方便程序员软件设计，组织线程。

- thread：一个CUDA的并行程序会被以许多个threads来执行。
- block：数个threads会被群组成一个block，同一个block中的threads可以同步，也可以通过shared memory通信。
- grid：多个blocks则会再构成grid。

软硬件对应关系：
thread: thread processor
block: SM（streaming multiprocessor） 共享内存shared memory
每个设备拥有global memory

# CUDA编程
## CUDA术语
Host主机端：通常指CPU，采用ANSI标准C语言编程

Device设备端：通常指GPU，采用扩ANSI标准扩展的C语言编程

Host和Device拥有各自的存储器。

CUDA编程包括Host和Device编码，

kernel：数据并行处理函数。

grid: 一维或多维线程块

block：多个线程（一二三维）。
  - 一个grid里面每个block中的线程数相同
  - block内部的每个线程可以：同步，访问共享存储器

每个block对应在一个SM上

device代码段可以：
- 读写每个线程的寄存器
- 读写每个线程的local memory
- 读写每个block的shared memory
- 读写每个grid的global memory
- 读每个grid的constant memory

host代码段可以：
- 读写每个grid的global memory和constant memory

> global memory和constant memory都在GPU芯片中

cudaMalloc() 在设备端分配global memory
cudaFree() 在设备端释放内存

cuda内存传输：host和device的内存传输
- `cudaMemcpy(Md,M,size,cudaMemcpyHostToDevice)`
- `cudaMemcpy(P,Pd,size,cudaMemcpyDeviceToHost)`

## cuda 矩阵乘法
```c
__global__ void MatrixMulkernel(float* Md,float* Nd,float* Pd,int Width){
    int tx= threadIdx.x;
    int ty= threadIdy.y;

    float Pvalue = 0;
    for (int k = 0;k<width;++k){
        float Mdelement = Md[ty * Md.width +k];
        float Ndelement = Nd[k * Nd.width + tx];
        Pvalue += Mdelement * Ndelement;
    }

    Pd[ty * width + tx] = Pvalue;
}




void MatrixMulOnDevice(float* M, float* N, float* P, int width){
    int size = width * width *sizeof(float);

    // load M and N to device memory
    cudaMalloc(Md,size);
    cudaMemcpy(Md,M,size,cudaMemcpyHostToDevice);
    cudaMalloc(Nd,size);
    cudaMemcpy(Nd,N,size,cudaMemcpyHostToDevice);

    cudaMalloc(Pd,size);

    // 
    dim3 dimBlock(width,width); // 1个block含width*width个线程
    dim3 dimGrid(1,1);

    MatrixMulKernel<<<dimGrid,dimBlock>>>(Md,Nd,Pd);

    // read p from device
    cudaMemcpy(P,Pd,size,cudaMemcpyDeviceToHost);
    cudaFree(Md);
    cudaFree(Nd);
    cudaFree(Pd);
}
```

## cuda函数声明
|声明|执行|调用|
|----|----|----|
|`__global__ void KernelFunc()`|设备端|主机端|
|`__device__ float DeviceFunc()`|设备端|设备端|
|`__host__ float HostFunc()`|主机端|主机端|

`__global__`返回类型必须是void

> global和device函数
> 
> 少用递归、不要用静态变量、少用malloc、小心通过指针实现函数调用

向量数据类型
- 通过`.x,.y,.z,.w`进行访问
- 部分函数列表

常用的数学函数：
intrinsic function:`__`开头，损失一定精度，但速度大幅提高

线程同步：
`__syncthreads();` 要求线程的执行时间尽量接近。只在一个块内部进行同步。

在if-else中使用同步可能死锁：不同的目的地。

Warp：一个block内部的一组线程

Warp中的线程切换是0开销的。

### 内存模型
local memory实际上存放在global memory当中。

shared memory可全速读写。

|变量声明|存储器|作用域|生命期|
|--------|------|------|------|
|单独的自动变量而不是数组|寄存器|thread|kernel|
|自动变量数组|local|thread|kernel|
|`__shared__ int sharedVar'`|shared|block|kernel|
|`__device__ int globalVar`|global|grid|application|
|`__constant__ int constantVar;`|constant|grid|application|


global和constant变量
- host可以通过以下函数访问：
  - `cudaGetSymbolAddress()`
  - `cudaGetSymbolSize()`
  - `cudaMemcpyToSymbol()`
  - `cudaMemcpyFromSymbol()`
- constants变量必须在函数外声明

原子操作：
`atomicAdd`

## CUDA程序优化
有效的数据并行算法+针对GPU架构特性的优化 = 优化的算法

### 并行规约
warp分割

Block被分割为32为单位的线程组，叫做warp。

warp是最基本的调度单元，warp一直执行相同的指令。

warp分割原则：threadIdx是连续增加的一组。

### 访存合并

### SM资源动态分割

### 存储优化
- 减少传输
  - 中间数据直接在GPU分配，操作，释放
  - 有时更适合在GPU上进行重复计算
  - 如果没有减少数据传输的话，将CPU代码移植到GPU可能无法提升性能。
- 组团传输
  - 大块传输好于小块。
- 内存传输与计算时间重叠。
  - 双缓存

避免每个线程访问连续的空间。相邻线程访问相邻的空间。

bank冲突：同一个周期对shared memory中同一个bank进行访问。

shared memory和registers一样快，如果没有bank冲突的话。

CUDA中的Texture内存。

优点：
- 数据被缓存
  - 特别适用于无法合并访存的场合
- 支持过滤
- Wrap模式
- 一二三维寻址

`# of blocks > # of SM`尽可能保证每个SM至少有一个work group在执行

块大小必须是32的倍数。最小64，通常采用128or256。依赖于问题本身，需要实验确定。

隐藏延时：需要有足够多的线程来掩藏延时。

循环展开：
自动实现`#pragma unroll BLOCK_SIZE`
