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
![第01章_JVM所处位置](https://img01.zzmr.club/img/第01章_JVM所处位置.jpg)

## JVM的整体结构
- HotSpot VM是目前市面上高性能虚拟机的代表作之一
- 它采用解释器与即时编译器并存的架构
- 在今天,Java程序的运行性能早已脱胎换骨,已经达到了以C/C++程序一较高下的的地步

![第02章_JVM架构-简图](https://img01.zzmr.club/img/第02章_JVM架构-简图.jpg)
*注意,这个图要会画...*

执行引擎(Execution Engine)的图解:
![第02章_JVM架构-中](https://img01.zzmr.club/img/第02章_JVM架构-中.jpg)
英文版:
![第02章_JVM架构-英](https://img01.zzmr.club/img/第02章_JVM架构-英.jpg)

---

>Java代码执行流程
![20230707102218](https://img01.zzmr.club/img/20230707102218.png)

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

![第02章_JVM架构-简图](https://img01.zzmr.club/img/第02章_JVM架构-简图.jpg)

- 类加载器子系统负责从文件系统或网络中加载class文件,class文件在文件开头有特定的文件标识
- ClassLoader只负责文件的加载,至于它是否可以运行,则由ExecutionEngine决定
- 加载的类信息存放于一块称为方法去的内存空间,除了类的信息外,方法区中还有存放运行时常量池信息,可能还包括字符串字面量和数字常量(这部分常量信息是Class文件中常量池部分的内存映射)

![20230708103023](https://img01.zzmr.club/img/20230708103023.png)

>类加载器ClassLoader角色
1. class file存在于本地磁盘上,可以理解为设计师画在纸上的模板,而最终这个模板在执行的时候是要加载到JVM当中来根据这个文件实例化出n个一摸一样的实例
2. class flie加载到JVM中,被称为DNA元数据模板,放在方法区
3. 在.class文件->JVM->最终成为元数据模板,此过程就要一个运输工具(类装载器Class loader),扮演一个快递员的角色.

![20230708103708](https://img01.zzmr.club/img/20230708103708.png)

---

类加载过程:
![第02章_类的加载过程](https://img01.zzmr.club/img/第02章_类的加载过程.jpg)

还有一张细分的图:
![20230708104839](https://img01.zzmr.club/img/20230708104839.png)

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
    - ![20230708163645](https://img01.zzmr.club/img/20230708163645.png)
    - 此方法不需要定义,是Javac编译器自动收集类中的所有类变量的赋值动作和静态代码块中的语句合并而来
    - ![20230708163915](https://img01.zzmr.club/img/20230708163915.png)
    - 构造器方法中指令按语句在源文件中出现的顺序执行
    - ![20230708164257](https://img01.zzmr.club/img/20230708164257.png)
    - `<clinit>()`不同于类的构造器(关联:构造器是虚拟机视角下的`<init>()`)
    - 若该类具有父类,JVM会保证子类的`<clinit>()`执行前,父类的`<clinit>()`已经执行完毕
    - 虚拟机必须保证一个类的`<clinit>()`方法在多线程下被同步加锁

如果没有静态变量,或者静态代码块中的变量赋值,是不会有`<clinit>`方法的:
![20230708164903](https://img01.zzmr.club/img/20230708164903.png)

任何一个类声明以后，内部至少存在一个类的构造器,对应的就是`<init>()`方法

## 类加载器分类

- JVM支持两种类型的类加载器,分别为**引导类加载器(bootstrap ClassLoader)**和**自定义类加载器(User-Defined ClassLoader)**
- 从概念上来讲,自定义类加载器一般指的是程序中由开发人员自定义的一类类加载器,但是Java虚拟机规范却没有这么定义,而是将所有派生于抽象类ClassLoader的类加载器都划分为自定义类加载器
- 无论类加载器的类型如何划分,在程序中我们最常见的类加载器**始终只有3个**

![20230708172205](https://img01.zzmr.club/img/20230708172205.png)

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
![20230709112455](https://img01.zzmr.club/img/20230709112455.png)

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
![20230709120828](https://img01.zzmr.club/img/20230709120828.png)

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
![20230709121830](https://img01.zzmr.club/img/20230709121830.png)

双亲委派机制举例图解:
![20230709122003](https://img01.zzmr.club/img/20230709122003.png)

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

---

Java程序对类的使用方式分为:主动使用和被动使用
- 主动使用,又分为七种情况
    - 创建类的实例
    - 访问某个类或接口的静态变量,或者对该静态变量赋值
    - 调用类的静态方法
    - 反射比如`Class.forName("com.zzmr.Test")`
    - 初始化一个类的子类
    - Java虚拟机启动时被标明为启动类的类
    - JDK7开始提供的动态语言支持:*java.lang.invoke.MethodHandle实例的解析结果,REF_getStatic,REF_putStatc,REF_invokeStatic句柄对应的类没有初始化,则初始化*
- 除了以上七种情况,其他使用Java类的方式都被看做是对**类的被动使用,都不会导致类的初始化**

# 运行时数据区概述及线程

运行时数据区简图:
![20230709180551](https://img01.zzmr.club/img/20230709180551.png)

内存是非常重要的系统资源,是硬盘和CPU的中间仓库及桥梁,承载着操作系统和应用程序的实时运行,JVM内存布局规定了Java在运行过程中内存申请,分配,管理的策略,保证了JVM的高效稳定运行,**不同的JVM对于内存的划分方式和管理机制存在着部分差异**,结合JVM虚拟机规范,来探讨一下经典的JVM内存布局

Java虚拟机定义了若干种程序运行期间会使用到的运行时数据区,其中有一些会随着虚拟机启动而创建,随着虚拟机退出而销毁,另外一些则是与线程一一对应的,这些与线程对应的数据区域会随着线程开始和结束而创建和销毁

灰色的为单独线程私有的,红色的为多个线程共享的,即:
- 每个线程:独立包括程序计数器,栈,本地栈
- 线程间共享:堆,堆外内存(永久代或元空间,代码缓存)

---

**Runtime**实例-每一个JVM(应用程序)对应一个Runtime实例

## 线程

- 线程是一个程序里的运行单元,JVM允许一个应用有多个线程并行的执行
- 在Hotspot JVM里,每个线程都与操作系统的本地线程直接映射
    - 当一个Java线程准备好执行以后,此时一个操作系统的本地线程也同时创建,Java线程执行终止后,本地线程也会回收
- 操作系统负责所有线程的安排调度到任何一个可用的CPU上,一旦本地线程初始化成功,它就会调用Java线程中的run()方法
- 如果你使用jconsole或者任何一个调试工具,都能看到在后台有许多线程在运行,这些后台线程不包括调用`public static void main(String[])`的main线程以及所有这个线程自己创建的线程
- 这些主要的后台系统线程在Hotspot JVM里主要是以下几个:
![20230711185443](https://img01.zzmr.club/img/20230711185443.png)

## 程序计数器(PC寄存器)

![20230711190245](https://img01.zzmr.club/img/20230711190245.png)

JVM中的程序计数寄存器(Program Counter Register)中,Register的命令源于CPU的寄存器,寄存器存储指令相关的现场信息,CPU只有把数据装载到寄存器才能够运行

这里,并非是广义上所指的物理寄存器,或许将其翻译为PC计数器(或者指令计数器)会更加贴切(也称为程序钩子),并且也不容易引起一些不必要的误会,**JVM中的PC寄存器是对物理PC寄存器的一种抽象模拟**

作用:PC寄存器用来存储指向下一条指令的地址,也即将要执行的指令代码,由执行引擎读取下一条指令
1. 它是一块很小的内存空间,几乎可以忽略不记,也是运行速度最快的存储区域
2. 在JVM规范中,**每个线程都有它自己的程序计数器**,是线程私有的,生命周期与线程的生命周期保持一致
3. **任何时间一个线程都只有一个方法在执行,**也就是所谓的**当前方法**,程序计数器会存储当前线程正在执行的Java方法的JVM指令地址;或者,如果是在执行native方法,则是未指定值(undefined)
4. 它是程序控制流的指示器,分支,循环,跳转,异常处理,线程恢复等基础功能都需要依赖这个计数器来完成
5. 字节码解释器工作时就是通过改变这个计数器的值来选取下一条需要执行的字节码指令
6. 它是唯一一个Java虚拟机规范中没有规定任何`OutOtMemoryError`情况的区域

### PC寄存器的使用举例

![20230711193122](https://img01.zzmr.club/img/20230711193122.png)
- 最左边的数值是**指令地址(偏移地址)**
- 第二列`bipush,iadd什么的`是**操作指令**

对于`5`来说,PC寄存器存储的就是这个`5`,执行引擎会取PC寄存器中存储的指令地址对应的操作指令(操作局部变量表,操作数栈),并翻译为机器指令,CPU再执行该指令

看下图:
![20230711193839](https://img01.zzmr.club/img/20230711193839.png)

### 两个常见问题

>使用PC寄存器存储字节码指令地址有什么用呢?

因为CPU需要不停的切换各个线程,这时候切换回来以后,就得知道接着从哪开始继续执行

>为什么使用PC寄存器记录当前线程的执行地址呢?

JVM的字节码解释器就需要通过改变PC寄存器的值来明确吓一跳应该执行什么样的字节码指令

>PC寄存器为什么会被设定为线程私有?
我们都知道所谓的多线程在一个特定的时间段内只会执行其中某一个线程的方法,CPU会不停地做任务切换,这样必然导致经常中断或恢复,如何保证分号无差呢,**为了能够准确地记录各个线程正在执行的当前字节码指令地址,最好的办法自然是每一个线程都分配一个PC寄存器**,这样一来各个线程之间便可以进行独立计算,从而不会出现相互干扰的情况

由于CPU时间片轮转限制,众多线程在并发执行过程中,任何一个确定的时刻,一个处理器或者多核处理器中的一个内核,只会执行某个线程中的一条指令

这样必然导致经常中断或恢复,如何保证分号无差呢?每个线程在创建后,都会产生自己的程序计数器和栈帧,程序计数器在各个线程之间互不影响
![20230711195425](https://img01.zzmr.club/img/20230711195425.png)

---

CPU时间片
CPU时间片即CPU分配给各个程序的时间,每个线程被分配一个时间段,称作它的时间片
在宏观上:我们可以同时打开多个应用程序,每个程序并行不悖,同时运行
但在微观上:由于只有一个CPU,一次只能处理程序要求的一部分,如何处理公平,一种方法就是引入时间片,每个程序轮流执行
![20230711195707](https://img01.zzmr.club/img/20230711195707.png)

## 虚拟机栈

### 虚拟机栈概述

出现的背景:
由于跨平台性的设计,Java的指令都是跟据栈来设计的,不同平台CPU架构不同,寄存器结构也就不同,所以不能设计为基于寄存器的

**优点是跨平台,指令集小,编译器容易实现,缺点是性能下降,实现同样的功能需要更多的指令**

---

>有不少Java开发人员一提到Java内存结构,就会非常粗粒度地将JVM中的内存区理解为仅有Java堆(heap)和Java栈(stack)为什么

**栈是运行时的单位,而堆是存储的单位**

即:栈解决程序的运行问题,即程序如何执行,或者说如何处理数据,堆解决的是数据存储问题,即数据怎么放,放在哪

---

- Java虚拟机栈是什么?
    - Java虚拟机栈(Java Virtual Machine Stack),早期也叫Java栈,每个线程在创建时都会创建一个虚拟机栈,其内部保存一个个的栈帧(Stack Frame),对应着一次次的Java方法调用(是线程私有的)
- 生命周期
    - 生命周期和线程一致
- 作用
    - 主管Java程序的运行,它保存方法的局部变量(8种基本数据类型,对象的引用地址),部分结果,并参与方法的调用和返回
    - 局部变量 vs 成员变量(或属性)
    - 基本数据变量 vs 引用类型变量(类,数组,接口)

栈的特点(优点)
- 栈是一种快速有效的分配存储方式,访问速度仅次于程序计数器
- JVM直接对Java栈的操作只有两个
    - 每个方法执行,伴随着进栈(入栈,压栈)
    - 执行结束后的出栈工作
- 对于栈来说不存在垃圾回收问题

![20230712220103](https://img01.zzmr.club/img/20230712220103.png)


---

面试题:开发中遇到的异常有哪些

栈中可能出现的异常
Java虚拟机规范允许**Java栈的大小是动态的或者是固定不变的**
- 如果采用固定大小的Java虚拟机栈,那每一个线程的Java虚拟机栈容量可以在创建的时候独立选定,如果线程请求分配的栈容量超过Java虚拟机栈允许的最大容量,Java虚拟机会抛出一个`StackOverflowError`异常
- 如果Java虚拟机栈可以动态扩展,并且在尝试扩展的时候无法申请到足够的内存,或者在创建新的线程时没有足够的内存去创建对应的虚拟机栈,那Java虚拟机会抛出一个`OutOtMemoryError`异常


```java
package com.zzmr.java;

/**
 * 演示栈中的异常
 *
 * @author zzmr
 * @create 2023-07-12 22:21
 */
public class StackErrorTest {
    public static void main(String[] args) {
        main(args);
    }
}
```
报错:![20230712222201](https://img01.zzmr.club/img/20230712222201.png)

---

### 设置栈内存大小

我们可以使用参数-Xss选项来设置线程的最大栈空间,栈的大小直接决定了函数调用的最大可达深度
![20230712222936](https://img01.zzmr.club/img/20230712222936.png)
```java
package com.zzmr.java;

/**
 * @author zzmr
 * @create 2023-07-12 22:23
 * 默认情况，count=6295
 * 设置栈的大小为 -Xss256k
 * 变成了4950
 */
public class StackDeepTest {
    private static int count = 1;

    public static void main(String[] args) {
        System.out.println(count);
        count++;
        main(args);
    }
}
```

### 栈的存储单位

栈中存储什么?
1. 每个线程都有自己的栈,栈的数据都是以`栈帧(Stack Frame)`的格式存在
2. 在这个线程上正在执行的每个方法都各自对应一个栈帧
3. 栈帧是一个内存区快,是一个数据集,维系着方法执行过程中的各种数据信息
4. JVM直接对Java栈的操作只有两个,就是对栈帧的**压栈**和**出栈**,遵循先进后出的原则
5. **在一条活动线程中,一个时间点上,只会有一个活动的栈帧**,即只有当前正在执行的方法的栈帧(栈顶栈帧)是有效的,这个栈帧(Current Frame),与当前帧帧相对应的方法就是当前方法(Current Method),定义这个方法的类就是当前类(Current Class)
6. 执行引擎运行的所有字节码指令只针对当前帧帧进行操作
7. 如果在该方法中调用了其他方法,对应新的栈帧就会被创建出来,放在栈的顶端,成为新的当前帧

```java
package com.zzmr.java;

/**
 * @author zzmr
 * @create 2023-07-21 12:05
 */
public class StackFrameTest {
    public static void main(String[] args) {
        StackFrameTest test = new StackFrameTest();
        test.method1();
    }

    public void method1() {
        System.out.println("method1()开始执行...");
        method2();
        System.out.println("method1()执行结束");

    }

    private int method2() {
        System.out.println("method2()开始执行...");
        int i = 10;
        int m = (int) method3();
        System.out.println("method2()即将结束");
        return i + m;
    }

    private double method3() {
        System.out.println("method3()开始执行...");
        double j = 20.0;
        System.out.println("method3()即将结束");
        return j;
    }
}
```

![20230721121322](https://img01.zzmr.club/img/20230721121322.png)
就是一个嵌套的感觉

![第05章_方法与栈桢](https://img01.zzmr.club/img/第05章_方法与栈桢.jpg)

---

>栈运行原理
- 不同线程中所包含的栈帧是不允许存在相互引用的,即不可能在一个栈帧之中应用另外一个线程的栈帧
- 如果当前方法调用了其他方法,方法返回之际,当前栈帧会传回此方法的执行结果给前一个栈帧,接着,虚拟机会丢弃当前栈帧,使得前一个栈帧重新成为当前栈帧
- Java方法有两种返回函数的方式,一种是正常的函数返回,使用return指令,另外一种是抛出异常,不管使用哪种方式,都会导致栈帧被弹出

### 栈帧的内部结构

每个栈帧中存储着:
- 局部变量表(Local Variables)
- 操作数栈(Operand Stack)或表达式栈
- 动态链接(Dynamic Linking)或指向运行时常量池的方法引用
- 方法返回地址(Return Address)或方法正常退出或异常退出的定义
- 一些附加信息
![第05章_栈桢内部结构](https://img01.zzmr.club/img/第05章_栈桢内部结构.jpg)

#### 局部变量表

- 局部变量表也被称之为局部变量数组或本地变量表
- **定义为一个数字数组,主要用于存储方法参数和定义在方法体内的局部变量**,这些数据类型包括各类基本数据类型,对象引用(reference)以及returnAddress类型
- 由于局部变量是建立在线程的栈上,是线程的私有数据,**因此不存在数据安全问题**
- **局部变量表所需的容量大小是在编译期确定下来的**,并保存在方法的Code属性的`maximum local variables`数据项中,在方法运行期间是不会改变局部变量表的大小的
- **方法嵌套调用的次数由栈的大小决定,一般来说,栈越大,方法嵌套调用次数越多**,对一个函数而言,它的参数和局部变量越多,使得局部变量表膨胀,它的栈帧就越大,以满足方法调用所需传递的信息增大的需求,进而函数调用就会占用更多的栈空间,导致其嵌套调用次数就会减少
- **局部变量表中的变量只在当前方法调用中有效**,在方法执行时,虚拟机通过使用局部变量表完成参数值到参数列表的传递过程,当方法调用结束后,**随着方法栈帧的销毁,局部变量表也会随之销毁**

main方法:
![20230721132426](https://img01.zzmr.club/img/20230721132426.png)

#### 关于Slot的理解

- 参数值的存放总是在局部变量数组的index0开始的,到数组长度-1的索引结束
- 局部变量表,最基本的存储单元是`slot(变量槽)`
- 局部变量表中存放编译期可知的各种基本数据类型(8种),引用类型(reference),returnAddress类型的变量
- 在局部变量表中,32位以内的类型只占用一个slot(包括returnAddress类型),64位的类型(long和double)占用两个slot
    - byte,short,char在存储前被转换为int,boolean也被转换成int,0表示false,非零表示true
    - long和double则占据两个slot
![20230722162102](https://img01.zzmr.club/img/20230722162102.png)

```txt
这里刚好能理解为什么static方法中不能使用this了
因为this变量不存在于当前静态方法的局部变量表中,所以引用不到
```

---

比如下面这个非静态方法:
```java
    public void test1() {
        Date date = new Date();
        String name1 = "atguigu.com";
        String info = test2(date, name1);
        System.out.println(date + name1);
    }
```

它的局部变量表中就有4个变量,也就是说存在`this`,且放在首位
![20230722162657](https://img01.zzmr.club/img/20230722162657.png)

test2中,由于weight是double类型的,会占用两个slot,weight的开始索引是3,所以下一个gender的开始索引就是5
![20230722163137](https://img01.zzmr.club/img/20230722163137.png)

>**栈帧中的局部变量表中的槽位是可以重用的**,如果一个局部变量过了其作用域,那么在其作用域之后申明的新的局部变量就很有可能会复用过期局部变量的槽位,从而**达到节省资源额目的**
```java
    public void test4() {
        int a = 0;
        {
            int b = 0;
            b = a + 1;
        }
        // 变量c使用之前已经销毁的变量b占据的slot的位置
        int c = a + 1;
    }
```

b的作用域只在代码块中,出了代码块,b就被销毁了,此时b的空间还在,所以c就直接使用的b的空间:
![20230722164207](https://img01.zzmr.club/img/20230722164207.png)

---

静态变量与局部变量的对比
- 参数表分配完毕之后,再根据方法体内定义的变量的顺序和作用域分配
- 我们知道类变量表有两次初始化的机会,第一次是在准备阶段,执行系统初始化,对类变量设置零值,另一次则是在初始化阶段,赋予程序员在代码中定义的初始值
- 和类变量初始化不同的是,局部变量表不存在系统初始化的过程,这意味着一旦定义了局部变量则必须认为的初始化,否则无法使用
```txt
变量的分类：按照数据类型分：① 基本数据类型  ② 引用数据类型
                按照在类中声明的位置分：
① 成员变量：在使用前，都经历过默认初始化赋值
                                类变量： linking的prepare阶段：给类变量默认赋值  ---> initial阶段：给类变量显式赋值即静态代码块赋值
                                实例变量：随着对象的创建，会在堆空间中分配实例变量空间，并进行默认赋值
② 局部变量：在使用前，必须要进行显式赋值的！否则，编译不通过
```

---

补充:

- 在栈帧中,与性能调优琯溪最为密切的部分就是前面提到的局部变量表,在方法执行时,虚拟机使用局部变量表完成方法的传递
- 局部变量表中的变量也是重要的垃圾回收根节点,只要被局部变量表中直接或间接引用的对象都不会被回收

#### 操作数栈

- 每一个独立的栈帧中除了包含局部变量表以外,还包含一个**后进先出**的操作数栈,也可以称之**表达式栈**
- **操作数栈,在方法执行过程中,根据字节码指令,往栈中写入数据或提取数据,即入栈/出栈**
    - 某些字节码指令将值压入操作数栈,其余的字节码指令将操作数去除栈,使用它们后再把结果压入栈
    - 比如:执行复制,交换,求和等操作
- ![20230722170500](https://img01.zzmr.club/img/20230722170500.png)
- 操作数栈,**主要用于保存计算过程的中间结果,同时作为计算过程中变量临时的存储空间**
- 操作数栈就是JVM执行引擎的一个工作区,当一个方法刚开始执行的时候,一个新的栈帧也会随之被创建出来,**这个方法的操作数栈是空的**
- 每一个操作数栈都会拥有一个明确的栈深度用于存储数值,其所需的最大深度在编译器就定好了,保存在方法的Code属性中,为max_stack的值
- 栈中的任何一个元素都是可以任意的Java数据类型
    - 32bit的类型占用一个栈单位深度
    - 64bit的类型占用两个栈单位深度
- 操作数栈**并且采用访问索引的方式来进行数据访问的**,而是只能通过标准的入栈,出栈操作来完成一次数据访问

---

代码如下:
```java
package com.zzmr.java;

/**
 * @author zzmr
 * @create 2023-07-22 17:23
 */
public class OperandStackTest {
    public void testAddOperation() {
        byte i = 15;
        int j = 8;

        int k = i + j;
    }
}
```

得到的字节码指令:
![20230722173635](https://img01.zzmr.club/img/20230722173635.png)

- byte,short,char,boolean,都以int型来保存,所以是`bipush`,然后将15放到操作数栈中
- 第二行,`istore_1`,表示将上一步放入操作数栈的数据放到局部变量表中,索引为1(因为此方法是非静态的,所以0索引是this变量)
- 下面的`bipush 8`和`istore_2`也同理
- `iload_1`和`iload_2`表示将数据从局部变量表中取出1,2,然后放到操作数栈中,入栈
- `iadd`就是出栈,然后将两数相加,最终将结果放到操作数栈的栈顶
- `istore_3`将结果存入局部变量表中,索引为3
![20230722174428](https://img01.zzmr.club/img/20230722174428.png)
- 如果被调用的方法带有返回值的话,其返回值将会被压入当前栈帧的操作数栈中,并更新PC寄存器中下一条需要执行的字节码指令
- 操作数栈中元素的数据类型必须与字节码指令的序列严格匹配,这由编译器在编译期间进行验证,同时在类加载过程中的类检验阶段的数据流分析阶段要再次验证
- 另外,我们说Java虚拟机的**解释引擎是基于栈的执行引擎**,其中的栈指的就是操作数栈

```java

    public int getSum() {
        int m = 10;
        int n = 20;
        int k = m + n;
        return k;
    }

    public void testGetSum() {
        // 获取上一个栈帧返回的结果，并保存在操作数栈中
        int i = getSum();
        int j = 10;
    }
```
执行了`aload_0`
![20230722181644](https://img01.zzmr.club/img/20230722181644.png)


---

```java
    /**
     * 程序面试题:
     * i++和++i的区别:放到字节码篇章再介绍
     */
    public void add() {
        // 第一类问题
        int i1 = 10;
        i1++;

        int i2 = 10;
        ++i2;

        // 第二类问题
        int i3 = 10;
        int i4 = i3++;

        int i5 = 10;
        int i6 = ++i5;

        // 第三类问题
        int i7 = 10;
        i7 = i7++;

        int i8 = 10;
        i8 = ++i8;

        // 第四类问题
        int i9 = 10;
        int i10 = i9++ + ++i9;
    }
```

这么恶心的题吗哈哈哈操
![20230722182213](https://img01.zzmr.club/img/20230722182213.png)

第一个问题,可以根据字节码指令来看,会发现字节码指令是完全一样的,这也就说明,第一种是一样的情况
后面几个问题后续再讲

>栈顶缓存(Top-of-Stack Cashing)技术

前面提到,基于栈式架构的虚拟机所使用的零地址指令更加紧凑,但完成一项操作的时候必然需要使用**更多的入栈和出栈指令**,这同时也就意味着将需要更多的指令分派次数和内存读/写次数

由于操作数是存储在内存中的,因此频繁地执行内存读/写操作必然会影响执行速度,为了解决这个问题,就提出了栈顶缓存技术:**将栈顶元素全部缓存在屋里CPU的寄存器中,以此降低对内存的读/写次数,提升执行引擎的执行效率**

*寄存器:指令更少,执行速度更快*

#### 动态链接

方法返回地址,动态链接,一些附加信息三者被称为**帧数据区**

- 每一个栈帧内部都包含一个指向*运行时常量池***中该栈帧所属方法的引用**,包含这个引用的目的就是为了支持当前方法的代码能够实现动态链接(Dynamic Linking),比如:invokedynamice指令
- 在Java源文件被编译到字节码文件中时,所有的变量和方法引用都作为符号引用(Symbolic reference)保存在class文件的常量池里,比如:描述一个方法调用了另外的其他方法时,就是通过常量池中指向方法的符号用来表示的,那么**动态链接的作用就是为了将这些符号引用转换为调用方法的直接引用**

#### **方法的调用**

在JVM中,**将符号引用转换为调用方法的直接引用**与方法的绑定机制相关

- 静态链接:当一个字节码文件被装载进JVM内部时,如果**被调用的目标方法在编译期可知且运行期保持不变时**,这种情况下调用方法的符号引用转换为直接引用的过程称之为静态链接
- 动态链接:如果被调用的方法在编译器无法被确定下来,也就是说,只能够在程序运行期将调用方法的符号引用转换为直接引用,由于这种引用转换过程具备动态性,因此也就称为动态链接

对应的方法的绑定机制为:早期绑定和晚期绑定,**绑定是一个字段,方法或者类在符号引用被替换为直接引用的过程,这仅仅发生一次**

- 早期绑定:早期绑定就是指被调用的**目标方法如果在编译期可知,且运行期保持不变时**,即可将这个方法与所属的类型进行绑定,这样一来,由于明确了被调用的目标方法究竟是哪一个,因此也就可以使用静态链接的方式将符号引用转换为直接引用
- 晚期绑定:如果**被调用的方法在编译期无法被确定下来,只能够在程序运行期间根据实际的类型绑定相关的方法**,这种绑定方式被称为晚期绑定

---

**虚方法与非虚方法**
- 如果方法在编译期就确定了具体的调用版本,这个版本在运行时是不可变的,这样的方法称为**非虚方法**
- 静态方法,私有方法,final方法,实例构造器,父类方法都是非虚方法
- 其他方法称为虚方法

虚拟机中提供了以下几条方法调用指令:
1. 普通调用指令:
    - **invokestatic:调用静态方法,解析阶段确定唯一方法版本**
    - **invokespeial:调用`<init>`方法,私有及父类方法,解析阶段确定唯一方法版本**
    - invokevirtual:调用所有虚方法
    - invokeinterface:调用接口方法
2. 动态调用指令
    - invokedynamic:动态解析出所需要调用的方法,然后执行

前四条指令固化在虚拟机内部,方法的调用执行不可人为干预,而invokedynamic指令则支持由用户确定方法版本,其中invokestatic指令和invokespecial指令调用的方法称为非虚方法,其余(final修饰除外)称为虚方法

---

**方法重写的本质**
1. 找到操作数栈的第一个元素所执行的对象的实际类型,记作C
2. 如果在类型C中找到与常量中的描述符合简单名称都相符的方法,则进行访问权限校验,如果通过则返回这个方法的直接引用,查找过程结束;如果不通过,则返回`Java.lang.IllegalAccessError`异常
3. 否则,按照继承关系从下往上以此对C的各个父类进行第二步的搜索和验证过程
4. 如果始终没有找到合适的方法,则抛出`java.lang.AbstactMethodError`异常

`Java.lang.IllegalAccessError`介绍
程序试图访问或修改一个属性或调用一个方法,这个属性或方法,你没有权限访问,一般的,这个会引起编译器异常,这个错误如果发生在运行时,就说明一个类发生了不兼容的改变

---

**虚方法表**
- 在面向对象的编程中,会很频繁的使用到动态分派,如果在每次动态分派的过程中都要重新在类的方法元数据中搜索合适的目标就可能影响到执行效率,因此,为了提高性能,JVM采用在类的方法区建立一个虚方法表(非虚方法不会出现在此表中)来实现,使用索引表来代替查找
- **每个类中都有一个虚方法表**,表中存放着各个方法的实际入口
- 虚方法表在类加载的链接阶段被创建并开始初始化,类的变量初始值准备完成之后,JVM会把该类的方法表也初始化完成

#### 方法返回地址

- 存放调用该方法的PC寄存器的值
- 一个方法的结束,有两种方式:
    - 正常执行完成
    - 出现未处理的异常,非正常退出
- 无论通过哪种方式退出,在方法退出后都返回到该方法被调用的位置,方法正常退出时,**调用者的PC计数器的值作为返回地址,即调用该方法的指令的下一条指令的地址**,而通过异常退出的,返回地址是通过异常表来确定的,栈帧中一般不会保存这部分的信息


本质上,方法的退出就是当前栈帧出栈的过程,此时,需要恢复上层方法的局部变量表,操作数栈,将返回值压入调用者栈帧的操作数栈,设置PC寄存器值等,让调用者方法继续执行下去

**正常完成出口和异常完成出口的区别在于:通过异常完成出口退出的不会给他的上层调用者产生任何的返回值**

---

当一个方法开始执行后,只有两种方式可以退出这个方法
1. 执行引擎遇到任意一个方法返回的字节码指令(return),会有返回值传递给上层的方法调用者,简称正常完出口
    - 一个方法在正常调用完成之后究竟需要使用哪一个返回指令还需要根据方法返回值的实际数据类型而定
    - 在字节码指令中,返回指令包含`ireturn(当返回值是boolean,byte,char,short和int类型时使用),lreturn,freturn,dreturn,以及areturn`,另外还有一个return指令供声明为void的方法,实例初始化方法,类和接口的初始化方法使用
2. 在方法执行的过程中遇到了异常,并且这个异常没有在方法内进行处理,也就是说只要在本方法的异常表中没有搜索到匹配的异常处理,就会导致方法退出,简称异常完成出口
    - 方法执行过程中抛出异常时的异常处理,存储在一个异常处理表,方便在发生异常的时候找到处理异常的代码
    - ![20230725111939](https://img01.zzmr.club/img/20230725111939.png)

*各种返回类型对应的情况不同:*
![20230725111330](https://img01.zzmr.club/img/20230725111330.png)

```java
package com.zzmr.java;

import java.io.FileReader;
import java.io.IOException;
import java.util.Date;

/**
 *
 * 返回指令包含ireturn（当返回值是boolean、byte、char、short和int类型时使用）、
 * lreturn、freturn、dreturn以及areturn，另外还有一个return指令供声明为void的方法、
 * 实例初始化方法、类和接口的初始化方法使用。
 *
 * @author shkstart
 * @create 2020 下午 4:05
 */
public class ReturnAddressTest {
    public boolean methodBoolean() {
        return false;
    }

    public byte methodByte() {
        return 0;
    }

    public short methodShort() {
        return 0;
    }

    public char methodChar() {
        return 'a';
    }

    public int methodInt() {
        return 0;
    }

    public long methodLong() {
        return 0L;
    }

    public float methodFloat() {
        return 0.0f;
    }

    public double methodDouble() {
        return 0.0;
    }

    public String methodString() {
        return null;
    }

    public Date methodDate() {
        return null;
    }

    public void methodVoid() {

    }

    static {
        int i = 10;
    }


    //
    public void method2() {

        methodVoid();

        try {
            method1();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void method1() throws IOException {
        FileReader fis = new FileReader("atguigu.txt");
        char[] cBuffer = new char[1024];
        int len;
        while ((len = fis.read(cBuffer)) != -1) {
            String str = new String(cBuffer, 0, len);
            System.out.println(str);
        }
        fis.close();
    }


}
```

>一些附加信息
*在一些课中被省略的部分*
栈帧中还允许携带与Java虚拟机实现相关的一些附加信息,例如:对程序调试提供支持的信息

### 栈的相关面试题

1. 举例栈溢出的情况(StackOverflowError)?
    - 通过-Xss设置栈的大小;OOM(内存不足)
2. 调整栈的大小,就能保证部出现溢出吗?
    - 不能,调整大小,可以让StackOverflowError出现的较晚一些,但是还是可能存在溢出的
3. 分配的栈内存越大越好吗?
    - `治标不治本`
4. 垃圾回收是否会涉及到虚拟机栈?
    - 不会涉及到,`因为栈只有出栈和入栈的情况`
5. 方法中定义的局部变量是否线程安全?
    - 具体问题具体分析
    - 线程安全:如果只有一个线程才可以操作此数据,则必是线程安全的,如果有多个线程操作此数据,则此数据是共享数据,如果不考虑同步机制的话,会存在线程安全问题.

```java
package com.zzmr.java;

/**
 * 面试题：
 * 方法中定义的局部变量是否线程安全？具体情况具体分析
 *
 *   何为线程安全？
 *      如果只有一个线程才可以操作此数据，则必是线程安全的。
 *      如果有多个线程操作此数据，则此数据是共享数据。如果不考虑同步机制的话，会存在线程安全问题。
 * @author shkstart
 * @create 2020 下午 7:48
 */
public class StringBuilderTest {

    int num = 10;

    //s1的声明方式是线程安全的
    public static void method1(){
        //StringBuilder:线程不安全
        StringBuilder s1 = new StringBuilder();
        s1.append("a");
        s1.append("b");
        //...
    }
    //sBuilder的操作过程：是线程不安全的
    public static void method2(StringBuilder sBuilder){
        sBuilder.append("a");
        sBuilder.append("b");
        //...
    }
    //s1的操作：是线程不安全的
    public static StringBuilder method3(){
        StringBuilder s1 = new StringBuilder();
        s1.append("a");
        s1.append("b");
        return s1;
    }
    //s1的操作：是线程安全的
    public static String method4(){
        StringBuilder s1 = new StringBuilder();
        s1.append("a");
        s1.append("b");
        return s1.toString();
    }

    public static void main(String[] args) {
        StringBuilder s = new StringBuilder();


        new Thread(() -> {
            s.append("a");
            s.append("b");
        }).start();

        method2(s);

    }

}
```

## 本地方法接口

*先讲这个,然后再回到运行时数据区看本地方法栈*

>什么是本地方法?
**简单地讲,一个Native method就是一个Java调用非Java代码的接口**,一个Native Method是这样一个Java方法:该方法的实现由非Java语言实现,比如C,这个特征并非Java所特有,很多其他的编程语言都有这一机制

在定义一个native method时,并不提供实现体,因为其实现体是由非Java语言在外面实现的

本地接口的作用是融合不同的编程语言为Java所用,它的初衷是融合C/C++程序

```java
package com.zzmr.java;

/**
 * @author zzmr
 * @create 2023-07-26 20:37
 */
public class IHaveNatives {

    public native void Native1(int x);

    native static public long Native2();

    native synchronized private float Native3();

    native void Native4(int[] ary) throws Exception;

}
```

**native和abstract**不能共用

---

>为什么要使用Native Method?
Java使用起来非常方便,然后有些层次的任务用Java实现起来不容易,或者我们对程序的效率很在意时,问题就来了
1. 与Java环境外交互
    - **有时Java应用需要与Java外面的环境交互,这是本地方法存在的主要原因**,Java需要与一些底层系统,如操作系统或某些硬件交换信息时,本地方法正是这样一种交流机制,它为我们提供了一个非常简洁的接口,而且我们无需去了解Java应用之外的繁琐的细节
2. 与操作系统的交互
    - JVM支持着Java语言本身和运行时库,它是Java程序赖以生存的平台,它由一个解释器(解释字节码)和一些连接到本地代码的库组成,然后不管怎样,他毕竟不是一个完整的系统,它经常依赖于一些底层系统的支持,这些底层系统常常是强大的操作系统,**通过本地方法,我们得以用Java实现了jre的与底层系统的交互,甚至JVM的一些部分就是C写的**,还有,如果我们要使用一些Java语言本身没有提供封装的操作系统的特性时,我们也需要使用本地方法
3. Sun's Java
    - **Sun的解释器是C实现的,这使得它能像一些普通的C一样与外部交互**,jre大部分是Java实现的,它也通过一些本地方法与外界交互,例如:类java.lang.Thread的setPriority()方法是用Java实现的,但是它实现调用的是该类里的本地方法setPriority0(),这个本地方法是用C实现的,并被植入JVM内部,在Windows 95的平台上,这个本地方法最终将被调用win32 SetPriority() API,这是一个本地方法的具体实现,由JVM子哦姐提供,更多的情况是本地方法由外部的动态链接库提供,然后被JVM调用.

---

>现状
目前该方法使用的越来越少了,除非是与硬件有关的应用,比如通过Java程序驱动打印机或者Java系统管理生产设备,在企业级应用中已经比较少见,因为现在的异构领域间的通信很发达,比如可以使用Socket通信,也可以使用Web Service等等.

## 本地方法栈

- **Java虚拟机栈用于管理Java方法的调用,而本地方法栈用于管理本地方法的调用**
- 本地方法栈,也是线程私有的
- 允许被实现成固定或者是可动态扩展的内存大小(在内存溢出方面是相同的)
    - 如果被线程请求分配的栈容量超过本地方法栈允许的最大容量,Java虚拟机将会抛出一个StackOverflowError异常
    - 如果本地方法栈可以动态扩展,并且在尝试扩展的时候无法申请到足够的内存,或者在创建新的线程时没有足够的内存去创建对应的本地方法栈,那么Java虚拟机将会抛出一个OutOfMemoryError异常
- 本地方法是使用C语言实现的
- 它的具体做法是Native Method Stack中登记native方法,在Execution Engine执行时加载本地方法库
- **当某个线程调用一个本地方法时,它就进入了一个全新的并且不再受虚拟机限制的世界,它和虚拟机拥有同样的权限**
    - 本地方法可以通过本地方法接口**来访问虚拟机内部的运行时数据区**
    - 它甚至可以直接使用本地处理器中的寄存器
    - 直接从本地内存的堆中分配任意数量的内存
- *并不是所有的JVM都支持本地方法,因为Java虚拟机规范并没有明确要求本地方法栈的使用语言,具体实现方式,数据结构等,如果JVM产品不打算支持native方法,也可以无需实现本地方法栈
- 在Hotspot JVM中,直接将本地方法栈和虚拟机栈和二为一

## 堆

*运行时数据区最重要的一部分*

### 堆的核心概述

- 一个JVM实例只存在一个堆内存,堆也是Java内存管理的核心区域
- Java堆区在JVM启动的时候即被创建,其空间大小也就确定了,是JVM管理的最大一块内存空间
    - 堆内存的大小是可以调节
- `《Java虚拟机规范》`规定,堆可以处于**物理上不连续**的内存空间,但是**逻辑上**它应该被视为**连续**的
- 所有的线程共享Java堆,在这里还可以划分线程私有的缓冲区
- `《Java虚拟机规范》`中对Java栈的描述是,所有的对象实例以及数组都应当在运行时分配在堆上(几乎所有的对象实例都在这里分配内存,从实际实用角度来看)
- 数组和对象可能永远不会存储在栈上,因为栈帧中保存引用,这个引用指向对象或者数组在堆中的位置
![2023-10-29112717](https://img01.zzmr.club/img/2023-10-29112717.jpg)
- 在方法结束后,堆中的对象不会马上被删除,仅仅在垃圾收集的时候才会被删除(方法结束,栈中存储堆中对象地址的变量出栈,但堆中的对象并不会马上被删除,要等到GC时进行判断,判断出这两个对象没有引用了,没有地址了,此时就会进行垃圾回收)
- 堆,是GC执行垃圾回收的重点区域

---

有两个类,内容一样
```java
package com.zzmr;

/**
 * @author zzmr
 * @create 2023-10-29 10:30
 */
public class HeapDemo {

    public static void main(String[] args) {
        System.out.println("start...");
        try {
            Thread.sleep(1000000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("end...");
    }

}

// ===

package com.zzmr;

/**
 * @author zzmr
 * @create 2023-10-29 10:30
 */
public class HeapDemo1 {

    public static void main(String[] args) {
        System.out.println("start...");
        try {
            Thread.sleep(1000000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("end...");
    }

}


```

1. 给这两个类配置运行参数,一个10,一个20
![20231029104625](https://img01.zzmr.club/img/20231029104625.png)
2. 然后打开jvisualVM,能看到正在运行的两个进程
![20231029104719](https://img01.zzmr.club/img/20231029104719.png)
3. 打开该进程,能看到详细数据,其中包含前面设置的参数
![20231029105425](https://img01.zzmr.club/img/20231029105425.png)
4. 点击visualGC,其中这四行相加对应的就是设置的参数,这里是20m
![2023-10-29110444](https://img01.zzmr.club/img/2023-10-29110444.jpg)

**这也就说明了,每一个JVM实例都对应一个堆空间**

[jvisualVM下载地址](https://visualvm.github.io/archive/uc/8u40/updates.html)

---

**内存细分**
现代垃圾收集器大部分都基于分代收集理论设计,堆空间细分为

- Java7及之前堆内存逻辑上分为三部分:**新生区+养老区+永久区**
    - `young generation space`新生区`young/new`,又被划分为Eden区和Survivor区
    - `Tenure generation space`养老区`old/Tenure`
    - `Permanent space`永久区,Perm
- Java8及之后堆内存逻辑上分为三部分:**新生区+养老区+元空间**
    - `young generation space`新生区`young/new`,又被划分为Eden区和Survivor区
    - `Tenure generation space`养老区`old/Tenure`
    - `Meta Space`元空间,Meta

>如图所示,是7,8之间堆内存的区别
![023-10-2960029](https://img01.zzmr.club/img/023-10-2960029.jpg)

`约定:新生区=新生代=年轻化,养老区=老年区=老年代,永久区=永久代`

上面是设置了10M大小的,也就是新生代+老年代=10M,或者说,堆空间目前只包括这两部分-**新生代和老年代**
![2023-10-29155327](https://img01.zzmr.club/img/2023-10-29155327.png)

### 设置堆内存大小与OOM

1. Java堆区用于存储Java对象实例,那么堆的大小在JVM启动时就已经设定好了,大家可以通过选项`-Xmx`和`-Xms`来进行设置
    - `Xmx`用于表示堆区的最大内存,等价于`-XX:MaxHeapSize`
    - `Xms`表示堆区的起始内存,等价于`-XX:InitiaHeapSize`
2. 一旦堆区的内存大小超过`-Xms`所指定的最大内存时,将会抛出`OutOfMemoryError`异常-也就是OOM
3. 通常会将`-Xms`和`-Xmx`两个参数配置相同的值,其目的是**为了能够在Java垃圾回收机制清理完堆区后不需要重新分割计算堆区的大小,从而提高性能**
    - `-X`是jvm的运行参数
    - `ms`是memory start
    - `Xmx`用来设置堆空间(年轻代+老年代)的最大内存大小
    - `Xms`用来设置堆空间(年轻代+老年代)的初始内存大小
4. 默认的堆空间大小
    - 初始内存大小:电脑物理内存大小/64
    - 最大内存大小:电脑物理内存大小/4


**以下代码能查看堆内存以及系统内存**(但是我这里一直不能正确显示,得到的系统内存只有1GB,堆最大内存只有247MB)
```java
public class HeapSpaceInitial {
    public static void main(String[] args) {
        // 返回Java虚拟机中的堆内存总量
        long initialMemory = Runtime.getRuntime().totalMemory() / 1024 / 1024;

        // 返回Java虚拟机试图使用的最大堆内存量
        long maxMemory = Runtime.getRuntime().maxMemory() / 1024 / 1024;

        System.out.println("-Xms: " + initialMemory + "M");
        System.out.println("-Xmx: " + maxMemory + "M");

        System.out.println("系统内存大小为：" + initialMemory * 64.0 / 1024 + "G");
        System.out.println("系统内存大小为：" + maxMemory * 4.0 / 1024 + "G");

    }
}
```

>查看设置的参数
- 命令行执行`jps`
![20231029205756](https://img01.zzmr.club/img/20231029205756.png)
- 再执行`jstat -gc pid`,就是上一步输出的编号,就可以查看各个空间占用情况
![20231029210505](https://img01.zzmr.club/img/20231029210505.png)

或者可以添加参数`-XX:+PrintGCDetails`
![20231029211524](https://img01.zzmr.club/img/20231029211524.png)

>在开发中建议将初始堆内存和最大堆内存设置成相同的值,避免因为堆内存不够,扩容造成性能降低

---

**OOM异常**

用下面这个代码来测试OOM异常
```java
package com.zzmr;

import java.util.ArrayList;
import java.util.Random;

/**
 * @author zzmr
 * @create 2023-10-29 21:26
 */
public class OOMTest {

    public static void main(String[] args) {

        ArrayList<Picture> list = new ArrayList<>();
        while (true) {
            try {
                Thread.sleep(20);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            list.add(new Picture(new Random().nextInt(1024 * 1024)));
        }

    }

}

class Picture {
    private byte[] pixels;

    public Picture(int length) {
        this.pixels = new byte[length];
    }
}
```

设置一个小一点的堆内存大小,运行代码一段时间后就会报错:`OutOfMemoryError`
![20231029214036](https://img01.zzmr.club/img/20231029214036.png)

### 年轻代与老年代

- 存储在JVM中的Java对象可以被划分为两类
    1. 一类是生命周期较短的瞬时对象,这类对象的创建和消亡都非常迅速
    2. 另外一类对象的生命周期却非常长,在某些极端的情况下还能够与JVM的生命周期保持一致
- Java堆区进一步细分的话,可以划分为年轻代,老年代
- 其中年轻代又可以划分为`Eden`空间,`Survivor0`空间和`Survivor1`空间(有时也叫做from区,to区)
![第08章_堆空间细节](https://img01.zzmr.club/img/第08章_堆空间细节.jpg)
- 下面这个参数开发中一般不会调
![20231029221850](https://img01.zzmr.club/img/20231029221850.png)
- 配置新生代与老年代在堆结构的占比
    - 默认`-XX:NewRatio=2`,表示新生代占1,老年代占2,新生代占整个堆的1/3
    - 可以修改为`-XX:NewRatio=4`,表示新生代占1,老年代占4,新生代占整个堆的1/5
- 在HotSpot中,Eden空间和另外两个Survivor空间所占比是`8:1:1`,这里分配的是600M的内存,所以老年代是400,新生代是200,新生代中Eden占160,另外两个合占40(但是有时候并不是这么算的,可能会出现一个是150,另外两个分别是25,25,也就是自适应模式)
![20231029223310](https://img01.zzmr.club/img/20231029223310.png)
- 可以通过选项`-XX:SurvivorRatio`调整这个空间比例,比如`-XX:SurvivorRatio=8`
- 几乎所有的Java对象都是在Eden区被new出来的
- 绝大部分的Java对象的销毁都在新生代进行了
    - 新生代中80%的对象都是**朝生夕死**
- 可以使用选项`-Xmn`设置新生代最大内存大小(这个参数也是一般使用默认值)

### 图解对象分配过程

内存分配算法与内存回收算法密切相关,所以还需要考虑GC执行完内存回收后是否会在内存空间中产生内存碎片

1. new的对象先放在Eden区,此区有大小限制
2. **当伊甸园区的空间填满时**,程序又需要创建对象,JVM的垃圾回收器将对伊甸园区进行垃圾回收(YGC/Minor GC),将伊甸园区中的不再被其他对象所引用的对象进行销毁,再加载新的对象放到伊甸园区
3. 然后将伊甸园区中的剩余对象移动到幸存者0区
4. 如果再次触发垃圾回收,此时上次幸存下来放到幸存者0区的,如果没有回收,就会被放到幸存者1区
5. 如果再次经历垃圾回收,此时会重新放回幸存者0区,接着再去幸存者1区
6. 啥时候能去养老区呢?**可以设置次数,默认是15次**
    - 可以设置`-XX:MaxTenuringThreshold=<N>`进行设置
7. 在养老区,相对悠闲,当养老区内存不足时,再次触发GC(Major GC),进行养老区的内存清理
8. 若养老区执行了`Major GC`之后发现依然无法进行对象的保存,就会产生OOM异常

![023-10-30093601](https://img01.zzmr.club/img/023-10-30093601.jpg)

>注意,伊甸园区满了之后,才会触发YGC,而幸存者区满了并不会触发YGC

**总结**
1. 针对幸存者s0,s1区的总结:**复制之后有交换,谁空谁是to**
2. 关于垃圾回收:频繁在新生区收集,很少会在养老区收集,几乎不在永久区/元空间收集

![2023-10-3094857](https://img01.zzmr.club/img/2023-10-3094857.jpg)

例子:
```java
public class HeapInstanceTest {

    byte[] buffer = new byte[new Random().nextInt(1024 * 1024)];

    public static void main(String[] args) {
        ArrayList<HeapInstanceTest> list = new ArrayList<>();
        while (true) {
            list.add(new HeapInstanceTest());
            try {
                Thread.sleep(20);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```
在jvisualVM中能查看到清晰的图形:
![20231030102143](https://img01.zzmr.club/img/20231030102143.png)

当老年代也存满之后,程序就报了OOM

### Minor GC,Major GC与Full GC

JVM在进行GC时,并非每次都对上面三个内存区域一起回收的,大部分时候回收的都是指新生代,针对HotSpot的实现,它里面的GC按照回收区域又分为两种类型,一种是部分收集,一种是整堆收集

- 部分收集:不是完整收集整个Java堆的垃圾收集,其中分为
    - Minor GC,也叫Young GC,是Eden区满时触发的GC,只针对于新生代
    - Major GC,就是老年代的GC,当老年代满时触发的GC
        - 目前,只有CMS GC会有单独收集老年代的行为
        - 注意,很多时候Major GC会和Full GC混淆使用,需要具体分辨是老年代回收还是整堆回收
    - 混合收集(Mixed GC):收集整个新生代以及部分老年代的垃圾收集
        - 目前只有G1 GC会有这种行为
- Full GC,会收集整个java堆和方法区的垃圾收集

---

**年轻代GC(Minor GC)触发机制**
1. 当年轻代中的Eden区满时,就会触发Minor GC,Survivor区满时并不会触发GC,Minor GC会清理整个年轻代的内存
2. 因为Java对象**大多数具备朝生夕灭**的的特征,所以Minor GC非常频繁,一般回收速度也比较快,这一定义即清晰又易于理解
3. Minor GC会引发STW,暂停其他用户的线程,等垃圾回收结束,用户线程才能恢复

---

**老年代GC(Major GC/Full GC)触发机制**
1. 指发生在老年代的GC,对象从老年代消失时,我们说`Major GC`或`Full GC`发生了
2. 出现了Major GC,经常会伴随至少一次的Minor GC(但非绝对的,在Parallel Scavenge收集器的收集策略里就有直接进行Major GC的策略选择过程)
    - 也就是在老年代空间不足时,会先尝试触发Minor GC,如果之后空间还不足,则触发Major GC
3. Major GC的速度一般会比Minor GC慢10倍以上,STW的时间更长
4. 如果Major GC后,内存还不足,就报OOM了

---

### GC举例与日志分析

代码
```java
public class GCTest {

    public static void main(String[] args) {
        int i = 0;
        try {
            List<String> list = new ArrayList<>();
            // 字符串常量池是存放在堆空间的，所以这里不断拼接会导致OOM
            String a = "zzmr.club";
            while (true) {
                list.add(a);
                a = a + a;
                i++;
            }
        } catch (Throwable t) {
            t.printStackTrace();
            System.out.println("遍历次数为：" + i);
        }
    }
}
```

控制台输出:
![2023-10-30153539](https://img01.zzmr.club/img/2023-10-30153539.jpg)

让GPT解释一下输出的内容:

```txt
这段代码的运行结果表明已经发生了多次垃圾回收，包括Young区（新生代）和Tenured区（老年代）的回收，以及方法区（Metaspace）的使用情况。最终，代码触发了一次Full GC，并抛出了java.lang.OutOfMemoryError: Java heap space异常。

下面是输出的解释：

[GC (Allocation Failure) [DefNew: ...]: 这是Young区（新生代）的垃圾回收。"Allocation Failure"表示分配内存失败，因此需要进行垃圾回收。DefNew是新生代的垃圾回收器的名称。输出显示了垃圾回收前后的内存使用情况，以及垃圾回收的持续时间。

[Tenured: ...]: 这是老年代（Tenured区）的垃圾回收。这一行显示了老年代垃圾回收前后的内存使用情况，以及垃圾回收的持续时间。

[Metaspace: ...]: 这是方法区（Metaspace）的使用情况，包括已使用的空间、容量、已分配的空间和保留的空间。

java.lang.OutOfMemoryError: Java heap space: 最终，代码抛出了Java堆空间不足的异常，表明Java堆内存已用尽，无法继续分配更多的内存。这是由于在循环中不断添加更大的字符串，导致内存耗尽。

根据输出，可以看到Java堆内存已经用尽，因此在最后触发了Full GC，但无论如何，Java堆内存已经无法满足代码的内存需求，因此触发了OutOfMemoryError异常。根据输出中的"遍历次数为：17"可以看出，代码在进行17次迭代后达到了内存极限。你在命令行参数中设置了-Xms9m和-Xmx9m，这意味着堆内存的最小和最大大小都为9MB，但由于循环中不断分配内存，堆内存最终用尽了。
```

---

>为什么需要把Java堆分代?不分代就不能正常工作了吗
**经研究,不同对象的生命周期不同,70%-99%的对象都是临时对象**

其实不分代完全可以,分代的唯一理由就是优化GC性能,如果没有分代,那所有的对象都在一块,就如同把一个学校的人都关在一个教室,GC的时候需要找到哪些对象没用,这样就会对堆的所有区域进行扫描,而很多对象都是朝生夕死的,如果分代的话,把新创建的对象放到某一地方,当GC的时候先把这块存储朝生夕死对象的区域进行回收,这样就会腾出很大的空间出来

### 内存分配策略

如果对象在Eden出生并经过第一次MinorGC后仍然存活,并且能被Survivor容纳的话,将被移动到Survivor空间中,并将对象年龄设为1,对象在Survivor区中每熬过一次MinorGC,年龄就增加1,当它的年龄增加到一定程度(默认15,其实每个JVM,每个GC都有所不同)时,就会被晋升到老年代中

针对不同年龄的对象分配原则如下
1. 优先分配到Eden
2. 大对象直接分配到老年代
    - 尽量避免程序中出现过多的大对象
3. 长期存活的对象分配到老年代
4. 动态对象年龄判断
    - 如果Survivor区中相同年龄的所有对象大小的总和大于Survivor空间的一半,年龄大于或等于该年龄的对象可以直接进入老年代,无须等到`MaxTenuringThreshold`中要求的年龄
5. 空间分配担保
    - `-XX:HandlePromotionFailure`

---

大对象直接分配到老年代例子:
```java
public class YoungOldAreaTest {
    public static void main(String[] args) {
        byte[] bytes = new byte[1024 * 1024 * 30];
    }
}
```
此程序创建了一个30MB的字节数组,在运行代码时,加上参数:`-Xms60m -Xmx60m -XX:+PrintGCDetails`
表示堆空间为60MB,也就是说,新生代为20MB,老年代为40MB,这个数组是放不到新生代(Eden为16MB)里面的,所以会直接放到老年代中:
![20231030163335](https://img01.zzmr.club/img/20231030163335.png)

### 为对象分配内存:TLAB

TLAB:Thread Local Allocation Buffer

- 堆区是线程共享区域,任何线程都可以访问到堆区中的共享数据
- 由于对象实例的创建在JVM中非常频繁,因此在并发环境下从堆区中划分内存空间还是线程不安全的
- 为避免多个线程操作同一地址,需要使用加锁等机制,进而影响分配速度

什么是TLAB
- 从内存模型而不是垃圾收集的角度,堆Eden区域继续进行划分,JVM为**每一个线程分配了一个私有的缓存区域**,它包含在Eden空间内
- 多线程同时分配内存时,使用TLAB可以避免一系列的非线程安全问题,同时还能够提升内存分配的吞吐量,因此我们可以将这种内存分配方式称之为**快速分配策略**

就是线程分配内存空间时,会先使用线程私有的缓冲区,用完之后才会使用Eden中的公共部分
![2023-10-3191012](https://img01.zzmr.club/img/2023-10-3191012.jpg)

**对于TLAB的说明**
1. 尽管不是所有的对象实例都能够在TLAB中成功分配内存,但是JVM确实是将TLAB作为内存分配的首选
2. 在程序中,开发人员可以通过选项`-XX:UseTLAB`设置是否开启TLAB空间
3. 默认情况下,TLAB空间的内存非常小,**仅占有整个Eden空间的1%**,当然我们可以通过选项,`-XX:TLABWasteTargetPercent`设置TLAB空间所占用Eden空间的百分比大小
4. 一旦对象在TLAB空间分配内存失败时,JVM就会尝试着通过**加锁机制**确保数据操作的原子性,从而直接在Eden空间中分配内存

看个例子
```java
/**
 * @author zzmr
 * @create 2023-10-31 9:22
 * 测试-XX:UseTLAB参数是否开启的情况
 */
public class TLABTest {
    public static void main(String[] args) {
        System.out.println("ZZMR");
        try {
            Thread.sleep(1000000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```
打开命令行查看进程信息
![20231031092355](https://img01.zzmr.club/img/20231031092355.png)

就能够发现是`-XX:+UseTLAB`了,也就是表示开启了TLAB

如果我们给启动参数上加上`-XX:-UseTLAB`,那就表示不使用了
![20231031092612](https://img01.zzmr.club/img/20231031092612.png)

### 小结堆空间参数设置

1. `-XX:+PrintFlagsInitial`:查看所有的参数的默认初始值
2. `-XX:+PrintFlagsFinal`:查看所有的参数的最终值(可能会存在修改,不再是初始值)
3. `-Xms`:初始堆空间内存(默认为物理内存的1/64)
4. `-Xmx`最大堆空间内存(默认为物理内存的1/4)
5. `-Xmn`:设置新生代的大小(初始值及最大值)
6. `-XX:NewRatio`:配置新生代与老年代在堆结构的占比
7. `-SurvivorRatio`:设置新生代中Eden和S0/S1空间的比例
8. `-MaxTenuringThreshold`:设置新生代垃圾的最大年龄
9. `-XX:+PrintGCDetails`:输出详细的GC处理日志
10. 打印GC简要日志:`-XX:+PrintGC`或者是`-verbose:gc`
11. `-XX:HandlePromotionFailure`:是否设置空间分配担保

对于这个空间分配担保的解释:

发生MinorGC之前,虚拟机会检查老年代最大可用的连续空间是否大于新生代所有对象的总空间
- 如果大于,则此次MinorGC是安全的
- 如果小于,则虚拟机会查看`-XX:HandlePromotionFailure`设置值是否允许担保失败
    - 如果`HandlePromotionFailure=true`,那么会**继续检查老年代最大可能连续空间是否大于历次晋升到老年代的对象的平均大小**
        - 如果大于,则尝试进行一次Minor GC,但这次MinorGC依然是有风险的
        - 如果小于,则改为进行一次FullGC
    - 如果参数设置为false,则改为进行一次FullGC

在JDK6 Update24之后,HandlePromotionFailure参数不会再影响到虚拟机的空间分配担保策略，虽然源码中还定义了这个参数，但是代码中已经不会使用它了

**规则变为只要老年代的连续空间大于新生代对象总大小或者历次晋升的平均大小就会进行MinorGC,否则将进行FullGC**

也就是说,可以把这个参数看成为true了,先判断老年代的空间够不够塞,不够的话判断之前塞进老年代的对象的平均值是否大于当前老年代的空间,如果比平均值大,则进行MinorGC,如果两个都小了,就进行FullGC了

### 堆空间是对象分配的唯一选择吗

随着JIT编译期的发展与**逃逸分析技术**逐渐成熟,**栈上分配,标量替换优化技术**将会导致一些微妙的变化,所有的对象都分配到堆上也渐渐变得不那么**绝对了**

在Java虚拟机中,对象是在Java堆中分配内存的,这是个普遍的常识,但是,有一种特殊情况,那就是**如果经过逃逸分析Escape Analysis后发现,一个对象并没逃逸出方法的话,那么就可能被优化成栈上分配**,这样就无需在堆上分配内存,也无需进行垃圾回收了,这也是最常见的堆外存储技术

---

如何将堆上的对象分配到栈,需要使用逃逸分析手段

这是一种可以有效减少Java程序中同步负载和内存堆分配压力的跨函数全局数据流分析算法

通过逃逸分析,Java HotSpot编译期能够分析出一个新的对象的引用的适用范围从而决定是否要将这个对象分配到堆上

逃逸分析的基本行为就是分析对象动态作用域
1. 当一个对象在方法中被定义后,对象只在方法内部使用,则认为没有发生逃逸
2. 当一个对象在方法中被定义后,它被外部方法所引用,则认为发生逃逸,例如作为调用参数传递到其他地方中

**开发中能使用局部变量的,就不要使用在方法外定义**

---

### 代码优化

使用逃逸分析,编译器可以对代码做如下优化
1. **栈上分配**,将堆分配转化为栈分配,如果一个对象在子程序中被分配,要使指向该对象的指针永远不会逃逸,对象可能是栈分配的候选,而不是堆分配
2. **同步省略**,如果一个对象被发现只能从一个线程被访问到,那么对于这个对象的操作可以不考虑同步
3. **分离对象或标量替换**,有的对象可能不需要作为一个连续的内存结构存在也可以被访问到,那么对象的部分(或全部),可以不存储在内存,而是存储在CPU寄存器中

---

**栈上分配**
JIT编译器在编译期间根据逃逸分析的结果,发现如果一个对象并没有逃逸出访问的话,就可能被优化成栈上分配,分配完成后,继续在调用栈内执行,最后线程结束,栈空间被回收,局部变量对象也被回收,这样就无须进行垃圾回收了

栈上分配的例子
```java
public class StackAllocation {

    public static void main(String[] args) {
        long start = System.currentTimeMillis();
        for (int i = 0; i < 10000000; i++) {
            alloc();
        }
        long end = System.currentTimeMillis();
        System.out.println("花费时间：" + (end - start) + "ms");

        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    private static void alloc() {
        User user = new User();
    }
    static class User {
    }
}
```

首先不开启逃逸分析(这个逃逸分析,要在Server模式下才是默认打开的)
```java
-Xmx1G -Xms1G -XX:-DoEscapeAnalysis -XX:+PrintGCDetails
```

运行代码后,使用jvisualVM能看到内存的使用情况,是有10000000的user对象的

但是将参数设置为`-XX:+DoEscapeAnalysis`,也就是启用逃逸分析,就会发现堆内存中并没有10000000个对象,而且程序的运行速度也变快了

---

**同步省略**
如果一个对象被发现只能从一个线程被访问到,那么对于这个对象的操作可以不考虑同步

线程同步的代价是相当高的,同步的后果是降低并发性和性能

在动态编译同步块的时候,JIT编译器可以借助逃逸分析来**判断同步块所使用的锁对象是否只能够被一个线程访问而没有被发布到其他线程**,如果没有,那么JIT编译器在编译这个同步块的时候就会取消对这部分代码的同步,这样就能大大提高并发性和性能,这个取消同步的过程就叫同步省略,也叫**锁消除**

如以下代码
```java
    public void f() {
        Object a = new Object();
        synchronized (a) {
            System.out.println(a);
        }
    }
```
同步代码块选择a作为同步监视器,但是这个a的生命周期只在f()方法中,并不会被其他线程所访问到,所以在JIT编译阶段就会被优化掉
```java
    public void f() {
        Object a = new Object();
        System.out.println(a);
    }
```

**这恰恰说明,同步监视器要选择一个唯一的**

---

**分离对象或标量替换**

有的对象可能不需要作为一个连续的内存结构存在也可以被访问到,那么对象的部分(或全部),可以不存储在内存,而是存储在CPU寄存器中

**标量(Scalar)**是指一个无法再分解成更小的数据的数据,Java中的原始数据类型就是标量

相对的,那些还可以分解的数据叫做**聚合量**,Java中的对象就是聚合量,因为他可以分解成其他聚合量和标量

在JIT阶段,如果经过逃逸分析,发现一个对象不会被外界访问的话,那么经过JIT优化,就会把这个对象拆解成若干个其中包含的若干个成员变量来代替,这个过程就是**标量替换**

```java
public static void main(String[] args){
    alloc();
}

private static void alloc(){
    Point point = new Point(1,2);
    System.out.println("point.x="+point.x+"; point.y="+point.y);
}

class Point{
    private int x;
    private int y;
}
```

以上代码,经过标量替换,就会变成
```java
private static void alloc(){
    int x = 1;
    int y = 2;
    System.out.println("point.x="+x+"; point.y="+y);
}
```

可以看到,Point这个聚合量经过逃逸分析后,发现他并没有逃逸,就被替换成两个聚合量了,那么标量替换有什么好处呢?就是可以大大减少堆内存的占用,因为一旦不需要创建对象了,那么就不再需要分配堆内存了

---

**总结**
上面问到的`堆空间是对象分配的唯一选择吗`,是,又不完全是,因为栈上分配并不成熟,**记住一句话:对象实例都是分配在堆上的**

```
-server -Xmx100m -Xms100m -XX:+DoEscapeAnalysis -XX:+PrintGC -XX:+EliminateAllocations
```
- `-server`:启动server模式,因为在Server模式下,才可以启用逃逸分析

## 方法区

### 栈-堆-方法区-的交互关系

从线程共享与否的角度来看
![2023-11-0183605](https://img01.zzmr.club/img/2023-11-0183605.jpg)

创建对象时三者的关系
![2023-11-0084158](https://img01.zzmr.club/img/2023-11-0084158.jpg)

对象的访问定位-如何找到对应的堆
![2023-11-084502](https://img01.zzmr.club/img/2023-11-084502.jpg)

### 方法区的具体理解

`《Java虚拟机规范》`中明确说明:尽管所有的方法区在逻辑上是堆空间的一部分,但是一些简单的实现可能不会选择去进行垃圾回收或者进行压缩,但是对于HotSpotJVM而言,方法区还有一个别名叫做**Non-Heap**,非堆,目的就是要和堆分开

所以,方法去看作是一块独立于Java堆的内存空间

---

- 方法区与Java堆一样,是各个线程共享的内存区域
- 方法区在JVM启动的时候被创建,并且它的实际的物理内存空间和Java堆区一样都可以是不连续的
- 方法区的大小,跟堆空间一样,可以选择固定大小或者可扩展
- **方法区的大小决定了系统可以保存多少个类**,如果系统定义了太多的类,导致方法区溢出,虚拟机同样会抛出内存溢出错误OOM
- 关闭JVM就会释放这个区域的内存

---

如下代码,运行后,打开jvisualVM能看到装载的类有1000多个
![20231101160322](https://img01.zzmr.club/img/20231101160322.png)
```java
public class MethodAreaDemo {
    public static void main(String[] args) {
        System.out.println("start...");
        try {
            Thread.sleep(1000000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("end...");
    }
}
```

---

**HotSpot中方法区的演进**
- 在JDK7及以前,习惯上把方法区,称为永久代,JDK8开始,使用元空间取代了永久代
- 本质上,方法区和永久代并不等价,仅是对HotSpot而言的,例如,在JRockit和IBM J9中不存在永久代概念
- 现在看来,当年使用永久代是有问题的,会导致Java程序更容易OOM
- 而到了JDK8,终于完全废弃了永久代的概念,改用与JRockit,J9一样在本地内存中实现的元空间(Metaspace)来代替
![2023-11-0163309](https://img01.zzmr.club/img/2023-11-0163309.jpg)
- **元空间的本质和永久代类似,都是对JVM规范中方法区的实现**,不过元空间与永久代最大的区别在于:**元空间不在虚拟机设置的内存中,而是使用本地内存**
- 永久代,元空间而这并不只是名字变了,内部结构也调整了
- 如果方法区无法满足新的内存分配需求时,将抛出OOM异常

### 设置方法区大小与OOM

方法区的大小不必是固定的,JVM可以根据应用的需要动态调整

在JDK7及以前
- 通过`-XX:PermSize`来设置永久代初始分配空间,默认值是20.75M
- `-XX:MaxPermsize`来设置永久代最大可分配空间,32位机器默认值是64M,64位机器默认值82M
- 当JVM加载的类信息容量超过了这个值,会报异常`OutOfMemoryError:PermGen`

在JDK8以后
- 元空间大小可以使用参数`-XX:MetaspaceSize`和`-XX:MaxMetaspaceSize`指定,替代上述原有的两个参数
- 默认值依赖于平台,windows下,`-XX:MetaspaceSize`是21M,`-XX:MaxMetaspaceSize`的值是-1,即没有限制
- 与永久代不同,如果不指定大小,默认情况下,虚拟机会耗尽所有的可用系统内存,如果元数据区发生溢出,虚拟机一样会抛出OOM:Metaspace
![20231101170213](https://img01.zzmr.club/img/20231101170213.png)
- `-XX:MetaspaceSize`,设置初始的元空间大小,对于一个64位服务器端JVM来说,其默认值21MB,这就是初始的高水位线,一旦触及这个水位线,FullGC将会被触发并卸载没用的类(即这些类对应的类加载器不再存活),然后这个高水位线将会重置,新的高水位线的值取决于GC后释放了多少元空间,如果释放的空间不足,那么在不超过`MaxMetaspaceSize`时,适当提高该值,如果释放空间过多,则适当降低该值
- 如果初始化的高水位线设置过低,上述高水位线调整情况会发生很多次,通过垃圾回收器日志可以观察到FullGC多次调用,为了避免频繁的GC,建议将`-XX:MetaspaceSize`设置位一个相对较高的值

下面就是设置元空间大小的参数
```java
-XX:MetaspaceSize=100m
```

---

*如何解决OOM*
1. 要解决OOM异常或Heap Space的异常,一般的手段是首先通过内存映射分析工具对dump出来的的堆转储快照进行分析,重点是确认内存中的对象是否是必要的,也就是要先分清楚到底是出现了内存泄露还是内存溢出
2. 如果是内存泄漏,可进一步通过工具查看泄露对象到GC Roots的引用链,于是就能找到泄露对象是通过怎样的路径与GC Roots相关联并导致垃圾收集器无法自动回收它们的,掌握了泄露对象的类型信息,以及GC Roots引用链的信息,就可以比较准确定位出泄露代码的位置
3. 如果不存在内存泄漏,换句话就是说内存中的对象确实都还必须存活着,那就应当检查虚拟机的堆参数(-Xmx与-Xms),与机器物理内存对比看是否还可以调大,从代码上检查是否存在某些对象声明周期过长,持有状态时间过长的情况,尝试减少程序运行期的内存消耗

### 方法区的内部结构

![2023-11-0211829](https://img01.zzmr.club/img/2023-11-0211829.jpg)

`《深入理解Java虚拟机》`书中对方法区的描述如下:
它用于存储已被虚拟机加载的类型信息,常量,静态变量,即时编译器编译后的代码缓存等

**类型信息**
对每个加载的类型(类class,接口interface,枚举enum,注解annotation),JVM必须在方法区中存储以下类型信息
1. 这个类型的完整有效名称(全名=包名.类名)
2. 这个类型直接父类的完整有效名(对于interface或者是java.lang.Object,都没有父类)
3. 这个类型的修饰符(public,abstract,final的某个子集)
4. 这个类型直接接口的一个有序列表

**域信息**(属性)
- JVM必须在方法区中保存类型的所有域的相关信息以及域的声明顺序
- 域的相关信息包括:域名称,域类型,域修饰符(public,private,protected,static,final方法,volatile,transient的某个子集)

**方法信息**
JVM必须保存所有方法的以下信息,同域信息一样包括声明顺序
- 方法名称
- 方法的返回类型(或void)
- 方法参数的数据和类型(按顺序)
- 方法的修饰符(public,private,protected,static,final方法,synchronized,native,abstract的一个子集)
- 方法的字节码(bytecodes),操作数栈,局部变量表及大小(abstarct和native方法除外)
- 异常表(abstarct和native方法除外)
    - 每个异常处理的开始位置,结束位置,代码处理在程序计数器中的偏移地址,被捕获的异常类的常量池索引

**non-final的类变量**
- 静态变量和类关联到一起,随着类的加载而加载,它们成为类数据在逻辑上的一部分
- 类变量被列的所有实例共享,即使没有类实例时你也可以访问它

---

补充说明:全局常量
被声明为final的类变量的处理方法则不同,每个全局常量在编译的时候就会被分配了

```java
public class MethodAreaTest {
    public static void main(String[] args) {
        Order order = null;
        order.hello();
        System.out.println(order.count);

        // 都可以
        Order.hello();
        System.out.println(Order.count);
    }
}

class Order {
    public static int count = 1;
    public static final int number = 2;

    public static void hello() {
        System.out.println("hello!");
    }
}
```

反编译Order,发现number这个final修饰的常量,是直接赋值为2的,但是count并没有直接赋值
```java
  public static int count;
    descriptor: I
    flags: ACC_PUBLIC, ACC_STATIC

  public static final int number;
    descriptor: I
    flags: ACC_PUBLIC, ACC_STATIC, ACC_FINAL
    ConstantValue: int 2
```

### 运行时常量池

运行时常量池vs常量池
- 方法区内部包含了运行时常量池
- 字节码文件内部包含了常量池
- 要弄清楚方法区,需要理解清楚classFile,因为加载类的信息都在方法区
- 要弄清楚方法区的运行时常量池,需要理解清楚classFile中的常量池

---

一个有效的字节码文件中除了包含类的版本信息,字段,方法以及接口等描述信息外,还包含一项信息那就是常量池表(Constant Pool Table),包括各种字面量和对类型,域和方法的符号引用

**为什么需要常量池?**
一个Java源文件中类,接口,编译后产生一个字节码文件,而Java中的字节码需要数据支持,通常这种数据会很大以至于不能直接存到字节码中,换另一种方式,可以存到常量池,这个字节码包含了指向常量池的引用,在动态链接的时候会用到运行时常量池

```java
public class SimpleClass{
    public void sayHello(){
        System.out.println("hello);
    }
}
```

虽然这个文件只有194字节,但是里面却使用了String,System,PrintStream及Object等结构,这里代码量其实已经很小了,如果代码多,引用到的结构会更多,这里就需要常量池了

---

>常量池,可以看作是一张表,虚拟机指令根据这张常量表找到要执行的类名,方法名,参数类型,字面量等类型

**运行时常量池**
- 运行时常量池是方法区的一部分
- 常量池表是Class文件的一部分,**用于存放编译期生成的各种字面量与符号引用**,这部分内容将在类加载后存放到方法区的运行时常量池中
- 运行时常量池,在加载类和接口到虚拟机后,就会创建对应的运行时常量池
- JVM为每个已加载的类型(类接口)都维护了一个常量池,池中的数据项像数组项一样,是通过索引访问的
- 运行时常量池中包含多种不同的常量,包括编译期就已经明确的数值字面量,也包括到运行期解析后才能获得的方法或者字段引用,此时不再是常量池中的符号地址了,这里换位真实地址
    - 运行时常量池:相对于Class文件常量池的另一重要特征是:**具备动态性**
- 运行时常量池类似于传统编程语言中的符号表,但是它所包含的数据却比符号表要更加丰富一些
- 当创建类或接口的运行时常量池时,如果构造运行时常量池所需的内存空间超过了方法区所能提供的最大值,则JVM会抛OOM异常

### 方法区在6,7,8的演进过程

**首先明确,只有HotSpot才有永久代**
BEA JRockit,IBM J9等来说,是不存在永久代的概念的(它们用的都是元空间),原则上如何实现方法区属于虚拟机实现细节,不受`Java虚拟机规范`约束,并不要求统一

**HotSpot方法区的变化**
| 版本 | 变化 |
| --- | --- |
| jdk1.6及之前 | 有永久代(Permanent generation),静态变量存放在永久代上 |
| jdk1.7 | 有永久代,但已经逐步去永久代,字符串常量池,静态变量移除,保存在堆中 |
| jdk1.8及之后 | 无永久代,类型信息,字段,方法,常量保存在本地内存的元空间,**但字符串常量池,静态变量仍在在堆** |

如图所示
![hotspot](https://img01.zzmr.club/img/hotspot.jpg)

>Motivation
>This is part of the JRockit and Hotspot convergence effort.JRockit customers do not need to configure the permanent generation(since JRockit does not have a permanent generation)and are accustomed to not configuring the permanent generation

---

为什么要用元空间替换永久代?
- 虽然自Java8开始,HotSpotVM中已经没有永久代了,但是这不意味着类的元数据也消失了,这些数据被移到了一个与**堆不相连的本地内存区域,这个区域叫做元空间**
- 由于类的元数据分配在本地内存中,元空间的最大可分配空间就是系统可用内存空间
- 这项改动是很有必要的,原因有:
    - 为永久代设置空间大小是很难确定的,在某些场景下,如果动态加载类过多,容易产生Perm区的OOM,比如某个实际Web工程中,因为功能点比较多,在运行过程中,要不断动态加载很多类,经常出现致命错误OOM
    - 而元空间和永久代之间最大的区别在于:元空间并不在虚拟机中,而是使用本地内存,因此,默认情况下,元空间的大小仅受本地内存限制
- 对永久代进行调优是很困难的

>The proposed implementation will allocate class meta-data in native memory and move interned Strings and class statics to the Java heap.[来源](https://openjdk.org/jeps/122)
意思就是字符串常量池和静态变量都还是放在堆空间的

---

>StringTable字符串常量池为什么要调整
JDK7中将StringTable放到了堆空间中,因为永久代的回收效率很低,在FullGC的时候才会触发,而FullGC是老年代的空间不足,永久代不足时才会触发,这就导致StringTable回收效率不高,而我们开发中会有大量的字符串被创建,回收效率低,导致永久代内存不足,放到堆中,能及时回收内存

### 方法区的垃圾回收

有些人认为方法区是没有垃圾收集行为的,**其实不然**

`《Java虚拟机规范》`对方法区的约束是非常宽松的,提到过可以不要虚拟机在方法区中实现垃圾收集,事实上也确实有未实现或未能完整实现方法区类型卸载的收集器存在

一般来说**这个区域的回收效果比较难以令人满意,尤其是类型的卸载,条件相当苛刻**,但是这个区域的回收**有时又确实是必要的**,老版的hotspot虚拟机容易出现对此区域未完全回收而导致内存泄漏

**方法区的垃圾回收主要回收两部分内容,常量池中废弃的常量和不再使用的类型**

