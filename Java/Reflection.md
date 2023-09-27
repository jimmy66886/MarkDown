再回顾一下反射吧

# 反射概念

Java程序中,所有的对象都有两种类型,`编译时类型`和`运行时类型`,而很多时候对象的编译时类型和运行时类型`不一致`

```java
Object obj = new String("hello")
```

例如:某些变量或形参的声明类型是Object类型,但是程序却需要调用该对象运行时类型的方法,该方法不是Object中的方法,那么如何解决呢?

1. 在编译和运行时都完全知道类型的具体信息,在这种情况下,我们可以直接先使用`instanceof`运算怒进行判断,再利用强制类型转换符将其转换成运行时类型的变量即可
2. 编译时根本无法预知该对象和类的真实信息,程序只能靠`运行时信息`来发现该对象和类的真实信息

---

Reflection反射是被视为`动态语言`的关键,反射机制允许程序在`运行期间`借助于Reflection API取得任何类的内部信息,并能直接擦欧总任意对象的内部属性及方法

加载完类之后,在堆内存的方法区就产生了一个Class类型的对象(一个类只有一个Class对象),这个对象就包含了完整的类的结构信息,我们可以通过这个对象看到类的结构,这个对象就像是`一面镜子,透过这个镜子看到类的结构,所以我们形象的称之为反射`

# 反射示例

一个简单的Person类
```java
package com.zzmr.reflection;

/**
 * @author zzmr
 * @create 2023-09-23 13:50
 */
public class Person {

    private String name;

    public int age;

    private Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // 构造器
    public Person() {
        System.out.println("Person()...");
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    public Person(int age) {
        this.age = age;
    }

    public void show() {
        System.out.println("你好,我是一个Person");
    }

    private String showNation(String nation) {
        return "我的国籍是:" + nation;
    }

}
```

一个测试类:
```java
package com.zzmr.reflection;

import org.junit.Test;

import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

/**
 * @author zzmr
 * @create 2023-09-23 13:53
 */
public class ReflectionTest {

    /**
     * 使用反射之前可以执行的操作
     */
    @Test
    public void test1() {

        // 1. 创建Person类的对象
        Person p1 = new Person();

        // 2. 调用属性 age是public,可以直接调用
        p1.age = 10;

        // 3. 调用方法
        p1.show();

    }

    /**
     * 使用反射
     */
    @Test
    public void test2() throws Exception {
        // 1. 创建Person类的对象
        Class<Person> clazz = Person.class;
        Person p1 = clazz.newInstance();
        System.out.println(p1);

        // 2. 调用属性
        Field ageField = clazz.getField("age");
        ageField.set(p1, 10);
        System.out.println(ageField.get(p1));

        // 3. 调用方法 show()
        Method showMethod = clazz.getMethod("show");
        showMethod.invoke(p1);

    }

    /**
     * 出了Person类之后,就不能直接调用Person类中声明的private权限修饰的结构(属性,方法)
     * 但是反射可以
     */
    @Test
    public void test3() throws Exception {
        Class<Person> clazz = Person.class;
        // 调用私有的构造器
        Constructor<Person> cons = clazz.getDeclaredConstructor(String.class, int.class);
        cons.setAccessible(true);
        Person p1 = cons.newInstance("tom", 23);
        System.out.println(p1);

        // 调用私有的属性
        Field nameField = clazz.getDeclaredField("name");
        nameField.setAccessible(true);
        nameField.set(p1, "Jerry");
        System.out.println(p1);

        // 调用私有的方法
        Method showNationMethod = clazz.getDeclaredMethod("showNation", String.class);
        showNationMethod.setAccessible(true);
        String result = (String) showNationMethod.invoke(p1, "只拿");
        System.out.println(result);

    }

}
```

---

面向对象中创建对象,调用指定结构(属性,方法)等功能,可以不使用反射,也可以使用反射,请问有什么区别

**不适用反射,我们需要考虑封装性,比如,出了Person类之后,就不能调用Person类中私有的结构,但是使用反射,可以调用Person类(运行时类)中任意的构造器,属性,方法,包括了私有的属性方法,构造器**

---

通过反射,可以调用类中私有的结构,是否与面向对象的封装性有冲突?是不是Java语言设计存在Bug?

不存在Bug,封装性体现的是是否建议我们调用内部API的问题,比如private声明的结构,意味着不建议调用,**反射体现的是我们能否调用的问题,因为类的完整结构都加载到了内存中,所以我们就有能力进行调用**

# Class类

针对于编写好的java源文件,会生成一个或多个.class字节码文件,然后进行解释运行,在这个过程中,我们需要将.class字节码文件加载到内存中,加载到内存中的.class文件对应的结构即为Class的一个实例(比如记载道内存中的Person类,String类..)

```java
/**
     * 获取Class实例的几种方式 --- 掌握前三种
     */
    @Test
    public void test1() throws ClassNotFoundException {
        // 1. 调用运行时类的静态属性 class
        Class clazz1 = Person.class;
        System.out.println(clazz1);

        // 2. 调用运行时类的对象的getClass()
        Person p1 = new Person();
        Class clazz2 = p1.getClass();
        System.out.println(clazz2);

        // **运行时类在内存中会缓存起来,在整个执行期间,只会加载一次**
        System.out.println(clazz1 == clazz2);

        // 3. 调用Class的静态方法forName(String className)
        String className = "com.zzmr.reflection.Person";
        Class clazz3 = Class.forName(className);
        System.out.println(clazz3);

        System.out.println(clazz1 == clazz2);
        System.out.println(clazz2 == clazz3);

        // 4. 使用类加载器
        Class clazz4 = ClassLoader.getSystemClassLoader().loadClass("com.zzmr.reflection.Person");
        System.out.println(clazz4);
        System.out.println(clazz3 == clazz4);
    }
```

# 类加载过程

- 过程1:类的装载(loading)
将类的class文件读入内存,并为之创建一个java.lang.Class对象,此过程由类加载器完成
- 过程2:链接(linking)
    - 验证(Verify):确保加载的类信息符合JVM规范,例如:以`cafebabe`开头,没有安全方面的问题
    - 准备(Prepare):正式为类变量(static)分配内存并设置类变量默认初始值的阶段,这些内存都将在方法区中进行分配
    - 解析(Resolve):虚拟机常量池内的符号引用(常量名)替换为直接引用(地址)的过程
- 过程3:初始化(initiallization),执行类构造器`<clinit>()`方法的过程,类构造器`<clinit>()`方法是由编译期自动收集类中所有类变量的赋值动作和静态代码块中的语句和并产生的

---

**类加载器**
作用:负责类的加载,并对应于一个Class的实例

**双亲委派机制**
![20230923214910](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230923214910.png)
能够保证系统核心API的安全:
当用户自定义了一个`java.lang.String`,此时通过双亲委派机制,此类会交给引导类加载器加载,但是引导类加载器发现和本身的String不一样,这时就不会加载用户自定义的这个String,而还是使用原本的String,保证安全.

双亲委派机制整体的流程就是,将一个类逐层传到引导类加载器,引导类加载器判断是否该由自己加载,不是则往下传

---

使用类加载器加载配置文件

```java
    @Test
    public void test3() throws IOException {
        Properties properties = new Properties();

        // 加载的默认模块根目录的
        FileInputStream fileInputStream = new FileInputStream(new File("info.properties"));
        properties.load(fileInputStream);
        String name = properties.getProperty("name");
        String password = properties.getProperty("password");
        System.out.println(name);
        System.out.println(password);

    }

    @Test
    public void test4() throws IOException {
        Properties properties = new Properties();
        // 加载的resources目录下的文件
        InputStream is = ClassLoader.getSystemClassLoader().getResourceAsStream("info.properties");
        properties.load(is);
        String name = properties.getProperty("name");
        String password = properties.getProperty("password");
        System.out.println(name);
        System.out.println(password);
    }
```

# 反射的应用

**创建运行时对象**

通过Class的实例调用newInstance()方法即可

但是要注意
1. 要求运行时实例必须提供空参构造
2. 空参构造的权限需要满足

**获取运行时类的内部结构**
1. 所有属性,所有方法,所有构造器
2. 父类,接口们,包,带泛型的父类,父类的泛型等

**调用指定的结构**:指定的属性,方法,构造器
1. 通过Class实例调用getDeclareField(String fieldName)获取运行时类指定名的属性,如果权限不够,需要`setAccessible(true)`


**通过反射获取注解的信息**