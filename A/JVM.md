# JVM

## 一、类字节码详解:airplane:

> 参考链接：[Java全栈知识体系](https://pdai.tech/md/java/jvm/java-jvm-class.html)

### 1.1 多语言编译为字节码在JVM运行

计算机是不能直接运行Java代码的，必须要先运行Java虚拟机，再由Java虚拟机运行编译后的Java代码，即Java字节码。

Java代码间接翻译成字节码，储存字节码的文件再交由运行于不同平台上的JVM虚拟机去读取执行，从而实现**一次编写、到处运行**的目的。JVM也不再只支持Java，由此衍生出了许多基于JVM的编程语言，如Groovy、Scala、Koltin等等。

![Java源码、字节码、JVM](JVM.assets/java-jvm-class-1.png)

### 1.2 Java字节码文件

`.class`文件本质上是一个**以8位字节为基础单位的二进制流**，各个数据项目严格按照顺序紧凑的排列在`.class`文件中。Jvm根据其特定的规则解析该二进制数据，从而得到相关信息。`.class`文件采用一种伪结构来存储数据，它有两种类型：无符号数和表。

#### .class文件的结构属性

![字节码文件结构属性](JVM.assets/java-jvm-class-2.png)

#### 从一个例子开始

```java
//Main.java
public class Main {
    
    private int m;
    
    public int inc() {
        return m + 1;
    }
}
```

生成一个`Main.class`文件：

```shell
javac Main.java
```

以文本形式打开，其内容如下：

```
cafe babe 0000 0034 0013 0a00 0400 0f09
0003 0010 0700 1107 0012 0100 016d 0100
0149 0100 063c 696e 6974 3e01 0003 2829
5601 0004 436f 6465 0100 0f4c 696e 654e
756d 6265 7254 6162 6c65 0100 0369 6e63
0100 0328 2949 0100 0a53 6f75 7263 6546
696c 6501 0009 4d61 696e 2e6a 6176 610c
0007 0008 0c00 0500 0601 0010 636f 6d2f
7268 7974 686d 372f 4d61 696e 0100 106a
6176 612f 6c61 6e67 2f4f 626a 6563 7400
2100 0300 0400 0000 0100 0200 0500 0600
0000 0200 0100 0700 0800 0100 0900 0000
1d00 0100 0100 0000 052a b700 01b1 0000
0001 000a 0000 0006 0001 0000 0003 0001
000b 000c 0001 0009 0000 001f 0002 0001
0000 0007 2ab4 0002 0460 ac00 0000 0100
0a00 0000 0600 0100 0000 0800 0100 0d00
0000 0200 0e
```

文件开头的4个字节（"cafe babe"）称之为**魔数**，唯有以"cafe babe"开头的`.class`文件方可被虚拟机所接受，这4个字节就是字节码文件的身份识别。0000是编译器JDK版本的次版本号0，0034转化为十进制是52也就是主版本号，**Java的版本号从45开始**，除`v1.0`和`v1.1`都是使用45.x外，以后每升一个大版本，版本号加一。也就是说，编译生成该`.class`文件的JDK版本为`v1.8.0`。

#### 反编译字节码文件

使用Java内置的反编译工具javap可以反编译字节码文件，用法：`javap <options> <classes>`。

输入命令`javap -verbose -p Main.class`查看输出内容：

```shell
Classfile /E:/JavaCode/TestProj/out/production/TestProj/com/rhythm7/Main.class
  Last modified 2018-4-7; size 362 bytes
  MD5 checksum 4aed8540b098992663b7ba08c65312de
  Compiled from "Main.java"
public class com.rhythm7.Main
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #4.#18         // java/lang/Object."<init>":()V
   #2 = Fieldref           #3.#19         // com/rhythm7/Main.m:I
   #3 = Class              #20            // com/rhythm7/Main
   #4 = Class              #21            // java/lang/Object
   #5 = Utf8               m
   #6 = Utf8               I
   #7 = Utf8               <init>
   #8 = Utf8               ()V
   #9 = Utf8               Code
  #10 = Utf8               LineNumberTable
  #11 = Utf8               LocalVariableTable
  #12 = Utf8               this
  #13 = Utf8               Lcom/rhythm7/Main;
  #14 = Utf8               inc
  #15 = Utf8               ()I
  #16 = Utf8               SourceFile
  #17 = Utf8               Main.java
  #18 = NameAndType        #7:#8          // "<init>":()V
  #19 = NameAndType        #5:#6          // m:I
  #20 = Utf8               com/rhythm7/Main
  #21 = Utf8               java/lang/Object
{
  private int m;
    descriptor: I
    flags: ACC_PRIVATE

  public com.rhythm7.Main();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 3: 0
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       5     0  this   Lcom/rhythm7/Main;

  public int inc();
    descriptor: ()I
    flags: ACC_PUBLIC
    Code:
      stack=2, locals=1, args_size=1
         0: aload_0
         1: getfield      #2                  // Field m:I
         4: iconst_1
         5: iadd
         6: ireturn
      LineNumberTable:
        line 8: 0
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       7     0  this   Lcom/rhythm7/Main;
}
SourceFile: "Main.java"
```

#### 字节码文件信息

上面的开头7行信息包括：`.class`文件当前所在位置、最后修改时间、文件大小、MD5值、编译自哪个文件、类的全限定名、JDK次版本号、主版本号。

然后紧接着的是该类的访问标志：`ACC_PUBLIC`，`ACC_SUPER`，访问标志的含义如下：

|      名称      | 标志值 |                             含义                             |
| :------------: | :----: | :----------------------------------------------------------: |
|   ACC_PUBLIC   | 0x0001 |                      是否为`Public`类型                      |
|   ACC_FINAL    | 0x0010 |             是否被声明为`final`，只有类可以设置              |
|   ACC_SUPER    | 0x0020 |        是否允许使用`invokespecial`字节码指令的新语义         |
| ACC_INTERFACE  | 0x0200 |                       标志这是一个接口                       |
|  ACC_ABSTRACT  | 0x0400 | 是否为`abstract`类型，对于接口或者抽象类来说，次标志值为真，其他类型为假 |
| ACC_SYNTHETIC  | 0x1000 |                 标志这个类并非由用户代码产生                 |
| ACC_ANNOTATION | 0x2000 |                       标志这是一个注解                       |
|    ACC_ENUM    | 0x4000 |                       标志这是一个枚举                       |

#### 常量池

`Constant pool`意为常量池。常量池可以理解成`.class`文件中的资源仓库。主要存放的是两大类常量：**字面量（Literal）**和**符号引用（Symbolic References）**。字面量类似于Java中的常量概念，如文本字符串，`final`常量等，而符号引用则属于编译原理方面的概念，包括以下三种：

- 类和接口的全限定名（Fully Qualified Name）。
- 字段的名称和描述符号（Descriptor）。
- 方法的名称和描述符。

不同于C/C++，JVM是在加载`.class`文件的时候才进行的动态链接，也就是说**这些字段和方法符号引用只有在运行期转换后才能获得真正的内存入口地址**。当虚拟机运行时，**需要从常量池获得对应的符号引用，再在类创建或运行时解析并翻译到具体的内存地址中**。 直接通过反编译文件来查看字节码内容：

```shell
#1 = Methodref          #4.#18         // java/lang/Object."<init>":()V
#4 = Class              #21            // java/lang/Object
#7 = Utf8               <init>
#8 = Utf8               ()V
#18 = NameAndType        #7:#8          // "<init>":()V
#21 = Utf8               java/lang/Object
```

**第一个常量**是一个方法定义，指向了第4和第18个常量。以此类推，最后可以拼接成第一个常量右侧的注释内容：`java/lang/Object."<init>":()V`。

这段可以理解为该类的实例构造器的声明，由于Main类没有重写构造方法，所以调用的是父类的构造方法。此处也说明了Main类的直接父类是`Object`。 该方法默认返回值是V，也就是`void`，无返回值。

**第二个常量**同理可得：

```shell
#2 = Fieldref           #3.#19         // com/rhythm7/Main.m:I
#3 = Class              #20            // com/rhythm7/Main
#5 = Utf8               m
#6 = Utf8               I
#19 = NameAndType        #5:#6          // m:I
#20 = Utf8               com/rhythm7/Main
```

此处声明了一个字段m，类型为I, I即是`int`类型。关于字节码的类型对应如下：

| 标识字符 |                      含义                       |
| :------: | :---------------------------------------------: |
|    B     |                 基本类型`byte`                  |
|    C     |                 基本类型`char`                  |
|    D     |                基本类型`double`                 |
|    F     |                 基本类型`float`                 |
|    I     |                  基本类型`int`                  |
|    J     |                 基本类型`long`                  |
|    S     |                 基本类型`short`                 |
|    Z     |                基本类型`boolean`                |
|    V     |                 特殊类型`void`                  |
|    L     | 对象类型，以分号结尾，如：`Ljava/lang/Object`； |

对于数组类型，每一位使用一个前置的`[`字符来描述，如定义一个`java.lang.String[][]`类型的维数组，将被记录为`[[Ljava/lang/String;`

#### 方法表合集

在常量池之后的是对类内部的方法描述，在字节码中以表的集合形式表现，暂且不管字节码文件的16进制文件内容如何，直接看反编译后的内容，这里声明了一个私有变量m，类型为int，返回值为int：

```shell
private int m;
  descriptor: I
  flags: ACC_PRIVATE
```

```shell
public com.rhythm7.Main();
   descriptor: ()V
   flags: ACC_PUBLIC
   Code:
     stack=1, locals=1, args_size=1
        0: aload_0
        1: invokespecial #1                  // Method java/lang/Object."<init>":()V
        4: return
     LineNumberTable:
       line 3: 0
     LocalVariableTable:
       Start  Length  Slot  Name   Signature
           0       5     0  this   Lcom/rhythm7/Main;
```

这里是构造方法：`Main()`，返回值为`void`，公开方法。

code内的主要属性为：

- **`stack`**：**最大操作数栈**，JVM运行时会根据这个值来分配栈帧（Frame）中的操作栈深度，此处为1。
- **`locals`**：**局部变量所需的存储空间**，单位为Slot。**Slot是虚拟机为局部变量分配内存时所使用的最小单位，为4个字节大小**。方法参数（包括实例方法中的隐藏参数`this`）、显示异常处理器的参数（`try-catch`中的`catch`块所定义的异常）、方法体中定义的局部变量**都需要使用局部变量表来存放**。值得一提的是，**`locals`的大小并不一定等于所有局部变量所占的Slot之和，因为局部变量中的Slot是可以重用的**。
- **`args_size`**：**方法参数的个数**，这里是1，因为每个实例方法都会有一个隐藏参数`this`。
- **`attribute_info`**：**方法体内容**，0、1、4为字节码"行号"，该段代码的意思是将**第一个引用类型本地变量推送至栈顶，然后执行该类型的实例方法，也就是常量池存放的第一个变量，也就是注释里的`java/lang/Object."":()V`**，然后执行返回语句，结束方法。
- **`LineNumberTable`**：该属性的作用是描述**源码行号与字节码行号（字节码偏移量）之间的对应关系**。可以使用`-g:none`或`-g:lines`选项来取消或要求生成这项信息，如果选择不生成，当程序运行异常时将无法获取到发生异常的源码行号，也无法按照源码的行数来调试程序。
- **`LocalVariableTable`**：该属性的作用是**描述帧栈中局部变量与源码中定义的变量之间的关系**。可以使用`-g:none`或`-g:vars`来取消或生成这项信息，**如果没有生成这项信息，那么当别人引用这个方法时，将无法获取到参数名称，取而代之的是`arg0`、`arg1`这样的占位符**。`start`表示该局部变量在哪一行开始可见，`length`表示可见行数，`Slot`代表所在帧栈位置，`Name`是变量名称，然后是类型签名。

#### 类名

最后一行显然是表示源码文件。

## 二、类加载机制:rocket:

> 参考链接：[Java全栈知识体系](https://pdai.tech/md/java/jvm/java-jvm-classload.html)

### 2.1 类的生命周期

其中类加载的过程包括了**加载**、**验证**、**准备**、**解析**、**初始化**五个阶段。在这五个阶段中，**加载**、**验证**、**准备**和**初始化**这四个阶段发生的顺序是**确定**的，而**解析**阶段则不一定，**它在某些情况下可以在初始化阶段之后开始，这是为了支持Java语言的运行时绑定（也成为动态绑定或晚期绑定）**。另外注意这里的几个阶段是**按顺序开始**，**而不是按顺序进行或完成**，因为这些阶段通常都是互相交叉地混合进行的，**通常在一个阶段执行的过程中调用或激活另一个阶段**。

![类的生命周期](JVM.assets/java_jvm_classload_2.png)

#### 类的加载：查找并加载类的二进制数据

**加载是类加载过程的第一个阶段**，在加载阶段，虚拟机需要完成以下三件事情：

1. 通过一个类的全限定名来获取其定义的**二进制字节流**；
2. 将这个字节流所代表的**静态存储结构转化为方法区的运行时数据结构**；
3. 在Java**堆中生成一个代表这个类的`java.lang.Class`对象**，作为对**方法区中这些数据的访问入口**。

![加载](JVM.assets/java_jvm_classload_1.png)

相对于类加载的其他阶段而言，加载阶段（准确地说，是加载阶段**获取类的二进制字节流的动作**）是**可控性最强**的阶段，因为**开发人员既可以使用系统提供的类加载器来完成加载，也可以自定义自己的类加载器来完成加载**。

加载阶段完成后，**虚拟机外部的二进制字节流就按照虚拟机所需的格式存储在方法区之中**，而且在Java堆中也创建一个`java.lang.Class`类的对象，这样便可以**通过该对象访问方法区中的这些数据**。

**类加载器并不需要等到某个类被“首次主动使用”时再加载它**，JVM规范**允许类加载器在预料某个类将要被使用时就预先加载它**，如果在预先加载的过程中遇到了`.class`文件缺失或存在错误，**类加载器必须在程序首次主动使用该类时才报告错误（LinkageError错误）**，如果这个类一直没有被程序主动使用，那么类加载器就不会报告错误。

#### 连接

##### 验证：确保被加载的类的正确性

**验证是连接阶段的第一步**，这一阶段的目的是为了确保`.class`文件的**字节流中包含的信息符合当前虚拟机的要求**，并且不会危害虚拟机自身的安全。验证阶段大致会完成4个阶段的检验动作：

- 文件格式验证：**验证字节流是否符合`.class`文件格式的规范**。例如：是否以`0xCAFEBABE`开头、主次版本号是否在当前虚拟机的处理范围之内、常量池中的常量是否有不被支持的类型。
- 元数据验证：**对字节码描述的信息进行语义分析**，以保证其描述的信息符合Java语言规范的要求。例如：这个类是否有父类，除了`java.lang.Object`之外。
- 字节码验证：通过数据流和控制流分析，**确定程序语义是合法的、符合逻辑的**。
- 符号引用验证：确保解析动作能正确执行。

补充：验证阶段是非常重要的，但不是必须的，它对程序运行期没有影响，如果所引用的类经过反复验证，那么可以考虑采用`-Xverifynone`参数来**关闭大部分的类验证措施**，以缩短虚拟机类加载的时间。

##### 准备：为类的静态变量分配内存并将其初始化为默认值

##### 解析：把类中的符号引用转换为直接引用