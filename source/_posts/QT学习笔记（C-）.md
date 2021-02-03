---
title: QT学习笔记（C++）
toc: true
date: 2020-01-12 20:34:33
tags: [c++,QT]
categories:
- C++
- 基础
---

# 开始
<!--more-->
## makefile 的编写
```makefile
# Makefile for building: hellorect
CC          = gcc
CXX         = g++
LINKER      = g++
LFLAGS      = -lm -static

OBJECTS     = rect.o hellorect.o
DSTTARGET   = hellorect
# Default rule
all: $(DSTTARGET)


$(DSTTARGET): $(OBJECTS)
	$(LINKER)  $(LFLAGS)  -o $@  $(OBJECTS)

hellorect.o: hellorect.cpp
	$(CXX) -c  -o  $@  $<  

rect.o: rect.cpp
	$(CXX) -c  -o  $@  $<  

clean:
	rm  $(OBJECTS)  hellorect.exe
```
这里解释一下上面脚本意思（# 打头的是注释，忽略掉）：
中间带有等于号的都是定义变量，引用变量的方式就是 $(变量名) , 脚本里 CC 是 C 语言编译器，CXX 是 C++ 编译器，LINKER 是链接器， LFLAGS 是链接器的参数。OBJECTS 是编译得到的目标文件，DSTTARGET 是可执行的目标程序。
接下来是 Makefile 的生成规则，Makefile 的基本规则是：

生成目标: 依赖文件
[tab字符] 系统命令

 示例的 Makefile 中

all: $(DSTTARGET)

是默认生成规则，依赖文件 $(DSTTARGET) ，它的下一行没有命令。 而如何生成 $(DSTTARGET) 呢，继续往下找

$(DSTTARGET): $(OBJECTS)

生成 $(DSTTARGET) 需要 $(OBJECTS)，有了目标文件之后执行命令

```
	$(LINKER)  $(LFLAGS)  -o $@  $(OBJECTS)
```

即调用链接器 $(LINKER)，根据链接器参数 $(LFLAGS) 和 $(OBJECTS)，生成 $@ 。 $@ 就是上一行冒号左边的要生成的目标。注意系统命令 $(LINKER) 之前一定要有制表符 tab 字符， 不能用 4 个空格代替，否则 make 时会出现没有分隔符（separator）的错误。
接下来的四句：

```
hellorect.o: hellorect.cpp      
	$(CXX) -c  -o  $@  $<  

rect.o: rect.cpp
	$(CXX) -c  -o  $@  $<  
```

是使用编译器生成目标文件 hellorect.o 和 rect.o ，$@ 是上一行冒号左边的目标，$< 是上一行冒号右边第一个依赖文件。 hellorect.o 和 rect.o就是链接器需要的 $(OBJECTS) 。
最后的两句是清除规则：

```
clean:
	rm  $(OBJECTS)  hellorect.exe
```

rm 是删除命令，如果 Windows 系统里没有 rm 命令，请安装一个 msysgit 工具（ http://msysgit.github.io/）， 然后系统环境变量里面会有 msysgit 工具路径，里面有 rm 工具。 clean 做的事情就是删除项目生成的 .o 和 .exe 文件。（注：Linux 系统里可执行程序没有 .exe 后缀，需要去掉 .exe 后缀。）

## 自定义QWidget

在自定义的类继承QWidget的同时，要包含Q_OBJECT宏。但是，第一次使用会报错
> https://stackoverflow.com/questions/23595961/qt-a-missing-vtable-usually-means-the-first-non-inline-virtual-member-function
> Q_OBJECT是必要的
> Q_OBJECT是信号，插槽，可调用项,qobject_cast翻译，属性，枚举和方法自省等的前提。

```cmake
cmake_minimum_required(VERSION 3.0)
project(test)

# 指定c++标准的版本
set(CMAKE_CXX_STANDARD 17)

# 自动调用moc，uic，rcc处理qt的扩展部分
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTORCC ON)

# 设置Qt5的cmake模块所在目录，如果不设置将使用系统提供的版本
# QT_DIR和QT_VERSION是指定了qt安装目录和版本的环境变量
# 如果你使用了系统的cmake，那么会优先使用系统提供模块，因为cmake会优先搜索CMAKE_SYSTEM_PREFIX_PATH
# 如果你不想让cmake优先搜索系统目录（会导致编译使用系统安装的qt而不是我们配置的），需要提示find_package命令
set(CMAKE_PREFIX_PATH ${CMAKE_PREFIX_PATH} $ENV{QT_DIR}/$ENV{QT_VERSION}/gcc_64/lib/cmake)

# 找到对应的qt模块，名字为qmake中QT += <name>中的name首字母大写后加上Qt5前缀
# 例如core为QtCore，你也可以去${CMAKE_PREFIX_PATH}的目录中找到正确的模块名
# 如果不想使用系统qt，这样写（注意NO_DEFAULT_PATH参数，它会让find_package跳过系统目录的查找）：
find_package(Qt5Widgets REQUIRED NO_DEFAULT_PATH)

# 如果你想要使用系统自带的qt，这样写：
# find_package(Qt5Widgets REQUIRED)

# 将当前目录的所有源文件添加进变量
aux_source_directory(. DIRS_SRCS)

# 通常这样设置就可以，如果你的项目包含qrc文件，那么需要将它们单独添加进来
# 例如add_executable(test ${DIRS_SRCS} resources.qrc)
add_executable(test ${DIRS_SRCS})

# 把对应Qt模块的库链接进程序
target_link_libraries(test Qt5::Widgets)
```

资源文件格式
```xml
<RCC>
    <qresource prefix="/icon">
        <file>icon/icon_THP.png</file>
    </qresource>
</RCC>
```

# 注意事项
## 信号与槽的连接
①connect()绑定函数，如果放到主窗口的构造函数中，此时，子窗口的类还没新建对象，没有分配内存地址，如果new一个对象，程序不会报错（搜一下，有好新手跟我一样有这个疑问），但是新建的子窗口对象与通过主窗口的点击按钮后，新建的子窗口对象是两码事，在内存中是两个不同的地址，我们要绑定的应该是后者与主窗口的信号槽绑定，所以，connect函数应该放到主窗口点击子窗口按钮代码里面。
②connect()绑定函数，如果放到子窗口的构造函数中，发送用this，接收用主窗口指针，原理一样，如果此处新建主窗口对象，然后建立连接，其实内存中有两个主窗口对象了，绑定的并不是我想显示日志信息的主窗口对象，此处可以直接调用出窗口已新建好对象的指针。
③connect()，在子窗口和主窗口交互的过程中，其实放到哪里不重要，重要的是发送和接收的指针一定要是已新建好的主窗口/子窗口对象的指针，并且确保是新建好后再执行该connect()绑定函数。

## 复杂多窗口设计
在多窗口设计过程中：
1. 涉及到控件之间的信号连接，需要在控件被建立后再创建。
2. 对于可能被重建（在deleterlater之后new对象）的控件，将其控件的相关连接单独写入一个函数。删除对象时，注意需要先删除对象相关的图标（Actions对应的toolbar和menubar中的item），然后通过deleterLater（）方法删除对象。

## 删除机制
在qt中如果对象继承自一个QObject对象，则QT会自动对对象进行管理。

# 窗口布局
## 窗口中添加分割线
```c++
    QFrame *line;
    line = new QFrame();
    line->setFrameShape(QFrame::VLine);
//    line->setFrameShadow(QFrame::Sunken);
    line->setLineWidth(3);
```

