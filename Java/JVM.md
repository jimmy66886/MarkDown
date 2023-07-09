我实在不知道我到底要干什么了,不如先看着JVM吧,反正早晚都要看.

# JVM与Java体系结构

`write once, run anywhere`

Java虚拟机根本不关心运行在其内部的程序到底是使用何种编程语言编写的,它只关心"字节码"文件,也就是说Java虚拟机拥有语言无关性,并不会单纯地与Java语言终身绑定,只要其他编程语言的编译结果满足并包含Java虚拟机内部指令集,符号表以及其他的辅助信息,他就是一个有效的字节码文件,就能够被虚拟机所识别病状并装载运行.

## 什么是Java虚拟机
- Java虚拟机是一台执行Java字节码的虚拟计算机,它拥有独立的运行机制,其运行的Java字节码也未必由Java语言编译而成
- JVM平台的各种语言可以共享Java虚拟机带来的跨平台性,优秀的垃圾回收器,以及可靠的即时编译器
- Java技术的核心就是Java虚拟机(JVM,Java Virtual Machine),因为所有的Java程序都运行在Java虚拟机内部

>作用
Java虚拟机就是二进制字节码的运行环境,负责装在字节码到其内部,解释/编译为对应平台上的机器指令执行,每一条Java指令,Java虚拟机规范中都有详细定义,如怎么取操作数,怎么处理操作数,处理结果放在哪

>特点
- 一次编译,到处运行
- 自动内存管理
- 自动垃圾回收功能

---

JVM的位置:JVM是运行在操作系统之上的,它与硬件没有直接的交互
![第01章_JVM所处位置](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/第01章_JVM所处位置.jpg)

## JVM的整体结构
- HotSpot VM是目前市面上高性能虚拟机的代表作之一
- 它采用解释器与即时编译器并存的架构
- 在今天,Java程序的运行性能早已脱胎换骨,已经达到了以C/C++程序一较高下的的地步

![第02章_JVM架构-简图](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/第02章_JVM架构-简图.jpg)
*注意,这个图要会画...*

执行引擎(Execution Engine)的图解:
![第02章_JVM架构-中](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/第02章_JVM架构-中.jpg)
英文版:
![第02章_JVM架构-英](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/第02章_JVM架构-英.jpg)

---

>Java代码执行流程
![20230707102218](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230707102218.png)

## JVM的架构模型

Java编译器输入的指令流基本上是一种`基于栈的指令集架构`,另外一种指令集架构则是基于`寄存器的指令集架构`

具体来说,两种架构之间的区别:
- 基于栈式架构的特点
    - 设计和实现更简单,适用于资源受限的系统
    - 避开寄存器的分配难题,使用零零地址指令方式分配
    - 指令流中的指令大部分是零地址指令,其执行过程依赖于操作栈,指令集更小,编译器容易实现
    - 不需要硬件支持,可移植性更好,更好实现阔平台
- 基于寄存器架构的特点
    - 典型的应用是x86的二进制指令集,比如传统的PC以及Android的Davlik虚拟机
    - **指令集架构则完全依赖硬件,可移植性差**
    - **性能优秀和执行更高效**
    - 花费更少的指令去完成一项操作
    - 在大部分情况下,基于寄存器架构的指令集往往都以一地址指令,二地址指令和三地址指令为主,而基于栈式架构的指令集却是以零地址指令为主

---

总结:
由于跨平台性的设计,**Java的指令都是跟据栈来设计的**,不同平台CPU架构不同,所以不能设计为基于寄存器的,有点事跨平台,指令集小,编译器容器实现,缺点是性能下降,实现同样的功能需要更多的指令

时至今日,尽管嵌入式平台已经不是Java程序的主流运行平台了(准确来说应该是HotSpotVM的宿主环境已经不局限于嵌入式平台了),那么为什么不将架构更换为基于寄存器的架构呢

**简单来说,就是基于栈的架构,也够用了**

## JVM的生命周期

>虚拟机的启动

Java虚拟机的启动是通过引导类加载器(bootstrap class loader)创建一个初始类(initial class)来完成的,这个类是由虚拟机的具体实现指定的

>虚拟机的执行
- 一个运行中的Java虚拟机有着一个清晰的任务:执行Java程序
- 程序开始执行时他才运行,程序结束时他就停止
- 执行一个所谓的Java程序的时候,真真正正在执行的是一个叫做Java虚拟机的进程

>虚拟机的退出
有如下几种情况
- 程序正常执行结束
- 程序在执行过程中遇到了异常或者错误而异常终止
- 由于操作系统出现错误而导致Java虚拟机进行终止
- 某线程调用Runtime类或System类的exit方法,或Runtime类的halt方法,并且Java安全管理器也允许这次exit或halt操作
- 除此之外,JNI(Java Native Interface)规范描述了用JNI Invocation API 来加载或下载Java虚拟机时,Java虚拟机的退出情况

## JVM发展历程

Sun Classic VM
- sun公司发布,是世界上**第一款商用**Java虚拟机,至java1.4时完全被淘汰
- 只提供解释器
- 如果要使用JIT编译器,就需要进行外挂,且**解释器和编译器不能配合工作**
- 现在hotspot**内置了此虚拟机**

Exact VM
- jdk1.2时,sun提供了此虚拟机
- Exact Memory Management:准确式内存管理
    - 也可以叫Non-Conservative/Accurate Memory Management
    - 虚拟机可以知道内存中某个位置的数据具体是什么类型
- 具备现代高性能虚拟机的雏形
    - 热点探测
    - 编译器与解释器混合工作模式
- 只在Solaris平台短暂使用,其他平台上还是classic vm
    - 英雄气短,终被hotspot虚拟机替换

HotSpot VM
- 最初由一家名为"Longview Tecnologies"的小公司设计
- jdk1.3时,HotSpot VM成为默认虚拟机
- 目前HotSpot占有绝对的市场地位
    - 不管是现在仍在广泛使用的JDK6,还是JDK8,默认的虚拟机都是HotSpot
    - Sun/Oracle JDK 和 OpenJDK的默认虚拟机
    - 因此本课程默认介绍的虚拟机都是HotSpot,相关机制也主要是指HotSpot的GC机制
- 从服务器,桌面,到移动端,嵌入式都有应用
- 名称中的HotSpot指的就是它的热点代码探测技术
    - 通过计数器找到最具编译价值的代码,触发即时编译或栈上替换
    - 通过编译器与解释器协同工作,在最优的程序响应时间与最佳执行性能中取得平衡

BEA的JRockit
- **专注于服务器端应用**
    - 它可以不太关注程序启动速度,因此JRockit内部不包含解析器实现,u暗部代码都靠即时编译器编译后执行
- 大量的行业基准测试显示,JRockit JVM是世界上最快的JVM
    - 使用JRockit产品,客户已经体验到了显著的性能提高(一些超过了70%)和硬件成本的减少(达50%)
- 优势:全面的Java运行时解决方案组合
    - JRockit面向延迟敏感型应用的解决方案是JRockit Real Time提供以毫秒或微秒级的JVM响应时间,适合财务,军事指挥,电信网络的需要
    - MissionControl服务套件,它是一组以极低的开销来监控,管理和分析生产环境中的应用程序的工具
- 后Oracle整合二者,整合的方式是在HotSpot的基础上,移植JRockit的优秀特性
- 高斯林:目前就职于谷歌,研究人工智能和水下机器人

IBM的J9
- 市场定位和HotSpot接近,服务器端,桌面应用,嵌入式等多用途VM
- 广泛用于IBM的各种Java产品
- 有影响力的三大商用虚拟机之一,也号称是世界上最快的Java虚拟机
- 2017年,IBM发布了开源的J9 VM,命名为OpenJ9,交给Eclipse基金会管理,也称为Eclipse OpenJ9

**主要记HotSpot和JRockit以及J9就行了**

# 类加载子系统

## 内存结构概述

![第02章_JVM架构-简图](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/第02章_JVM架构-简图.jpg)

- 类加载器子系统负责从文件系统或网络中加载class文件,class文件在文件开头有特定的文件标识
- ClassLoader只负责文件的加载,至于它是否可以运行,则由ExecutionEngine决定
- 加载的类信息存放于一块称为方法去的内存空间,除了类的信息外,方法区中还有存放运行时常量池信息,可能还包括字符串字面量和数字常量(这部分常量信息是Class文件中常量池部分的内存映射)

![20230708103023](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230708103023.png)

>类加载器ClassLoader角色
1. class file存在于本地磁盘上,可以理解为设计师画在纸上的模板,而最终这个模板在执行的时候是要加载到JVM当中来根据这个文件实例化出n个一摸一样的实例
2. class flie加载到JVM中,被称为DNA元数据模板,放在方法区
3. 在.class文件->JVM->最终成为元数据模板,此过程就要一个运输工具(类装载器Class loader),扮演一个快递员的角色.

![20230708103708](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230708103708.png)

---

类加载过程:
![第02章_类的加载过程](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/第02章_类的加载过程.jpg)

还有一张细分的图:
![20230708104839](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230708104839.png)

1. 加载
    1. 通过一个类的全限定名获取定义此类的二进制字节流
    2. 将这个字节流所代表的静态存储结构转换为方法区的运行时数据库
    3. 在内存中生成一个代表这个类的java.lang.Class对象,作为方法区这个类的各种数据的访问入口
2. 链接
    1. 验证(Verify)
        - 目的在于确保Class文件的字节流中包含信息符合当前虚拟机要求,保证被加载类的正确性,不会危害虚拟机自身安全
        - 主要包括四种验证,文件格式验证,元数据验证,字节码验证,符号引用验证
    2. 准备(Prepare)
        - 为类变量分配内存并且设置该变量的默认初始值,即零值
        - 这里不包含用final修饰的static,因为final在编译的时候就会分配了,准备阶段会显式初始化
        - 这里不会为实例变量分配初始化,类变量会分配在方法区中,而实例变量是会随着对象一起分配到Java堆中
    3. 解析
        - 将常量池内的符号引用转换为直接引用的过程
        - 事实上,解析操作往往会伴随着JVM在执行完初始化之后执行
        - 符号应用就是一组符号来描述所引用的目标,符号应用的字面量形式明确定义在Java虚拟机规范中的Class文件格式中,直接引用就是直接指向目标的指针,相对偏移量或一个间接定位到目标的句柄
        - 解析动作主要针对类或接口,字段,类方法,接口方法,方法类型等,对应常量池中的CONSTANT_Class_info,ConSTANT_Fieldref_info,CONSTANT_Methodref_info等
3. 初始化
    - 初始化阶段就是执行类构造器方法`<clinit>()`的过程
    - ![20230708163645](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230708163645.png)
    - 此方法不需要定义,是Javac编译器自动收集类中的所有类变量的赋值动作和静态代码块中的语句合并而来
    - ![20230708163915](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230708163915.png)
    - 构造器方法中指令按语句在源文件中出现的顺序执行
    - ![20230708164257](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230708164257.png)
    - `<clinit>()`不同于类的构造器(关联:构造器是虚拟机视角下的`<init>()`)
    - 若该类具有父类,JVM会保证子类的`<clinit>()`执行前,父类的`<clinit>()`已经执行完毕
    - 虚拟机必须保证一个类的`<clinit>()`方法在多线程下被同步加锁

如果没有静态变量,或者静态代码块中的变量赋值,是不会有`<clinit>`方法的:
![20230708164903](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230708164903.png)

任何一个类声明以后，内部至少存在一个类的构造器,对应的就是`<init>()`方法

## 类加载器分类

- JVM支持两种类型的类加载器,分别为**引导类加载器(bootstrap ClassLoader)**和**自定义类加载器(User-Defined ClassLoader)**
- 从概念上来讲,自定义类加载器一般指的是程序中由开发人员自定义的一类类加载器,但是Java虚拟机规范却没有这么定义,而是将所有派生于抽象类ClassLoader的类加载器都划分为自定义类加载器
- 无论类加载器的类型如何划分,在程序中我们最常见的类加载器**始终只有3个**

![20230708172205](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230708172205.png)

**这里的四者之间的关系是包含关系,不是上层下层,也不是子父类继承关系**

```java
package com.zzmr.java;

/**
 * @author zzmr
 * @create 2023-07-08 17:25
 */
public class ClassLoaderTest {
    public static void main(String[] args) {
        // 获取系统类加载器
        ClassLoader systemClassLoader = ClassLoader.getSystemClassLoader();
        System.out.println(systemClassLoader); // sun.misc.Launcher$AppClassLoader@dad5dc

        // 获取其上层： 扩展类加载器
        ClassLoader extClassLoader = systemClassLoader.getParent();
        System.out.println(extClassLoader); // sun.misc.Launcher$ExtClassLoader@16d3586

        // 获取其上层-- 获取不到引导类加载器
        ClassLoader bootstrapClassLoader = extClassLoader.getParent();
        System.out.println(bootstrapClassLoader); // null

        // 对于用户自定义类来说-- 默认使用系统类加载器进行加载
        ClassLoader classLoader = ClassLoaderTest.class.getClassLoader();
        System.out.println(classLoader);// sun.misc.Launcher$AppClassLoader@dad5dc

        // String类是使用引导类加载器进行加载的--系统的核心类库都使用引导类进行加载的
        ClassLoader classLoader1 = String.class.getClassLoader();
        System.out.println(classLoader1); // null

    }
}
```

---

虚拟机自带的加载器
1. 启动类加载器(引导类加载器,bootstrap ClassLoader)
    - 这个类加载使用C/C++语言实现的,嵌套在JVM内部
    - 它用来加载Java的核心库(JAVA_HOME/jre/lib/rt.jar,resources.jar或sun.boot.class.path路径下的内容),用于提供JVM自身需要的类
    - 并不继承自java.lang.ClassLoader,没有父加载器
    - 加载扩展类和应用程序类加载器,并指定为他们的父类加载器
    - 出于安全考虑,Bootstrap启动类加载器只加载包名为java,javax,sun等开头的类
2. 扩展类加载器(Extenion classLoader)
    - Java语言编写,由sun.misc.Launcher$ExtClassLoader实现
    - 派生于ClassLoader类
    - 父类加载器为启动类加载器
    - 从java.ext.dirs系统属性所指定的目录中加载类库,或从JDK的安装目录的jre/lib/ext子目录(扩展目录)下加载类库,如果用户创建的jar放在此目录下,也会自动由扩展类加载器加载
3. 应用程序类加载器(系统类加载器-AppClassLoader)
    - java语言编写,由sun.misc.Launcher$AppClassLoader实现
    - 派生于ClassLoader类
    - 父类加载器为扩展类加载器
    - 它负责加载环境变量classpath或系统属性java.class.path指定路径下的类库
    - 该类加载是程序中默认的类加载器,一般来说,java应用的类都是由它来完成加载
    - 通过`ClassLoader#getSystemClassLoader()`方法可以获取到该类加载器

```java
package com.zzmr.java;

import com.sun.javafx.PlatformUtil;
import com.sun.net.ssl.internal.ssl.Provider;
import sun.misc.Launcher;

import java.net.URL;

/**
 * @author zzmr
 * @create 2023-07-09 11:00
 */
public class ClassLoaderTest1 {
    public static void main(String[] args) {
        System.out.println("********启动类加载器**********");
        // 获取BootstrapClassLoader能够加载的api路径
        URL[] urLs = Launcher.getBootstrapClassPath().getURLs();
        for (URL element : urLs) {
            System.out.println(element.toExternalForm());
        }
        // 从上面的路径中随意选择一个类，来看看它的类加载器是什么
        ClassLoader classLoader = Provider.class.getClassLoader();
        System.out.println(classLoader);


        System.out.println("********扩展类加载器**********");
        String extDirs = System.getProperty("java.ext.dirs");
        for (String path : extDirs.split(";")) {
            System.out.println(path);
        }
        // 从上面的路径中随意选择一个类，来看看它的类加载器是什么
        ClassLoader classLoader1 = PlatformUtil.class.getClassLoader();
        System.out.println(classLoader1);
    }
}

```

---

用户自定义类加载器

在Java的日常应用程序开发中,类的加载几乎是由上述3种类加载器相互配合执行的,在必要时,我们还可以自定义类加载器,来定制类的加载方式

为什么要自定义类加载器
- 隔离加载类
- 修改类加载的方式
- 扩展加载源
- 防止源码泄露

---

>关于ClassLoader
ClassLoader类,它是一个抽象类,其后所有的类加载器都继承自ClassLoader(不包括启动了加载器)
![20230709112455](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230709112455.png)

获取类的加载器
```java
package com.zzmr.java;

/**
 * @author zzmr
 * @create 2023-07-09 11:53
 */
public class ClassLoaderTest2 {
    public static void main(String[] args) {
        try {
            // 1.
            ClassLoader classLoader = Class.forName("java.lang.String").getClassLoader();
            System.out.println(classLoader);

            // 2.
            ClassLoader classLoader1 = Thread.currentThread().getContextClassLoader();
            System.out.println(classLoader1);

            // 3.
            ClassLoader classLoader2 = ClassLoader.getSystemClassLoader().getParent();
            System.out.println(classLoader2);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}

```

## 双亲委派机制

Java虚拟机对class文件采用的是**按需加载**的方式，也就是说当需要使用该类才会将它的class文件加载到内存中生成class对象，而且加载某个类的class文件时，Java虚拟机采用的是**双亲委派机制**，即把请求交给父类处理，它是一种任务委派模式

>工作原理
1. 如果一个类加载器收到了类加载请求,它并不会自己先去加载,而是把这个请求委托给父类的加载器去执行
2. 如果父类加载器还存在其他父类加载器,则进一步向上委托,以此递归,请求最终将到达顶层的启动类加载器
3. 如果父类加载器可以完成类加载任务,就成功返回,倘若父类加载器无法完成此加载任务,子加载器才会尝试自己去加载,这就是双亲委派模式
![20230709120828](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230709120828.png)

此时可以做一个测试
一个测试类:
```java
package com.zzmr.java;

/**
 * @author zzmr
 * @create 2023-07-09 12:00
 */
public class StringTest {
    public static void main(String[] args) {
        String str = new String();
        System.out.println("hello,zzmr.club");
    }
}

```
这里创建一个String对象
再创建一个包:
```java
package java.lang;

/**
 * @author zzmr
 * @create 2023-07-09 12:03
 */
public class String {
    static {
        System.out.println("我是自定义的String类的静态代码块");
    }
}

```

此时StringTest并不会使用我们自己定义的String类,而是使用的原始的String
以为String这个类层层往上,会被启动类加载器加载,所以就会使用原始的String

如果这么写:
```java
package java.lang;

/**
 * @author zzmr
 * @create 2023-07-09 12:03
 */
public class String {
    static {
        System.out.println("我是自定义的String类的静态代码块");
    }

    public static void main(String[] args) {
        System.out.println("he");
    }
}

```
会报错:
![20230709121830](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230709121830.png)

双亲委派机制举例图解:
![20230709122003](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230709122003.png)

---

双亲委派机制的优势
1. 避免类的重复加载
2. 保护程序安全,防止核心API被随意篡改

---

自定义String类,但是在加载自定义String类的时候会率先使用引导类加载器加载,而引导类加载器在加载的过程中会先加载jdk自带的文件`(rt.jar包中java\lang\String.class)`,报错信息说没有mian方法,就是因为加载的是rt.jar包中的String类,这样可以保证对java核心源代码的保护,这就是**沙箱安全机制**

## 其他

- 在JVM中表示两个class对象是否为同一个类存在两个必要条件:
    - 类的完整类名必须一致,包括包名
    - 加载这个类的ClassLoader(指ClassLoader实例对象)必须相同
- 换句话说,在JVM中,即使这两个类对象(class对象)来源同一个Class文件,被同一个虚拟机所加载,但只要加载它们的ClassLoader实例对象不同,那么这两个类对象也是不相等的

>对类加载器的引用
JVM必须知道一个类是由启动加载器加载的还是由用户类加载器加载的,如果一个类型是由用户类加载器加载的,那么JVM会将这个类加载器的一个引用作为类型信息的一部分保存在方法区中,当解析一个类型到另一个类型的引用的时候,JVM需要保证这两个类型的类加载器是相同的



