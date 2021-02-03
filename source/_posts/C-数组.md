---
title: C++数组
toc: true
mathjax: true
date: 2020-07-24 17:09:18
tags:
categories:
- C++
- 基础
---

c++中的数组真的是个神奇的存在,数组仅在定义其的域范围内可确定大小.
<!--more-->
本文需要解决C++中关于数组的2个问题：
\1. 数组作为函数参数，传值还是传址？
\2. 函数参数中的数组元素个数能否确定？


先看下面的代码。

 

```cpp
#include <iostream>
using namespace std;

void testArrayArg(int a[])
{
  cout << endl;
  cout << "in func..." << endl;
  cout << "array address: " << a << endl;
  cout << "array size: " << sizeof(a) << endl;
  cout << "array element count: " << sizeof(a) / sizeof(a[0]) << endl;
  cout << "changing the 4th element's value to 10." << endl;
  a[3] = 10;
}

int main(){
  int a[] = {1, 2, 3, 4, 5};
  cout << "in main..." << endl;
  cout << "array address: " << a << endl;
  cout << "array size: " << sizeof(a) << endl;
  cout << "array element count: " << sizeof(a) / sizeof(a[0]) << endl;
  testArrayArg(a);
  cout << endl << "the 4th element's value: " << a[3] << endl;
  return 0;
}
```



 

运行结果如下：

 

in main...
array address: 0012FF4C
array size: 20
array element count: 5

 

in func...
array address: 0012FF4C
array size: 4
array element count: 1
changing the 4th element's value to 10.

 

the 4th element's value: 10

 

当我们直接将数组a作为参数调用testArrayArg()时，实参与形参的地址均是0012FF4C。并且，在testArrayArg()中将a[3]的值修改为10后，返回main()函数中，a[3]的值也已经改变。这些都说明**C++中数组作为函数参数是传址**。

 

特别需要注意的是，在main()中，数组的大小是可以确定的。

 

array size: 20
array element count: 5

 

但作为函数参数传递后，其大小信息丢失，只剩下数组中第一个元素的信息。

 

array size: 4
array element count: 1

 

这是因为C++实际上是将数组作为指针来传递，而该指针指向数组的第一个元素。至于后面数组在哪里结束，C++的函数传递机制并不负责。

 

上面的特性可总结为，**数组仅在定义其的域范围内可确定大小**。

 

因此，如果在接受数组参数的函数中访问数组的各个元素，需在定义数组的域范围将数组大小作为另一辅助参数传递。则有另一函数定义如下：

```cpp
void testArrayArg2(int a[], int arrayLength)
{
  cout << endl << "the last element in array is: " << a[arrayLength - 1] << endl;
}
```

可在main()中这样调用：

 

testArrayArg2(a, sizeof(a) / sizeof(a[0]));

 

这样，testArrayArg2()中便可安全地访问数组元素了。

# 数组指针
```
int main() {
    int t[10]{1, 2, 3, 4, 5, 5};
    int *p=t;
    p++;
    // 下面输出的都是t[1]的地址
    std::cout << t+1<<std::endl;
    std::cout << p<<std::endl;
    std::cout << &t[0]+1<<std::endl;
}
```
不能执行t++

