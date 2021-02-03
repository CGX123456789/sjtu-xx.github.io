---
title: C-Primer读书笔记-第四部分：高级主题
toc: true
date: 2020-01-05 23:45:38
tags: [c++]
categories: 
- C++
- 基础
---



# 17.标准库特殊实施

<!--more-->

## tuple类型

tuple支持的操作

| 操作                                 | 说明                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| tuple<T1,T2,...,Tn> t;               | t是一个tuple，成员数为n，所有成员都进行值初始化              |
| tuple<T1,T2,...,Tn> t(v1,v2,...,vn); | t是一个tuple，成员类型为T1...Tn。每个成员都用对应的vi进行初始化。此函数是explicit的。 |
| make_tuple(v1,v2,...,vn);            | 返回一个用给定初始值初始化的tuple。tuple的类型从初始值的类型判断。 |
| t1==t2                               |                                                              |
| t1!=t2                               |                                                              |
| `get<i>(t)`                          | 返回t的第i个数据成员的引用；如果t是一个左值，结果返回一个左值引用；如果t是一个右值，结果返回一个右值引用。tuple所有成员都是public的。 |
| `tuple_size<tupleType>::value`       | 一个类模板，可以通过一个tuple类型来初始化。她有一个名为value的public constexpr static数据成员，类型为size_t,表示给定tuple类型中成员的数量。 |
| tuple_element<i,tupleType>::type     | 一个类模板，可以通过一个整型常量和一个tuple类型来初始化。她有一个名为type的public成员，表示给定tuple类型中指定成员的类型。 |

tuple的一个常见用途是从一个函数返回多个值。

## 正则表达式

| 类              | 类说明                                                       |
| --------------- | ------------------------------------------------------------ |
| regex           | 表示有一个正则表达式的类                                     |
| regex_match     | 讲一个字符序列与一个正则表达式匹配                           |
| regex_search    | 寻找第一个与正则表达式匹配的子序列                           |
| regex_replace   | 是用给定格式替换一个正则表达式                               |
| sregex_iterator | 迭代器适配器，调用regex_search来遍历一个string中所有匹配的字串 |
| smatch          | 容器类，保存在string中搜索的结果                             |
| ssub_match      | string中匹配的表达式的结果                                   |



默认情况，regex使用的正则表达式语言是ECMAScript。在ECMAScript中，模式[[:alpha:]]匹配任意字母。



regex选项：定义在regex和regex_constants::syntax_option_type

| 标志       | 说明                          |
| ---------- | ----------------------------- |
| icase      | 在匹配过程中忽略大小写        |
| nosubs     | 不保存匹配的子表达式          |
| optimize   | 执行速度优先与构造速度        |
| ECMAScript | 使用ECMA-262指定的语法        |
| basic      | 使用POSIX基本的正则表达式语法 |
| extended   | 使用POSIX扩展的正则表达式语法 |
| awk        | 使用POSIX版本的awk语言的语法  |
| grep       | 使用POSIX版本的grep的语法     |
| egrep      | 使用POSIX版本的egrep的语法    |

使用`regex r("[[:alnum:]]+\\.(cpp|cxx|xx)$",regex::icase)`

> 一个正则表达式所表示的“程序”是在运行时而非编译时编译的。正则表达式的编译是一个非常慢的操作，特别是在你使用了扩展的正则表达式语法或是复杂的正则表达式时。应该努力避免创建很多不必要的regex。

| 迭代器                    | 说明                                                         |
| ------------------------- | ------------------------------------------------------------ |
| sregex_iterator it(b,e,r) | 一个sregex_iterator,遍历迭代器b和e表示的string。他调用sregex_search(b,e,r)将it定位到输入中第一个匹配的位置。 |
| sregex_iterator end;      | sregex_iterator的尾后迭代器。                                |
| *it                       |                                                              |
| it->                      |                                                              |
| ++it                      |                                                              |
| it++                      |                                                              |
| it1==it2                  |                                                              |
| it1!=it2                  |                                                              |



## 随机数

定义在头文件random中：随机数引擎类和随机数分布类

```C++
default_random_engine e; //生成随机无符号数
e();
```

| 引擎操作            | 说明                                       |
| ------------------- | ------------------------------------------ |
| Engine e            | 默认构造函数，使用默认种子                 |
| Engine e(s)         | 使用整数值s作为种子                        |
| e.seed(s)           |                                            |
| e.min()             | 该引擎可生成的最小值和最大值               |
| e.max()             |                                            |
| Engine::result_type | 该引擎生成的unsigned整型类型               |
| e.discard(u)        | 将引擎推进u步，u的类型为unsigned long long |



| 分布                                       | 说明                         |
| ------------------------------------------ | ---------------------------- |
| uniform_int_distribution<unsigned> u(0,9)  | 生成0到9之间均匀分布的随机数 |
| uniform_real_distribution<unsigned> u(0,1) | 生成0到1之间的随机实数       |
| normal_distribution<> n(4,1.5)             | 均值4，标准差1.5             |
| bernoulli_distribution(0.55)               | 是一个普通类，返回bool值     |

```C++
vector<unsigned> good_randVec()
{
    //由于我们希望引擎和分布对象保持状态，因此应该将他们定义为static的，从而每次调用都生成新的数
    static default_random_engine e;
    static uniform_int_distribution<unsigned> u(0,9);
    vector<unsigned> ret;
    for (size_t i=0;i<100;++i)
        ret.push_back(u(e));
    return ret;
}
```



## IO库再探

格式化输入输出

| 操作符       | 说明                                    |
| ------------ | --------------------------------------- |
| boolalpha    | 将true和false输出为字符串               |
| noboolalpha  | 将true和false输出为0，1                 |
| showbase     | 对整型值输出表示进制的前缀              |
| noshowbase   | 不生成表示进制的前缀                    |
| showpoint    | 对浮点值总是显示小数点                  |
| noshowpoint  | 只有当浮点值包含小数部分时才显示小数点  |
| showpos      | 对非负数显示+                           |
| noshowpos    | 对非负数不显示+                         |
| uppercase    | 在十六进制中打印0X，在科学计数法中打印E |
| nouppercase  | 在十六进制中打印0x，在科学计数法中打印e |
| dec          | 整数显示为十进制                        |
| hex          | 整数显示为十六进制                      |
| oct          | 整数显示为八进制                        |
| left         | 在值的右侧添加填充字符                  |
| right        | 在值的左侧添加填充字符                  |
| internal     | 在符号和值之间添加填充字符              |
| fixed        | 浮点值显示为定点十进制                  |
| scientific   | 浮点值现实为科学计数法                  |
| hexfloat     | 浮点值显示为十六进制                    |
| defaultfloat | 重置浮点数格式为十进制                  |
| unitbuf      | 每次输出操作后都刷新缓存区              |
| nounitbuf    | 恢复正常的缓冲区刷新方式                |
| skipws       | 输入运算符跳过空白符                    |
| noskipws     | 输入运算符不跳过空白符                  |
| flush        | 刷新ostream缓冲区                       |
| ends         | 插入空字符，然后刷新ostream缓冲区       |
| endl         | 插入换行，然后刷新ostream缓冲区         |



iomanio中的操作符

| 函数            | 说明                    |
| --------------- | ----------------------- |
| setfill(ch)     | 用ch填充空白            |
| setprecision(n) | 将浮点精度设置为n       |
| setw(w)         | 读或写值的宽度为w个字符 |
| setbase(b)      | 将整数输出为b进制       |

### 流随机访问

seek和tell函数

g输入，p输出

| 函数            | 说明                                                     |
| --------------- | -------------------------------------------------------- |
| tellg()         | 返回一个输入流中（tellg）或输出流中（tellp）标记当前位置 |
| tellp()         |                                                          |
| seekg(pos)      |                                                          |
| seekp(pos)      |                                                          |
| seekg(off,from) | 定位到from之前或之后off个字符：from可以是(beg,cur,end)   |
| seekp(off,from) |                                                          |



# 18.用于大型程序的工具

## 异常处理

- 一个异常如果没有被捕获，则它将终止当前的程序。程序将调用标准库函数terminate
- 通常情况下，如果catch接收的异常与某个继承体系有关，最好将该catch的参数定义成引用类型。
- 重新抛出仍然是一个throw语句，只不过不包含表达式`throw;`。

- 处理构造函数初始值异常的唯一方法是将构造函数写成函数try语句块。
- 一旦一个noexcept函数抛出了异常，程序就会调用terminate以确保遵守不在运行时抛出异常的承诺。



## 命名空间

- 一个命名空间的定义包含两部分：首先是关键字namespace，随后是命名空间的名字。在命名空间名字后面是一系列由花括号括起来的声明和定义。

- 命名空间结束后无需分号。

- 命名空间可以是不连续的，命名空间可以定义在几个不同的部分。
- 命名空间中定义的成员可以直接使用名字，无须前缀；命名空间之外定义的成员必须使用含有前缀的名字
- 未命名的命名空间仅在特定的文件内部有效，其作用范围不会横跨多个不同的文件。
- 命名空间的别名：`namespace primer=cplusplusprimer`
- `using namespace A;` 把A中的名字
- 对于using声明，只是简单的令名字在局部作用域内有效。using指示是令整个命名空间的所有内容变得有效。通常情况下，命名空间中会有一些不能出现在局部作用域中的定义。因此，using指令一般被看做是出现在最近的外层作用域中。

# 特殊工具与技术

## 控制内存分配

### 重载new和delete

1. new

执行new实际上执行了三步操作。

- new表达式调用一个名为`operator new（或者operator new[]）`的标准库函数。该函数分配一块足够大的、原始的、未命名的内存空间 以便存储特定类型的对象。
- 编译器运行响应的构造函数以构造这些对象，并为其传入初始值。
- 对象被分配了空间并构造完成，返回一个指向该对象的指针。

2. delete

两步操作。

- 对所指的对象或者数组中的元素执行对应的析构函数
- 编译器调用名为`operator delete(或 operator delete[])`的标准库函数释放内存空间。

#### malloc函数和free函数

### dynamic_cast

```C++
dynamic_cast<type*>(e); //e必须是指针，如果转换失败返回0
dynamic_cast<type&>(e);  //e必须是左值，如果转换失败抛出bad_cast异常
dynamic_cast<type&&>(e); //e不能是左值，如果转换失败抛出bad_cast异常
```

### typeid

typeid运算符允许程序向表达式提问，你的对象是什么类型？

`typeid(e)`返回`type_info`类型的对象


