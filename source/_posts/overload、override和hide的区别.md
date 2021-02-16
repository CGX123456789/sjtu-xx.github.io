---
title: overload、override和hide的区别
toc: true
mathjax: true
date: 2020-07-24 20:44:53
tags:
categories:
- C++
- 基础
---


# 重载(overload)、覆盖(override)、隐藏(hide)的区别 
<!--more-->
这三个概念都是与OO中的多态有关系的。如果单是区别重载与覆盖这两个概念是比较容易的，但是隐藏这一概念却使问题变得有点复杂了，下面说说它们的区别吧。

 重载是指不同的函数使用相同的函数名，但是函数的参数个数或类型不同。调用的时候根据函数的参数来区别不同的函数。

 覆盖（也叫重写）是指在派生类中重新对基类中的虚函数（注意是虚函数）重新实现。即函数名和参数都一样，只是函数的实现体不一样。

 隐藏是指派生类中的函数把基类中相同名字的函数屏蔽掉了。隐藏与另外两个概念表面上看来很像，很难区分，其实他们的关键区别就是在多态的实现上。什么叫多态？简单地说就是一个接口，多种实现吧。

 还是引用一下别人的代码来说明问题吧（引用自林锐的《高质量C/C++编程指南》）。

仔细看下面的代码： 

```javascript
 1 #include <iostream.h>  
 2 
 3 class Base  
 4 
 5 {  
 6 
 7 public:  
 8 
 9     virtual void f(float x){ cout << "Base::f(float) " << x << endl; }  
10 
11     void g(float x){ cout << "Base::g(float) " << x << endl; } 
12 
13     void h(float x){ cout << "Base::h(float) " << x << endl; }  
14 
15 };  
 1 class Derived : public Base 
 2 
 3 {  
 4 
 5 public:  
 6 
 7     virtual void f(float x){ cout << "Derived::f(float) " << x << endl; }  
 8 
 9     void g(int x){ cout << "Derived::g(int) " << x << endl; } 
10 
11     void h(float x){ cout << "Derived::h(float) " << x << endl; } 
12 
13 };  
```

看出什么了吗？下面说明一下：

（1）函数Derived::f(float)覆盖了Base::f(float)。 

（2）函数Derived::g(int)隐藏了Base::g(float)，而不是重载。 

（3）函数Derived::h(float)隐藏了Base::h(float)，而不是覆盖。  

 嗯，概念大概明白了，但是在实际的编程中，我们会因此遇到什么问题呢？再看下面的代码：

```javascript
 1 void main(void)  
 2 
 3 {  
 4     Derived  d;  
 5     Base *pb = &d;  
 6     Derived *pd = &d; 
 7 
 8     // Good : behavior depends solely on type of the object  
 9     pb->f(3.14f); // Derived::f(float) 3.14  
10     pd->f(3.14f); // Derived::f(float) 3.14  
11 
12     // Bad : behavior depends on type of the pointer  
13     pb->g(3.14f); // Base::g(float) 3.14  
14     pd->g(3.14f); // Derived::g(int) 3        (surprise!)  
15 
16     // Bad : behavior depends on type of the pointer  
17     pb->h(3.14f); // Base::h(float) 3.14      (surprise!)  
18     pd->h(3.14f); // Derived::h(float) 3.14  
19 } 
```

在第一种调用中，函数的行为取决于指针所指向的对象。在第二第三种调用中，函数的行为取决于指针的类型。所以说，隐藏破坏了面向对象编程中多态这一特性，会使得OOP人员产生混乱。 

不过隐藏也并不是一无是处，它可以帮助编程人员在编译时期找出一些错误的调用。但我觉得还是应该尽量使用隐藏这一些特性，该加virtual时就加吧。

**成员函数被重载的特征** （1）相同的范围（在同一个类中）； （2）函数名字相同； （3）参数不同； （4）virtual 关键字可有可无。 **覆盖是指派生类函数覆盖基类函数，特征是** （1）不同的范围（分别位于派生类与基类）； （2）函数名字相同； （3）参数相同； （4）基类函数必须有virtual 关键字。 **“隐藏”是指派生类的函数屏蔽了与其同名的基类函数，规则如下** （1）如果派生类的函数与基类的函数同名，但是参数不同。此时，不论有无virtual关键字，基类的函数将被隐藏（注意别与重载混淆）。 （2）如果派生类的函数与基类的函数同名，并且参数也相同，但是基类函数没有virtual 关键字。此时，基类的函数被隐藏（注意别与覆盖混淆） 

3种情况怎么执行：

1。重载：看参数

2。隐藏：用什么就调用什么

3。覆盖：调用派生类
