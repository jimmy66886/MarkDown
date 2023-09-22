补一下线程的知识.

# 创建和启动线程

## 概述

JVM允许程序运行多个线程,使用`java.lang.Thread`类代表**线程**,所有的线程对象都必须是`Thread`类或其子类的实例

Thread类的特性
- 每个线程都是通过某个特定Thread对象的run()方法完成操作的,因此把run()方法体称为`线程执行体`
- 通过该Thread对象的start()方法来启动这个线程,而非直接调用run()
- 要想实现多线程,必须在主线程中创建新的线程对象

## 继承Thread类

1. 创建一个继承Thread类的子类
2. 重写Thread类的run()方法,将此线程要执行的操作,声明在此方法体中
3. 创建当前Thread的子类对象
4. 通过对象调用start()方法

创建线程,打印100以内的偶数
```java
public class Test1 {
    public static void main(String[] args) {
        Thread1 t1 = new Thread1();
        /**
         * start()方法的作用
         * 1. 启动线程
         * 2. 调用当前线程的run()方法
         */
        t1.start();
    }
}

class Thread1 extends Thread {
    @Override
    public void run() {
        for (int i = 0; i <= 100; i++) {
            if (i % 2 == 0) {
                System.out.println(i);
            }
        }
    }
}
```

**start()方法的作用**
1. 启动线程
2. 调用当前线程的run()方法

---

`问题1`,如果不使用start而使用run,会发生什么?能实现分线程的创建和调用吗-**不能**
从运行结果可以看出,t1线程根本没有启动,**而是去调用run()这个普通的方法**,执行完之后,再回到主线程继续执行
```java
    // t1.start();
    t1.run();
```
![20230921165209](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230921165209.png)

`问题2`,如果创建两个分线程,启动两个分线程,该怎么操作?
```java
    t1.start();
    t1.start();
```
调用两次start是不行的,会报错,所以,一个线程不能重复的start()
![20230921165908](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230921165908.png)

所以要想再创建一个分线程,可以再创建一个线程对象
```java
        Thread1 t1 = new Thread1();
        Thread1 t2 = new Thread1();
        t1.start();
        t2.start();
```

---

还可以使用匿名内部类的形式创建线程,或者说,一般都是这么创建线程的
```java
        // 匿名内部类
        new Thread() {
            @Override
            public void run() {
                for (int i = 0; i <= 100; i++) {
                    if (i % 2 == 0) {
                        System.out.println(i + Thread.currentThread().getName() + "*");
                    }
                }
            }
        }.start();
```

## 实现Runnable接口

1. 创建一个实现Runnable接口的类
2. 实现(重写)接口中的run方法
3. 创建当前实现类的对象
4. 将此对象作为参数传递到Thread构造器中,创建Thread类的实例
5. 通过调用Thread实例的start()方法

实例:
```java
public class TestRunnable implements Runnable {

    // 实现Runnable接口，重写run方法
    @Override
    public void run() {
        for (int i = 0; i <= 100; i++) {
            if (i % 2 != 0) {
                System.out.println(i + Thread.currentThread().getName() + "*");
            }
        }
    }
}

class Test {
    public static void main(String[] args) {
        TestRunnable runnable = new TestRunnable();
        Thread t1 = new Thread(runnable);
        t1.start();
    }
}
```

也可以使用匿名内部类的形式创建
```java
    new Thread(new Runnable() {
        @Override
        public void run() {
            for (int i = 0; i <= 100; i++) {
                if (i % 2 != 0) {
                    System.out.println(i + Thread.currentThread().getName() + "*");
                }
            }
        }
    }).start();
```

**这里就可以使用lambda来简化代码了**
```java
    // 使用lambda表达式
    new Thread(() -> {
        for (int i = 0; i <= 100; i++) {
            if (i % 2 != 0) {
                System.out.println(i + Thread.currentThread().getName() + "*");
            }
        }
    });
```

想要使用这种lambda表达式,要满足以下条件:
1. 目标接口是函数式接口：Lambda表达式通常用于创建实现函数式接口的对象。函数式接口是只包含一个抽象方法的接口。例如，Runnable接口就是一个函数式接口，因为它只有一个抽象方法run()。
2. 目标接口是函数式接口：Lambda表达式通常用于创建实现函数式接口的对象。函数式接口是只包含一个抽象方法的接口。例如，Runnable接口就是一个函数式接口，因为它只有一个抽象方法run()。
3. Lambda表达式的语法：Lambda表达式的语法通常如下：
```java
(参数列表) -> { 表达式或代码块 }
```

---

两种方式的对比
- 共同点
    1. 启动线程都要用Thread类中的start()方法
    2. 创建的线程对象都是`new Thread`或Thread子类对象
- 不同点
    1. 一个是类的继承,一个是接口的实现

**建议使用实现runnable接口的形式**
实现Runnable接口的好处:
- 避免类的单继承的局限性
- 更适合处理有共享数据的问题
- 代码逻辑和数据分离

---

**联系**:`public
class Thread implements Runnable{}`

查看源码,会发现Thread也是实现了Runnable接口

# Thread类的常用结构

## Thread的构造器

常用的构造器:
- `public Thread()`:分配一个新的线程对象
- `public Thread(Runnable target)`:分配一个指定名字的新的线程对象
- `public Thread(String name)`:指定创建线程的目标对象,它实现了Runnbale接口中的run()方法
- `public Thread(Runnable target, String name)`:分配一个带有指定目标新的线程对象并指定名字

对于`public Thread(String name)`,可以给继承Thread类的子类增加一个有参的构造方法:`XxxThread(String name)`,然后调用`super()`,即可调用到父类Thread的`Thread(String name)`
```java
public class Test1 {

    public static void main(String[] args) {
        OddThread t1 = new OddThread("线程一");

        t1.start();

        for (int i = 0; i <= 100; i++) {
            if (i % 2 == 0) {
                System.out.println(i + Thread.currentThread().getName());
            }
        }

    }
}

class OddThread extends Thread {

    /**
     * 构造器，调用父类的一个有参构造
     *
     * @param name
     */
    public OddThread(String name) {
        super(name);
    }

    @Override
    public void run() {
        for (int i = 0; i <= 100; i++) {
            if (i % 2 == 0) {
                System.out.println(i + Thread.currentThread().getName() + "*");
            }
        }
    }
}
```
![20230921181344](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230921181344.png)

对于`public Thread(Runnable target, String name)`,就是在将接口实现类的对象放入Thread构造方法中,增加一个name
```java
public class ThreadCon implements Runnable {
    @Override
    public void run() {
        System.out.println("线程执行了: " + Thread.currentThread().getName());
    }
}

class TesCon {
    public static void main(String[] args) {
        ThreadCon threadCon = new ThreadCon();
        Thread t1 = new Thread(threadCon, "线程一");
        t1.start();
    }
}
```
即可实现给线程命名:
![20230921182005](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230921182005.png)

## 常用方法

1. start():
    - 启动线程
    - 调用Thread的run()方法
2. run():
    - 将线程要执行的操作,声明在run()中
3. currentThread()
    - 获取当前线程
4. getName()
    - 获取线程名
5. setName()
    - 设置线程名
6. sleep(long mills)
    - 静态方法,使当前线程睡眠指定的毫秒数
7. yield()
    - 静态方法,释放CPU的执行权
8. join()
    - 在a线程中通过b线程调用的`join`方法,来使a线程进入阻塞状态,直到b线程执行结束
    - 下面这个代码就是,主线程中通过`t1.join();`,来使主线程进入阻塞状态,直至t1执行完毕
```java
    public static void main(String[] args) {
        OddThread t1 = new OddThread("线程一");
        t1.start();
        for (int i = 0; i <= 100; i++) {
            if (i % 2 == 0) {
                System.out.println(i + Thread.currentThread().getName());
            }
            if (i == 20) {
                try {
                    t1.join();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
```
9. isAlive()
    - 判断当前线程是否存活
10. set/getPriority()
    - 获取/设置优先级,范围`[1-10]`,越大优先级越高,优先级越高,被CPU执行的概率就越大

# 生命周期

JDK1.5之前是这样的:
线程的状态转换
![20230921200903](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230921200903.png)

JDK1.5及之后,状态发生了变化
![20230921221711](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230921221711.png)

1. 新建(NEW)
2. 可运行(Runnable)
3. 锁阻塞(BLOCKED)
4. 计时等待(TIMED_WAITNG)
5. 无限等待(WAITING)
6. 死亡(TERMINATED)
![20230921221849](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230921221849.png)

**大概就是阻塞变得复杂了**

# 线程安全问题

当我们使用多个线程访问同一资源(可以是同一个变量,同一个文件,同一条记录等)的时候,若多个线程只有读操作,那么不会发生线程安全问题,但是如果多个线程中资源有读和写的操作,就容易出现线程安全问题

**之前的项目里都几乎是单线程操作,因为测试也只有一个人进行测试**

一个简单的案例,卖票
```java
class SaleTicket implements Runnable {

    int ticket = 100;

    @Override
    public void run() {
        while (true) {
            if (ticket > 0) {
                System.out.println(Thread.currentThread().getName() + "售票,票号为:" + ticket);
                ticket--;
            } else {
                break;
            }
        }
    }
}

public class WindowTest {

    public static void main(String[] args) {
        SaleTicket saleTicket = new SaleTicket();
        Thread t1 = new Thread(saleTicket, "窗口一");
        Thread t2 = new Thread(saleTicket, "窗口二");
        Thread t3 = new Thread(saleTicket, "窗口三");
        t1.start();
        t2.start();
        t3.start();
    }

}
```
运行代码会发现,卖出的票很明显超过100张,且有可能卖到-1(加sleep),也就是**重票或者错票**

什么原因导致的?**线程1操作ticket的过程中,尚未结束的情况下,其他线程也参与进来,对ticket进行操作**

如何解决:**必须保证一个线程a在操作ticket的过程中,其他线程必须等待,直到线程a操作ticket结束以后,其他线程才可以进行操作**

Java是如何解决线程的安全问题的:使用线程的同步机制

## 同步代码块

1. 介绍
    - `synchronized(同步监视器){需要被同步的代码}`
    - 需要被同步的代码,即为操作共享数据的代码
    - 共享数据,即多个线程需要操作的数据,比如上面的ticket
    - 需要被同步的代码,在被synchronized包裹以后,就使得一个线程在操作这些代码的过程中,其他线程必须等待
    - 同步监视器,锁,哪个线程获取了锁,哪个线程就能执行需要被同步的代码
    - 同步监视器,可以使用任何一个类的对象充当,但是多个线程必须公用同一个同步监视器,**必须唯一**
    - ![20230921231357](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230921231357.png)
    - 关于同步监视器对象的选用,当线程是基于实现的,可以用this,而是基于继承的,可以用Class对象
```java
class SaleTicket implements Runnable {

    int ticket = 100;

    Object obj = new Object();

    @Override
    public void run() {
        while (true) {

            try {
                Thread.sleep(5);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            // 使用同步代码块解决线程安全问题
            // 用this也行,只要是唯一的对象都行
            synchronized (obj) {
                if (ticket > 0) {

                    try {
                        Thread.sleep(10);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    System.out.println(Thread.currentThread().getName() + "售票,票号为:" + ticket);
                    ticket--;
                } else {
                    break;
                }
            }
        }
    }
}

public class WindowTest {

    public static void main(String[] args) {
        SaleTicket saleTicket = new SaleTicket();
        Thread t1 = new Thread(saleTicket, "窗口一");
        Thread t2 = new Thread(saleTicket, "窗口二");
        Thread t3 = new Thread(saleTicket, "窗口三");
        t1.start();
        t2.start();
        t3.start();
    }

}

// 继承的也可以实现这种效果,不过同步监视器要换
class SaleTicket2 extends Thread {

    static int ticket = 100;

    static Integer t = ticket;

    @Override
    public void run() {
        while (true) {
            synchronized (WindowTest2.class) {
                if (ticket > 0) {
                    System.out.println(Thread.currentThread().getName() + "售票,票号为:" + ticket);
                    ticket--;
                } else {
                    break;
                }
            }
        }
    }
}

public class WindowTest2 {
    public static void main(String[] args) {
        SaleTicket2 s1 = new SaleTicket2();
        s1.setName("窗口一");
        SaleTicket2 s2 = new SaleTicket2();
        s2.setName("窗口二");
        SaleTicket2 s3 = new SaleTicket2();
        s3.setName("窗口三");

        s1.start();
        s2.start();
        s3.start();
    }
}
```

## 同步方法

如果操作共享数据的代码完整的声明在一个方法中,那么可以将此方法声明为同步方法即可

如下,即为一个同步方法,实现的效果也是一样的.
```java

    @Override
    public void run() {
        while (isFlag) {
            show();
        }
    }

    public synchronized void show() {
        if (ticket > 0) {

            try {
                Thread.sleep(10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println(Thread.currentThread().getName() + "售票,票号为:" + ticket);
            ticket--;
        } else {
            isFlag = false;
        }
    }
```

**但是这种写法,没有声明同步监视器,那现在的同步监视器是什么?**

**如果同步方法是非静态的,那么同步监视器默认就是`this`,且不能修改**

上面是基于**实现**的线程,下面是基于**继承**的:

如果还这样写,会发现**还是线程不安全的**,因为这里的this并不唯一,下面会创建三个线程对象,所以this不唯一,也就不能作为同步监视器
```java
    public synchronized void show() {
        if (ticket > 0) {
            try {
                Thread.sleep(10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "售票,票号为:" + ticket);
            ticket--;
        } else {
            isFlag = false;
        }
    }
```

所以要么将方法改成static,但是有些情况并不能改为static,所以当某些情况不满足同步方法时,不要强行使用,还是可以在方法中使用同步代码块.

## 总结

synchronized的好处:**解决了线程安全问题**
缺点:效率变低,在操作共享数据时,多线程其实是串行执行的,性能低

练习题,模拟用户存钱:
```java
package com.zzmr.threadsafe;

/**
 * @author zzmr
 * @create 2023-09-22 9:07
 */

class Account {
    private double balance;

    public double getBalance() {
        return balance;
    }

    public void setBalance(double balance) {
        this.balance = balance;
    }

    public synchronized void deposit(double amt) {
        if (amt > 0) {
            balance += amt;
        }

        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println(Thread.currentThread().getName() + "存钱1000块，余额为：" + balance);
    }
}

class Customer extends Thread {
    Account account;

    public Customer(Account account) {
        this.account = account;
    }

    public Customer(Account account, String name) {
        super(name);
        this.account = account;
    }

    @Override
    public void run() {

        for (int i = 0; i < 3; i++) {
            account.deposit(1000);
        }

    }
}

public class Exer {

    public static void main(String[] args) {
        Account account = new Account();
        Customer c1 = new Customer(account, "客户一");
        Customer c2 = new Customer(account, "客户二");
        c1.start();
        c2.start();
    }

}
```

## 线程安全的懒汉式

**埋个坑:单例和多例**

```java
public class BankTest {

    static Bank b1 = null;
    static Bank b2 = null;

    public static void main(String[] args) {
        Thread t1 = new Thread(() -> b1 = Bank.getInstance());

        Thread t2 = new Thread(() -> b2 = Bank.getInstance());
        t1.start();
        t2.start();

        try {
            t1.join();
            t2.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println(b1);
        System.out.println(b2);
        System.out.println(b1 == b2);
    }

}

class Bank {
    private Bank() {

    }

    private static Bank instance = null;

    public static synchronized Bank getInstance() {
        if (instance == null) {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            instance = new Bank();
        }
        return instance;
    }
}
```

用同步方法来解决单例模式的线程安全问题

## 死锁问题

不同的线程分别占用对方需要的同步资源不放弃,都等待对方放弃自己需要的同步资源,就形成了线程的死锁

一个简单的死锁例子
```java
public class DeadBlockTest {

    public static void main(String[] args) {
        StringBuilder s1 = new StringBuilder();
        StringBuilder s2 = new StringBuilder();

        new Thread(() -> {
            synchronized (s1) {
                s1.append("a");
                s2.append("1");

                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

                synchronized (s2) {
                    s1.append("b");
                    s2.append("2");
                    System.out.println(s1);
                    System.out.println(s2);
                }

            }
        }).start();

        new Thread(() -> {
            synchronized (s2) {
                s1.append("c");
                s2.append("3");

                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

                synchronized (s1) {
                    s1.append("d");
                    s2.append("4");
                    System.out.println(s1);
                    System.out.println(s2);
                }

            }
        }).start();
    }
}
```
第一个线程执行,锁住s1,当需要用到s2时,线程2又锁到了s2,需要s1,这就导致两个线程都不能执行下去

**诱发死锁的原因**
1. 互斥条件
2. 占用且等待
3. 不可抢占
4. 循环等待

以上4个条件,同时出现触发死锁

**解决死锁**
死锁一旦出现,基本很难人为干预,只能尽量规避,可以考虑打破上面的诱发条件
1. 针对条件1,互斥条件基本上无法被破坏,因为线程需要通过互斥解决安全问题
2. 针对条件2,可以考虑一次性申请所有所需的资源,这样就不存在等待的问题
3. 针对条件3,占用部分资源的线程在进一步申请其他资源时,如果申请不到,就主动释放掉已经占用的资源
4. 针对条件4,可以将资源改为线性顺序,申请资源时,先申请序号较小的,这样避免循环等待的问题

## Lock的使用

除了使用synchronized同步机制处理线程安全问题之外,还可以使用JDK5.0提供的Lock锁的方式

```java
class SaleTicket2 extends Thread {

    static int ticket = 100;

    private static final ReentrantLock lock = new ReentrantLock();

    @Override
    public void run() {
        while (true) {

            try {
                // 锁定对共享资源的调用
                lock.lock();
                if (ticket > 0) {
                    System.out.println(Thread.currentThread().getName() + "售票,票号为:" + ticket);
                    ticket--;
                } else {
                    break;
                }
            } finally {
                // 释放对共享数据的锁定
                lock.unlock();
            }
        }
    }
}
```

lock和unlock中间的,其实就是之前放到同步代码块中的代码

需要确保ReentrantLock的对象唯一,公用,需要考虑将此对象声明为`static final`(如果是实现,可能就不需要了)

**synchronized和lock对比**
- synchronized不管是同步代码块还是同步方法,都需要在结束一对`{}`之后,释放对同步监视器的调用,Lock是通过两个方法控制需要被同步的代码,更灵活一些
- Lock作为接口,提供了多种实现类,适合更多更复杂的场景,效率更高

# 线程的通信

为什么要处理线程间通信
当我们需要多个线程来共同完成一件任务,并且我们希望他们`有规律的执行`,那么多线程之间需要一些通信机制,可以协调它们的工作,以此实现多线程共同操作一份数据

比如:线程A用来生产包子,线程B用来吃包子,包子可以理解为同一资源,线程A与线程B处理的动作,一个是生产者,一个是消费者,此时B线程必须等到A线程完成后才能执行,那么线程A与线程B之间就需要线程通信,即`等待唤醒机制`

## 等待唤醒机制

这是多个线程间的一种协作机制,谈到线程我们常常想到的是线程间的`竞争(race)`,比如去争夺锁,但这并不是故事的全部,线程间也会有协作机制

在一个线程满足某个条件时,就进入等待状态`wait()/wait(time)`,等待其他线程执行完他们的指定代码过后再将其唤醒`notify()`或者可以指定wait的时间,等时间到了自动唤醒,在有多个线程进行等待时,如果需要,可以使用`notifyAll()`来唤醒所有等待线程,`wait/notify`就是线程间的一种协作机制

**案例1**
使用两个线程打印1-100,线程1,线程2交替打印

```java
class PrintNumber implements Runnable {

    private int number = 1;

    @Override
    public void run() {
        while (true) {
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            synchronized (this) {
                notify();
                if (number <= 100) {
                    System.out.println(Thread.currentThread().getName() + ": " + number);
                    number++;
                    try {
                        // 线程一旦执行此方法，就进入等待状态，会释放对同步监视器的调用
                        wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                } else {
                    break;
                }
            }
        }
    }
}

public class PrintNumberTest {
    public static void main(String[] args) {
        PrintNumber printNumber = new PrintNumber();
        Thread t1 = new Thread(printNumber, "线程1");
        Thread t2 = new Thread(printNumber, "线程2");
        t1.start();
        t2.start();
    }
}
```

主要流程就是,当线程一执行到`wait`时,会进入等待状态,且释放掉同步监视器,而后线程2拿到同步监视器,先执行了`notify`,这个方法会唤醒线程一,而后打印,再执行wait,线程2等待,此时线程一拿到同步监视器,后从等待的地方继续开始执行,以此往复,即可实现交替打印1-100

下面是三个方法的介绍,**三个方法必须使用在同步代码块或者同步方法中,不能使用在lock中,且此方法的调用者,必须是同步监视器**
1. `wait()`:线程一旦执行此方法,就进入等待状态,同时,会释放对同步监视器的调用
2. `notify()`一旦执行,就会唤醒被wait()的线程中优先级最高的那个,如果多个线程的优先级相同,则随机唤醒一个,**被唤醒的线程从wait的位置继续执行** 
3. `notifyAll()`,一旦执行此方法,就会唤醒所有被wait的方法

---

wait和sleep的区别
**相同点**:一旦执行,当前线程都会进入阻塞状态
**不同点**
- 声明的位置
    1. wait():声明在Object类中
    2. sleep():声明在Thread类中,静态的
- 使用的场景不同
    1. wait()只能使用在同步代码块/同步方法中
    2. sleep()使用在任何地方. 
- 使用在同步代码块/同步方法中
    1. wait()会释放同步监视器
    2. sleep()不会释放同步监视器
- 结束阻塞的方法
    1. wait,到达指定时间自动结束阻塞,或通过notify唤醒
    2. sleep,到达指定时间自动结束阻塞

## 生产者消费者案例

生产者(Productor)将产品交给店员(Clerk),而消费者(Customer)从店员处取走产品,店员一次只能持有固定数量的产品(比如:20),如果生产者视图生产更多的产品,店员会叫生产者停一下,如果店中有空位放产品了再通知生产者继续生产,如果店员中没有产品了,店员会告诉消费者等一下,如果店中有产品了再通知消费着来取走产品

```java
package com.zzmr.communication;

/**
 * @author zzmr
 * @create 2023-09-22 16:28
 */

class Clerk {
    private int productNumber = 0;

    // 增加产品数量
    public synchronized void addProduct() {
        if (productNumber >= 20) {
            // 等待
            try {
                wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        } else {
            productNumber++;
            // 唤醒消费者
            notifyAll();
            System.out.println(Thread.currentThread().getName() + "生产了第" + productNumber + "个产品");
        }
    }

    // 减少产品数量
    public synchronized void minusProduct() {
        if (productNumber <= 0) {
            try {
                wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        } else {
            System.out.println(Thread.currentThread().getName() + "消费了第" + productNumber + "个产品");
            productNumber--;
            // 唤醒生产者
            notifyAll();
        }
    }

}

class Producer extends Thread {

    private Clerk clerk;

    public Producer(Clerk clerk) {
        this.clerk = clerk;
    }

    @Override
    public void run() {
        while (true) {
            System.out.println("生产者开始生产产品");

            try {
                Thread.sleep(50);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            clerk.addProduct();
        }

    }
}

class Consumer extends Thread {

    private Clerk clerk;

    public Consumer(Clerk clerk) {
        this.clerk = clerk;
    }

    @Override
    public void run() {
        while (true) {

            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println("消费者开始消费产品");
            clerk.minusProduct();
        }
    }
}

public class ProducerCustomer {

    public static void main(String[] args) {
        Clerk clerk = new Clerk();
        Producer producer = new Producer(clerk);
        Consumer consumer = new Consumer(clerk);
        Consumer consumer2 = new Consumer(clerk);
        producer.setName("生产者1");
        consumer.setName("消费者1");
        consumer2.setName("消费者2");
        producer.start();
        consumer.start();
        consumer2.start();
    }
}
```

# 补充

## 新增两种创建线程的方式

1. 实现Callable(JDK5.0新增的)
    - call()可以有返回值
    - call()可以使用throws的方式处理异常
    - 提供泛型,可以决定call()方法的返回类型
    - 如果在主线程需要获取分线程call()的返回值,则此时的主线程是阻塞状态的.
2. 使用线程池

现有问题,如果并发的线程数量很多,并且每个线程都是执行一个时间很短的任务就结束了,这样频繁创建线程就会大大降低系统的效率,因为频繁创建线程和销毁线程需要时间

那么有没有一种办法使得线程可以复用,即执行完一个任务,并不被销毁,而是可以继续执行其他的任务

**思路**:提前创建好多个线程,放入线程池中,使用时直接获取,使用完放回池中,可以避免频繁创建销毁,实现重复利用,类似生活中的公共交通工具
![20230922172350](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230922172350.png)

使用线程池的好处:
1. 提高了程序执行的效率(因为线程已经提前创建好了)
2. 提高资源的利用率
3. 可以设置相关的参数,对线程池中的线程进行管理

示例:
```java
package com.zzmr.pool;

import java.util.concurrent.Callable;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.ThreadPoolExecutor;

/**
 * @author zzmr
 * @create 2023-09-22 17:33
 */

class NumberThread implements Runnable {

    @Override
    public void run() {
        for (int i = 0; i <= 100; i++) {
            if (i % 2 == 0) {
                System.out.println(Thread.currentThread().getName() + ":" + i);
            }
        }
    }
}

class NumberThread1 implements Runnable {

    @Override
    public void run() {
        for (int i = 0; i <= 100; i++) {
            if (i % 2 != 0) {
                System.out.println(Thread.currentThread().getName() + ":" + i);
            }
        }
    }
}

public class TestPool {
    public static void main(String[] args) {
        // 创建指定线程数量的线程池
        ExecutorService service = Executors.newFixedThreadPool(10);
        ThreadPoolExecutor service1 = (ThreadPoolExecutor) service;

        // 设置线程池的属性
        service1.setMaximumPoolSize(50);// 设置线程池中线程数的上限

        // 执行指定的线程的操作，需要提供实现Runnable接口或者Callable接口实现类对象
        service.execute(new NumberThread());
        service.execute(new NumberThread1());

        // service.execute(Callable callable); // 适用于Callable

        // 关闭
        service.shutdown();
    }
}
```