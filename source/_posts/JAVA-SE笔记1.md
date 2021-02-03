---
title: JAVA_SE笔记1
toc: true
mathjax: true
date: 2020-12-28 19:38:51
tags: JAVA
categories:
- JAVA
- 基础
---

学习廖雪峰的JAVA入门教程的笔记第一部分。

准备工作、JAVA面向对象编程、JAVA核心类、异常处理
<!--more-->

# 准备工作

## 第一个JAVA程序

```java
public class Hello{
    public static void main(String[] args){
        System.out.println("Hello world");
    }
}
```

执行代码

```
// 编译
javac Hello.java
// 执行
java Hello
```

## JAVA程序基础

### 变量和数据类型

```java
/*
  基本类型及字节数：
  整数类型: byte(1),short(2),int(4),long(8)
  浮点数类型：float(4)，double(8)
  字符类型:char(2)
  布尔类型：boolean(没有明确规定，通常JVM会将其处理为4字节的整数)
*/
int a = 1;

float f1 = 3.14f;
float f2 = 3.14e38f; // 科学计数法表示的3.14x10^38
double d = 1.79e308;
double d2 = -1.79e308;
double d3 = 4.9e-324; // 科学计数法表示的4.9x10^-324

boolean b1 = true;
boolean b2 = false;
boolean isGreater = 5 > 3; // 计算结果为true
int age = 12;
boolean isAdult = age >= 18; // 计算结果为false

char a = 'A';
char zh = '中';

/*
  引用类型
*/
String s = "hello";

```

定义变量的时候，如果加上`final`修饰符，这个变量就变成了常量：
`final double PI = 3.14; // PI是一个常量`

### 整数运算

```java
int x = 12345 / 67; // 184,商
int y = 12345 % 67; // 12345÷67的余数是17

n += 100; // 3409, 相当于 n = n + 100;
n -= 100; // 3309, 相当于 n = n - 100;

int n = 3300;
n++; // 3301, 相当于 n = n + 1;
n--; // 3300, 相当于 n = n - 1;

int n = 7;       // 00000000 00000000 00000000 00000111 = 7
int a = n << 1;  // 00000000 00000000 00000000 00001110 = 14
int b = n << 2;  // 00000000 00000000 00000000 00011100 = 28
int c = n << 28; // 01110000 00000000 00000000 00000000 = 1879048192
int d = n << 29; // 11100000 00000000 00000000 00000000 = -536870912

//如果对一个负数进行右移，最高位的1不动，结果仍然是一个负数：
int n = -536870912;
int a = n >> 1;  // 11110000 00000000 00000000 00000000 = -268435456
int b = n >> 2;  // 11111000 00000000 00000000 00000000 = -134217728
int c = n >> 28; // 11111111 11111111 11111111 11111110 = -2
int d = n >> 29; // 11111111 11111111 11111111 11111111 = -1

//无符号的右移运算
int n = -536870912;
int a = n >>> 1;  // 01110000 00000000 00000000 00000000 = 1879048192
int b = n >>> 2;  // 00111000 00000000 00000000 00000000 = 939524096
int c = n >>> 29; // 00000000 00000000 00000000 00000111 = 7
int d = n >>> 31; // 00000000 00000000 00000000 00000001 = 1
//对byte和short类型进行移位时，会首先转换为int再进行位移。

// 位运算
n = 0 & 0; // 0
n = 0 | 1; // 1
n = ~0; // 1
n = 0 ^ 1; // 1
```

> 如果参与运算的两个数类型不一致，那么计算结果为较大类型的整型。

### 浮点数运算

```java
// 比较x和y是否相等，先计算其差的绝对值:
double r = Math.abs(x - y);
// 再判断绝对值是否足够小:
if (r < 0.00001) {
    // 可以认为相等
} else {
    // 不相等
}

// 在一个复杂的四则运算中，两个整数的运算不会出现自动提升
double d = 1.2 + 24 / 5; // 5.2

// 强制转型
int n1 = (int) 12.3; // 12
int n2 = (int) 12.7; // 12
int n2 = (int) -12.7; // -12
int n3 = (int) (12.7 + 0.5); // 13
int n4 = (int) 1.2e20; // 2147483647
```

### 布尔运算

布尔运算是一种关系运算，包括以下几类：

- 比较运算符：`>`，`>=`，`<`，`<=`，`==`，`!=`
- 与运算 `&&`
- 或运算 `||`
- 非运算 `!`

**三元运算符**

三元运算符`b ? x : y`

### 字符和字符串

#### 字符类型

```java
char c1 = 'A';
char c2 = '中';

int n1 = 'A'; // 字母“A”的Unicodde编码是65
int n2 = '中'; // 汉字“中”的Unicode编码是20013
// 注意是十六进制:
char c3 = '\u0041'; // 'A'，因为十六进制0041 = 十进制65
char c4 = '\u4e2d'; // '中'，因为十六进制4e2d = 十进制20013
```

#### 字符串类型

```java
String s = ""; // 空字符串，包含0个字符
String s1 = "A"; // 包含一个字符
String s2 = "ABC"; // 包含3个字符
String s3 = "中文 ABC"; // 包含6个字符，其中有一个空格


String s = "abc\"xyz"; // 包含7个字符: a, b, c, ", x, y, z
```

常见的转义字符包括：

- `\"` 表示字符`"`
- `\'` 表示字符`'`
- `\\` 表示字符`\`
- `\n` 表示换行符
- `\r` 表示回车符
- `\t` 表示Tab
- `\u####` 表示一个Unicode编码的字符

Java的编译器对字符串做了特殊照顾，可以使用`+`连接任意字符串和其他数据类型，这样极大地方便了字符串的处理。

从Java 13开始，字符串可以用`"""..."""`表示多行字符串（Text Blocks），多行字符串前面共同的空格会被去掉

```java
String s = """
    SELECT * FROM
    users
    WHERE id > 100
    ORDER BY name DESC
    """;
```

**字符串不可变**

引用类型的变量可以指向一个空值`null`，它表示不存在，即该变量不指向任何对象。

### 数组类型

```java
int[] ns = new int[5];
ns[0] = 68;
ns[1] = 79;
ns[2] = 91;
ns[3] = 85;
ns[4] = 62;

//
int[] ns = { 68, 79, 91, 85, 62 };
System.out.println(ns.length); // 5

int[] ns = new int[] { 68, 79, 91, 85, 62 };



public class Main {
    public static void main(String[] args) {
        String[] names = {"ABC", "XYZ", "zoo"};
        String s = names[1];
        names[1] = "cat";
        System.out.println(s); // s是"XYZ"还是"cat"?: XYZ
    }
}

```

## 流程控制

**格式化输出**

```java
double d = 3.1415926;
System.out.printf("%.2f\n", d); // 显示两位小数3.14
System.out.printf("%.4f\n", d); // 显示4位小数3.1416

```

java中的占位符

| 占位符 | 说明                             |
| :----- | :------------------------------- |
| %d     | 格式化输出整数                   |
| %x     | 格式化输出十六进制整数           |
| %f     | 格式化输出浮点数                 |
| %e     | 格式化输出科学计数法表示的浮点数 |
| %s     | 格式化字符串                     |

连续两个%%表示一个%字符本身。

```java
int n = 12345000;
System.out.printf("n=%d, hex=%08x", n, n); // 注意，两个%占位符必须传入两个数
```
**输入**

```java
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in); // 创建Scanner对象
        System.out.print("Input your name: "); // 打印提示
        String name = scanner.nextLine(); // 读取一行输入并获取字符串
        System.out.print("Input your age: "); // 打印提示
        int age = scanner.nextInt(); // 读取一行输入并获取整数
        System.out.printf("Hi, %s, you are %d\n", name, age); // 格式化输出
    }
}

//scanner.nextLine() / nextInt() / nextDouble() / ...
```

### if判断

```java
if (n < 60) {
    // ...
} else if (n < 90) {
    // ...
} else {
    // ...
}
```



> **判断引用类型是否相等**
>
> 判断引用类型的变量是否相等时，`==`表示“引用是否相等”，或者说，是否指向同一个对象。
>
> 判断引用类型的值是否相等必须使用equals函数。(s1.equals(s2))

### switch多重选择

```java
int option = 1;
switch (option) {
    case 1:
        System.out.println("Selected 1");
        break;
    case 2:
        System.out.println("Selected 2");
        break;
    case 3:
        System.out.println("Selected 3");
        break;
    default:
        System.out.println("No fruit selected");
        break;
}

//java12的新语法
//不需要break
public class Main {
    public static void main(String[] args) {
        String fruit = "apple";
        switch (fruit) {
        case "apple" -> System.out.println("Selected apple");
        case "pear" -> System.out.println("Selected pear");
        case "mango" -> {
            System.out.println("Selected mango");
            System.out.println("Good choice!");
        }
        default -> System.out.println("No fruit selected");
        }
    }
}

//新语法可以直接返回值
public class Main {
    public static void main(String[] args) {
        String fruit = "apple";
        int opt = switch (fruit) {
            case "apple" -> 1;
            case "pear", "mango" -> 2;
            default -> 0;
        }; // 注意赋值语句要以;结束
        System.out.println("opt = " + opt);
    }
}

// yield
// 大多数时候，在switch表达式内部，我们会返回简单的值。
// 但是，如果需要复杂的语句，我们也可以写很多语句，放到{...}里，然后，用yield返回一个值作为switch语句的返回值：
public class Main {
    public static void main(String[] args) {
        String fruit = "orange";
        int opt = switch (fruit) {
            case "apple" -> 1;
            case "pear", "mango" -> 2;
            default -> {
                int code = fruit.hashCode();
                yield code; // switch语句返回值
            }
        };
        System.out.println("opt = " + opt);
    }
}

```

> **编译检查**
>
> 使用IDE时，可以自动检查是否漏写了`break`语句和`default`语句，方法是打开IDE的编译检查。
>
> 在Eclipse中，选择`Preferences` - `Java` - `Compiler` - `Errors/Warnings` - `Potential programming problems`，将以下检查标记为Warning：
>
> - 'switch' is missing 'default' case
> - 'switch' case fall-through
>
> 在Idea中，选择`Preferences` - `Editor` - `Inspections` - `Java` - `Control flow issues`，将以下检查标记为Warning：
>
> - Fallthrough in 'switch' statement
> - 'switch' statement without 'default' branch
>
> 当`switch`语句存在问题时，即可在IDE中获得警告提示。

### 循环

```java
// while
while (条件表达式) {
    循环语句
}

do {
    执行循环语句
} while (条件表达式);

// for
for (初始条件; 循环检测条件; 循环后更新计数器) {
    // 执行语句
}

// for each
for (int n : ns) {
    System.out.println(n);
}
```

在循环过程中，可以使用`break`语句跳出当前循环。

`continue`则是提前结束本次循环，直接继续执行下次循环。

## 数组操作

### 遍历数组

```java
import java.util.Arrays;

int[] ns = { 1, 4, 9, 16, 25 };
for (int i=0; i<ns.length; i++) {
    int n = ns[i];
    System.out.println(n);
}

for (int n : ns) {
    System.out.println(n);
}

System.out.println(Arrays.toString(ns));
```

### 排序

```java
int[] ns = { 28, 12, 89, 73, 65, 18, 96, 50, 8, 36 };
Arrays.sort(ns);
```

**对数组排序实际上修改了数组本身。**

### 多维数组

```java
int[][] ns = {
    { 1, 2, 3, 4 },
    { 5, 6, 7, 8 },
    { 9, 10, 11, 12 }
};

System.out.println(Arrays.deepToString(ns)); //多维数组打印
```

## 命令行参数

```java
public class Main {
    public static void main(String[] args) {
        for (String arg : args) {
            if ("-version".equals(arg)) {
                System.out.println("v 1.0");
                break;
            }
        }
    }
}
```

# JAVA面向对象编程

## 面向对象基础

```java
public class Person{
    public String name;
    public int age;
}
Person ming = new Person();
ming.name = "小明";
ming.age = 12; //对字段赋值
```

- 一个class可以包含多个field（字段）

### 数据封装

```java
public class Person{
    private String name;
    private int age;
    
    // 如果自定义了构造方法，编译器不会生成默认构造方法
    public Person(String name, int age){
        this.name = name;
        this.age = age;
    }
    
    // 调用其他构造方法
    public Person(String name){
        this(name,18);
    }
    
    public void setName(String name){
        this.name = name;
    }
    
    public String getName(){
        return this.name;
    }
    
// 可变参数
// 可变参数用类型...定义，可变参数相当于数组类型：
class Group {
    private String[] names;

    public void setNames(String... names) {
        this.names = names;
    }
}
    
Group g = new Group();
g.setNames("Xiao Ming", "Xiao Hong", "Xiao Jun"); // 传入3个String
g.setNames("Xiao Ming", "Xiao Hong"); // 传入2个String
g.setNames("Xiao Ming"); // 传入1个String
g.setNames(); // 传入0个String
}
```

**构造方法的初始化顺序：**

1. 初始化字段
2. 没有赋值的字段初始化为默认值：基本类型=0；默认类型=null；
3. 再执行构造方法的代码

### 继承和多态

**JAVA只允许class继承自一个类**

```java
public class Person{
    private String name;
    private int age;
    
    public void run(){}
}

public class Student extends Person{
    //person类定义的private字段无法被子类访问；
    //protected字段可以被子类访问
    private int score;
   	public Student(){
        // 第一行必须调用父类的构造方法，如果不写，默认生成不带参数的super();
        super(); // 调用父类的构造方法
    }
    
    //覆写父类方法
    @Override //非必须，让编译器帮忙检查
    public void run(){
        //调用父类被Override的方法
        super.run();
    };
    public void setScore(int score){};
}

//向上转型
Person p = new Person();
Student s = new Student();

Person ps = new student(); //upcasting
Object o1 = p; //upcasting
Object o2 = s; //upcasting

//向下转型
Student s = (Student) p; // ClassCastException

//instanceof操作符判断对象的类型
Person p = new Person();
System.out.println(p instanceof Person); //true
System.out.println(p instanceof Student); //false

Student s = new Student();
System.out.println(s instanceof Person); //true
System.out.println(s instanceof Student); //true

Student n = null;
System.out.println(n instanceof Student); //false
```

```flow
ob=>operation: Object
person=>operation: Person
student=>operation: Student

student->person->ob
```

**多态**

- 多态是指针对某个类型的方法调用，其真正执行的方法取决于运行时实际类型的方法
- 对某个类型调用方法，执行的方法可能是某个子类的方法
- 利用多态，允许添加更多类型的子类实现功能扩展

**Object类定义的几个重要方法**

```java
public class Person{
    ...
    @Overrride
    public String toString(){} //把instance输出为String
    @Override
    public boolean equals(Object o){} //判断两个instance是否逻辑相等
    @Override
    public int hashCode(){} //计算一个instance的哈希值
}
```

**final**

- 用final修饰的方法不能被Override
- 用final修饰的类不能被继承
- 用final修饰的字段在初始化后不能被修改

```java
public class Person{
    public final void setName(String name){}
}

public final class Student extends Person{
    private final int score;
}
```



### 抽象类和接口

**抽象类**

抽象方法所在的类必须声明为抽象类。

抽象类可以

```java
public abstract class Person{
    public abstract void run();
}
```

**接口**

如果一个类没有字段，所有方法全部是抽象方法，就可以把该抽象类改写为接口

因为接口定义的所有方法默认都是`public abstract`的，所以这两个修饰符不需要写出来（写不写效果都一样）。

```java
interface Person {
    void run();
    String getName();
}

class Student implements Person {
    private String name;

    public Student(String name) {
        this.name = name;
    }

    @Override
    public void run() {
        System.out.println(this.name + " run");
    }

    @Override
    public String getName() {
        return this.name;
    }
}

//在Java中，一个类只能继承自另一个类，不能从多个类继承。但是，一个类可以实现多个interface，例如：
class Student implements Person, Hello { // 实现了两个interface
    ...
}
```

**接口继承**

一个`interface`可以继承自另一个`interface`。`interface`继承自`interface`使用`extends`，它相当于扩展了接口的方法。例如：

```
interface Hello {
    void hello();
}

interface Person extends Hello {
    void run();
    String getName();
}
```

**default方法**

在接口中，可以定义`default`方法。例如，把`Person`接口的`run()`方法改为`default`方法：

```java
public class Main {
    public static void main(String[] args) {
        Person p = new Student("Xiao Ming");
        p.run();
    }
}

interface Person {
    String getName();
    default void run() {
        System.out.println(getName() + " run");
    }
}

class Student implements Person {
    private String name;

    public Student(String name) {
        this.name = name;
    }

    public String getName() {
        return this.name;
    }
}

```

`default`方法和抽象类的普通方法是有所不同的。因为`interface`没有字段，`default`方法无法访问字段，而抽象类的普通方法可以访问实例字段。

### 静态字段和静态方法

`static field`类的所有实例共享

```java
class Person {
    public String name;
    public int age;
    // 定义静态字段number:
    public static int number;
}
```

静态方法使用类名就可以调用。在静态方法中无法访问this变量，也无法访问实例字段，只能访问静态字段。

**接口的静态字段**

```java
public interface Person {
    public static final int MALE = 1;
    public static final int FEMALE = 2;
}
//接口的字段必须是public static final，所以可以省略
public interface Person {
    // 编译器会自动加上public statc final:
    int MALE = 1;
    int FEMALE = 2;
}
```

### 包和作用域

```java
//person.java  1
package ming; // 申明包名ming

public class Person {
}

//person.java  2
package mr.jun; // 申明包名mr.jun

public class Arrays {
}
```

> 包没有父子关系。`java.util`和`java.util.zip`是不同的包，两者没有任何继承关系。

**包作用域**

位于同一个包的类，可以访问包作用域的字段和方法。不用`public`、`protected`、`private`修饰的字段和方法就是包作用域。例如，`Person`类定义在`hello`包下面：

```java
package hello;

public class Person {
    // 包作用域:
    void hello() {
        System.out.println("Hello!");
    }
}

package hello;

public class Main {
    public static void main(String[] args) {
        Person p = new Person();
        p.hello(); // 可以调用，因为Main和Person在同一个包
    }
}
```

**import**

```java
//1：写出完整的类名
mr.jun.Arrays arrays = new mr.jun.Arrays();
//2：import
import mr.jun.Arrays;
Arrays arrays = new Arrays();
//3：import static导入类的静态字段和静态方法
import static java.lang.System.*;
```

**作用域**

- 定义为`public`的`class`、`interface`可以被其他任何类访问
- `private`访问权限被限定在`class`的内部，而且与方法声明顺序*无关*。
- `protected`作用于继承关系。定义为`protected`的字段和方法可以被子类访问，以及子类的子类
- Java支持嵌套类，如果一个类内部还定义了嵌套类，那么，嵌套类拥有访问`private`的权限
- 包作用域是指一个类允许访问同一个`package`的没有`public`、`private`修饰的`class`，以及没有`public`、`protected`、`private`修饰的字段和方法。

**final**

- 用`final`修饰`class`可以阻止被继承

- 用`final`修饰`method`可以阻止被子类覆写

- 用`final`修饰`field`可以阻止被重新赋值

- 用`final`修饰局部变量可以阻止被重新赋值

  ```java
  protected void hi(final int t) {
      t = 1; // error!
  }
  ```

> 如果不确定是否需要`public`，就不声明为`public`，即尽可能少地暴露对外的字段和方法。
>
> 把方法定义为`package`权限有助于测试，因为测试类和被测试类只要位于同一个`package`，测试代码就可以访问被测试类的`package`权限方法。
>
> 一个`.java`文件只能包含一个`public`类，但可以包含多个非`public`类。如果有`public`类，文件名必须和`public`类的名字相同。

### 内部类

> 定义在另一个类内部的类

```java
public class Main {
    public static void main(String[] args) {
        Outer outer = new Outer("Nested"); // 实例化一个Outer
        Outer.Inner inner = outer.new Inner(); // 实例化一个Inner
        inner.hello();
    }
}

class Outer {
    private String name;

    Outer(String name) {
        this.name = name;
    }

    class Inner {
        void hello() {
            System.out.println("Hello, " + Outer.this.name);
        }
    }
}
```

**匿名类（Anonymous class）**

```java
//匿名类和Inner Class一样，可以访问Outer Class的private字段和方法。之所以我们要定义匿名类，是因为在这里我们通常不关心类名，比直接定义Inner Class可以少写很多代码。
// Outer.this访问外部类
public class Main {
    public static void main(String[] args) {
        Outer outer = new Outer("Nested");
        outer.asyncHello();
    }
}

class Outer {
    private String name;

    Outer(String name) {
        this.name = name;
    }

    void asyncHello() {
        Runnable r = new Runnable() {
            @Override
            public void run() {
                System.out.println("Hello, " + Outer.this.name);
            }
        };
        new Thread(r).start();
    }
}

//匿名类也可以继承自普通类
//<classname> <name> = new <classname>(){}
import java.util.HashMap;

public class Main {
    public static void main(String[] args) {
        HashMap<String, String> map1 = new HashMap<>();
        HashMap<String, String> map2 = new HashMap<>() {}; // 匿名类! HashMap<String,String>为父类名
        HashMap<String, String> map3 = new HashMap<>() {
            {
                put("A", "1");
                put("B", "2");
            }
        };
        System.out.println(map3.get("A"));
    }
}
```

**静态内部类**

用`static`修饰的内部类和Inner Class有很大的不同，它不再依附于`Outer`的实例，而是一个完全独立的类，因此无法引用`Outer.this`，但它可以访问`Outer`的`private`静态字段和静态方法。如果把`StaticNested`移到`Outer`之外，就失去了访问`private`的权限。

```java
class Outer {
    private static String NAME = "OUTER";
    private String name;

    static class StaticNested {
        void hello() {
            System.out.println("Hello, " + Outer.NAME);
        }
    }
}
```

### classpath和jar

`classpath`是JVM用到的一个环境变量，它用来指示JVM如何搜索`class`。

启动JVM时设定：`java -classpath .;C:\work\project1\bin;C:\shared abc.xyz.Hello`

jar包的使用：`java -cp ./hello.jar abc.xyz.Hello`

> 因为jar包就是zip包，所以，直接在资源管理器中，找到正确的目录，点击右键，在弹出的快捷菜单中选择“发送到”，“压缩(zipped)文件夹”，就制作了一个zip文件。然后，把后缀从`.zip`改为`.jar`，一个jar包就创建成功。

### 模块

> java9开始，JDK中引入了模块（Module）。来解决不同jar包之间的依赖问题。
>
> 个人理解，模块主要用于减小JRE发布的体积。

模块之间的依赖项写入单独的文件（module-info.java）。

```java
//  ./src/module-info.java
module hello.world{
    //依赖包
    requires java.base; //可不写
    requires java.xml;
  
    // 对外开放的接口
    exports java.xml;
    exports javax.xml.catalog;
    exports javax.xml.datatype;
}
```

**创建模块的步骤**

- 编译字节码`javac -d bin src/module-info.java src/com/itranswarp/sample/*.java`
- 创建jar包：`jar --create --file test.jar --main-class com.mmm.Main -C bin .`
- 创建模块：`jmod create --class-path test.jar test.jmod`(必要时创建环境变量)
- 运行模块：`java --module-path test.jar --module test`

**打包JRE**

- `jlink --module-path test.jmod --add-modules java.base,java.xml,test --output jre/`

## JAVA核心类

### String

两个字符串比较，必须总是使用`equals()`方法。

要忽略大小写比较，使用`equalsIgnoreCase()`方法。

`String`类还提供了多种方法来搜索子串、提取子串。常用的方法有：

```java
//子串
"Hello".contains("ll"); // true
"Hello".indexOf("l"); // 2
"Hello".lastIndexOf("l"); // 3
"Hello".startsWith("He"); // true
"Hello".endsWith("lo"); // true
"Hello".substring(2); // "llo"
"Hello".substring(2, 4); "ll"
sb.delete(sb.length() - 2, sb.length());

//掐头去尾
"  \tHello\r\n ".trim(); // "Hello"
"\u3000Hello\u3000".strip(); // "Hello" // 和trim()不同的是，类似中文的空格字符\u3000也会被移除
" Hello ".stripLeading(); // "Hello "
" Hello ".stripTrailing(); // " Hello"

//空
"".isEmpty(); // true，因为字符串长度为0
"  ".isEmpty(); // false，因为字符串长度不为0
"  \n".isBlank(); // true，因为只包含空白字符
" Hello ".isBlank(); // false，因为包含非空白字符

//替换
String s = "hello";
s.replace('l', 'w'); // "hewwo"，所有字符'l'被替换为'w'
s.replace("ll", "~~"); // "he~~o"，所有子串"ll"被替换为"~~"

//正则表达式替换
String s = "A,,B;C ,D";
s.replaceAll("[\\,\\;\\s]+", ","); // "A,B,C,D"

//分割
String s = "A,B,C,D";
String[] ss = s.split("\\,"); // {"A", "B", "C", "D"}

//拼接
String[] arr = {"A", "B", "C"};
String s = String.join("***", arr); // "A***B***C"

String[] names = {"Bob", "Alice", "Grace"};
var sj = new StringJoiner(", ");
for (String name : names) {
  sj.add(name);
}
System.out.println(sj.toString());

//格式化字符串
//%s：显示字符串；
//%d：显示整数；
//%x：显示十六进制整数；
//%f：显示浮点数。
String s = "Hi %s, your score is %d!";
System.out.println(s.formatted("Alice", 80));

//类型转换
String.valueOf(123); // "123"
String.valueOf(45.67); // "45.67"
String.valueOf(true); // "true"
String.valueOf(new Object()); // 类似java.lang.Object@636be97c

int n1 = Integer.parseInt("123"); // 123
int n2 = Integer.parseInt("ff", 16); // 按十六进制转换，255
boolean b1 = Boolean.parseBoolean("true"); // true
boolean b2 = Boolean.parseBoolean("FALSE"); // false
Integer.getInteger("java.version"); // 版本号，11  把该字符串对应的系统变量转换为Integer

//string和char[]的转换
char[] cs = "Hello".toCharArray(); // String -> char[]
String s = new String(cs); // char[] -> String

//编码转换
byte[] b1 = "Hello".getBytes(); // 按系统默认编码转换，不推荐
byte[] b2 = "Hello".getBytes("UTF-8"); // 按UTF-8编码转换
byte[] b2 = "Hello".getBytes("GBK"); // 按GBK编码转换
byte[] b3 = "Hello".getBytes(StandardCharsets.UTF_8); // 按UTF-8编码转换

byte[] b = ...
String s1 = new String(b, "GBK"); // 按GBK转换
String s2 = new String(b, StandardCharsets.UTF_8); // 按UTF-8转换
```

### StringBuilder

为了能高效拼接字符串，Java标准库提供了`StringBuilder`，它是一个可变对象，可以预分配缓冲区，这样，往`StringBuilder`中新增字符时，不会创建新的临时对象：

```java
StringBuilder sb = new StringBuilder(1024);
for (int i = 0; i < 1000; i++) {
    sb.append(',');
    sb.append(i);
    //进行链式操作的关键是，定义的append()方法会返回this
    sb.append("Mr ")
      .append("Bob")
      .append("!")
      .insert(0, "Hello, ");
}
String s = sb.toString();
```

> 对于普通的字符串`+`操作，并不需要我们将其改写为`StringBuilder`，因为Java编译器在编译时就自动把多个连续的`+`操作编码为`StringConcatFactory`的操作。在运行期，`StringConcatFactory`会自动把字符串连接操作优化为数组复制或者`StringBuilder`操作。

### 包装类型

引用类型可以赋值为`null`，表示空，但基本类型不能赋值为`null`

java核心库中为每种基本类型都提供了对应的包装类型

| 基本类型 | 对应的引用类型      |
| :------- | :------------------ |
| boolean  | java.lang.Boolean   |
| byte     | java.lang.Byte      |
| short    | java.lang.Short     |
| int      | java.lang.Integer   |
| long     | java.lang.Long      |
| float    | java.lang.Float     |
| double   | java.lang.Double    |
| char     | java.lang.Character |

```java
int i = 100;
// 通过new操作符创建Integer实例(不推荐使用,会有编译警告):
Integer n1 = new Integer(i);
// 通过静态方法valueOf(int)创建Integer实例:
Integer n2 = Integer.valueOf(i);
// 通过静态方法valueOf(String)创建Integer实例:
Integer n3 = Integer.valueOf("100");
System.out.println(n3.intValue());
```

**Auto Boxing**

自动拆包和解包只发生在编译阶段：

```java
Integer n = 100; // 编译器自动使用Integer.valueOf(int)
int x = n; // 编译器自动使用Integer.intValue()
```

**不变性**

所有的包装类型都是不变类。`private final class Integer{private final int value;}`

**进制转换**

```java
int x1 = Integer.parseInt("100"); // 100
int x2 = Integer.parseInt("100", 16); // 256,因为按16进制解析
Integer.toString(100);

System.out.println(Integer.toString(100)); // "100",表示为10进制
System.out.println(Integer.toString(100, 36)); // "2s",表示为36进制
System.out.println(Integer.toHexString(100)); // "64",表示为16进制
System.out.println(Integer.toOctalString(100)); // "144",表示为8进制
System.out.println(Integer.toBinaryString(100)); // "1100100",表示为2进制
```

**包装类型中的静态变量**

```java
// boolean只有两个值true/false，其包装类型只需要引用Boolean提供的静态字段:
Boolean t = Boolean.TRUE;
Boolean f = Boolean.FALSE;
// int可表示的最大/最小值:
int max = Integer.MAX_VALUE; // 2147483647
int min = Integer.MIN_VALUE; // -2147483648
// long类型占用的bit和byte数量:
int sizeOfLong = Long.SIZE; // 64 (bits)
int bytesOfLong = Long.BYTES; // 8 (bytes)

// 向上转型为Number:
Number num = new Integer(999);
// 获取byte, int, long, float, double:
byte b = num.byteValue();
int n = num.intValue();
long ln = num.longValue();
float f = num.floatValue();
double d = num.doubleValue();
```

在Java中，并没有无符号整型（Unsigned）的基本数据类型。`byte`、`short`、`int`和`long`都是带符号整型，最高位是符号位。

```java
byte x = -1;
byte y = 127;
System.out.println(Byte.toUnsignedInt(x)); // 255
System.out.println(Byte.toUnsignedInt(y)); // 127
```

### JavaBean

如果读写方法符合以下这种命名规范：

```
// 读方法:
public Type getXyz()
// 写方法:
public void setXyz(Type value)
```

那么这种`class`被称为`JavaBean`.`boolean`字段比较特殊，它的读方法一般命名为`isXyz()`.

```java
// 枚举JavaBean属性
import java.beans.*;
public class Main {
    public static void main(String[] args) throws Exception {
        BeanInfo info = Introspector.getBeanInfo(Person.class);
        for (PropertyDescriptor pd : info.getPropertyDescriptors()) {
            System.out.println(pd.getName());
            System.out.println("  " + pd.getReadMethod());
            System.out.println("  " + pd.getWriteMethod());
        }
    }
}
class Person {
    private String name;
    private int age;
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
}
```

### 枚举类

```java
enum Weekday {
    SUN, MON, TUE, WED, THU, FRI, SAT;
}

Weekday day = Weekday.SUN;
if (day == Weekday.SAT || day == Weekday.SUN) {
  System.out.println("Work at home!");
} else {
  System.out.println("Work at office!");
}
```

`enum`类型的每个常量在JVM中只有一个唯一实例，所以可以直接用`==`比较

```java
// 返回index
int n = Weekday.MON.ordinal(); // 1
```

```java
enum Weekday {
    MON(1), TUE(2), WED(3), THU(4), FRI(5), SAT(6), SUN(0);

    public final int dayValue;

    private Weekday(int dayValue) {
        this.dayValue = dayValue;
    }
}

enum Weekday {
    MON(1, "星期一"), TUE(2, "星期二"), WED(3, "星期三"), THU(4, "星期四"), FRI(5, "星期五"), SAT(6, "星期六"), SUN(0, "星期日");

    public final int dayValue;
    private final String chinese;

    private Weekday(int dayValue, String chinese) {
        this.dayValue = dayValue;
        this.chinese = chinese;
    }

    @Override
    public String toString() {
        return this.chinese;
    }
}
```

### 记录类

使用`String`、`Integer`等类型的时候，这些类型都是不变类，一个不变类具有以下特点：

1. 定义class时使用`final`，无法派生子类；
2. 每个字段使用`final`，保证创建实例后无法修改任何字段。

从Java 14开始，引入了新的`Record`类。我们定义`Record`类时，使用关键字`record`。

```java
public class Main {
    public static void main(String[] args) {
        Point p = new Point(123, 456);
        System.out.println(p.x());
        System.out.println(p.y());
        System.out.println(p);
    }
}

public record Point(int x, int y) {}
// 等效的类：
public final class Point extends Record {
    private final int x;
    private final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int x() {
        return this.x;
    }

    public int y() {
        return this.y;
    }

    public String toString() {
        return String.format("Point[x=%s, y=%s]", x, y);
    }

    public boolean equals(Object o) {
        ...
    }
    public int hashCode() {
        ...
    }
}
```

如果构造类需要检查参数：

```java
public record Point(int x, int y) {
    public Point {
        if (x < 0 || y < 0) {
            throw new IllegalArgumentException();
        }
    }
}
```

```java
public record Point(int x, int y) {
    public static Point of() {
        return new Point(0, 0);
    }
    public static Point of(int x, int y) {
        return new Point(x, y);
    }
}
var z = Point.of();
var p = Point.of(123, 456);
```

### BigInteger

`java.math.BigInteger`就是用来表示任意大小的整数。`BigInteger`内部用一个`int[]`数组来模拟一个非常大的整数

```java
BigInteger i = new BigInteger("123456789000");
System.out.println(i.longValue()); // 123456789000
System.out.println(i.multiply(i).longValueExact()); // java.lang.ArithmeticException: BigInteger out of long range
```

### BigDecimal

`BigDecimal`可以表示一个任意大小且精度完全准确的浮点数。

```java
BigDecimal d1 = new BigDecimal("123.4500");
BigDecimal d2 = d1.stripTrailingZeros();
System.out.println(d1.scale()); // 4
System.out.println(d2.scale()); // 2,因为去掉了00

//scale()表示小数位数
BigDecimal d1 = new BigDecimal("123.4500");
BigDecimal d2 = d1.stripTrailingZeros();
System.out.println(d1.scale()); // 4
System.out.println(d2.scale()); // 2,因为去掉了00

BigDecimal d3 = new BigDecimal("1234500");
BigDecimal d4 = d3.stripTrailingZeros();
System.out.println(d3.scale()); // 0
System.out.println(d4.scale()); // -2

//可以对一个BigDecimal设置它的scale，如果精度比原始值低，那么按照指定的方法进行四舍五入或者直接截断：
BigDecimal d1 = new BigDecimal("123.456789");
BigDecimal d2 = d1.setScale(4, RoundingMode.HALF_UP); // 四舍五入，123.4568
BigDecimal d3 = d1.setScale(4, RoundingMode.DOWN); // 直接截断，123.4567

//除法需要截断
BigDecimal d1 = new BigDecimal("123.456");
BigDecimal d2 = new BigDecimal("23.456789");
BigDecimal d3 = d1.divide(d2, 10, RoundingMode.HALF_UP); // 保留10位小数并四舍五入
BigDecimal d4 = d1.divide(d2); // 报错：ArithmeticException，因为除不尽

//使用equals()方法不但要求两个BigDecimal的值相等，还要求它们的scale()相等
```

> 总是使用compareTo()比较两个BigDecimal的值，不要使用equals()！

### 常用工具类

#### Math

```java
Math.abs(-100); // 100
Math.max(100, 99); // 100
Math.min(1.2, 2.3); // 1.2
Math.pow(2, 10); // 2的10次方=1024
Math.sqrt(2); // 1.414...
Math.exp(2); // 7.389...
Math.log(4); // 1.386...
Math.sin(3.14); // 0.00159...
Math.cos(3.14); // -0.9999...
Math.tan(3.14); // -0.0015...
Math.asin(1.0); // 1.57079...
Math.acos(1.0); // 0.0
double pi = Math.PI; // 3.14159...
double e = Math.E; // 2.7182818...
Math.sin(Math.PI / 6); // sin(π/6) = 0.5
Math.random(); // 0.53907... 每次都不一样
```

#### Random

```java
//伪随机
Random r = new Random(098);
r.nextInt(); // 2071575453,每次都不一样
r.nextInt(10); // 5,生成一个[0,10)之间的int
r.nextLong(); // 8811649292570369305,每次都不一样
r.nextFloat(); // 0.54335...生成一个[0,1)之间的float
r.nextDouble(); // 0.3716...生成一个[0,1)之间的double
```

#### SecureRandom(真随机)

```java
SecureRandom sr = new SecureRandom();
System.out.println(sr.nextInt(100));
```

> 需要使用安全随机数的时候，必须使用SecureRandom，绝不能使用Random！

# 异常处理

## Java的异常

`Throwable`是异常体系的根，它继承自`Object`。`Throwable`有两个体系：`Error`和`Exception`，`Error`表示严重的错误，程序对此一般无能为力，例如：

- `OutOfMemoryError`：内存耗尽
- `NoClassDefFoundError`：无法加载某个Class
- `StackOverflowError`：栈溢出

而`Exception`则是运行时的错误，它可以被捕获并处理。

某些异常是应用程序逻辑处理的一部分，应该捕获并处理。例如：

- `NumberFormatException`：数值类型的格式错误
- `FileNotFoundException`：未找到文件
- `SocketException`：读取网络失败

还有一些异常是程序逻辑编写不对造成的，应该修复程序本身。例如：

- `NullPointerException`：对某个`null`的对象调用方法或字段
- `IndexOutOfBoundsException`：数组索引越界

`Exception`又分为两大类：

1. `RuntimeException`以及它的子类；
2. 非`RuntimeException`（包括`IOException`、`ReflectiveOperationException`等等）

Java规定：

- 必须捕获的异常，包括`Exception`及其子类，但不包括`RuntimeException`及其子类，这种类型的异常称为Checked Exception。
- 不需要捕获的异常，包括`Error`及其子类，`RuntimeException`及其子类。

## 捕获异常

```java
import java.io.UnsupportedEncodingException;
import java.util.Arrays;
public class Main {
    public static void main(String[] args) {
        byte[] bs = toGBK("中文");
        System.out.println(Arrays.toString(bs));
      
        // 多catch语句
        // catch的顺序非常重要：子类必须写在前面
        try {
            process1();
            process2();
            process3();
        } catch (IOException | NumberFormatException e) { // IOException或NumberFormatException
            System.out.println("Bad input");
        } catch (Exception e) {
            System.out.println("Unknown error");
        } finally {
        System.out.println("END");
        }
    }
    // throw
    static byte[] toGBK(String s) throws UnsupportedEncodingException {
        return s.getBytes("GBK");
    
    // try catch
    static byte[] toGBK(String s) {
        try {
            // 用指定编码转换String为byte[]:
            return s.getBytes("GBK");
        } catch (UnsupportedEncodingException e) {
            // e.printStackTrace();
            // 如果系统不支持GBK编码，会捕获到UnsupportedEncodingException:
            System.out.println(e); // 打印异常信息
            return s.getBytes(); // 尝试使用用默认编码
        }
    }
}
```

`printStackTrace()`可以打印出方法的调用栈。

**抛出异常**

```java
void process1(String s) {
    try {
        process2();
    } catch (NullPointerException e) {
        //转换异常
        //为了能追踪到完整的异常栈，在构造异常的时候，把原始的Exception实例传进去，新的Exception就可以持有原始Exception信息。
        throw new IllegalArgumentException(e);
    }
}

void process2(String s) {
    if (s==null) {
        throw new NullPointerException();
    }
}
```

在`catch`中抛出异常，不会影响`finally`的执行。JVM会先执行`finally`，然后抛出异常。

在极少数的情况下，我们需要获知所有的异常。如何保存所有的异常信息？方法是先用`origin`变量保存原始异常，然后调用`Throwable.addSuppressed()`，把原始异常添加进来，最后在`finally`抛出：

```java
public class Main {
    public static void main(String[] args) throws Exception {
        Exception origin = null;
        try {
            System.out.println(Integer.parseInt("abc"));
        } catch (Exception e) {
            origin = e;
            throw e;
        } finally {
            Exception e = new IllegalArgumentException();
            if (origin != null) {
                e.addSuppressed(origin);
            }
            throw e;
        }
    }
}
```

## 自定义异常

Java标准库定义的常用异常包括：

```ascii
Exception
│
├─ RuntimeException
│  │
│  ├─ NullPointerException
│  │
│  ├─ IndexOutOfBoundsException
│  │
│  ├─ SecurityException
│  │
│  └─ IllegalArgumentException
│     │
│     └─ NumberFormatException
│
├─ IOException
│  │
│  ├─ UnsupportedCharsetException
│  │
│  ├─ FileNotFoundException
│  │
│  └─ SocketException
│
├─ ParseException
│
├─ GeneralSecurityException
│
├─ SQLException
│
└─ TimeoutException
```

自定义异常通常从`RuntimeException`派生：`public class BaseException extends RuntimeException {}`

自定义的`BaseException`应该提供多个构造方法：

```java
public class BaseException extends RuntimeException {
    public BaseException() {
        super();
    }

    public BaseException(String message, Throwable cause) {
        super(message, cause);
    }

    public BaseException(String message) {
        super(message);
    }

    public BaseException(Throwable cause) {
        super(cause);
    }
}
```

## NullPointerException

`NullPointerException`是一种代码逻辑错误，遇到`NullPointerException`，遵循原则是早暴露，早修复，严禁使用`catch`来隐藏这种编码错误.

```java
//成员变量在定义时初始化：
//使用空字符串""而不是默认的null可避免很多NullPointerException，编写业务逻辑时，用空字符串""表示未填写比null安全得多。
public class Person {
    private String name = "";
}
//返回空字符串""、空数组而不是null
```

## 断言

```java
public static void main(String[] args) {
    double x = Math.abs(-123.45);
    assert x >= 0;
    //使用assert语句时，还可以添加一个可选的断言消息：
    assert x >= 0 : "x must >= 0";
    System.out.println(x);
}
```

断言失败时会抛出`AssertionError`，导致程序结束退出。因此，断言不能用于可恢复的程序错误，只应该用于开发和测试阶段。

对于可恢复的程序错误，不应该使用断言。

> idea中开启断言的方法
>
> 在VM optons中 加入 -ea或-enableassertions

> 实际开发中，很少使用断言。更好的方法是编写单元测试，后续我们会讲解`JUnit`的使用。

## JDK Logging

`java.util.logging`需要在JVM启动时传递参数`-Djava.util.logging.config.file=<config-file-name>`。

> Java标准库内置的Logging使用并不是非常广泛。

```java
// logging
import java.util.logging.Level;
import java.util.logging.Logger;

public class Hello {
    public static void main(String[] args) {
        Logger logger = Logger.getGlobal();
        logger.info("start process...");
        logger.warning("memory is running out...");
        logger.fine("ignored.");
        logger.severe("process will be terminated...");
    }
}
```

JDK的Logging定义了7个日志级别，从严重到普通：

- SEVERE
- WARNING
- INFO
- CONFIG
- FINE
- FINER
- FINEST

## Commons Logging

Commons Logging是一个第三方日志库，它是由Apache创建的日志模块。

Commons Logging可以挂接不同的日志系统，并通过配置文件指定挂接的日志系统。默认情况下，Commons Loggin自动搜索并使用Log4j（Log4j是另一个流行的日志系统），如果没有找到Log4j，再使用JDK Logging。

```java
import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
// 在实例方法中引用Log:
public class Person {
    protected final Log log = LogFactory.getLog(getClass());//相比下面的方法，这种方法子类也可以使用该log实例
		//protected final Log log = LogFactory.getLog(Person.class);
    void foo() {
        log.info("foo");
    }
}
```

Commons Logging的日志方法，例如`info()`，除了标准的`info(String)`外，还提供了一个非常有用的重载方法：`info(String, Throwable)`

```java
try {
    ...
} catch (Exception e) {
    log.error("got exception!", e);
}
```

## Log4j

配置文件

```xml
// log4j2.xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
	<Properties>
        <!-- 定义日志格式 -->
		<Property name="log.pattern">%d{MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36}%n%msg%n%n</Property>
        <!-- 定义文件名变量 -->
		<Property name="file.err.filename">log/err.log</Property>
		<Property name="file.err.pattern">log/err.%i.log.gz</Property>
	</Properties>
    <!-- 定义Appender，即目的地 -->
	<Appenders>
        <!-- 定义输出到屏幕 -->
		<Console name="console" target="SYSTEM_OUT">
            <!-- 日志格式引用上面定义的log.pattern -->
			<PatternLayout pattern="${log.pattern}" />
		</Console>
        <!-- 定义输出到文件,文件名引用上面定义的file.err.filename -->
		<RollingFile name="err" bufferedIO="true" fileName="${file.err.filename}" filePattern="${file.err.pattern}">
			<PatternLayout pattern="${log.pattern}" />
			<Policies>
                <!-- 根据文件大小自动切割日志 -->
				<SizeBasedTriggeringPolicy size="1 MB" />
			</Policies>
            <!-- 保留最近10份 -->
			<DefaultRolloverStrategy max="10" />
		</RollingFile>
	</Appenders>
	<Loggers>
		<Root level="info">
            <!-- 对info级别的日志，输出到console -->
			<AppenderRef ref="console" level="info" />
            <!-- 对error级别的日志，输出到err，即上面定义的RollingFile -->
			<AppenderRef ref="err" level="error" />
		</Root>
	</Loggers>
</Configuration>
```

## SLF4J和Logback

SLF4J类似于Commons Logging，也是一个日志接口，而Logback类似于Log4j，是一个日志的实现。

在Commons Logging中，我们要打印日志，有时候得这么写：

```java
int score = 99;
p.setScore(score);
log.info("Set score " + score + " for Person " + p.getName() + " ok.");
```

拼字符串是一个非常麻烦的事情，所以SLF4J的日志接口改进成这样了：

```java
int score = 99;
p.setScore(score);
logger.info("Set score {} for Person {} ok.", score, p.getName());
```

如何使用SLF4J？它的接口实际上和Commons Logging几乎一模一样：

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

class Main {
    final Logger logger = LoggerFactory.getLogger(getClass());
}
```

```xml
// logback.xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

	<appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
		<encoder>
			<pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
		</encoder>
	</appender>

	<appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
		<encoder>
			<pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
			<charset>utf-8</charset>
		</encoder>
		<file>log/output.log</file>
		<rollingPolicy class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy">
			<fileNamePattern>log/output.log.%i</fileNamePattern>
		</rollingPolicy>
		<triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
			<MaxFileSize>1MB</MaxFileSize>
		</triggeringPolicy>
	</appender>

	<root level="INFO">
		<appender-ref ref="CONSOLE" />
		<appender-ref ref="FILE" />
	</root>
</configuration>
```


