---
title: C++中零碎知识点拾遗
toc: true
mathjax: true
date: 2020-07-22 16:59:52
tags:
categories:
- C++
- 基础
---

# STL
STL(标准模板库)，是目前C++内置支持的library。它的底层利用了C++类模板和函数模板的机制，由三大部分组成：容器、算法和迭代器。
<!--more-->

目前STL有六大组件:
- 容器 container
- 算法 algorthm
- 迭代器 iterator
- 仿函数 function object
- 适配器 adaptor
- 空间配置器 allocator

# 多态的实现方式
1. 覆盖

覆盖是指子类重新定义父类的虚函数的做法。

2. 重载

重载是指允许存在多个同名函数，而这些函数的参数表不同（或许参数个数不同，或许参数类型不同，或许两者都不同）。

# 软件设计原则
SOLID原则

|简称|英文名|中文名|
|--------|-------|---------|
|SRP|The Single Responsibility Principle|单一职责原则|
|OCP|The Open Closed Principle|开放封闭原则|
|LSP|The Liskov Substitution Principle|里氏替换原则|
|ISP|The Interface Segregation Principle|接口分离原则|
|DIP|The Dependency Inversion Principle|一来导致原则|

- S:单一责任原则。就一个类而言,应该仅有一个引起它变化的原因,如果你能想到多于一个的动机去改变一个类,那么这个类就具有多于一个的职责.应该把多于的指责分离出去,分别再创建一些类来完成每一个职责。注重的是职责，主要是约束类，其次才是接口和方法，它针对的是程序中的实现和细节。
- O:开闭原则。一个软件实体应当对扩展开放,对修改关闭.说的是,再设计一个模块的时候,应当使这个模块可以在不被修改的前提下被扩展.换言之,应当可以在不必修改源代码的情况下改变这个模块的行为，在保持系统一定稳定性的基础上，对系统进行扩展。这是面向对象设计（OOD）的基石，也是最重要的原则。对新增开放，对修改关闭。主要是用多态性，面向接口面层。
- L:里氏替换原则。 换言之,一个软件实体如果使用的是一个基类的话,那么一定适用于其子类,而且它根本不能察觉出基类对象和子类对象的区别.只有衍生类可以替换基类，软件单位的功能才能不受影响，基类才能真正被复用，而衍生类也能够在基类的基础上增加新功能。反过来的代换不成立。父类可用的情况下，子类也可以使用。也就是说子类条件更严格。
- I:接口分离原则。一个类对另外一个类的依赖是建立在最小的接口上。使用多个专门的接口比使用单一的总接口要好.根据客户需要的不同,而为不同的客户端提供不同的服务是一种应当得到鼓励的做法.就像"看人下菜碟"一样,要看客人是谁,再提供不同档次的饭菜。注重对接口依赖的隔离，主要约束接口接口，主要针对抽象，针对程序整体框架的构建。
- D:依赖倒置原则。抽象不应当依赖于细节,细节应当依赖于抽象。高层模块不应该依赖低层模块，二者都应该依赖其抽象；抽象不应该依赖细节；细节应该依赖抽象，主要是面向接口编程而非面向实现编程。


# 设计模式
- 创建型模式（Creational Patterns）用于构建对象，以便它们可以从实现系统中分离出来。
- 结构型模式（Structural Patterns）用于在许多不同的对象之间形成大型对象结构。
- 行为型模式（Behavioral Patterns）用于管理对象之间的算法、关系和职责。

## 创建型模式

- [单例模式](http://blog.csdn.net/liang19890820/article/details/61615495)（Singleton Pattern）
  保证一个类仅有一个实例，并提供一个访问它的全局访问点。
- [抽象工厂模式](http://blog.csdn.net/liang19890820/article/details/70653800) （Abstract Factory Pattern）
  提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类。
- [建造者模式](http://blog.csdn.net/liang19890820/article/details/66968761)（Builder Pattern）
  将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。
- [工厂方法模式](http://blog.csdn.net/liang19890820/article/details/70652858) （Factory Method Pattern）
  定义一个用于创建对象的接口，让子类决定将哪一个类实例化。Factory Method 使一个类的实例化延迟到其子类。
- [原型模式](http://blog.csdn.net/liang19890820/article/details/66969965)（Prototype Pattern）
  用原型实例指定创建对象的种类，并且通过拷贝这个原型来创建新的对象。

## 结构型模式

- [适配器模式](http://blog.csdn.net/liang19890820/article/details/66973296)（Adapter Pattern）
  将一个类的接口转换成客户希望的另外一个接口。Adapter 模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。
- [桥接模式](http://blog.csdn.net/liang19890820/article/details/79501177)（Bridge Pattern）
  将抽象部分与它的实现部分分离，使它们都可以独立地变化。
- [装饰者模式](http://blog.csdn.net/liang19890820/article/details/66973836)（Decorator Pattern）
  动态地给一个对象添加一些额外的职责。就扩展功能而言，它比生成子类方式更为灵活。
- [组合模式](http://blog.csdn.net/liang19890820/article/details/71240662)（Composite Pattern）
  将对象组合成树形结构以表示“部分-整体”的层次结构。它使得客户对单个对象和复合对象的使用具有一致性。
- [外观模式](http://blog.csdn.net/liang19890820/article/details/70850367)（Facade Pattern）
  为子系统中的一组接口提供一个一致的界面，Facade 模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。
- [享元模式](http://blog.csdn.net/liang19890820/article/details/79629690)（Flyweight Pattern）
  运用共享技术有效地支持大量细粒度的对象。
- [代理模式](http://blog.csdn.net/liang19890820/article/details/78533179)（Proxy Pattern）
  为其他对象提供一个代理以控制对这个对象的访问。

## 行为型模式

- [模版方法模式](http://blog.csdn.net/liang19890820/article/details/79403900) （Template Method Pattern）
  定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。Template Method 使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。
- [命令模式](http://blog.csdn.net/liang19890820/article/details/62060457)（Command Pattern）
  将一个请求封装为一个对象，从而使你可用不同的请求对客户进行参数化；对请求排队或记录请求日志，以及支持可取消的操作。
- 迭代器模式（Iterator Pattern）
  提供一种方法顺序访问一个聚合对象中各个元素，而又不需暴露该对象的内部表示。
- [观察者模式](http://blog.csdn.net/liang19890820/article/details/61925314)（Observer Pattern）
  定义对象间的一种一对多的依赖关系，以便当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并自动刷新。
- [中介者模式](http://blog.csdn.net/liang19890820/article/details/79242388)（Mediator Pattern）
  用一个中介对象来封装一系列的对象交互。中介者使各对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变它们之间的交互。
- [备忘录模式](http://blog.csdn.net/liang19890820/article/details/79292435) （Memento Pattern）
  在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态。这样以后就可将该对象恢复到保存的状态。
- 解释器模式（Interpreter Pattern）
  给定一个语言，定义它的文法的一种表示，并定义一个解释器，该解释器使用该表示来解释语言中的句子。
- [状态模式](http://blog.csdn.net/liang19890820/article/details/78647519)（State Pattern）
  允许一个对象在其内部状态改变时改变它的行为。对象看起来似乎修改了它所属的类。
- [策略模式](http://blog.csdn.net/liang19890820/article/details/79242297)（Strategy Pattern）
  定义一系列的算法，把它们一个个封装起来，并且使它们可相互替换。本模式使得算法的变化可独立于使用它的客户端。
- [职责链模式](http://blog.csdn.net/liang19890820/article/details/79077715) （Chain of Responsibility Pattern）
  为解除请求的发送者和接收者之间耦合，而使多个对象都有机会处理这个请求。将这些对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理它。
- [访问者模式](http://blog.csdn.net/liang19890820/article/details/79364406) （Visitor Pattern）
  表示一个作用于某对象结构中的各元素的操作。它使你可以在不改变各元素的类的前提下定义作用于这些元素的新操作。

# 面向对象与面向过程的区别

面向过程就是分析出解决问题所需要的步骤，然后用函数把这些步骤一步一步实现，使用的时候一个一个依次调用就可以了；

面向对象是把构成问题事务分解成各个对象，建立对象的目的不是为了完成一个步骤，而是为了描叙某个事物在整个解决问题的步骤中的行为。

可以拿生活中的实例来理解面向过程与面向对象，例如五子棋，面向过程的设计思路就是首先分析问题的步骤：1、开始游戏，2、黑子先走，3、绘制画面，4、判断输赢，5、轮到白子，6、绘制画面，7、判断输赢，8、返回步骤2，9、输出最后结果。把上面每个步骤用不同的方法来实现。

如果是面向对象的设计思想来解决问题。面向对象的设计则是从另外的思路来解决问题。整个五子棋可以分为1、黑白双方，这两方的行为是一模一样的，2、棋盘系统，负责绘制画面，3、规则系统，负责判定诸如犯规、输赢等。第一类对象（玩家对象）负责接受用户输入，并告知第二类对象（棋盘对象）棋子布局的变化，棋盘对象接收到了棋子的变化就要负责在屏幕上面显示出这种变化，同时利用第三类对象（规则系统）来对棋局进行判定。

可以明显地看出，面向对象是以功能来划分问题，而不是步骤。同样是绘制棋局，这样的行为在面向过程的设计中分散在了多个步骤中，很可能出现不同的绘制版本，因为通常设计人员会考虑到实际情况进行各种各样的简化。而面向对象的设计中，绘图只可能在棋盘对象中出现，从而保证了绘图的统一。

# 智能指针的用法
- 使用std::unique_ptr时，你不打算持有到同一对象的多个引用。例如，将其用作指向内存的指针，该内存在进入某些范围时被分配，而在退出范围时被取消分配。指针在一个确定的范围存在。
- 使用std::shared_ptr当你想从多个地方是指你的对象-并且不希望你的对象是去分配，直到所有这些提法本身就没了。
- std::weak_ptr当您确实想从多个位置引用对象时，请使用-对于可以忽略和取消分配的引用（因此，当您尝试取消引用时，它们只会指出对象已消失）。避免循环引用。

weak_ptr是为了配合shared_ptr而引入的一种智能指针，它指向一个由shared_ptr管理的对象而不影响所指对象的生命周期，也就是将一个weak_ptr绑定到一个shared_ptr不会改变shared_ptr的引用计数。不论是否有weak_ptr指向，一旦最后一个指向对象的shared_ptr被销毁，对象就会被释放。

weak_ptr的使用：
```c++
class A
{
public:
    A() : a(3) { cout << "A Constructor..." << endl; }
    ~A() { cout << "A Destructor..." << endl; }

    int a;
};

int main() {
    shared_ptr<A> sp(new A());
    weak_ptr<A> wp(sp);
    //sp.reset();

    if (shared_ptr<A> pa = wp.lock())
    {
        cout << pa->a << endl;
    }
    else
    {
        cout << "wp指向对象为空" << endl;
    }
}
```

```c++
struct Owner {
    std::shared_ptr<Owner> other;
    ~Owner(){
        cout<<"destroyed";
    }
};

int main() {
    std::shared_ptr<Owner> p1 (new Owner());
    std::shared_ptr<Owner> p2 (new Owner());
    cout<<"p1:"<<p1<<" p2:"<<p2<<endl;
    p1->other = p2; // p1 references p2
    p2->other = p1; // p2 references p1
}
```
对于上面的循环引用，在程序结束时，无法正确析构对象。
如果采用weak_ptr则可以析构。

# 深拷贝和浅拷贝
深拷贝指的是将对象的值保存。
浅拷贝副本的某些成员可能引用与原始对象相同的对象。

浅拷贝：
```c++
class X
{
private:
    int i;
    int *pi;
public:
    X()
        : pi(new int)
    { }
    X(const X& copy)   // <-- copy ctor
        : i(copy.i), pi(copy.pi)
    { }
};
```
深拷贝：
```C++
class X
{
private:
    int i;
    int *pi;
public:
    X()
        : pi(new int)
    { }
    X(const X& copy)   // <-- copy ctor
        : i(copy.i), pi(new int(*copy.pi))  // <-- note this line in particular!
    { }
};
```


