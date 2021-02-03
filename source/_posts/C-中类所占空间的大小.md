---
title: C++中类所占空间的大小
toc: true
mathjax: true
date: 2020-07-20 22:21:20
tags: [C++]
categories:
- C++
- 基础
---

struct,class
<!--more-->
```C++
#include <stdio.h>
#include <iostream>
using namespace std;
struct Empyt{
    
};
struct S{
    static long c; // static 变量不占用S的存储空间，S为空，那么S占用一个字节
};
int main()
{
    char a;
    cout<<sizeof(a)<<endl;
    
    char *b = &a;
    cout<<"*:"<<sizeof(b)<<endl;
    
    int c;
    cout<<"int:"<<sizeof(c)<<endl;
 
    long d;
    cout<<"long:" <<sizeof(d)<<endl;
    
    long long f;
    cout<<"long long:" << sizeof(f) <<endl;
    
    double m;
    cout<<"double:" << sizeof(m) <<endl;
 
    cout<<"Empyt: "<<sizeof(Empyt)<<endl;
    
    cout<<"S: "<<sizeof(S)<<endl;
}
```

在64位系统中的结果如下,static 的变量占用全局的内存. 和全局变量分配的内存在同一个区域里面.所以sizeof（s）的时候没有将其计算在内。
```
Char: 1

*: 8

int: 4

long: 8

long long: 8

double: 8

Empyt: 1

S: 1
```

## 字节对齐
字节对齐的细节和编译器实现相关，但一般而言，满足三个准则：

1) 结构体变量的首地址能够被其最宽基本类型成员的大小所整除；
2) 结构体每个成员相对于结构体首地址的偏移量（offset）都是成员大小的整数倍，如有需要编译器会在成员之间加上填充字节；
3) 结构体的总大小为结构体最宽基本类型成员大小的整数倍，如有需要编译器会在最末一个成员之后加上填充字节；

## 字节对齐实际计算方法
看了以后还是不是很理解，也很正常，我们自己试试差不多就能理解了，通俗点讲，就是类（结构体）占用的大小永远都是其内定义的最宽（就是所占字节最大的变量）字节的整数倍，如果不是，那么编译器会自动帮你补上，如K，最宽的类型是int，占四个字节，加上一个char 一个字节 =5，不能被4整除哇，所以编译器将其自动补齐为8。我们再改一改K：

struct K{

    int a;

    char b;

    char c;

};

这时候K 占几个字节？ 没错，答案还是8，看到这里是不是觉得自己应该掌握了， 哈哈，还没有，我们再看下面，我们把K当中的变量顺序换一换：

struct K{

    char b;

    int a;

    char c;

};

是不是感觉没什么变化，我们只把int a 放在了 b 的下面。答案还是8吗？看我都这么说了，结果肯定不是啦，为先说下答案，是12，知道为什么了吗？看到答案为想大家也应该明白了，类当中的空间存储是自上而下的，我还是通俗点讲，先发现char b，字节大小为1，然后往下走碰到了 a 四个字节，这时候对b 进行自动补齐，字节大小变为八，也就是b的偏移量变成了3.继续走又给c分配四个字节，最后结果就是12.那之前为什么是8呢，很简单呀因为 前面的b，c 共同占用四个字节（他们是连在一起的）

所以现在懂了吗，出个小题目：

struct K{

    int a;

    long f;

    char c;

    int b;

    char e;

    char g;

    char h;

    char i;

    char j;

};

现在k占几个字节？


·

·

答案是32
————————————————
原文链接：https://blog.csdn.net/monster_acm/java/article/details/81169289
