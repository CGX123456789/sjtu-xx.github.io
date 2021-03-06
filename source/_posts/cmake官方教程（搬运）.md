---
title: cmake官方教程（搬运）
toc: true
date: 2019-11-26 11:14:47
tags: [c,c++,cmake]
categories: 
- C++
- 基础
---
[原链接 ref](https://www.jianshu.com/p/6df3857462cd)

# 起点（Step 1）

参看下面的CMakeLists.txt文件，最简单的一个工程需要有一个这样的cmake文件，一共就这么两行

```css
  cmake_minimum_required (VERSION 2.6)
  project (Tutorial) 
  add_executable (Tutorial tutorial.cxx)
```
<!--more-->
注意到这里都是用的小写的命令，在cmake文件里面大小写不严格区分，都可以用。
 add_executable添加一个可编译的目标到工程里面

```css
add_executable(<name> [WIN32] [MACOSX_BUNDLE]
               [EXCLUDE_FROM_ALL]
               source1 [source2 ...])
```

- name: 工程所要构建的目标名称
- WIN32/..: 目标app运行的平台
- source1：构建目标App的源文件

tutorial.cxx的源代码计算一个数的平方根。第一版的代码很简单，参看如下：

```cpp
  // A simple program that computes the square root of a number
  #include <stdio.h>
  #include <stdlib.h>
  #include <math.h>

  int main (int argc, char *argv[])
  {
    if (argc < 2)
    {
      fprintf(stdout, "Uage: %s number\n", argv[0]);
      return 1;
    }
    double inputValue = atof(argv[1]);
    double outputValue = sqrt(inputValue);
    fprintf(stdout, "The square root of %g is %g\n",
              inputValue, outputValue);
    return 0;
  }
```

编写完上面两个文件以后，在根目录下新建一个build目录
 .
 ├── build
 ├── CMakeLists.txt
 └── tutorial.cxx
 然后运行如下命令

```ruby
  $ cd buld
  $ CMake ..
  $ make
```

- CMake会自动加载上级目录里面的CMakeLists.txt文件，编译所需的文件都会生成在build目录下
- make之后会生成可执行文件Tutorial

## 添加一个版本号和配置的头文件

修改CMakeList.txt来添加version number：

```bash
cmake_minimum_required (VERSION 2.6)
project (Tutorial)
# 版本号.
set (Tutorial_VERSION_MAJOR 1)
set (Tutorial_VERSION_MINOR 0)
 
# 配置一个头文件来传递一些CMake设置到源代码
configure_file (
  "${PROJECT_SOURCE_DIR}/TutorialConfig.h.in"
  "${PROJECT_BINARY_DIR}/TutorialConfig.h"
  )
 
# 添加TutorialConfig.h的路径到头文件的搜索路径
include_directories("${PROJECT_BINARY_DIR}")
 
# 添加目标可执行文件
add_executable(Tutorial tutorial.cxx)
```

configure_file会拷贝一个文件到另一个目录并修改文件内容:

```css
configure_file(<input> <output>
               [COPYONLY] [ESCAPE_QUOTES] [@ONLY]
               [NEWLINE_STYLE [UNIX|DOS|WIN32|LF|CRLF] ])
```

cmake会自动定义两个变量

- ${PROJECT_SOURCE_DIR}：  当前工程最上层的目录
- ${PROJECT_BINARY_DIR}：　当前工程的构建目录（本例中新建的build目录）

在这个例子里，configure_file命令的源文件是TutorialConfig.h.in，手动创建这个文件：

```cpp
// Tutorial工程的配置选项和设置
#define Tutorial_VERSION_MAJOR @Tutorial_VERSION_MAJOR@
#define Tutorial_VERSION_MINOR @Tutorial_VERSION_MINOR@
```

调用CMake的时候会在build目录下新的头文件，并且使用CMakeList.txt中定义的值来替换@Tutorial_VERSION_MAJOR@和@Tutorial_VERSION_MINOR@这两个变量。
 下一步要在源文件tutorial.cxx中包含这个配置的头文件，就能使用这些版本信息了。

```cpp
  // A simple program that computes the square root of a number
  #include <stdio.h>
  #include <stdlib.h>
  #include <math.h>
  #include "TutorialConfig.h"

  int main (int argc, char *argv[])
  {
    if (argc < 2)
    {
      fprintf(stdout, "%s Version %d.%d\n", 
                argv[0],
                Tutorial_VERSION_MAJOR,
                Tutorial_VERSION_MINOR)
      fprintf(stdout, "Uage: %s number\n", argv[0]);
      return 1;
    }
    double inputValue = atof(argv[1]);
    double outputValue = sqrt(inputValue);
    fprintf(stdout, "The square root of %g is %g\n",
              inputValue, outputValue);
    return 0;
  }
```

运行如下命令查看结果

```ruby
  $ cmake ..
  $ make
  $ ./Tutorial

```

这个时候控制台会打印出来版本号

```tsx
./Tutorial Version 1.0
Uage: ./Tutorial number

```

# 添加Library（Step 2）

现在我们尝试添加一个library到我们的工程。这个lib提供一个自定义的计算平方根的函数，用来替换编译器提供的函数。
 lib的源文件放到一个叫MathFunctions的子目录中，在目录下新建CMakeList.txt文件，添加如下的一行

```css
 add_library(MathFunctions mysqrt.cxx)

```

源文件mysqrt.cxx包含一个函数mysqrt用于计算平方根。代码如下

```cpp
#include "MathFunctions.h"
#include <stdio.h>

// a hack square root calculation using simple operations
double mysqrt(double x)
{
  if (x <= 0) {
    return 0;
  }

  double result;
  double delta;
  result = x;

  // do ten iterations
  int i;
  for (i = 0; i < 10; ++i) {
    if (result <= 0) {
      result = 0.1;
    }
    delta = x - (result * result);
    result = result + 0.5 * delta / result;
    fprintf(stdout, "Computing sqrt of %g to be %g\n", x, result);
  }
  return result;
}

```

还需要添加一个头文件MathFunctions.h以提供接口给main函数调用

```cpp
double mysqrt(double x);

```

现在的目录结构
 .
 ├── build
 ├── CMakeLists.txt
 ├── MathFunctions
 │   ├── CMakeLists.txt
 │   ├── MathFunctions.h
 │   └── mysqrt.cxx
 ├── TutorialConfig.h.in
 └── tutorial.cxx

CMakeLists.txt文件需要相应做如下改动

- 添加一行add_subdirectory来保证新加的library在工程构建过程中被编译。
- 添加新的头文件搜索路径MathFunction/MathFunctions.h。
- 添加新的library到executable。

CMakeList.txt的最后几行变成了这样：

```bash
include_directories ("${PROJECT_SOURCE_DIR}/MathFunctions")
add_subdirectory (MathFunctions)

# 添加executable
add_executable (Tutorial tutorial.cxx)
target_link_libraries (Turorial MathFunctions)

```

最后要修改tutorial.cxx文件来调用自定义的mysqrt函数
 最后编译一下试试

```ruby
  $ cmake ..
  $ make
  $ ./Tutorial

```

看一下编译的log

```csharp
Scanning dependencies of target MathFunctions
[ 50%] Building CXX object MathFunctions/CMakeFiles/MathFunctions.dir/mysqrt.cxx.o
Linking CXX static library libMathFunctions.a
[ 50%] Built target MathFunctions
Scanning dependencies of target Tutorial
[100%] Building CXX object CMakeFiles/Tutorial.dir/tutorial.cxx.o
Linking CXX executable Tutorial
[100%] Built target Tutorial

```

这里编译生成了新的库libMathFunctions.a

## 现在我们考虑把MathFunctions库配置成可选的

首先在最顶层的CMakeList.txt文件添加一个option

```bash
#需要用自定义的数学函数么？
option (USE_MYMATH
            "Use tutorial provided math implementation" ON)

```

运行ccmake ..会跳出来配置的GUI，在GUi中会看到新添加的这个选项，用户可以根据需要进行修改。
 下一个改变是依据配置来判断是否编译和链接MathFunctions库。按照如下所示的修改CMakeList.txt的末尾几行：

```bash
# add the MathFunctions library?
if (USE_MYMATH)
  include_directories ("${PROJECT_SOURCE_DIR}/MathFunctions")
  add_subdirectory (MathFunctions)
  SET (EXTRA_LIBS ${EXTRA_LIBS} MathFunctions)
endif (USE_MYMATH)

# add executable
add_executable (Tutorial tutorial.cxx)
target_link_libraries (Turorial ${EXTRA_LIBS})

```

这个例子里还是用了变量（EXTRA_LIBS）来收集后面link进可执行文件的时候任意可选的库。这是一个常用的方法，在工程非常大有很多optional的组件的时候，可以让这个编译文件保持干净。
 代码的修改就更直接了(用宏定义隔离开)：

```cpp
// A simple program that computes the square root of a number
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include "TutorialConfig.h"
#ifdef USE_MYMATH
#include "MathFunctions.h"
#endif
 
int main (int argc, char *argv[])
{
  if (argc < 2)
    {
    fprintf(stdout,"%s Version %d.%d\n", argv[0],
            Tutorial_VERSION_MAJOR,
            Tutorial_VERSION_MINOR);
    fprintf(stdout,"Usage: %s number\n",argv[0]);
    return 1;
    }
 
  double inputValue = atof(argv[1]);
 
#ifdef USE_MYMATH
  double outputValue = mysqrt(inputValue);
#else
  double outputValue = sqrt(inputValue);
#endif
 
  fprintf(stdout,"The square root of %g is %g\n",
          inputValue, outputValue);
  return 0;
}

```

在源代码里面同样可以使用USE_MYMATH，只要在TutorialConfig.h.in里面添加一行

```bash
#cmakedefine USE_MYMATH

```

# 安装测试 (Step 3)

下一步我们会添加install规则和testing到工程。install规则非常直接。对于MathFunctions库，我们通过在MathFunctions的CMakeList文件中添加如下两行来安装库和头文件。

```css
install (TARGETS MathFunctions DESTINATION bin)
install(FILES MathFunctions.h DESTINATION include)

```

对于应用程序，为了安装executable和配置头文件，需要在最上层的CMakeList.txt文件中添加下面几行

```bash
# add the install targets
install (TARGETS Tutorial DESTINATION bin)
install (FILES "${PROJECT_BINARY_DIR}/TutorialConfig.h" DESTINATION include)

```

到了这里你就可以构建整个tutorial了，通过命令make install，系统会自动安装对应的头文件，库以及可执行文件。CMake变量CMAKE_INSTALL_PREFIX用来指定这些文件需要安装到哪个根目录。
 添加测试用例也很直接，只要在最上层的CMakeList.txt文件添加一系列的基础测试来验证应用程序是否正常工作。

```php
include(CTest)

# does the application run
add_test (TutorialRuns Tutorial 25)

# does it sqrt of 25
add_test (TutorialComp25 Tutorial 25)
set_tests_properties (TutorialComp25 PROPERTIES PASS_REGULAR_EXPRESSION "25 is 5")

# does it handle negative numbers
add_test (TutorialNegative Tutorial -25)
set_tests_properties (TutorialNegative PROPERTIES PASS_REGULAR_EXPRESSION "-25 is 0")

# does it handle small numbers
add_test (TutorialSmall Tutorial 0.0001)
set_tests_properties (TutorialSmall PROPERTIES PASS_REGULAR_EXPRESSION "0.0001 is 0.01")

# does the usage message work?
add_test (TutorialUsage Tutorial)
set_tests_properties (TutorialUsage PROPERTIES PASS_REGULAR_EXPRESSION "Usage:.*number")

```

编译完成后可以通过在命令行运行ctest来执行这些测试用例。如果你希望添加很多测试用例来测试不同的输入值，这个时候推荐你创建一个宏，这样添加新的case会更轻松：

```bash
#define a macro to simplify adding tests, then use it
macro (do_test arg result)
  add_test (TutorialComp${arg} Tutorial ${arg})
  set_tests_properties (TutorialComp${arg}
    PROPERTIES PASS_REGULAR_EXPRESSION ${result})
endmacro (do_test)
 
# do a bunch of result based tests
do_test (25 "25 is 5")
do_test (-25 "-25 is 0")

```

每次调用do_test，都会添加一个新的test case到工程。

# 添加系统回顾 (Step 4)

下一步我们考虑在工程中添加一些代码，这些代码会依赖的某些特性在运行的目标平台上可能没有。比如说，我们添加了一些代码，这些代码需要用到log和exp函数，但某些目标平台上可能没有这些库函数。如果平台有log函数那么我们就是用log来计算平方根，我们首先通过CheckFunctionExists.cmake来测试一下是否有这些函数，在最上层的CMakeList文件中添加如下内容

```tsx
# does this system provide the log and exp functions?
include (CheckFunctionExists)
check_function_exists (log HAVE_LOG)
check_function_exists (exp HAVE_EXP)

```

Next we modify the TutorialConfig.h.in to define those values if CMake found them on the platform as follows:
 下一步，如果CMake发现平台有我们需要的这些函数，则需要修改TutorialConfig.h.in来定义这些值

```bash
// does the platform provide exp and log functions?
#cmakedefine HAVE_LOG
#cmakedefine HAVE_EXP

```

有一点很重要，就是log和exp的测试工作需要在配置TutorialConfig.h前完成。最后在mysqrt函数中我们可以提供一个可选的实现方式：

```cpp
// if we have both log and exp then use them
#if defined (HAVE_LOG) && defined (HAVE_EXP)
  result = exp(log(x)*0.5);
#else // otherwise use an iterative approach
  . . .

```

# 添加生成文件和生成器 (Step 5)

这一节中我们会演示一下怎么添加一个生成的源文件到引用程序的构建过程中。例如说，我们希望在构建过程中创建一个预先计算好的平方根表，然后把这个表格编译进我们的应用程序。首先我们需要一个能生成这张表的程序。在MathFunctions子目录中，定义一个新的源文件MakeTable.cxx:

```cpp
// A simple program that builds a sqrt table 
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
 
int main (int argc, char *argv[])
{
  int i;
  double result;
 
  // make sure we have enough arguments
  if (argc < 2)
    {
    return 1;
    }
  
  // open the output file
  FILE *fout = fopen(argv[1],"w");
  if (!fout)
    {
    return 1;
    }
  
  // create a source file with a table of square roots
  fprintf(fout,"double sqrtTable[] = {\n");
  for (i = 0; i < 10; ++i)
    {
    result = sqrt(static_cast<double>(i));
    fprintf(fout,"%g,\n",result);
    }
 
  // close the table with a zero
  fprintf(fout,"0};\n");
  fclose(fout);
  return 0;
}

```

注意到这里的需要传递正确的输出文件给app，然后才会生成table。下一步是在MathFunctions的CMakeList.txt添加相应的命令来编译生成可执行文件MakeTable，然后在编译过程中运行这个程序。如下所示的添加一些命令：

```bash
# first we add the executable that generates the table
add_executable(MakeTable MakeTable.cxx)
 
# add the command to generate the source code
add_custom_command (
  OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/Table.h
  COMMAND MakeTable ${CMAKE_CURRENT_BINARY_DIR}/Table.h
  DEPENDS MakeTable
  )
 
# add the binary tree directory to the search path for 
# include files
include_directories( ${CMAKE_CURRENT_BINARY_DIR} )
 
# add the main library
add_library(MathFunctions mysqrt.cxx ${CMAKE_CURRENT_BINARY_DIR}/Table.h  )

```

- 首先添加可执行的MakeTable。
- 然后我们添加一个用户命令指定怎么通过允许MakeTable来生成Table.h。
- 下一步需要让CMAKE知道mysqrt.cxx依赖生成的Table.h。把生成的Table.h添加到MathFunctions库的资源列表中。
- 还需要添加当前的bin的目录添加到include的list中，这样mysqrt.cxx编译时候可以找到Table.h。
- 最后编译包含Table.h的mysqrt.cxx来生成MathFunctions库
   到这儿最上层的CMakeList.txt文件就如下面所示：

```bash
cmake_minimum_required (VERSION 2.6)
project (Tutorial)

include(CTest) 
# The version number.
set (Tutorial_VERSION_MAJOR 1)
set (Tutorial_VERSION_MINOR 0) 

# does this system provide the log and exp functions?
include (${CMAKE_ROOT}/Modules/CheckFunctionExists.cmake) check_function_exists (log HAVE_LOG)
check_function_exists (exp HAVE_EXP) 

# should we use our own math functions
option(USE_MYMATH "Use tutorial provided math implementation" ON) 

# configure a header file to pass some of the CMake settings
# to the source code
configure_file ( "${PROJECT_SOURCE_DIR}/TutorialConfig.h.in" "${PROJECT_BINARY_DIR}/TutorialConfig.h" ) 

# add the binary tree to the search path for include files
# so that we will find TutorialConfig.h
include_directories ("${PROJECT_BINARY_DIR}")

# add the MathFunctions library?
if (USE_MYMATH) 
  include_directories ("${PROJECT_SOURCE_DIR}/MathFunctions")
  add_subdirectory (MathFunctions) 
  set (EXTRA_LIBS ${EXTRA_LIBS} MathFunctions)
endif (USE_MYMATH) 

# add the executable
add_executable (Tutorial tutorial.cxx)
target_link_libraries (Tutorial ${EXTRA_LIBS}) 

# add the install targets
install (TARGETS Tutorial DESTINATION bin)
install (FILES "${PROJECT_BINARY_DIR}/TutorialConfig.h" DESTINATION include) 

# does the application run
add_test (TutorialRuns Tutorial 25) 
# does the usage message work?
add_test (TutorialUsage Tutorial)
set_tests_properties (TutorialUsage PROPERTIES PASS_REGULAR_EXPRESSION "Usage:.*number" ) 
#define a macro to simplify adding tests
macro (do_test arg result) 
  add_test (TutorialComp${arg} Tutorial ${arg}) 
  set_tests_properties (TutorialComp${arg} PROPERTIES PASS_REGULAR_EXPRESSION ${result} )
endmacro (do_test) 

# do a bunch of result based tests
do_test (4 "4 is 2")
do_test (9 "9 is 3")
do_test (5 "5 is 2.236")
do_test (7 "7 is 2.645")
do_test (25 "25 is 5")
do_test (-25 "-25 is 0")
do_test (0.0001 "0.0001 is 0.01")
```

TutorialConfig.h.in 文件如下:

```cpp
// the configured options and settings for Tutorial
#define Tutorial_VERSION_MAJOR @Tutorial_VERSION_MAJOR@
#define Tutorial_VERSION_MINOR @Tutorial_VERSION_MINOR@
#cmakedefine USE_MYMATH 
// does the platform provide exp and log functions?
#cmakedefine HAVE_LOG
#cmakedefine HAVE_EXP
```

MathFunctions的文件CMakeLists.txt如下:

```ruby
# first we add the executable that generates the table
add_executable(MakeTable MakeTable.cxx)
# add the command to generate the source code
add_custom_command ( OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/Table.h DEPENDS MakeTable COMMAND MakeTable ${CMAKE_CURRENT_BINARY_DIR}/Table.h )

# add the binary tree directory to the search path 
# for include files
include_directories( ${CMAKE_CURRENT_BINARY_DIR} ) 

# add the main library
add_library(MathFunctions mysqrt.cxx ${CMAKE_CURRENT_BINARY_DIR}/Table.h) 
install (TARGETS MathFunctions DESTINATION bin)
install (FILES MathFunctions.h DESTINATION include)
```


