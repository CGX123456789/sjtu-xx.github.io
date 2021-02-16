---
title: fortran语法
toc: true
date: 2020-04-04 20:30:05
tags:
categories:
- Fortran
---

把fortran的语法简单总结一下,方便以后查阅
<!--more-->
# 基本语法
```fortran
program program_name
implicit none      

! type declaration statements      
! executable statements  

end program program_name
```

## 加法fortran程序
```fortran
program addNumbers

! This simple program adds two numbers     
   implicit none
   
! Type declarations
   real :: a, b, result 
   
! Executable statements 
   a = 12.0
   b = 15.0
   result = a + b
   print *, 'The total is ', result                   
   
end program addNumbers
```

- 所有Fortran程序start关键字程序和end关键字结束程序，然后是该程序的名称。		
- 隐无语句允许编译器检查所有的变量类型是正确声明。必须始终使用无隐在每个程序的开始。
- 在Fortran语言注释开始使用感叹号（！），因为在这之后的所有字符（除字符串）被编译器忽略。
- print*命令在屏幕上显示数据。
- 代码行缩进，是保持一个程序读取一个很好的做法。
- Fortran语言允许大写和小写字母。 Fortran语言是区分大小写的，除了字符串常量。

# 内置数据类型
Fortran语言提供了五种内在数据类型：整型(integer),实型(real),复杂类型(complex，储存复数)，逻辑类型(logical)，字符类型(character)
```fortran
program testing
implicit none
    !4bytes整数
    integer(kind=4)::largeval
    !输出integer允许的最大值
    print *,huge(largeval)

    real::p,q

    !如果不指定长度，默认为1
    character(len=40)::name
    name = "zara ali"
    !name(1:4)得到zara
end program testing
```

# 变量
变量声明`type-specifier :: variable_name`
```fortran
integer :: total  	
real :: average 
complex :: cx  
logical :: done 
character(len=80) :: message ! a string of 80 characters
```

变量赋值
```fortran
total = 20000  
average = 1666.67   
done = .true.   
message = “A big Hello from Tutorials Point” 
cx = (3.0, 5.0) ! cx = 3.0 + 5.0i
```

# 常量
```fortran
!parameter 表示常量
real, parameter :: pi = 3.1415927
```

# 运算符
```fortran
!算术运算符
+,-,*,/,**

!关系运算符
== !等于
/= !不等
>
<
>=
<=

!逻辑运算符
.and.
.or.
.not.
.eqv.
.neqv.
```

# fortran选择决策
## if语句
普通if
```fortran
if (logical expression) then      
   statement  
end if

if (logical expression) then      
   statement(s)  
else
   other_statement(s)
end if

[name:] 
if (logical expression 1) then 
   ! block 1   
else if (logical expression 2) then       
   ! block 2   
else if (logical expression 3) then       
   ! block 3  
else       
   ! block 4   
end if [name]
```

命名if例子：
```fortran
program markGradeA  
implicit none  
   real :: marks
   ! assign marks   
   marks = 90.4
   ! use an if statement to give grade
  
   gr: if (marks > 90.0) then  
   print *, " Grade A"
   end if gr
end program markGradeA
```

# select case结构
```fortran
[name:] select case (expression) 
   case (selector1)          
   ! some statements          
   ... case (selector2)           
   ! other statements           
   ...       
   case default          
   ! more statements          
   ...   
end select [name]

```
也可以指定为一个范围
```fortran
program selectCaseProg
implicit none

   ! local variable declaration
   integer :: marks = 78

   select case (marks)
   
      case (91:100) 
         print*, "Excellent!" 

      case (81:90)
         print*, "Very good!"

      case (71:80) 
         print*, "Well done!" 

      case (61:70)
         print*, "Not bad!" 

      case (41:60)
         print*, "You passed!"  

      case (:40)
         print*, "Better try again!"  

      case default
         print*, "Invalid marks" 
         
   end select
   print*, "Your marks is ", marks
 
end program selectCaseProg
```

# 循环
```fortran
!do循环
do var = start, stop [,step]    
   ! statement(s)
   …
end do

!do while
do while (logical expr) 
   statements
end do
```

- exit语句终止循环或select case语句。
- cycle语句相当于python中的continue
- stop终止program

# fortran字符
## 字符串接
```fortran
program hello
implicit none

   character(len=15) :: surname, firstname 
   character(len=6) :: title 
   character(len=40):: name
   character(len=25)::greetings
   
   title = 'Mr. ' 
   firstname = 'Rowan ' 
   surname = 'Atkinson'
   
   name = title//firstname//surname
   greetings = 'A big hello from Mr. Beans'
   
   print *, 'Here is ', name
   print *, greetings
   
end program hello

! 输出
! Here is Mr.Rowan Atkinson       
! A big hello from Mr.Bean
```

# fortran数组
```fortran
!声明
!创建一个5×5的二维矩阵命名的整数数组
integer,dimension(5,5)::matrix
!声明某些明确的下限
real, dimension(2:6) :: numbers
integer, dimension (-3:2,0:4) :: matrix

!赋值
numbers = (/1.5, 3.2,4.5,0.9,7.2 /)

!索引
array ([lower]:[upper][:stride], ...)
```

# 向量和矩阵乘法
```fortran
dot_product(vector_a,vector_b)
matmul(matrix_a,matrix_b)
```

# 动态数组
动态数组是一种数组，其尺寸在编译时不知道，而是在执行时才已知/确定的。
动态数组的属性使用 allocatable 声明。

`real, dimension (:,:), allocatable :: darray`

示例
```fortran
program dynamic_array 
implicit none 

   !rank is 2, but size not known   
   real, dimension (:,:), allocatable :: darray    
   integer :: s1, s2     
   integer :: i, j     
   
   print*, "Enter the size of the array:"     
   read*, s1, s2      
   
   ! allocate memory      
   allocate ( darray(s1,s2) )      
   
   do i = 1, s1           
      do j = 1, s2                
         darray(i,j) = i*j               
         print*, "darray(",i,",",j,") = ", darray(i,j)           
      end do      
   end do      
   
   deallocate (darray)  
end program dynamic_array
```

## data语句
data 语句可用于初始化多个阵列，或用于阵列部分的初始化。
```fortran
data variable / list / ...
```

示例
```fortran
program dataStatement
implicit none

   integer :: a(5), b(3,3), c(10),i, j
   data a /7,8,9,10,11/ 
   
   data b(1,:) /1,1,1/ 
   data b(2,:)/2,2,2/ 
   data b(3,:)/3,3,3/ 
   data (c(i),i=1,10,2) /4,5,6,7,8/ 
   data (c(i),i=2,10,2)/5*2/
   
   Print *, 'The A array:'
   do j = 1, 5                
      print*, a(j)           
   end do 
   
   Print *, 'The B array:'
   do i = lbound(b,1), ubound(b,1)
      write(*,*) (b(i,j), j = lbound(b,2), ubound(b,2))
   end do

   Print *, 'The C array:' 
   do j = 1, 10                
      print*, c(j)           
   end do      
   
end program dataStatement
```

## where语句
where与numpy.where类似
```fortran
program whereStatement
implicit none

   integer :: a(3,5), i , j
   
   do i = 1,3
      do j = 1, 5                
         a(i,j) = j-i          
      end do 
   end do
   
   Print *, 'The A array:'
   
   do i = lbound(a,1), ubound(a,1)
      write(*,*) (a(i,j), j = lbound(a,2), ubound(a,2))
   end do
   
   where( a<0 ) 
      a = 1 
   elsewhere
      a = 5
   end where
  
   Print *, 'The A array:'
   do i = lbound(a,1), ubound(a,1)
      write(*,*) (a(i,j), j = lbound(a,2), ubound(a,2))
   end do   
   
end program whereStatement
```

# 导出数据类型(结构)
## 定义
声明格式
```fortran
type type_name      
   declarations
end type
```
示例
```fortran
type Books
   character(len=50) :: title
   character(len=50) :: author
   character(len=150) :: subject
   integer :: book_id
end type Books
```

## 访问结构成员
`type(Books) :: book1`
```fortran
book1%title = "C Programming"
book1%author = "Nuha Ali"
book1%subject = "C Programming Tutorial"
book1%book_id = 6495407
```

## 结构数组
`type(Books), dimension(2) :: list`

# 指针
## 声明
```fortran
integer, pointer :: p1 ! pointer to integer  
real, pointer, dimension (:) :: pra ! pointer to 1-dim real array  
real, pointer, dimension (:,:) :: pra2 ! pointer to 2-dim real array
```

## 分配指针空间
```fortran
program pointerExample
implicit none

   integer, pointer :: p1
   allocate(p1)
   
   p1 = 1
   Print *, p1
   
   p1 = p1 + 4
   Print *, p1
   
end program pointerExample
```

目标是另一个正态变量，空间预留给它。目标变量必须与目标属性进行声明。
一个指针变量使用的关联操作符使目标变量相关联(=>)。
```fortran
program pointerExample
implicit none

   integer, pointer :: p1
   integer, target :: t1 
   
   p1=>t1
   p1 = 1
   
   Print *, p1
   Print *, t1
   
   p1 = p1 + 4
   
   Print *, p1
   Print *, t1
   
   t1 = 8
   
   Print *, p1
   Print *, t1
   
end program pointerExample
```

# 基本输入输出
我们可以使用打印print*语句，以及读取键盘使用read*语句，并显示数据输出到屏幕上。这种形式的输入输出是自由格式的I/O，它被称为列表控制的输入输出。

```fortran
read(*,*) item1, item2, item3...
print *, item1, item2, item3
write(*,*) item1, item2, item3...
```

## 格式化
```fortran
read fmt, variable_list 
print fmt, variable_list 
write fmt, variable_list 
```

# 文件输入输出
OPEN, WRITE, READ 和 CLOSE语句可以实现这一目标。

`open (list-of-specifiers)`

`close ([UNIT=]u[,IOSTAT=ios,ERR=err,STATUS=sta])`

`read ([UNIT=]u, [FMT=]fmt, IOSTAT=ios, ERR=err, END=s)`

`write([UNIT=]u, [FMT=]fmt, IOSTAT=ios, ERR=err, END=s)`

|修辞符|描述|
|---------|----------|
|[UNIT=] u |单元数u可以是任何数量范围内9-99，它表明该文件，可以选择任何号码，但在程序中每一个打开的文件必须有一个唯一的数字|
|IOSTAT= ios 		|它是在I/O状态标识符和应为整数的变量。如果打开的语句是成功，则返回IOS值为零，否则为一个非零值。|
|ERR = err 		 			|它是一个标签到该控制跳以防有错误。|
|FILE = fname 		 			|文件名，一个字符串。|
|STATUS = sta 		 			|它示出了该文件的先前状态。一个字符串，可以有三个值NEW, OLD 或 SCRATCH。一个临时文件被创建和删除，当关闭或程序结束。|
|ACCESS = acc 		 			|它是该文件的访问模式。可以有两个值SEQUENTIAL 或 DIRECT。默认值是SEQUENTIAL。|
|FORM= frm|它给该文件的格式的状态。可以有FORMATTED 或UNFORMATTED两个值。默认值是UNFORMATTED|
|RECL = rl|它指定的每个记录中的一个直接访问文件的长度。|

# fortran过程
包括函数和子程序
## 函数
函数是返回一个数量的过程。函数不修改其参数。
返回数值被称为函数值，并将其表示为函数名。

**语法**
```fortran
function name(arg1, arg2, ....)  
   [declarations, including those for the arguments]   
   [executable statements] 
end function [name]
```

**示例**
```fortran
program calling_func

   real :: a
   a = area_of_circle(2.0) 
   
   Print *, "The area of a circle with radius 2.0 is"
   Print *, a
   
end program calling_func


! this function computes the area of a circle with radius r  
function area_of_circle (r)  

! function result     
implicit none      

   ! dummy arguments        
   real :: area_of_circle   
   
   ! local variables 
   real :: r     
   real :: pi
   
   pi = 4 * atan (1.0)     
   area_of_circle = pi * r**2  
   
end function area_of_circle
```

## 子程序
子程序没有返回值，可以修改其参数

**语法**
```fortran
subroutine name(arg1, arg2, ....)    
   [declarations, including those for the arguments]    
   [executable statements]  
end subroutine [name]
```

**调用子程序**
使用call来调用子程序
```fortran
program calling_func
implicit none

   real :: a, b
   a = 2.0
   b = 3.0
   
   Print *, "Before calling swap"
   Print *, "a = ", a
   Print *, "b = ", b
   
   call swap(a, b)
   
   Print *, "After calling swap"
   Print *, "a = ", a
   Print *, "b = ", b
   
end program calling_func


subroutine swap(x, y) 
implicit none

   real :: x, y, temp   
   
   temp = x  
   x = y 
   y = temp  
   
end subroutine swap
```

## 指定参数意图
intent(in) 用作输入
intent(out) 用作输出，他们将被覆盖
intent(inout) 参数都使用和覆盖
```fortran
program calling_func
implicit none

   real :: x, y, z, disc
   
   x= 1.0
   y = 5.0
   z = 2.0
   
   call intent_example(x, y, z, disc)
   
   Print *, "The value of the discriminant is"
   Print *, disc
   
end program calling_func


subroutine intent_example (a, b, c, d)     
implicit none     

   ! dummy arguments      
   real, intent (in) :: a     
   real, intent (in) :: b      
   real, intent (in) :: c    
   real, intent (out) :: d   
   
   d = b * b - 4.0 * a * c 
   
end subroutine intent_example
```

## 递归
当一个函数被递归使用，则 result 选项要被使用。
```fortran
program calling_func
implicit none

   integer :: i, f
   i = 15
   
   Print *, "The value of factorial 15 is"
   f = myfactorial(15)
   Print *, f
   
end program calling_func

! computes the factorial of n (n!)      
recursive function myfactorial (n) result (fac)  
! function result     
implicit none     

   ! dummy arguments     
   integer :: fac     
   integer, intent (in) :: n     
   
   select case (n)         
      case (0:1)         
         fac = 1         
      case default    
         fac = n * myfactorial (n-1)  
   end select 
   
end function myfactorial
```

## 内部过程
当一个过程被包含在程序中，它被称为程序的内部程序。
```
program mainprog  
implicit none 

   real :: a, b 
   a = 2.0
   b = 3.0
   
   Print *, "Before calling swap"
   Print *, "a = ", a
   Print *, "b = ", b
   
   call swap(a, b)
   
   Print *, "After calling swap"
   Print *, "a = ", a
   Print *, "b = ", b
 
contains   
   subroutine swap(x, y)     
      real :: x, y, temp      
      temp = x 
      x = y  
      y = temp   
   end subroutine swap 
   
end program mainprog 
```

# 模块
模块用于：
- 包装子程序，数据和接口块。
- 定义，可以使用多于一个常规全局数据。
- 声明可以选择的任何程序内提供的变量。
- 导入整个模块，可使用在另一个程序或子程序。

**使用模块**
`use name`

```fortran
module constants  
implicit none 

   real, parameter :: pi = 3.1415926536  
   real, parameter :: e = 2.7182818285 
   
contains      
   subroutine show_consts()          
      print*, "Pi = ", pi          
      print*,  "e = ", e     
   end subroutine show_consts 
   
end module constants 


program module_example     
use constants      
implicit none     

   real :: x, ePowerx, area, radius 
   x = 2.0
   radius = 7.0
   ePowerx = e ** x
   area = pi * radius**2     
   
   call show_consts() 
   
   print*, "e raised to the power of 2.0 = ", ePowerx
   print*, "Area of a circle with radius 7.0 = ", area  
   
end program module_example
```

缺省情况下，在一个模块中的所有的变量和子程序被提供给正在使用的模块代码，通过 use 语句声明。

但是，可以控制模块代码中使用的private 和 public 属性的访问性。当声明一些变量或子程序为私有，这是不可以用在模块之外使用。

# common，module共享数据
## common
在新书写的代码里，避免使用 COMMON 语句，而使用 Module 语句代替它。
```fortran
COMMON /a/b, /c/d, e, f
!表示在公用区a中有变量（或者数组）b，在公用区c中有d, e, f 。
integer b,d,e,f
!在使用公用区变量前，必须再次说明类型，非常容易发生错误
```

## module
save 属性表明这些变量会被保存起来，以便在不同的程序单元间保持同样的值。
（虽然语法未明确指出，但所有的编译器都默许 Module 中的变量具有 save 属性，因此，很多时候 save 也可以忽略不写）
```fortran
Module modname
  Implicit None
  !// 明确变量类型，顺序无关
  Integer , save :: a = 1 , b = 2  
End Module modname

program www_fcode_cn
  use modname !// 无需，也不能再定义 a b
  Implicit None 
  write(*,*) b , a !// 输出 2，1 正确
  a = 3
  b = 4
  !// 按变量名对应，因此倒序输出，其值也倒序
  call Sub()
End Program www_fcode_cn

Subroutine Sub()
  use modname !// 无需，也不能再定义 a b
  Implicit None
  write(*,*) a , b !// 输出 3，4 正确
End Subroutine Sub
```