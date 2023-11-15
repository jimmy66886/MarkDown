2023年11月8日 13点41分

# 字节码文件

**字节码文件里是什么**

源代码经过编译器编译后便会生成一个字节码文件，字节码是一种二进制的类文件，它的内容是JVM的指令，而不像C,C++经由编译器直接生成机器码

**生成class文件的编译器**
生成class文件的编译器-`javac`全量编译器

还有就是Eclipse中的ECJ,和Javac全量式编译不同,ECJ是一种增量式的编译器

`javac`编译器也被称为**前端编译器**,主要任务就是负责把符合Java语法规范的Java代码转化为符合JVM规范的字节码文件

**哪些类型对应有Class的对象**

- class
外部类,成员(成员内部类,静态内部类),局部内部类,匿名内部类
- interface
- 数组
- enum
- annotation
- primitive type:基本数据类型
- void

## 字节码相关面试题

**什么是字节码指令**(byte code)
Java虚拟机的指令由一个字节长度,代表着某种特定操作含义的`操作码`(opcode),以及跟随其后的零至多个代表此操作所需参数的`操作数`(operand)所构成,虚拟机中许多指令不包含操作数,只有一个操作码

---

**i++和++i有什么区别**
```java
public class ByteCodeInterview {

    // 面试题1 i++和++i的区别
    @Test
    public void test1() {
        int i = 10;
        // i++;
        ++i;
        System.out.println(i);
    }

}
```

`++i`的字节码指令为:
```txt
 0 bipush 10
 2 istore_1
 3 iinc 1 by 1
 6 getstatic #2 <java/lang/System.out : Ljava/io/PrintStream;>
 9 iload_1
10 invokevirtual #3 <java/io/PrintStream.println : (I)V>
13 return
```

`i++`的字节码指令为
```txt
 0 bipush 10
 2 istore_1
 3 iinc 1 by 1
 6 getstatic #2 <java/lang/System.out : Ljava/io/PrintStream;>
 9 iload_1
10 invokevirtual #3 <java/io/PrintStream.println : (I)V>
13 return
```

**会发现,编译后的字节码指令,`i++`和`++i`是完全一致的**

---

下面的方法执行结果为?
```java
    @Test
    public void test2() {
        int i = 10;
        i = i++;
        System.out.println(i);
    }
```

是10
```txt
 0: bipush        10          // 将常量10压入操作数栈
 2: istore_1                   // 将栈顶的值（10）存储到本地变量1（i）
 3: iload_1                    // 将本地变量1（i）的值加载到操作数栈
 4: iinc          1, 1         // i++ 操作，将i的值加1
 7: istore_1                   // 将栈顶的值（10）赋给本地变量1（i）
 8: getstatic     #2           // 获取System.out的引用
11: iload_1                    // 将本地变量1（i）的值加载到操作数栈
12: invokevirtual #3           // 调用PrintStream.println方法
15: return

执行 i++ 表达式，这会先将 i 的值（10）压入操作数栈，然后将 i 的值加1（变成11），最后将栈顶的值（10）赋给 i。
```

---

**包装类对象的缓存问题**
```java
    @Test
    public void test4() {
        Integer i1 = 10;
        Integer i2 = 10;
        System.out.println(i1 == i2);// true

        Integer i3 = 128;
        Integer i4 = 128;
        System.out.println(i3 == i4);// false

        Boolean b1 = true;
        Boolean b2 = true;
        System.out.println(b1 == b2);// true
    }
```

字节码文件:
```txt
 0 bipush 10
 2 invokestatic #4 <java/lang/Integer.valueOf : (I)Ljava/lang/Integer;>
 5 astore_1
 6 bipush 10
 8 invokestatic #4 <java/lang/Integer.valueOf : (I)Ljava/lang/Integer;>
11 astore_2
12 getstatic #2 <java/lang/System.out : Ljava/io/PrintStream;>
15 aload_1
16 aload_2
17 if_acmpne 24 (+7)
20 iconst_1
21 goto 25 (+4)
24 iconst_0
25 invokevirtual #5 <java/io/PrintStream.println : (Z)V>
28 sipush 128
31 invokestatic #4 <java/lang/Integer.valueOf : (I)Ljava/lang/Integer;>
34 astore_3
35 sipush 128
38 invokestatic #4 <java/lang/Integer.valueOf : (I)Ljava/lang/Integer;>
41 astore 4
43 getstatic #2 <java/lang/System.out : Ljava/io/PrintStream;>
46 aload_3
47 aload 4
49 if_acmpne 56 (+7)
52 iconst_1
53 goto 57 (+4)
56 iconst_0
57 invokevirtual #5 <java/io/PrintStream.println : (Z)V>
60 iconst_1
61 invokestatic #6 <java/lang/Boolean.valueOf : (Z)Ljava/lang/Boolean;>
64 astore 5
66 iconst_1
67 invokestatic #6 <java/lang/Boolean.valueOf : (Z)Ljava/lang/Boolean;>
70 astore 6
72 getstatic #2 <java/lang/System.out : Ljava/io/PrintStream;>
75 aload 5
77 aload 6
79 if_acmpne 86 (+7)
82 iconst_1
83 goto 87 (+4)
86 iconst_0
87 invokevirtual #5 <java/io/PrintStream.println : (Z)V>
90 return
```

第一个true,是因为Integer的缓存机制(局部变量表),只要在-128-127之间的整数,使用了valueOf,那就会被缓存
```java
    public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }

// 而最后面的true,是由于Boolean内有两个常量TRUE,FALSE,如果传入的是true,那就返回的是常量TRUE,所以这两个对象都是TRUE
    public static Boolean valueOf(boolean b) {
        return (b ? TRUE : FALSE);
    }
```

一张图描述包装类对象的缓存问题
![2023-11-09103806](https://img01.zzmr.club/img/2023-11-09103806.jpg)

## Class文件结构的分析

class文件的总体结构如下
- 魔数(cafebabe)
- Class文件版本
- 常量池
- 访问标识
- 类索引,父类索引,接口索引集合
- 字段表集合
- 方法表集合
- 属性表集合

---

>谈谈对符号引用,直接引用的理解

在Class文件中不会保存各个方法,字段的最终内存布局信息,因此这些字段,方法的符号引用不经过运行期转换的话无法得到真正的内存入口地址,也就是无法直接被虚拟机使用,当虚拟机运行时,需要从常量池获得对应的符号引用,再在类创建时或运行时解析,翻译到具体的内存地址之中

符号引用和直接引用的区别与关联
- 符号引用:符号引用以一组符号来描述所引用的目标,符号可以是任何形式的字面量,只要使用时能无歧义地定位到目标即可,**符号引用与虚拟机实现的内存布局无关**,引用的目标并不一定已经加载到内存中
- 直接引用:直接引用可以是直接**指向目标的指针,相对偏移量或是一个能直接定位到目标的句柄,直接引用是与虚拟机实现的内存布局相关的**,同一个符号引用在不同虚拟机实例上翻译出来的直接引用一般不会相同,如果有了直接引用,那说明引用的目标必定已经存在于内存之中了

## 字节码指令

可以将JVM中的字节码指令按用途大致分成9类
- 加载与存储指令
- 算术指令
![2023-11-11104303](https://img01.zzmr.club/img/2023-11-11104303.jpg)
- 类型转换指令
- 对象的创建与访问指令
- 方法调用与返回指令
    - `invokevirtual`用于调用对象的实例方法
    - `invokespecial`用于一些需要特殊处理的实例方法,包括**实例初始化方法(构造器),私有方法和父类方法,这些方法都是静态类型绑定的,不会在调用时进行动态派发
- 操作数栈管理指令
- 控制转移指令
- 异常处理指令
- 同步控制指令


