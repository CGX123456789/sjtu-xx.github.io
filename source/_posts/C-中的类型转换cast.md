---
title: C++中的类型转换cast
toc: true
mathjax: true
date: 2020-07-22 14:48:42
tags:
categories:
- C++
- 基础
---

### static_cast
<!--more-->
static_cast用于基本上想要逆转隐式转换的情况，但有一些限制和添加。static_cast不执行运行时检查。如果您知道您引用的是特定类型的对象，则应该使用此方法，因此检查将是不必要的。例子:
```c++
func(void *data) {
    MyClass* -> void*的转换是隐式的
    MyClass* c = static_cast<MyClass*>(data);
    …
}
int main () {
    MyClass c;
    start_thread(&func， &c) //将调用func(&c)
    . join ();
}
```
在本例中，您知道您传递了一个MyClass对象，因此不需要运行时检查来确保这一点。

### dynamic_cast
当你不知道对象的动态类型时，dynamic_cast很有用。如果引用的对象不包含被转换为基类的类型，则返回空指针(在这种情况下，当转换为引用时，会抛出bad_cast异常)。
```C++
if (JumpStm *j = dynamic_cast<JumpStm*>(&stm)) {
…
} else if (ExprStm *e = dynamic_cast<ExprStm*>(&stm)) {
…
}
```
如果向下转换(转换到派生类)且参数类型不是多态的，则不能使用dynamic_cast。例如，以下代码是无效的，因为Base不包含任何虚函数:
```C++
struct Base {};
struct Derived:Base {};
int main () {
	Derived d;
	Base *b = &d;
	dynamic_cast <Derived* > (b);/ /无效

}
```
对于static_cast和dynamic_cast，“up-cast”(转换到基类)始终是有效的，而且不需要任何转换，因为“up-cast”是隐式转换。

### 常规cast
这些转换也称为c型转换。C风格的转换基本上等同于尝试一系列c++转换，并进行第一次有效的c++转换，而不考虑dynamic_cast。不用说，它结合了所有的const_cast、static_cast和reinterpret_cast，因此功能更加强大，但是它也不安全，因为它没有使用dynamic_cast。

另外，c样式的强制转换不仅允许您这样做，而且还允许您安全地强制转换到一个私有基类，而“等效的”static_cast序列会为此给出一个编译时错误。

有些人喜欢c风格的类型转换，因为它们很简洁。我只将它们用于数字类型转换，并在涉及用户定义的类型时使用适当的c++类型转换，因为它们提供了更严格的检查。
