---
title: cmake基础（搬运）
toc: true
date: 2019-11-26 11:30:12
tags: [c,c++,cmake]
categories: 
- C++
- 基础
---
[原链接 ref](https://www.jianshu.com/p/8df5b2aba316)

# 1. 编译单目录工程

1.创建工程文件夹

```c
mkdir hello #工程目录
cd hello
mkdir src   # 存放源代码的目录
mkdir build # 存放编译中间代码和目标代码的目录
```
<!--more-->
2.进入`src`目录，编写一个`main.c`文件

```c
#include <stdio.h>

int main(int argc, char **argv)
{
    printf("hello world\n");
    return 0;
}
```

3.编写工程顶层目录的`CMakeLists.txt`

```bash
cmake_minumum_required(VERSION 2.6)

#指定项目名
project(hello)

#指定子目录
add_subdirectory(src)
```

4.编写子目录`src`的`CMakeLists.txt`

```bash
aux_source_directory(. SRC_LIST)

add_executable(hello ${SRC_LIST})
```

5.编译工程

> 1.进入`build` 目录
>  2.执行命令`cmake ..`创建Makefile
>  3.执行命令`make`编译工程
>  4.在`build`的子目录`src`生成了执行文件

# 2. 编译多目录工程

1.创建工程目录

```bash
mkdir hello # 工程目录
cd hello
mkdir src   # 存放源码目录
mkdir build # 存放编译产生的中间文件
cd src      
mkdir hello # 存放hello 模块
mkdir world # 存放world 模块
```

2.编写`hello`模块

- 进入`hello`目录
- 编写`hello.h`文件

```c
#ifndef  HELLO_H
#define  HELLO_H

void Hello_Print(void);

#endif
```

- 编写`hello.c`文件

```c
#include "hello.h"

#include <stdio.h>

void Hello_Print(void)
{
    printf("hello ");
}
```

- 编写`CMakeLists.txt` 文件

```bash
aux_source_directory(. DIR_HELLO_SRC)

add_library(hello_lib ${DIR_HELLO_SRC})
```

3.编写`world`模块

- 进入`world`目录
- 编写`world.h`文件

```c
#ifndef  WORLD_H
#define  WORLD_H

void World_Print(void);

#endif

```

- 编写`world.c`文件

```c
#include "world.h"

#include <stdio.h>

void World_Print(void)
{
    printf("world");
}

```

- 编写`CMakeLists.txt` 文件

```bash
aux_source_directory(. DIR_WORLD_SRC)

add_library(world_lib ${DIR_WORLD_SRC})

```

4.编写主模块

- 进入`src`目录
- 编写`main.c` 文件

```c
#include "hello/hello.h"
#include "world/world.h"

int main(int argc, char **argv)
{
    Hello_Print();
    World_Print();

    return 0
}

```

- 编写`CMakeLists.txt` 文件

```bash
add_source_directory(. DIR_SRC)

# 添加子目录
add_subdirectory(hello)
add_subdirectory(world)

# 执行文件
add_executable(hello_prj ${DIR_SRC})
target_link_libraries(hello_prj ello_lib world_lib)

```

5.编写顶层目录的`CMakeLists.txt`文件

```css
cmake_minumum_required(VERSION 2.6)

project(hello_prj)

add_subdirectory(src)

```

# 3. 动态库和静态库的构建和使用

1.使用一个`hello world`工程来展开说明
 **项目结构**

```ruby
|-- CMakeLists.txt
|-- build
|-- include
|   |-- hello
|   |   `-- hello.h
|   `-- world
|       `-- world.h
|-- src
|   |-- CMakeLists.txt
|   |-- hello
|   |   `-- hello.c
|   `-- world
|       `-- world.c
`-- test
    |-- CMakeLists.txt
        `-- mytest.c

```

2.顶层目录`CMakeLists.txt`

```bash
 cmake_minimum_required(VERSION 2.6)
 
 project(helloworld)
 
 #设置库文件存放路径
 set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/build/lib)
 
 #设置执行文件存放路径
 set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/build/bin)
 
 #获取当前目录及子目录(递归获取),添加到头文件搜索路径
 function(include_sub_directories_recursively root_dir)
     if (IS_DIRECTORY ${root_dir})               # 当前路径是一个目录吗，是的话就加入到包含目录
         message("include dir: " ${root_dir})
         include_directories(${root_dir})
     endif()
 
     file(GLOB ALL_SUB RELATIVE ${root_dir} ${root_dir}/*) # 获得当前目录下的所有文件，让如ALL_SUB列表中
     foreach(sub ${ALL_SUB})
         if (IS_DIRECTORY ${root_dir}/${sub})
             include_sub_directories_recursively(${root_dir}/${sub}) # 对子目录递归调用，包含
         endif()
     endforeach()
 endfunction()
 
 #项目的所有目录都为头文件搜索路径
 include_sub_directories_recursively(${PROJECT_SOURCE_DIR})
 
 #添加库文件搜索路径
 link_directories(
     ${PROJECT_SOURCE_DIR}/build/lib
     )

 #添加子目录
 add_subdirectory(src)
 add_subdirectory(test)

 #设置安装目录
 set(CMAKE_INSTALL_PREFIX ${PROJECT_SOURCE_DIR}/install)
```

3.`helloworld`库的源代码
 **hello.h文件**

```c
#ifndef  HELLO_H
#define  HELLO_H

void Hello_Print(void);

#endif
```

**hello.c文件**

```c
#include "hello/hello.h"

#include <stdio.h>

void Hello_Print(void)
{
    printf("hello ");
}
```

**world.h文件**

```c
#ifndef  WORLD_H
#define  WORLD_H

void World_Print(void);

#endif
```

**world.c文件**

```c
#include "world/world.h"

#include <stdio.h>

void World_Print(void)
{
    printf("world");
}
```

4.子目录`src`下的`CMakeLists.txt`

```ruby
 #递归获取当前目录及子目录下的所有c文件
 file(GLOB_RECURSE c_files "*.c")
 
 #生成动态库和静态库
 add_library(helloworld_lib_shared  SHARED ${c_files})
 add_library(helloworld_lib_static STATIC ${c_files})
 
 #将动态库和静态库的名字设置为一致
 set_target_properties(helloworld_lib_shared PROPERTIES OUTPUT_NAME "helloworld")
 set_target_properties(helloworld_lib_static PROPERTIES OUTPUT_NAME "helloworld")
 
 #设置动态库版本
 set_target_properties(helloworld_lib_shared PROPERTIES VERSION 1.2 SOVERSION 1)
 
 #安装动态库和静态库
 INSTALL(TARGETS helloworld_lib_shared helloworld_lib_static
     LIBRARY DESTINATION lib
     ARCHIVE DESTINATION lib)
 
 #安装头文件
 INSTALL(DIRECTORY ${PROJECT_SOURCE_DIR}/include/ DESTINATION include)
```

5.`mytest.c`文件测试生成的库文件
 **mytest.c文件**

```cpp
#include "hello/hello.h"
#include "world/world.h"

#include <stdio.h>

int main(int argc, char **argv)
{
    Hello_Print();
    World_Print();

    printf("\n");
    return 0;
}
```

**CMakeLists.txt文件**

```bash
#递归获取所有当前目录及子目录下的C文件
file(GLOB_RECURSE c_files ./*.c)
  
#生成执行文件
add_executable(mytest ${c_files})
     
#链接外部库
target_link_libraries(mytest libhelloworld.so)
```

6.构建工程

```go
1.进入目录build
2.执行命令: cmake ..
3.执行命令: make
4.执行命令: make install
```

# 4. 指定编译器和编译选项

1.`CMAKE_C_COMPILER`: 指定C编译器
 2.`CMAKE_CXX_COMPILTER`:指定C++编译器
 3.`CMAKE_C_FLAGS`: 指定C编译选项
 4.`CMAKE_CXX_FLAGS`:指定C++编译选项
 5.`EXECUTABLE_OUTPUT_PATH`: 指定执行文件存放目录
 6.`LIBRARY_OUTPUT_PATH`: 指定库文件存放目录
 7.`CMAKE_BUILD_TYPE`:指定build类型[Debug|Release]
 8.`BUILD_SHARED_LIBS`: 指定默认库编译方式[OFF|ON]

**上述内部变量使用说明**:
 1.`CMakeLists.txt`文件上使用`set`命令
 2.cmake 命令中指定，如: `cmake -DCMAKE_C_COMPILER=gcc`

`add_definitions`:添加编译参数

# 5. 配置编译模块

# 6. CMake 常用变量和语句

1.`include_directories`:指定头文件搜索路径
 2.`link_directories`:指定库文件搜索路径
 3.`add_subdirectory`:添加子目录
 4.`target_link_libraries`:指定文件链接库文件
