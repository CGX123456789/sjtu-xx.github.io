---
title: enum枚举量声明、定义、使用与强类型枚举详解
toc: true
mathjax: true
date: 2020-07-22 16:26:35
tags:
categories:
- C++
- 基础
---

c++中的enum类型
<!--more-->
# 枚举量的声明
## 枚举类型定义
`enum enumType {Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday};`

这句话声明了一个enumType类型，声明Monday等为符号常量，默认值为0-6.
## 变量声明
`enumType Weekday`

也可以在定义枚举类型时定义枚举变量

`enum enumType {Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday}Weekday;
`

# 赋值
在不进行强制转换的前提下，只能将定义的枚举量赋值给该种枚举的变量。如：`Weekday=Sunday`

# 自定义枚举量的值
`enum enumType {Monday=1, Tuesday=2, Wednesday=3, Thursday=4, Friday=5, Saturday=6, Sunday=7};`

只定义一部分枚举量的值`enum enumType {Monday=1, Tuesday, Wednesday=1, Thursday, Friday, Saturday, Sunday};`，这样Monday、Wednesday 均被定义为 1，则 Tuesday=2，Thursday、Friday、Saturday、Sunday 的值默认分别为 2、3、4、5。

# 枚举的取值范围
枚举的上限是 大于最大枚举量的 最小的 2 的幂，减去 1。

枚举的下限有两种情况：一、枚举量的最小值不小于 0，则枚举下限取 0；二、枚举量的最小值小于 0，则枚举下限是小于最小枚举量的最大的 2 的幂，加上 1。

如`enum enumType1 { First=-5，Second=14，Third=10 };`枚举量的上限是16-1=15,枚举的下限是-8+1=-7（-8小于最小枚举量-5，且为2的幂）；

# 枚举应用
```c++
enum enumType{Step0, Step1, Step2}Step=Step0; // 注意这里在声明枚举的时候直接定义了枚举变量 Step,并初始化为 Step0

switch (Step)x
{
  case Step0:{...;break;}
  case Step1:{...;break;}
  case Step2:{...;break;}
  default:break;
}
```
另外枚举还有一种少见的用法是` enum { one ,two ,three}; `就是不指定一个名字,这样我们自然也没法去定义一些枚举类型了。此时就相当于 `static const int one = 0; `这样定义三个常量一样。然后用的话就是 `int no = one`.

# 强类型枚举
传统C++中枚举常量被暴漏在外层作用域中，这样若是同一作用域下有两个不同的枚举类型，但含有相同的枚举常量也是不可的（如`enum Side{Right,Left};enum Thing{Wrong,Right};`是不可的）。

强类型枚举使用`enum class`声明。
```c++
enum class Enumeration{
    VAL1,
    VAL2,
    VAL3=100,
    VAL4
};
```
这样，枚举类型时安全的，枚举值也不会被隐式转换为整数，无法和整数数值比较，比如（Enumeration：：VAL4==10会触发编译错误）。

另外枚举类型所使用的类型默认为int类型，也可指定其他类型，比如：
`enum class Enum:unsigned int{VAL1,VAL2};`

**强类型枚举能解决传统枚举不同枚举类下同枚举值名的问题，使用枚举类型的枚举名时，必须指明所属范围，比如：Enum::VAL1，而单独的VAL1则不再具有意义。**


