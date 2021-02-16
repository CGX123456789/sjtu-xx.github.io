---
title: C++零碎知识点
toc: true
mathjax: true
date: 2020-07-24 22:34:50
tags:
categories:
- C++
- 基础
---

## `#define`和`const`的区别
<!--more-->
define没有数据类型，const可以定义数据类型。

编译器可以对const进行类型检查，不能对define进行类型检查。

## malloc/free和new/delete的区别
malloc和free是标准库函数，new和delete是c++的运算符。

都可用于动态申请和释放内存。

## C++不是类型安全的
reinterpret cast可以强制转换

## 判断操作系统位数
sizeof查看指针长度

## 几种const
```C++
const char *p;  // 指向const char的指针
char const *p;  // 同上
char *const p;  // 指向char的常指针
```

## explicit
显式：函数参数不能进行隐式类型转换

## 数组
数组实参传递给函数时，传递的是地址。函数也无法通过sizeof(p)/sizeof(p[0])计算机制。但是可以通过下标访问函数元素。

## C++中的全局变量不是由main函数引起的。

## 将“引用”作为函数参数有哪些特点？

<1>传递引用给函数和传递指针的效果是一样的

<2>使用引用传递函数的参数，在内存中并没有产生实参的副本，它是直接对实参操作(当参数传递的数据较大时，用引用比用一般变量传递参数的效率和所占空间都好)

<3>与指针作为函数的参数，需要分配存储单元，且容易产生错误且程序的阅读性较差；另一方面，在主调函数的调用点处，必须用变量的地址作为实参，而引用更容易使用、更清晰

## “引用”与多态的关系？

引用是除指针外另一个可以产生多态效果的手段。这意味着，一个基类的引用可以指向它的派生类实例

例:Class A; Class B : Class A{...}; B b; A& ref = b;

## 只能用intialization list而不能用assignment
对于const和reference类型成员变量，它们只能够被初始化而不能做赋值操作，因此只能用初始化列表。

还有一种情况就是，类的构造函数需要调用其基类的构造函数的时候。

## 空类大小
空类大小等于1，是因为编译器为了区分不同的类，在类中加的一个char型。

## 如何打印出当前源文件的文件名以及源文件的当前行号？
答案：
cout << __FILE__ ;
cout<<__LINE__ ;
__FILE__和__LINE__是系统预定义宏，这种宏并不是在某个文件中定义的，而是由编译器定义的。

## main主函数执行完毕后，是否可能会再执行一段代码，给出说明？
答案：可以，可以用_onexit注册一个函数，它会在main之后执行int fn1(void), fn2(void), fn3(void), fn4 (void);

```c++
void main( void )
{
       String str("zhanglin");
       _onexit( fn1 );
       _onexit( fn2 );
       _onexit( fn3 );
       _onexit( fn4 );
       printf( "This is executed first.n" );
}
int fn1()
{
       printf( "next.n" );
       return 0;
}
int fn2()
{
       printf( "executed " );
       return 0;
}
int fn3()
{
       printf( "is " );
       return 0;
}
int fn4()
{
       printf( "This " );
       return 0;
}
```

## 如何判断一段程序是由C 编译程序还是由C++编译程序编译的？
```C++
#ifdef __cplusplus
cout<<"c++";
#else
cout<<"c";
#endif
```

## 文件中有一组整数，要求排序后输出到另一个文件中
```C++
#include <iostream>
#include <fstream>
#include <vector>

using namespace std;

void Order(vector<int> &data) {
    // 冒泡排序
    int count = data.size();
    int tag = false;
    for (int i = 0; i < count; i++) {
        for (int j = 0; j < count - i - 1; j++) {
            if (data[j] > data[j + 1]) {
                tag = true;
                int temp = data[j];
                data[j] = data[j + 1];
                data[j + 1] = temp;
            }
        }
        if (!tag) {
            break;
        }
    }
}

int main() {
    vector<int> data;
    ifstream in("data.txt");
    if (!in) {
        std::cout << "file error";
        exit(1);
    }

    int temp;
    while (!in.eof()) {
        in >> temp;
        data.push_back(temp);
    }
    in.close();
    Order(data);
    ofstream out("result.txt");
    if (!out) {
        cout << "file error!";
        exit(1);
    }
    for (const auto item:data)
        out << item << " ";
    out.close();
}
```

## 节点的定义方式
```C++
//方法1
struct node {
  int data;
  struct node *next;
};

typedef struct node Node;

//方法2
typedef struct node {
  int data;
  struct node *next;
} Node;

// 使用
//建立一个Node对象
struct node* a; //1
Node* b; //2
```

## 链表操作
1. 逆序
```C++
////41
typedef struct node {
    int data;
    struct node *next;
} Node;

Node *ReverseList(Node *head) {
    if (head == nullptr || head->next == nullptr) {
        return head;
    }
    Node *p1 = head;
    Node *p2 = head->next;
    Node *p3 = p2->next;
    p1->next = nullptr;
    while (p3 != nullptr) {
        p2->next = p1;
        p1 = p2;
        p2 = p3;
        p3 = p3->next;
    }
    p2->next = p1;
    head = p2;
    return head;
}
```

2. 合并链表
```c++
typedef struct node {
    int data;
    struct node *next;
} Node;

Node *Merge(Node *head1, Node *head2) {
    if (head1 == nullptr) {
        return head2;
    }
    if (head2 == nullptr) {
        return head1;
    }
    Node *head = nullptr;
    Node *p1 = nullptr;
    Node *p2 = nullptr;
    if (head1->data < head2->data) {
        head = head1;
        p1 = head1->next;
        p2 = head2;
    } else {
        head = head2;
        p1 = head1;
        p2 = head2->next;
    }

    Node *pcurhead = head;
    while (p1->next != nullptr && p2->next != nullptr) {
        if (p1->next < p2->next) {
            pcurhead->next = p1->next;
            p1 = p1->next;
        } else {
            pcurhead->next = p2->next;
            p2 = p2->next;
        }
    }
    if (p1->next == nullptr) {
        pcurhead->next = p2;
    } 
    if (p2->next == nullptr) {
        pcurhead->next = p1;
    }
    return head;
}
```

3. 合并链表（递归）
```C++
Node *Merge(Node *head1, Node *head2) {
    if (head1 == nullptr) {
        return head2;
    }
    if (head2 == nullptr) {
        return head1;
    }
    Node *head;
    if (head1->data < head2->data) {
        head = head1;
        head->next = Merge(head1->next, head2);
    } else {
        head = head2;
        head->next = Merge(head1, head2->next);
    }
    return head;
}
```

4. 链表中是否有环
```c++
typedef struct node {
    int data;
    struct node *next;
} Node;

bool check_loop(Node *head) {
    if (head == nullptr || head->next == nullptr) {
        return false;
    }
    Node *slow_ptr = head;
    Node *fast_ptr = head->next;
    while (fast_ptr != nullptr && fast_ptr->next != nullptr) {
        slow_ptr = slow_ptr->next;
        fast_ptr = fast_ptr->next->next;
        if (fast_ptr==slow_ptr){
            return true;
        }
    }
    return false;
};
```


5. strcpy的实现

```c++
#include <cassert>

char *strcpy(char *strDest, const char *strSrc) {
    assert((strDest != nullptr) && (strSrc != nullptr));
    char *address = strDest;
    while ((*strDest++ = *strSrc++) != '\0' );
    return address;
}
```

6. strlen的实现
```C++
#include <cassert>
int strlen(const char *dst) {
    assert(dst != nullptr);
    int count = 0;
    while (*dst++ != '\0') {
        count++;
    }
    return count;
}
```

## malloc和free的使用
free只是告诉操作系统，指针P所指向的这块内存我不实用了！由操作系统负责收回。而指针P他的空间中存储的还是原来的空间的地址。
当然在free之后再解引用指针就回发生段错误，所以每次free之后，最好p=NULL;

```C++
char *p = NULL;
p = (char *) malloc(sizeof(char));
memset(p, 'g', sizeof(char));
printf("%c\n", *p); // g
free(p);
p = NULL;
```

## include<file.h> 与 ＃include "file.h"的区别？
答：前者是从Standard Library的路径寻找和引用file.h，而后者是从当前工作路径搜寻并引用file.h。

## 堆栈溢出一般是由什么原因导致的？

1. 没有回收垃圾资源
2. 层次太深的递归调用

## 写一个“标准”宏MIN，这个宏输入两个参数并返回较小的一个。

`#define MIN(A,B) ((A) <= (B) ？ (A) : (B))`

## 不能做switch()的参数类型是？
C/C++中：
支持byte,char,short,int,long,bool,整数bai类型和du枚举类型。
不支持float，double，string

## 什么函数不能声明为虚函数：
一个类中将所有的成员函数都尽可能地设置为虚函数总是有益的。
设置虚函数须注意：
1：只有类的成员函数才能说明为虚函数；
2：静态成员函数不能是虚函数；
3：内联函数不能为虚函数；
4：构造函数不能是虚函数；
5：析构函数可以是虚函数，而且通常声明为虚函数。

写inline virtual void f(),不能保证函数f()一定是内联的，只能保证f()是虚函数（从而保证此函数一定不是内联函数）

## static 全局变量、局部变量、函数与普通全局变量、局部变量、函数有什么区别？static全局变量与普通的全局变量有什么区别？static局部变量和普通局部变量有什么区别？static函数与普通函数有什么区别？
全局变量(外部变量)的说明之前再冠以static 就构成了静态的全局变量。全局变量本身就是静态存储方式， 静态全局变量当然也是静态存储方式。 这两者在存储方式上并无不同。
这两者的区别虽在于非静态全局变量的作用域是整个源程序， 当一个源程序由多个源文件组成时，非静态的全局变量在各个源文件中都是有效的。 而静态全局变量则限制了其作用域， 即只在定义该变量的源文件内有效， 在同一源程序的其它源文件中不能使用它。由于静态全局变量的作用域局限于一个源文件内，只能为该源文件内的函数公用， 因此可以避免在其它源文件中引起错误。

从以上分析可以看出， 把局部变量改变为静态变量后是改变了它的存储方式即改变了它的生存期。把全局变量改变为静态变量后是改变了它的作用域， 限制了它的使用范围。

static函数与普通函数作用域不同。仅在本文件。只在当前源文件中使用的函数应该说明为内部函数(static)，内部函数应该在当前源文件中说明和定义。对于可在当前源文件以外使用的函数，应该在一个头文件中说明，要使用这些函数的源文件要包含这个头文件

static全局变量与普通的全局变量有什么区别：static全局变量只初使化一次，防止在其他文件单元中被引用;

static局部变量和普通局部变量有什么区别：static局部变量只被初始化一次，下一次依据上一次结果值；

static函数与普通函数有什么区别：static函数在内存中只有一份，普通函数在每个被调用中维持一份拷贝

程序的局部变量存在于（堆栈）中，全局变量存在于（静态区 ）中，动态申请数据存在于（ 堆）中。 

## volatile的含义
一个定义为volatile的变量是说这变量可能会被意想不到地改变，这样，编译器就不会去假设这个变量的值了。精确地说就是，优化器在用到这个变量时必须每次都小心地重新读取这个变量的值，而不是使用保存在寄存器里的备份。
