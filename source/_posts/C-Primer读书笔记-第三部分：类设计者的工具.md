---
title: C-Primer读书笔记-第三部分：类设计者的工具
toc: true
date: 2020-01-02 23:45:38
tags: [c++]
categories: 
- C++
- 基础
---



# 13.拷贝控制
<!--more-->
## 拷贝、赋值与销毁

五种特殊的成员函数：**拷贝构造函数、拷贝赋值构造函数、移动构造函数、移动赋值构造函数、析构函数**

### 拷贝构造函数
- 拷贝构造函数的第一个参数必须是引用类型。

```c++
class Foo{
    public:
    	Foo(); //默认构造函数
    	Foo(const Foo&);  //拷贝构造函数
}
```

- 拷贝初始化时依靠拷贝构造函数或移动构造函数来完成的。

```C++
string dots(10,'.'); //直接初始化
string s(dots); //直接初始化
string s2 = dots; //拷贝初始化
string null_book = "9-999-999"; //拷贝初始化
string nines = string(100,'9'); //拷贝初始化
```

> 赋值运算符通常应该返回一个指向其左侧运算对象的引用



### 拷贝赋值运算符

```C++
class Foo{
    public:
    	Foo& operator=(const Foo&); //赋值运算符
}
```

如果一个类未定义拷贝赋值运算符，编译器会为他生成一个合成拷贝赋值运算符。

### 析构函数

```c++
class Foo{
    public:
    	~Foo(); //析构函数
}
```

- 析构函数没有返回值，也不接受参数。

- 与普通指针不同，智能指针是类类型，所以具有析构函数。因此，与普通指针不同，智能指针在析构阶段会自动销毁。
- 析构函数体本身并不直接销毁成员。在整个对象销毁过程中，析构函数体是作为成员销毁步骤之外的另一部分而进行的。 

### 三/五原则

三个基本操作：拷贝构造函数、拷贝赋值构造函数、析构函数

### 使用=default

```c++
class Sales_data{
public:
    //拷贝控制成员
    Sales_data() = default;
    Sales_data(const Sales_data&) = default;
    Sales_data& operator=(const Sales_data &);
    ~Sales_data() = default;
    
}
```

当我们在类内使用=default修饰成员的声明时，合成的函数将隐式地声明为内联的。

### 阻止拷贝

可以通过将拷贝构造函数和拷贝复制构造函数定义为删除的函数来阻止拷贝。

```c++
struct NoCopy{
    NoCopy() = default;
    NoCopy(const NoCopy&) = delete; //阻止拷贝
    NoCopy &operator=(const NoCopy&) = delete; //阻止赋值
    ~NoCopy() = default; 
}
```

> 析构函数不能是删除的成员。对于析构函数已删除的成员，不能定义该类型的变量或释放指向该类型动态分配对象的指针。



## 拷贝控制和资源管理

### 行为像值的类

```c++
// value-like implementation of HasPtr
class HasPtr {
	friend void swap(HasPtr&, HasPtr&);
public:
    HasPtr(const std::string &s = std::string()): 
		ps(new std::string(s)), i(0) { }

	// each HasPtr  has its own copy of the string to which ps points
    HasPtr(const HasPtr &p): 
		ps(new std::string(*p.ps)), i(p.i) { }

	HasPtr& operator=(const HasPtr &);

	~HasPtr() { delete ps; }
private:
    std::string *ps;
    int    i;
};

inline
void swap(HasPtr &lhs, HasPtr &rhs)
{
	using std::swap;
	swap(lhs.ps, rhs.ps); // swap the pointers, not the string data
	swap(lhs.i, rhs.i);   // swap the int members
}

using std::string;
HasPtr& HasPtr::operator=(const HasPtr &rhs) 
{
	string *newp = new string(*rhs.ps);  // copy the underlying string
	delete ps;       // free the old memory
	ps = newp;       // copy data from rhs into this object
	i = rhs.i;
	return *this;    // return this object
}

HasPtr f(HasPtr hp)  // HasPtr passed by value, so it is copied
{
	HasPtr ret = hp; // copies the given HasPtr
	// process ret
	return ret;      // ret and hp are destroyed
}

int main()
{ 
	HasPtr h("hi mom!");  // allocates a new copy of "hi mom!"
	f(h);  // copy constructor copies h in the call to f
		   // that copy is destroyed when f exits
}  // h is destroyed on exit, which destroys its allocated memory
```

### 行为像指针的类

```C++
// reference counted version of HasPtr
#include <string>

#include <cstddef>

class HasPtr {
public:
	// constructor allocates a new string and a new counter, 
	// which it sets to 1
    HasPtr(const std::string &s = std::string()): 
	  ps(new std::string(s)), i(0), use(new std::size_t(1)) {}

	// copy constructor copies all three data members 
	// and increments the counter
    HasPtr(const HasPtr &p): 
		ps(p.ps), i(p.i), use(p.use) { ++*use; }

	HasPtr& operator=(const HasPtr&);

	~HasPtr();

private:
    std::string *ps;
    int    i;
	std::size_t *use;  // member to track how many objects share *ps
};

HasPtr::~HasPtr()
{
	if (--*use == 0) {   // if the reference count goes to 0
		delete ps;       // delete the string
		delete use;      // and the counter
	}
}

HasPtr& HasPtr::operator=(const HasPtr &rhs) 
{
	++*rhs.use;  // increment the use count of the right-hand operand
	if (--*use == 0) {   // then decrement this object's counter
		delete ps;       // if no other users 
		delete use;      // free this object's allocated members
	}
	ps = rhs.ps;         // copy data from rhs into this object
	i = rhs.i;
	use = rhs.use; 
	return *this;        // return this object
}

HasPtr f(HasPtr hp) // HasPtr passed by value, so it is copied
{
	HasPtr ret;
	ret = hp;        // assignment copies the given HasPtr
	// proces ret
	return ret;      // ret and hp are destroyed
}

int main()
{ 
	HasPtr h("hi mom!");
	HasPtr h2 = h;  // no new memory is allocated, 
	                // h and h2 share the same underlying string
} 
```



## 交换操作

```c++
//h是Foo中的一个HasPtr对象
void swap(Foo &lhs, Foo &rhs){
    std::swap(lhs.h,rhs.h) //错误，使用了标准库版本的swap
}
```

```c++
//h是Foo中的一个HasPtr对象
void swap(Foo &lhs, Foo &rhs){
    using std::swap;
    swap(lhs.h,rhs.h) //使用了HasPtr版本的swap
}
```

赋值运算符中使用swap

```c++
HasPtr& HasPtr::operator=(HasPtr rhs){
    swap(*this,rhs);
    return *this;
}
```



## 对象移动

> 标准库容器、string和shared_ptr类既支持移动也支持拷贝。IO类和unique_ptr类可以移动但不能拷贝。



> **右值和左值**
>
> 左值指的是可以取地址的变量，记住，左值与右值的根本区别在于能否获取内存地址，而能否赋值不是区分的依据。通常临时量均为右值。
>
> 临时变量（右值）生命周期
> a) 临时对象应该在完整表达式结束时销毁
> b) 常量左值引用会延长临时变量的生命

右值引用：通过&&而不是&获得右值引用。

```c++
int i = 42;
int &r = i;  //正确：r引用i
int &&rr = i; //错误：不能将一个右值引用绑定到一个左值上
int &r2 = i*42;  //错误：i*42是一个右值
const int &r3 = i*42;  //正确：我们可以将一个const的引用绑定到一个右值上
int &&rr2 = i*42;  //正确：将rr2绑定到乘法结果上
```

#### 左值持久，右值短暂

左值有持久的状态，右值要么是字面常量，要么是在表达式求值过程中创建的临时对象。

右值引用：

- 所引用的对象即将被销毁
- 该对象没有其他用户

不能将一个右值引用绑定到一个右值引用类型的变量上：

```c++
int &&rr1 = 42;  //正确：字面值常量是一个右值
int &&rr2 = rr1;  //错误：表达式rr1是左值
int &&rr2 = std::move(rr1);  //正确（move位于头文件utility中）
```



### 移动构造函数和移动赋值运算符

> 不抛出异常的移动构造函数和移动赋值运算符必须标记为noexcept

移动构造函数

```c++
StrVec::StrVec(StrVec &&s) noexcept //移动操作不应抛出任何异常
    :elements(s.elements),first_free(s.first_free){
        s.elements=s.first_free = nullptr;
    }
```

移动赋值运算符

```c++
StrVec &StrVec::operator=(StrVec &&rhs) noexcept{
    //直接检测自赋值
    if (this != &rhs){
        //释放已有的元素
        free();
        //从rhs接管资源
        elements = rhs.elements;
        first_free = rhs.first_free;
        cap = rhs.cap;
        //将rhs置于可析构状态
        rhs.elements = rhs.first_free = rhs.cap = nullptr;
    }
}
```

> 移动右值，拷贝左值，但如果没有移动构造函数，右值也被拷贝。



#### 移动迭代器

一个移动迭代器通过改变给定迭代器的解引用运算符的行为来适配此迭代器。一般来说，一个迭代器的解引用运算符返回一个指向元素的左值。与其他迭代器不同，移动迭代器的解引用运算符生成一个右值引用。

通过调用标准库的make_move_iterator函数将一个普通迭代器转换为一个移动迭代器。函数接受一个迭代器参数，返回一个移动迭代器。

> 不要随意使用移动操作。
>
> 在移动构造函数和移动赋值运算符这些类实现代码之外的地方，只有当你确信需要进行移动操作且移动操作是安全的，才可以使用std::move。

### 右值引用和成员函数

> 区分移动和拷贝重载函数通常有一个版本接受一个const T&，而另一个版本接受T&&。
>
> 一般来说，我们不需要为函数操作定义接受一个const X&&或是一个X&参数的版本。当我们希望从实参窃取数据时，通常传递一个右值引用。为了达到这一目的，实参不能是const的。类似的，拷贝操作不应改变对象。

#### 右值和左值引用成员函数

强制运算对象是左值可以使用引用限定符（`&`或`&&`）。引用限定符&表示本对象（this）是一个左值，&&表示本对象是一个右值。`const &`限定符表示本对象是一个const或左值。

```c++
class Foo{
    public:
    	Foo &operator=(const Foo&) &;  //只能向可修改的左值赋值
};

Foo &Foo::operator=(const Foo &rhs) &{
    return *this
}
```

对于&限定的函数，我们只能将他用于左值，对于&&限定的函数，只能用于右值：

```C++
Foo &retFoo();  //返回一个引用；（左值）
Foo retVal();  //返回一个值；（右值）
Foo i,j;    //i和j是左值
i = j;  //i是左值
retFoo() = j;  //正确：retFoo()返回一个左值
retVal() = j;  //错误：retVal()返回一个右值
i = retVal();  //正确：可以将一个右值作为赋值操作的右侧运算对象
```

const和&限定符可同时使用，const在前。



# 14.重载运算符与类型转换

## 基本概念

- 重载函数名由关键字operator和要定义的运算符号共同组成。
- 对二元运算符来说，左侧运算对象传递给第一个参数，右侧运算对象传递给第二个参数。
- 如果一个运算符函数是成员函数，则他的第一个（左侧）运算对象绑定到隐式的this指针上。
- 只能重载已有的运算符，而无权发明新的运算符

> 通常情况下，不应该重载逗号、取地址、逻辑与和逻辑或运算符。

#### 选择作为成员还是非成员函数

- 赋值（=）、下标（[]）、调用（()）、成员访问箭头(->)运算符必须是成员。
- 复合赋值运算符（+=）一般来说应该是成员，但非必须。
- 改变运算对象的运算符或与给定类型密切相关的运算符，如递增、递减、解引用等，通常应该是成员。
- 具有对称性的运算符可能转换任意一端的运算对象，例如算术、相等性、关系和位运算符等，他们通常应该是普通的非成员函数。

如果我们把运算符定义成成员函数时，他的左侧运算对象必须是运算符所属类别的一个对象：

```C++
string s ="world";
string t = s + "!";  //正确：我们能把一个const char*加到一个string对象中
string u = "hi" + s;  //如果+是string的成员，则产生错误
```



## 输入和输出运算符

```c++
ostream &operator>>(ostream &, const Sales_data &);
```

令输出运算符尽量减少格式化操作可以使用户有权控制输出的细节。

与iostream标准库兼容的输入输出运算符必须是普通的非成员函数，而不能是类的成员函数。

## 算术和关系运算符

- 如果一个类含有判断两个对象是否相等的操作，则他显然应该把函数定义成operator==而非一个普通的命名函数。
- 如果类定义了operator==，则该运算符应该能判断一组给定的对象中是否有重复的数据。

## 下标运算符

- 下标运算符必须是成员函数。
- 如果一个类包含下标运算符，则他通常会定义两个版本：一个返回普通引用，另一个是类的常量成员并返回常量引用。

```C++
class StrVec{
public:
    std::string& operator[](std::size_t n){return  element[n];}
    const std::string& operator[](std::size_t n) const {return  element[n];}
}
```

## 递增和递减运算符

```c++
class StrBlobPtr{
public:
    StrBlobPtr& operator++();  //前置运算符
    StrBlobPtr operator--();  //后置运算符
};
```



## 重载、类型转换与运算符

类型转换函数的一般类型如下：`operator type() const`

> 类型转换函数必须是类的成员函数；他不能声明返回类型，形参列表也必须为空。类型转换函数通常应该是const。

显式的类型转换运算符：

```c++
class SmallInt{
public:
    explicit operator int() const {return val;};
};

SmallInt si = 3;
static_cast<int>(si)+3;
```

如果表达式被用作条件，则编译器会将显示的类型转换自动应用于他。换句话说，当表达式出现在下列位置时，显式的类型转换被隐式的执行：

- if、while、do语句的条件部分
- for语句头的条件表达式
- 逻辑非、或、与（！，||，&&）的运算对象
- 条件运算符（?:）的条件表达式

> 向bool的类型转换通常用在条件部分，因此operator bool一般定义为explicit的。



> 通常情况下，不要为类定义相同的类型转换，也不要在类中定义两个及两个以上转换源或转换目标是算术类型的转换。

- **除了显式地向bool类型转换外，尽量避免定义类型转换函数并尽可能的显示那些“显然正确”的非现实构造函数**

> 在调用重载函数时，如果需要额外的标准类型转化，则该转换的级别只有当所有可行函数都请求同一个用户定义的类型转换时才有用。如果所需的用户定义的类型转换不止一个，则该调用具有二义性。
>
> 表达式中运算符的候选函数集既包含成员函数，也应该包括非成员函数。



# 15.面向对象程序设计

## 定义基类与派生类

```C++
#include <memory>
#include <iostream>
#include <string>
#include <cstddef>

// Item sold at an undiscounted price
// derived classes will define various discount strategies
class Quote {
friend std::istream& operator>>(std::istream&, Quote&);
friend std::ostream& operator<<(std::ostream&, const Quote&);
public:
	Quote(): price(0.0) { }
    Quote(const std::string &book, double sales_price):
                     bookNo(book), price(sales_price) { }

    // virtual destructor needed 
	// if a base pointer pointing to a derived object is deleted
    virtual ~Quote() { } // dynamic binding for the destructor

    std::string isbn() const { return bookNo; }

    // returns the total sales price for the specified number of items
    // derived classes will override and apply different discount algorithms
    virtual double net_price(std::size_t n) const 
               { return n * price; }

	// virtual function to return a dynamically allocated copy of itself

    virtual Quote* clone() const {return new Quote(*this);}
private:
    std::string bookNo; // ISBN number of this item
protected:
    double price;       // normal, undiscounted price
};

// abstract base class to hold the discount rate and quantity
// derived classes will implement pricing strategies using these data
class Disc_quote : public Quote {
public:
    // other members as before
    Disc_quote(): quantity(0), discount(0.0) { }
    Disc_quote(const std::string& book, double price,
              std::size_t qty, double disc):
                 Quote(book, price),
                 quantity(qty), discount(disc) { }

    double net_price(std::size_t) const = 0;

    std::pair<size_t, double> discount_policy() const
        { return std::make_pair(quantity, discount); }
protected:
    std::size_t quantity; // purchase size for the discount to apply
    double discount;      // fractional discount to apply
};
```



- 基类通常应该定义一个虚析构函数。即使该函数不执行任何实际操作也是如此。
- 基类通过在其成员函数的声明语句之前添加关键字virtual使得该函数执行动态绑定。
- 如果基类把一个函数声明成虚函数，则该函数在派生类中隐式地也是虚函数。
- 继承自多个基类的类，基类以逗号分隔。
- 派生类的作用域嵌套在积累的作用域之内。
- 派生类应该遵循基类的接口，并且通过调用基类的构造函数来初始化那些从基类中继承而来的成员。
- 派生类的声明中包含类名，但不包含他的派生列表。
- 防止继承发生的办法，在类名后加一个关键字final。
- 我们可以把基类的指针或引用绑定到派生类的对象上。这意味着，当使用基类的引用（或指针）时，我们并不清楚该引用（或指针）所绑定对象的真是类型。
- 智能指针类也支持派生类向基类的类型转。
- 不存在基类向派生类的隐式转换
- 从基类向派生类的类型转换只对指针或引用类型有效。
- 派生类向基类的类型转换也可能会由于访问受限而变得不可行。



## 虚函数

```C++

#include<iostream>  
using namespace std;  
  
class A  
{  
public:  
    void foo()  
    {  
        printf("1\n");  
    }  
    virtual void fun()  
    {  
        printf("2\n");  
    }  
};  
class B : public A  
{  
public:  
    void foo()  //隐藏：派生类的函数屏蔽了与其同名的基类函数
    {  
        printf("3\n");  
    }  
    void fun()  //多态、覆盖
    {  
        printf("4\n");  
    }  
};  
int main(void)  
{  
    A a;  
    B b;  
    A *p = &a;  
    p->foo();  //输出1
    p->fun();  //输出2
    p = &b;  
    p->foo();  //取决于指针类型，输出1
    p->fun();  //取决于对象类型，输出4，体现了多态
    return 0;  
}
```

纯虚函数是在基类中声明的虚函数，它在基类中没有定义，但要求任何派生类都要定义自己的实现方法。在基类中实现纯虚函数的方法是在函数原型后加“=0” 。

引入纯虚函数的原因：

       （1）为了方便使用多态特性，我们常常需要在基类中定义虚拟函数。
    
       （2）在很多情况下，基类本身生成对象是不合情理的。例如，动物作为一个基类可以派生出老虎、孔雀等子类，但动物本身生成对象明显不合常理。 

包含纯虚函数的类称为抽象类。由于抽象类包含了没有定义的纯虚函数，所以不能定义抽象类的对象。抽象类的主要作用是将有关的操作作为结果接口组织在一个继承层次结构中，由它来为派生类提供一个公共的根，派生类将具体实现在其基类中作为接口的操作。

       虚函数的作用是允许在派生类中重新定义与基类同名的函数，并且可以通过基类指针或引用来访问基类和派生类中的同名函数。

虚函数是C++中用于实现多态的机制。核心理念就是**通过基类访问派生类定义的函数**。如果父类或者祖先类中函数func()为虚函数，则子类及后代类中，函数func()是否加virtual关键字，都将是虚函数。为了提高程序的可读性，建议后代中虚函数都加上virtual关键字。

- 一个派生类的函数如果覆盖了某个继承而来的虚函数，则他的形参类型必须与被他覆盖的虚函数完全一致。
- 派生类中虚函数的返回类型也必须与基类中的完全一致。该规则存在一个例外，当类的虚函数返回类型是类本身的指针或引用时，上述规则无效。
- final说明符说明该函数不能被覆盖。
- override说明符说明该函数应该覆盖基类的函数。如果没有正确覆盖，则会报错。



#### 虚函数与默认实参

如果某次函数调用使用了默认实参，则该实参值由本次调用的静态类型决定。

如果我们通过基类的引用或指针调用函数，则使用基类中定义的默认实参，即使试运行的是派生类中的函数版本也是如此。此时，传入派生类函数的将是基类函数定义的默认实参。

> 如果基函数使用默认实参， 则基类和派生类中定义的默认实参最好一致。



#### 回避虚函数机制

```c++
double undiscounted = baseP->Quote::neet_price(42);
```

何时用？通常是当一个派生类的虚函数调用它覆盖的积累的虚函数版本时。



## 访问控制与继承

- 派生类的成员或友元只能通过派生类对象来访问基类的受保护成员。派生类对于一个基类对象中的受保护成员没有任何访问权。
- 派生类成员函数或友元对基类成员的访问权限只与基类中的访问说明符有关。
- 派生访问说明符的目的是控制派生类用户（包括派生类的派生类在内）对于基类成员的访问权限。

```C++
class Base{
public:
    void pub_mem();
protected:
    int prot_mem;
private:
    char priv_mem;  //private成员
};

struct Pub_Derv:public Base{
    //正确：派生类能访问protected成员
    int f() {return prot_mem;}
    //错误：private成员对于派生类来说是不可访问的
    char g() {return priv_mem;}
}
struct Priv_Derv:private Base{
    //private不影响对基类的访问权限
    int f1() const {return prot_mem;}
}

Pub_Derv d1;
Priv_Derv d2;
d1.pub_mem();  //正确
d2.pub_mem();  //错误:pub_mem在派生类中是private的
```



#### 派生类向基类转换的可访问性

- 只有当D共有地继承B时，用户代码才能使用派生类向基类的转换；如果D继承B的方式是受保护的或私有的，则用户代码不能使用该转换。
- 不论D以什么方式继承B，D的成员函数和友元都能使用派生类向基类的转换；派生类向其直接积累的类型转换对于派生类的成员和友元来说永远是可访问的。
- 如果D继承B的方式是公有的或受保护的，则D的派生类的成员和友元可以使用D向B的类型转换；反之，如果D继承B的方式是私有的，则不能使用。



#### 友元与继承

- 不能继承友元关系；每个类负责控制各自成员的访问权限。



#### 改变个别成员的可访问性

改变派生类继承的某个名字的访问级别，通过使用using声明：

```c++
class Base{
public:
    std::size_t size() const {return n;}
protected:
    std::size_t n;
}

class Derived:private Base{
public:
    //保留对象尺寸相关的成员的访问级别
    using Base::size;
protected:
    using Base::n;
}
```



#### 虚函数的作用域

```C++
#include <iostream>
using std::cout; using std::endl;

class Base {
public:
    virtual int fcn();
};

int Base::fcn() { cout << "Base::fcn()" << endl; return 0; }

class D1 : public Base {
public:
    // hides fcn in the base; this fcn is not virtual
    // D1 inherits the definition of Base::fcn() 
    int fcn(int);      // parameter list differs from fcn in Base
	virtual void f2(); // new virtual function that does not exist in Base
};

int D1::fcn(int) { cout << "D1::fcn(int)" << endl; return 0; }
void D1::f2() { cout << "D1::f2()" << endl; }

class D2 : public D1 {
public:
    int fcn(int); // nonvirtual function hides D1::fcn(int)
    int fcn();    // overrides virtual fcn from Base
	void f2();    // overrides virtual f2 from D1
};

int D2::fcn(int) { cout << "D2::fcn(int)" << endl; return 0; }
int D2::fcn() { cout << "D2::fcn()" << endl; return 0; }
void D2::f2() { cout << "D2::f2()" << endl; }

int main()
{
    D1 dobj, *dp = &dobj;
    dp->fcn(42); // ok: static call to D1::fcn(int)

    Base bobj;  D1 d1obj; D2 d2obj;

    Base *bp1 = &bobj, *bp2 = &d1obj, *bp3 = &d2obj;
    bp1->fcn(); // virtual call, will call Base::fcn at run time
    bp2->fcn(); // virtual call, will call Base::fcn at run time
    bp3->fcn(); // virtual call, will call D2::fcn at run time

	D1 *d1p = &d1obj; D2 *d2p = &d2obj;
	d1p->f2(); // virtual call, will call D1::f2() at run time
	d2p->f2(); // virtual call, will call D2::f2() at run time
	Base *p1 = &d2obj; D1 *p2 = &d2obj; D2 *p3 =  &d2obj;
	p2->fcn(42);  // statically bound, calls D1::fcn(int)
	p3->fcn(42);  // statically bound, calls D2::fcn(int)

    Base* bp = &d1obj; D1 *dp1 = &d2obj; D2 *dp2 = &d2obj;
    dp1->fcn(10); // static call to D1::fcn(int)
    dp2->fcn(10); // static call to D2::fcn(int)
}

```



#### 虚析构函数

通过在基类中将析构函数定义成虚函数以确保执行正确的析构函数版本。

虚析构函数将组织合成移动操作。

#### 派生类的拷贝控制成员

默认情况下，基类默认构造函数初始化派生类对象的基类部分。如果我们想拷贝（或移动）基类部分，必须在派生类的构造函数初始化列表中显式地使用基类的拷贝（或移动）构造函数。

```C++
class Base{};
class D:public Base{
public:
    D (const D& d):Base(d)  //拷贝基类成员
        /* D的成员的初始值 */{}  
    D (D&& d):Base(std::move(d))  //移动基类成员
        /* D的成员的初始值 */{}
};
```

- 派生类的赋值运算符必须显式地为其基类部分赋值
- 派生类析构函数只负责销毁由派生类自己分配的资源

> 如果构造函数或析构函数调用了某个虚函数，则我们应该执行与构造函数或析构函数所属类型相对应的虚函数版本。



### 继承的构造函数

类不能继承默认、拷贝和移动构造函数。派生类继承基类构造函数的方式是提供一条注明了（直接）基类名的using声明语句。

```C++
class Bulk_quote:public Disc_quote{
public:
    using Disc_quote::Disc_quote;  //继承Disc_quote的构造函数
    /*
    等价于
    Bulk_quote(const std::string& book,double price):Disc_quote(book,price){}
    */
};
```

- 由于using声明语句不能指定explicit或constexper。所以基类是explicit或constexper的，那么派生类具有相同的属性。
- 当一个基类构造函数含有默认实参，这些实参并不会被继承。相反，派生类将获得多个继承的构造函数，其中每个构造函数分别省略掉一个含有默认实参的形参。
- 如果基类有几个构造函数，大多数情况下派生类会继承所有这些构造函数。除了两个例外：1.如果派生类定义的构造函数与基类的构造函数具有相同的参数列表，则该构造函数不会被继承。2.默认、拷贝和移动构造函数不会被继承。



## 容器与继承

### 在容器中放置指针而非对象



# 16.模板与泛型编程

## 定义模板

#### 模板类型参数

类型参数前必须使用关键字class或typename，两者含义相同。

```C++
template <typename T> T foo(T* p){
    T tmp = *p;
    //...
    return tmp;
}
```

#### 非类型模板参数

非类型模板参数表示一个值而非一个类型。通过特定的类型名指定。

```c++
template<unsigned N,unsigned M>
int compare(const char (&p1)[N],const char (&p2)[M])
{
    return strcmp(p1,p2);
}
```

> 函数模板和类模板成员函数的定义通常放在头文件中

## 类模板

```C++
template <typename T> class Blob{
    //...
}

//实例化类模板
Blob<int> ia;
```

定义在类模板之外的成员函数必须以关键字template开始，后接类模板参数列表。

```C++
template <typename T>
ret-type Blob<T>::member-name(parm-list)
```

当我们使用一个类模板类型时，必须提供模板实参，但这一规则有一个例外。在类模板自己的作用域中，我们可以直接使用模板名而不提供实参。

#### 模板类型别名

```c++
typedef Blob<string> StrBlob;


//新标准
template<typename T> using twin = pair<T,T>;
twin<string> authors;  //authors是一个pair<string,string>
```

C++语言假定通过作用域运算符访问的名字不是类型。因此，如果我们希望通过一个模板类型参数的类型成员，就必须显式告诉编译器改名字是一个类型。通过使用关键字typename来实现：

```c++
template <typename T>
typename T::value_type top(const T& c)
{
    if (!c.empty())
        return c.back();
    else
        return typename T::value_type();
}
```

#### 默认模板实参

无论何时使用一个类模板，我们都必须在模板名之后接上尖括号。尖括号指出类必须从一个模板实例化而来。特别是，如果一个类模板为其所有模板参数都提供了默认实参，就必须在模板名之后跟一个尖括号对：

```C++
template<class T=int> class Number{
    public:
    Numbers(T v=0):val(v){}
    private:
    T val;
}

Numbers<long double> lots_of_precision;
Numbers<> average_precision;  //空表示希望使用默认类型
```



### 成员模板

一个类可以包含本身是模板的成员函数。这种成员被称为成员模板。成员模板不能是虚函数。

与类模板的普通函数成员不同，成员模板是函数模板。当我们在类模板外定义一个成员模板时，必须同时为类模板和成员模板提供模板参数列表。

```C++
template <typename T>
template <typename It>
	Blob<T>::Blob<It b, It e:
		data(std::make_shared<std::vector<T>>(b,e)){}
```

### 控制实例化

在多个文件中实例化相同模板的额外开销可能非常严重。在新标准中，我们可以通过显式实例化避免这种开销：

```C++
extern template declaration;  //实例化声明
template declaration;         //实例化定义

//例子
extern template class Blob<string>;            //声明
template int compare(const int &, const int&); //定义
```

对于一个给定的实例化版本，可能有多个声明，但只能有一个定义。

