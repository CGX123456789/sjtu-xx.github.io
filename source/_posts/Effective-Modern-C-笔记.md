---
title: Effective_Modern_C++笔记
toc: true
date: 2020-01-12 16:55:43
tags: [c++]
categories:
- C++
- 基础
---

# 型别推导
<!--more-->
```c++
auto x1 = 27;  //型别是int，值是27
auto x2(27); //型别是int，值是27
auto x3 = {27}; //型别是std::initializer_list<int>,值是27
auto x4{27}; //同上

//错误
auto x5 ={1,2,3.0};  推导不出std::initializer_list<T>中的T
```
当用于auto声明变量的初始化表达式是用大括号扩起时，推导所得的型别就属于std::initializer_list.

带有auto返回值的函数若要返回一个大括号括起来的初始化表达式是通不过编译的。
用auto来制定c++14中的lambda式的星灿类被是，也不能使用大括号括起来的初始化表达式。

```c++
//C++11 返回值型别尾序语法：
// 尾序返回值的好处在于，在指定返回值型别时，可以使用函数形参。
auto authAndAccess(Container &c, Index i)->decltype(c[i]){return c[i]}
```

```C++
Widget w;
const Widget& cw = w;
auto myWidget1 = cw;  //Widget
decltype(auto) myWidget2 = cw;  //const Widget &
```

# 转向现代c++
- 优先选用nullptr，而非0或NULL
- 优先选用using别名声明，而非typedef
- 优先选用限定作用域的枚举类别
    - 限定作用域的枚举类别可以进行前置声明
    `enum class Color; //正确`
    `enum Color; //错误`
    - 默认型别
    `enum class Status: std::uint32_t; `
- 优先选用删除函数，而非private未定义函数
- 为意在改写的函数添加override声明
- 优先选用const_iterator,而非iterator
- 只要函数不会发射异常，就为其加上noexcept声明
- 只要有可能使用constexpr，就使用他，

# 智能指针
- 使用unique_ptr管理具备专属所有权的资源
- 使用shared_ptr管理具备共享所有权的资源
- 对于类似shared_ptr但有可能空悬的指针使用weak_ptr
- 优先选用make_unique和make_shared，而非直接使用new

# 右值引用、移动语义和完美转发
**形参总是左值，即使其型别是右值引用**
```C++
void f(Widget&& w);
//w 是左值
```

## std::move 和 std::forward
std::move无条件的将实参强制转换成右值，而std::forward则仅在某个特定条件满足时才执行同一个强制转换。

如果想要取得某个对象执行移动操作的能力，不要将其声明为常量，因为针对常量对象执行的移动操作将一声不响的变换成复制操作；其次，std::move不仅不实际移动任何东西，甚至不保证经过其强制型别转换后的对象具备可移动能力。std::move的结果唯一可以确定的是，该结果是个右值。

std::forward是有条件的强制型别转换，仅当其实参是使用右值完成初始化时，他才会执行向右值型别的强制型别转换。

## 区分万能引用和右值引用
如果看到了`T&&`，却没有其涉及的型别推导，则其为右值引用
```C++
void f(Widget&& param); //右值引用
Widget&& var1 = Widget(); //右值引用
template<typename T>
void f(std::vector<T>&& param);  //右值引用

auto&& var2 = var1;  //万能引用
template<typername T>
void f(T&& param); //万能引用
```
万能引用的初始化物会决定他代表的是一个左值还是右值引用：如果初始化物是右值，万能应用就会对应到一个右值引用；如果初始化物是一个左值，则万能引用就会对应到一个右值引用

## 针对右值引用实施std::move,针对万能指针实施std::forward
## 避免依万能引用型别进行重载
把重载和万能引用这两者结合起来几乎总是一个馊主意：一旦万能引用成为重载候选，他就会吸引走大批的实参型别，远比撰写重载代码的程序员期望的要多。

## 假定移动操作不存在，成本高，未使用
std::array的复制和移动有相同的时间复杂度

# lambda表达式
c++14标准中，捕获成员变量的一种较好的方法是使用广义lambda捕获：
```C++
void Widget::addFilter() const
{
	filter.emplace_back(
	[divisor = divisor](int value){return value % divisor == 0;}) //将divisor复制入闭包，使用副本
}
```

## 优先使用lambda表达式而不是std::bind	
