2023年3月15日 20点43分

# RabbitMQ

**消息中间件**

从SpringCLoud过来了,先学完这个,然后再看Nginx

再回去看吧

技术要求:
JaveSe
JavaEE
Maven
SpringBoot
Linux系统命令基础
分布式集群思想(这个的话,可能有点难吧)

## 1 消息队列

### 1.1 MQ的相关概念

>什么是MQ

MQ(message queue),从字面意思上看,本质是个队列,FIFO先进先出,只不过队列中u南方的内容是message而已,还是一种跨进程的通信机制,用于上下游传递消息.在互联网架构中,MQ是一种非常常见的上下游:逻辑解耦+物理解耦的消息通信服务.使用MQ之后,消息发送上游只需要依赖MQ,不用依赖其他服务

上游:发送消息
下游:接收消息
发送和接受的过程就是上下游传递消息

*没看明白*

>**为什么要用MQ**

1. 流量消峰
举个例子,如果订单系统最多处理一万次订单,这个处理能力应付正常时段的下单时绰绰有余,正常时段我们下打单一秒后就能返回结果,但是在高峰期,如果有两万次下单操作系统是处理不了的,只能限制订单超过一万后不允许用户下单,使用消息队列做缓冲,我们可以取消这个限制,把一秒内下的订单分散成一段时间来处理,这时有些用户可能在下单十几秒后才能收到下单成功的操作,但是比不能下单的体验要好
2. 应用解耦
以电商应用为例,应用中有订单系统,库存系统,物流系统,支付系统,用户创建订单后,如果耦合调用库存系统,物流系统,支付系统,任何一个子系统出现了故障,都会造成下单操作异常.当转变成基于消息队列的方式后,系统间调用的问题会减少很多,比如物流系统因为发生故障,需要几分钟来修复,在这几分钟的时间内,物流系统要处理的内存被缓存在消息队列中,用户的下单操作可以正常完成,当物流系统恢复后,继续处理订单信息即可,下单用户感受不到物流系统的故障,提升系统的可用性
3. 异步处理
有些服务间调用是异步的,例如A调用B,B需要花费很长时间执行,但是A需要知道B什么时候可以执行完,以前一般有两种方式,A过一段时间去调用B的查询api查询,或者A提供一个callback api,B执行完之后调用api通知A服务.这两种方式都不是很优雅,使用消息总线,可以很方便解决这个问题,A调用B服务后,只需要监听B处理完后,会发送一条消息给MQ,MQ会将此消息转发给A服务,这样A服务不用循环调用B的查询api,也不用提供callback api,同样B服务也不用做这些操作,A服务还能及时的得到异步处理成功的消息

**意思就是B在处理完服务之后,发送一条消息给A,A就能及时得到异步处理成功的消息**

>**RabbitMQ适用场景**

结合erlang语言本身的并发优势,性能好**时效性微妙级**,社区活跃度比较高,管理界面用起来十分方便,如果你的数据量没有那么大,中小型公司优先选择功能比较完备的RabbitMQ

>**RabbitMQ的概念**

RabbitMQ是一个消息中间件:它接受并转发消息.你可以把它当作一个快递站点,当你要发送一个包裹时,你把你的包裹放到快递站,快递员会把你的快递送到收件人那里,按照这种逻辑RabbitMQ是一个快递站,一个快递员帮你传递快件,RabbitMQ与快递站的主要区别在于,它不处理快件而是接收,存储和转发消息数据

>**Rabbit四大核心**

1. 生产者
2. 交换机
3. 队列
4. 消费者

![20230315224621](https://img01.zzmr.club/img/20230315224621.png)

>RabbitMQ核心部分

1. "Hello World!"
2. Work queues
3. Publish/Subscribe
4. Routing
5. Topics
6. Publisher Confirms

>RabbitMQ工作原理

![20230315225037](https://img01.zzmr.club/img/20230315225037.png)

- Broker:接收和分发消息的应用,RabbitMQ Server就是Message Broker
- Virtual host:出于多租户和安全因素设计的,把AMQP的基本组件划分到一个虚拟的分组中,类似于网络中的namespace概念,当多个不同的用户在使用同一个RabbitMQ server提供的服务时,可以划分出多个vhost,每个用户在自己的vhost创建exchange/queue等
- Connection:publish/consumer和broker之间的TCP连接
- Channel:如果每一次访问RabbitMQ都建立一个Connection,在消息量大的时候建立TCP Connection的开销将是巨大的,效率也较低,Channel是在connection内部建立的逻辑连接,如果应用程序支持多线程,通常每个thread创建单独的channel进行通讯,AMQP method包含了channel id帮组客户端和message broker识别channel,所以channel之间是完全隔离的,Channel作为轻量级的Connection极大减少了操作系统建立TCP connection的开销
- Exchange:message到达broker的第一站,根据分发规则,匹配查询表中的routing key,分发消息到queue中去.常用的类型有:direct(point-to-point)topic(publish-subscribe)and fanout(multicast)
- Queue:消息最终被送到这里等待consumer取出
- Binding:exchange和queue之间的虚拟连接,binding中可以包含routing key,Bingding信息被保存到exchange中的查询表中,用于message的分发依据

### 1.2 RabbitMQ安装

直接用docker安装得了

1. 安装RabbitMQ
    1. `docker pull rabbitmq`
    2. `docker run -d --name rabbit -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin -p 15672:15672 -p 5672:5672 -p 25672:25672 -p 61613:61613 -p 1883:1883 rabbitmq:management`(网上抄来的命令,一步启动并安装了后台管理界面)
    3. `docker exec -it e71464f9a465 /bin/bash`

[安装链接](https://blog.csdn.net/weixin_44666439/article/details/127265712)

![20230316213101](https://img01.zzmr.club/img/20230316213101.png)

比手动安装简单的多,但是不知道对不对

![20230316215005](https://img01.zzmr.club/img/20230316215005.png)

## 2 Hello World

### 2.1 创建Java开发环境

在这个教程中,我们将用Java编写两个程序.发送单个消息的生产者和接收消息并打印出来的消费者.

下图中,P是我们的生产者,C是我们的消费者,中间的框是一个队列-RabbitMQ代表使用者保留的消息缓冲区

![20230316220939](https://img01.zzmr.club/img/20230316220939.png)

*就是idea新建一个项目*

```xml
    <dependency>
        <groupId>com.rabbitmq</groupId>
        <artifactId>amqp-client</artifactId>
    </dependency>
    <dependency>
        <groupId>commons-io</groupId>
        <artifactId>commons-io</artifactId>
        <version>2.6</version>
    </dependency>
```

### 2.2 生产者代码

![20230316224116](https://img01.zzmr.club/img/20230316224116.png)

`Ctrl+Shift+U`让选中内容大写

```java
package com.zzmr.zzmrrabbitmq.rabbitmq.one;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

/**
 * 生产者 发消息
 */

public class Producer {

    //    队列名称
    public static final String QUEUE_NAME = "hello";

    //    发消息
    public static void main(String[] args) throws IOException, TimeoutException {
        // 创建一个连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        // 设置工厂IP，连接 rabbitMQ的队列
        factory.setHost("1.14.102.11");
        // 设置用户名
        factory.setUsername("admin");
        // 密码
        factory.setPassword("admin");

        // 创建连接
        Connection connection = factory.newConnection();

        // 获取信道
        Channel channel = connection.createChannel();

        /**
         * 生成一个队列
         * 1. 队列名称
         * 2. 队列里面的消息是否持久化，默认情况消息存储在内存中
         * 3. 该队列是否只供一个消费者进行消费 （是否进行消息的共享,true 可以多个消费者消费）
         * 4. 表示是否自动删除 最后一个消费者端开连接以后，该队列是否自动删除，true表示自动删除
         * 5. 其他参数
         */
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);

        // 发消息
        String message = "hello world"; // 初次使用

        /**
         * 1. 发送到那个交换机
         * 2. 表示路由的key是哪个,本次是队列名称
         * 3. 表示其他参数信息
         * 4. 发送消息的消息体
         */
        channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
        System.out.println("消息发送完毕");

    }

}
```

运行之后,可以在后天管理界面看到队列

![20230317101936](https://img01.zzmr.club/img/20230317101936.png)

### 2.3 消费者代码

```java
package com.zzmr.zzmrrabbitmq.rabbitmq.one;

import com.rabbitmq.client.*;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

/**
 * 消费者  接收消息
 */

public class Consumer {

    //    队列名称
    public static final String QUEUE_NAME = "hello";

    public static void main(String[] args) throws IOException, TimeoutException {
        //    创建连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("1.14.102.11");
        factory.setUsername("admin");
        factory.setPassword("admin");

        Connection connection = factory.newConnection();

        Channel channel = connection.createChannel();

        // 声明 接收消息的回调
        DeliverCallback deliverCallback = (consumerTag, message) -> {
            System.out.println("消息如下: " + new String(message.getBody()));
        };

        // 声明 取消消息时的回调
        CancelCallback cancelCallback = (consumerTag) -> {
            System.out.println("消息消费被中断");
        };

        /**
         *  消费者接收消息
         *  1. 消费（接收）哪个队列
         *  2. 消费（接收）成功之后是否要自动应答：true自动应答
         *  3. 消费者未成功消费的回调
         *  4. 消费者取消消费的回调
         */
        channel.basicConsume(QUEUE_NAME, true, deliverCallback, cancelCallback);

    }

}
```

>要注意,要先开启消费者,再开启生产者,消费才能被接收到

![20230317104033](https://img01.zzmr.club/img/20230317104033.png)

## 3 Work Queues

>工作队列(又称任务队列)的主要思想是避免立即执行资源密集型任务,而不得不等待它完成.相反我们安排任务之后执行.当我们把任务封装为消息并将其发送到队列.在后台运行的工作进程将弹出任务并最终执行作业.当有多个工作线程时,这些工作线程将一起处理这些任务

![20230317105431](https://img01.zzmr.club/img/20230317105431.png)

### 3.1 抽取工具类

RabbitMqUtils.java
```java
public class RabbitMqUtils {
    public static Channel getChannel() throws IOException, TimeoutException {
        ConnectionFactory factory = new ConnectionFactory();

        factory.setHost("1.14.102.11");
        factory.setUsername("admin");
        factory.setPassword("admin");

        Connection connection = factory.newConnection();
        return connection.createChannel();
    }
}
```

### 3.2 消费者

```java
package com.zzmr.zzmrrabbitmq.rabbitmq.two;

import com.rabbitmq.client.CancelCallback;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.DeliverCallback;
import com.zzmr.zzmrrabbitmq.rabbitmq.utils.RabbitMqUtils;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

/**
 * 这是一个工作线程
 * 相当于之前的消费者
 */
public class Worker01 {
    //    队列名称
    public static final String QUEUE_NAME = "hello";

    //    接收消息
    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();

        DeliverCallback deliverCallback = (consumerTag, message) -> {
            System.out.println("接收到的消息" + new String(message.getBody()));
        };

        CancelCallback cancelCallback = consumerTag -> {
            System.out.println(consumerTag + "消息者取消消费接口回调逻辑");
        };

        //    消息的接收

        System.out.println("C2等待接收消息......");
        channel.basicConsume(QUEUE_NAME, true, deliverCallback, cancelCallback);
    }

}
```

>启动多个工作线程

一个选项:`Allow parallel run`

![20230317164442](https://img01.zzmr.club/img/20230317164442.png)

然后直接启动就行了

### 3.3 生产者

```java
package com.zzmr.zzmrrabbitmq.rabbitmq.two;

import com.rabbitmq.client.Channel;
import com.zzmr.zzmrrabbitmq.rabbitmq.utils.RabbitMqUtils;

import java.io.IOException;
import java.util.Scanner;
import java.util.concurrent.TimeoutException;

/**
 * 生产者
 * 发送消息-可以发送大量的消息
 */
public class Task01 {

    public static final String QUEUE_NAME = "hello";

    // 发送大量消息
    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();

        //    队列的声明
        /**
         * 生成一个队列
         * 1. 队列名称
         * 2. 队列里面的消息是否持久化，默认情况消息存储在内存中
         * 3. 该队列是否只供一个消费者进行消费 （是否进行消息的共享,true 可以多个消费者消费）
         * 4. 表示是否自动删除 最后一个消费者端开连接以后，该队列是否自动删除，true表示自动删除
         * 5. 其他参数
         */
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);

        //    从控制台当中接受信息，并发送信息
        Scanner scanner = new Scanner(System.in);

        while (scanner.hasNext()) {
            String message = scanner.next();
            /**
             * 1. 发送到那个交换机
             * 2. 表示路由的key是哪个,本次是队列名称
             * 3. 表示其他参数信息
             * 4. 发送消息的消息体
             */
            channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
            System.out.println("发送消息完成" + message);
        }

    }
}
```

从控制台接收内容并发送

### 3.4 测试发送

发送:AA BB CC DD

![20230317165824](https://img01.zzmr.club/img/20230317165824.png)

此时C1接收到AA CC
![20230317165850](https://img01.zzmr.club/img/20230317165850.png)

C2接收到BB DD
![20230317165914](https://img01.zzmr.club/img/20230317165914.png)

### 3.5 消息应答

>概念

消费者完成一个任务可能需要一段时间,如果其中一个消费者处理一个长的任务并仅只完成了部分突然它挂掉了,会发生什么情况.RabbitMQ一旦向消费者传递了一条消息,便立即将该消息标记为删除.在这种情况下,突然有个消费者挂掉了,我们将丢失正在处理的消息.以后后续发送给消费者的消息,因为它无法接收到

为了保证消息在发送过程中不丢失,rabbitMQ引入消息应答机制,消息应答就是**消费者在接收到消息并且处理该消息之后,告诉rabbitmq它已经处理了,rabbitmq可以把该消息删除了**

>**自动应答**

消息发送后立即被认为已经传送成功,这种模式需要在**高吞吐量和数据传输安全性方面做权衡**,因为这种模式如果消息在接收到之前,消费者那边出现连接或者channel关闭,那么消息就丢失了,当然另一方面这种模式消费则耐安可以传递过载的消息,**没有对传递的消息数量进行限制**,当然这样有可能使得消费者这边由于接收太多还来不及处理的消息,导致这些消息的积压,最终使得内存耗尽,最终这些消费者线程被操作系统杀死,**所以这种模式仅使用于消费者可以高效并以某种速率能够处理这些消息的情况下使用**

---

>**消息应答的方法**

1. `Channel.basicAck`(用于肯定确认)
    - RabbitMQ已知道该消息并且成功的处理消息,可以将其丢弃了
2. `Channel.basicNack`(用于否定确认)
3. `Channel.basicRejec`(用于否定确认)
    - 与Channel.basicNack相比少一个参数,不处理该消息了直接拒绝,可以将其丢弃了

>Multiple的解释

手动应答的好处是**可以批量应答并且减少网络拥堵**

![20230317183622](https://img01.zzmr.club/img/20230317183622.png)

multiple的true和false代表不同意思
- true表示批量应答channel上未应答的消息(比如说channel上有传送tag的消息5,6,7,8,当前tag是8,那么此时5-8的这些未应答的消息都会被确认收到消息应答)
![20230317184022](https://img01.zzmr.club/img/20230317184022.png)
- false同上面相比,只会应答tag=8的消息,5,6,7这三个消息依然不会被确认收到消息应答
![20230317184058](https://img01.zzmr.club/img/20230317184058.png)

>**消息自动重新入队**

这些概念真的重要吗?不知道,感觉看了也记不住

如果消费者由于某些原因失去了连接(其通道已关闭,连接已关闭或TCP连接丢失),导致消息未发送ACK确认,RabbitMQ将了解到消息未完全处理,并将对其重新排队.如果此时其他消费者可以处理,它将很快将其重新分发给另一个消费者.这样,即使某个消费者偶尔死亡,也可以确保不会丢失任何消息

![20230317212452](https://img01.zzmr.club/img/20230317212452.png)

---

>**消息手动应答代码**

默认消息采用的是自动应答,所以我们要想实现消息消费过程中不丢失,需要把自动应答改为手动应答,消费者在上面代码的基础上增加如下代码(这个消息应答只跟消费者有关系):

---

#### 3.5.1 消息应答代码

>生产者代码

```java
package com.zzmr.zzmrrabbitmq.rabbitmq.three;

import com.rabbitmq.client.Channel;
import com.zzmr.zzmrrabbitmq.rabbitmq.utils.RabbitMqUtils;

import java.io.IOException;
import java.util.Scanner;
import java.util.concurrent.TimeoutException;

/**
 * 生产者-发出消息
 * 消息在手动应答时是不丢失，放回队列中重新消费
 */
public class Task02 {

    // 队列名称
    public static final String TASK_QUEUE_NAME = "ack_queue";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();

        // 队列名称，是否持久化，是否共享，是否自动删除，其他参数
        channel.queueDeclare(TASK_QUEUE_NAME, false, false, false, null);

        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()) {
            String message = scanner.next();
            channel.basicPublish("", TASK_QUEUE_NAME, null, message.getBytes("UTF-8"));
            System.out.println("生产者发出消息：" + message);
        }
    }

}
```

生产者还是和之前一样,但是加上了UTF-8的编码集,这是为了防止中文乱码

---

>消费者代码:

在那之前,写了个睡眠工具类
```java
package com.zzmr.zzmrrabbitmq.rabbitmq.utils;

/**
 * 睡眠工具类
 */
public class SleepUtils {
    public static void sleep(int second) {
        try {
            Thread.sleep(1000 * second);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    }
}
```

一共两个,区别只在于C1和C2以及线程沉睡时间

```java
package com.zzmr.zzmrrabbitmq.rabbitmq.three;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.DeliverCallback;
import com.zzmr.zzmrrabbitmq.rabbitmq.utils.RabbitMqUtils;
import com.zzmr.zzmrrabbitmq.rabbitmq.utils.SleepUtils;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

/**
 * 消息在手动应答时是不丢失，放回队列中重新消费
 * 消费者
 */
public class Work03 {
    public static final String TASK_QUEUE_NAME = "ack_queue";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        System.out.println("C1等待接收消息处理，时间较短");

        DeliverCallback deliverCallback = (consumerTag, message) -> {
            // 睡10s钟
            SleepUtils.sleep(1);
            System.out.println("接受到了消息:" + new String(message.getBody(), "UTF-8"));
            /**
             * 消息的标记tag
             * 是否批量应答
             */
            channel.basicAck(message.getEnvelope().getDeliveryTag(), false);
        };

        Boolean autoAck = false;
        //    采用手动应答
        channel.basicConsume(TASK_QUEUE_NAME, autoAck, deliverCallback, (consumerTag -> {
            System.out.println(consumerTag + "消费者取消消费接口回调逻辑");
        }));

    }
}
```

Work03,也就是C1,睡1s,Work04,也就是C2,睡10s

#### 3.5.2 消息手动应答测试

直接执行消费者,会发现报错了:
![20230317221025](https://img01.zzmr.club/img/20230317221025.png)

这是由于在信道中并没有找到该队列,**需要先发送一个消息(生产者发送消息)创建这个队列,才能开始接收**

现在就有这个ack_queue队列了
![20230317221200](https://img01.zzmr.club/img/20230317221200.png)

整体的流程就是:
1. 生产者发送消息aa
2. 消费者1接收到消息,很快处理
3. 生产者发送消息bb
4. 消费者2该接收了,但是处理的慢,如果此时把2停掉,消费者1就会接收到该消息,并不会丢失

### 3.6 RabbitMQ持久化

>概念

刚刚我们已经看到了如何处理任务不丢失的情况,但是如何保证当RabbitMQ服务停掉以后消息生产者发送过来的消息不丢失?默认情况下RabbitMQ退出或由于某种原因崩溃时,它忽视队列和消息,除非告知它不要这样做,确保消息不会丢失需要做两件事:**我们需要将队列和消息都标记为持久化**

#### 3.6.1 队列如何实现持久化

之前我们创建的队列都是非持久化的,rabbitMQ如果重启的话,该队列就会被删除,如果要队列实现持久化,需要在声明队列的时候把durable参数设置为持久化(true)

```java
    boolean durable = true;
    // 队列名称，是否持久化，是否共享，是否自动删除，其他参数
    //channel.queueDeclare(TASK_QUEUE_NAME, false, false, false, null);
    channel.queueDeclare(TASK_QUEUE_NAME, durable, false, false, null);
```

但要注意的就是如果之前生命的队列不是持久化的,需要把原先队列先删除,如果要队列实现持久化,需要在生命队列的时候把`durable`参数设置为持久化,如果不把原先的队列删除,就会报:
![20230318092058](https://img01.zzmr.club/img/20230318092058.png)

删除队列就是直接在界面下删除就行了

删除之后重新运行代码:
![20230318092434](https://img01.zzmr.club/img/20230318092434.png)

**这时队列就实现了持久化**

---

#### 3.6.2 消息实现持久化

*要想让消息实现持久化需要在消息生产者修改代码,MessageProperties.PERSISTENT_TEXT_PLAIN*添加这个属性

```java
    //channel.basicPublish("", TASK_QUEUE_NAME, null, message.getBytes("UTF-8"));
    channel.basicPublish("", TASK_QUEUE_NAME, MessageProperties.PERSISTENT_TEXT_PLAIN, message.getBytes("UTF-8"));
```

改成这样就行了

**将消息标记为持久化并不能完全保证不会丢失消息,尽管它告诉RabbitMQ将消息保存到磁盘,但是这里依然存在当消息刚准备存储到磁盘时,但是还没有存储完,消息还在缓存的一个间隔点,此时并没有真正写入磁盘,持久性保证并不强,但是对于我们的简单任务队列而言,这已经绰绰有余了,如果需要更强有力的持久化策略,参考后边课件发布确认章节**

#### 3.6.3 不公平分发

在最开始的时候我们学习到RabbitMQ分发消息采用的轮询分发,但是在某种场景下这种策略并不是很好,**比如说有两个消费者在处理任务,其中有一个消费者1处理任务的速度非常快,而另外一个消费者2处理速度却很慢,这个时候我们还是采用轮询分发的话就会导致这个速度快的消费者很大一部分时间处于空闲状态,而处理慢的那个消费者一直在干活,这种分配方式在这种情况下其实就不太好**,但是RabbitMQ并不知道这种情况,它依然很公平的进行分发

为了避免这种情况,**我们可以在消费者中设置参数channel.basicQos(1);**,也就是能者多劳

```java
        // 设置不公平分发
        int prefetchCount = 1;
        channel.basicQos(prefetchCount);
```

要在接收消息之前设置,且所有的消费者都要设置

此时测试:

生产者连续发送4条消息
![20230318095904](https://img01.zzmr.club/img/20230318095904.png)

Work01由于处理较快,所以接收到3条:
![20230318095934](https://img01.zzmr.club/img/20230318095934.png)

Work02由于处理较慢,所以接收到1条
![20230318095958](https://img01.zzmr.club/img/20230318095958.png)

#### 3.6.4 预取值

>本身消息的发送就是异步发送的,所以在任何时候,channel上肯定不只只有一个消息,另外来自消费者的手动确认本质上也是异步的.因此这里就存在一个未确认的消息缓冲区,**因此希望开发人员能限制此缓冲区的大小,以避免缓冲区里面无限制的未确认消息问题**

**就是指定各个消费者的分配**

![20230318101158](https://img01.zzmr.club/img/20230318101158.png)

代码演示:

```java
        // 设置不公平分发  设置1，表示开启不公平分发
        //int prefetchCount = 1;
        int prefetchCount = 2;  // 预取值是2
        channel.basicQos(prefetchCount);
```

Work03,也就是速度快的,设置了预取值为2,另一个设置了5,所以现在情况就是:虽然Work03快,但是也只能接收到2条消息,剩下5条都要由Work04接收

但是这不是肯定的,channel中要有堆积,才能看到效果

好像不怎么理想,但是也差不多

玩奥日去了

## 4 发布确认

### 4.1 发布确认原理

生产者将信道设置成confirm模式,一旦信道进入confirm模式,**所有在该信道上面发布的消息都将会被指派一个唯一的ID(从1开始)**,一旦消息被投递到所有匹配的队列之后,broker就会发送一个确认给生产者(包含消息的唯一ID),这就使得生产者知道消息已经正确到达目的队列了,如果消息和队列时刻持久化的,那么确认消息会在消息写入磁盘之后发出,broker回传给生产者的确认消息中delivery-tag域包含了确认消息的序列号,此外broker也可以设置basic.ack的multiple域,表示到这个序列号之前的所有消息都已经得到了处理

confirm模式最大的好处在于他是异步的,一旦发布一条消息,生产者应用程序就可以在等信道返回确认的同时继续发送下一条消息.当消息最终得到确认之后,生产者应用便可以通过回调方法来处理该确认消息,如果RabbitMQ因为自身内部错误导致消息丢失,就会发送一条nack消息,生产者应用程序同样可以在回调方法中处理该nack消息

这都啥和啥啊,看不明白

意思就是,生产者在发出消息之后,如果队列和消息都是持久化的,那么生产者就会得到一个**确认消息**来表示消息已经持久化成功

### 4.2 发布确认的策略

>开启发布确认的方法

发布确认默认是没有开启的,如果要开启,需要调用`confirmSelect`,每当你要想使用发布确认,都需要在channel上调用该方法
```java
    // 开启发布确认
    channel.confirmSelect();
```

#### 4.2.1 单个确认发布

>这是一种简单的确认方式,它是一种**同步确认发布**的方式,也就是发布一个消息之后只有它被确认发布,后续的消息才能继续发布,`waitForConfirmOrDie(long)`这个方法之后在消息被确认的时候才返回,如果在指定时间范围内这个消息没有被确认那么它将抛出异常

这种确认方式有一个最大的缺点:**发布速度特别慢**,因为如果没有确认发布的消息就会阻塞所有后续的发布,这种方式最多提供每秒不超过数百条发布消息的吞吐量.当然对于某些程序来说这可能已经足够了

```java
package com.zzmr.zzmrrabbitmq.rabbitmq.four;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.ConnectionFactory;
import com.zzmr.zzmrrabbitmq.rabbitmq.utils.RabbitMqUtils;

import java.util.UUID;

/**
 * 发布确认模式  使用的时间来比较哪种确认方式速度快
 * 1. 单个确认模式    用时：1000单独确认消息耗时26671ms
 * 2. 批量确认模式
 * 3. 异步确认模式
 */
public class ConfirmMessage {

    // 批量发消息的个数
    public static final int MESSAGE_COUNT = 1000;

    public static void main(String[] args) throws Exception {
        //    1. 单个确认
        publishMessageIndividually();
        //    2. 批量确认
        //    3. 异步确认
    }

    //    单个确认
    public static void publishMessageIndividually() throws Exception {
        Channel channel = RabbitMqUtils.getChannel();
        // 队列的声明
        String queueName = UUID.randomUUID().toString();
        channel.queueDeclare(queueName, false, false, false, null);
        // 开启发布确认
        channel.confirmSelect();
        // 开始时间
        long begin = System.currentTimeMillis();

        //    批量的发消息
        for (int i = 0; i < MESSAGE_COUNT; i++) {
            String message = i + "";
            channel.basicPublish("", queueName, null, message.getBytes());
            //    单个消息就马上进行发布确认
            boolean flag = channel.waitForConfirms();
            if (flag) {
                System.out.println("消息发送成功");
            }
        }

        // 结束时间
        long end = System.currentTimeMillis();

        System.out.println("用时：" + MESSAGE_COUNT + "单独确认消息耗时" + (end - begin) + "ms");


    }
}
```

#### 4.2.3 批量确认发布

>上面那种方式非常慢,与单个等待确认消息相比,先发布一批消息然后一起确认可以极大地提高吞吐量,当然这种方式的缺点就是:当发生故障导致发布出现问题,不知道是哪个消息出现问题了,我们必须将整个批处理保存在内存中,以记录重要的信息而后重新发布消息.**当然这种方案仍然是同步的,也一样阻塞消息的发布**

```java
 //    批量发送发布确认
    public static void publishMessageBatch() throws Exception {
        Channel channel = RabbitMqUtils.getChannel();
        // 队列的声明
        String queueName = UUID.randomUUID().toString();
        channel.queueDeclare(queueName, false, false, false, null);
        // 开启发布确认
        channel.confirmSelect();
        // 开始时间
        long begin = System.currentTimeMillis();

        // 批量确认消息的大小
        int batchSize = 100;

        // 批量发送消息，并批量发布确认
        for (int i = 0; i < MESSAGE_COUNT; i++) {
            String message = i + "";
            channel.basicPublish("", queueName, null, message.getBytes());
            // 满100条，进行确认
            if (i % batchSize == 0 && i != 0) {
                boolean flag = channel.waitForConfirms();
                if (flag) {
                    System.out.println("消息发布确认成功");
                }
            }
        }
        //    发布确认


        // 结束时间
        long end = System.currentTimeMillis();

        System.out.println("用时：" + MESSAGE_COUNT + "批量确认消息耗时" + (end - begin) + "ms");

    }
```

**用时：1000批量确认消息耗时1105ms**

可见,几乎是快了20倍


#### 4.2.4 异步确认发布

>异步确认虽然编程逻辑比上两个要复杂,但是性价比最高,无论是可靠性还是效率都没得说,它是利用回调函数来达到消息可靠性传递的,这个中间件也是通过函数回调来保证是否投递成功,下面就让我们来详细讲解异步确认是怎么实现的

![20230318165103](https://img01.zzmr.club/img/20230318165103.png)

```java
//    异步确认
    public static void publishMessageAsync() throws Exception {
        Channel channel = RabbitMqUtils.getChannel();
        // 队列的声明
        String queueName = UUID.randomUUID().toString();
        channel.queueDeclare(queueName, false, false, false, null);
        // 开启发布确认
        channel.confirmSelect();
        // 开始时间
        long begin = System.currentTimeMillis();

        // 消息确认成功 回调函数
        // 1. 消息的标记
        // 2. 是否为批量确认
        ConfirmCallback ackCallback = (deliveryTag, multiple) -> {
            System.out.println("已确认的消息：" + deliveryTag);
        };

        // 消息确认失败 回调函数
        ConfirmCallback nackCallback = (deliveryTag, multiple) -> {
            System.out.println("未确认的消息：" + deliveryTag);
        };
        // 准备消息的监听器 监听哪些消息成功了 哪些消息失败了
        // 1. 监听哪些消息成功了
        // 2. 监听哪些消息失败了
        channel.addConfirmListener(ackCallback, nackCallback);  // 异步监听

        // 批量发送消息，并批量发布确认
        for (int i = 0; i < MESSAGE_COUNT; i++) {
            String message = "消息" + i;
            channel.basicPublish("", queueName, null, message.getBytes());
        }


        // 结束时间
        long end = System.currentTimeMillis();

        System.out.println("用时：" + MESSAGE_COUNT + "异步确认消息耗时" + (end - begin) + "ms");
    }
```

**用时：1000异步确认消息耗时58ms**

#### 4.2.5 如何处理未确认消息

最好的解决方案就是把未确认的消息放到一个基于内存的能被发布线程访问的队列,比如说用`ConcurrentLinkedQueue`这个给队列在confirm callbacks与发布线程之间进行消息的传递


>全部代码:

```java
package com.zzmr.zzmrrabbitmq.rabbitmq.four;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.ConfirmCallback;
import com.rabbitmq.client.ConnectionFactory;
import com.zzmr.zzmrrabbitmq.rabbitmq.utils.RabbitMqUtils;

import java.util.UUID;
import java.util.concurrent.ConcurrentNavigableMap;
import java.util.concurrent.ConcurrentSkipListMap;

/**
 * 发布确认模式  使用的时间来比较哪种确认方式速度快
 * 1. 单个确认模式    用时：1000单独确认消息耗时26671ms
 * 2. 批量确认模式    用时：1000批量确认消息耗时1105ms
 * 3. 异步确认模式    用时：1000异步确认消息耗时58ms
 */
public class ConfirmMessage {

    // 批量发消息的个数
    public static final int MESSAGE_COUNT = 1000;

    public static void main(String[] args) throws Exception {
        //    1. 单个确认
        //publishMessageIndividually();
        //    2. 批量确认
        //publishMessageBatch();
        //    3. 异步确认
        publishMessageAsync();
    }

    //    单个确认
    public static void publishMessageIndividually() throws Exception {
        Channel channel = RabbitMqUtils.getChannel();
        // 队列的声明
        String queueName = UUID.randomUUID().toString();
        channel.queueDeclare(queueName, false, false, false, null);
        // 开启发布确认
        channel.confirmSelect();
        // 开始时间
        long begin = System.currentTimeMillis();

        //    批量的发消息
        for (int i = 0; i < MESSAGE_COUNT; i++) {
            String message = i + "";
            channel.basicPublish("", queueName, null, message.getBytes());
            //    单个消息就马上进行发布确认
            boolean flag = channel.waitForConfirms();
            if (flag) {
                System.out.println("消息发送成功");
            }
        }

        // 结束时间
        long end = System.currentTimeMillis();

        System.out.println("用时：" + MESSAGE_COUNT + "单独确认消息耗时" + (end - begin) + "ms");

    }

    //    批量发送发布确认
    public static void publishMessageBatch() throws Exception {
        Channel channel = RabbitMqUtils.getChannel();
        // 队列的声明
        String queueName = UUID.randomUUID().toString();
        channel.queueDeclare(queueName, false, false, false, null);
        // 开启发布确认
        channel.confirmSelect();
        // 开始时间
        long begin = System.currentTimeMillis();

        // 批量确认消息的大小
        int batchSize = 100;

        // 批量发送消息，并批量发布确认
        for (int i = 0; i < MESSAGE_COUNT; i++) {
            String message = i + "";
            channel.basicPublish("", queueName, null, message.getBytes());
            // 满100条，进行确认
            if (i % batchSize == 0 && i != 0) {
                boolean flag = channel.waitForConfirms();
                if (flag) {
                    System.out.println("消息发布确认成功");
                }
            }
        }
        //    发布确认


        // 结束时间
        long end = System.currentTimeMillis();

        System.out.println("用时：" + MESSAGE_COUNT + "批量确认消息耗时" + (end - begin) + "ms");

    }

    //    异步确认
    public static void publishMessageAsync() throws Exception {
        Channel channel = RabbitMqUtils.getChannel();
        // 队列的声明
        String queueName = UUID.randomUUID().toString();
        channel.queueDeclare(queueName, false, false, false, null);
        // 开启发布确认
        channel.confirmSelect();

        //TODO 准备一个线程安全有序的哈希表  适用于高并发的情况下   1. 它能轻松的将序号与消息进行关联，2. 也可以轻松的批量删除条目 只要给到序号， 3. 支持高并发(多线程)
        ConcurrentSkipListMap<Long, String> outstandingConfirms =
                new ConcurrentSkipListMap<>();

        // 消息确认成功 回调函数
        // 1. 消息的标记
        // 2. 是否为批量确认
        ConfirmCallback ackCallback = (deliveryTag, multiple) -> {
            if (multiple) {
                //TODO 2. 删除掉已经确认的消息  剩下的就是未确认的消息
                ConcurrentNavigableMap<Long, String> confirmed = outstandingConfirms.headMap(deliveryTag);
                confirmed.clear();
            } else {
                outstandingConfirms.remove(deliveryTag);
            }

            System.out.println("已确认的消息：" + deliveryTag);
        };

        // 消息确认失败 回调函数
        ConfirmCallback nackCallback = (deliveryTag, multiple) -> {
            //TODO 3. 打印一下未确认的消息都有哪些
            String message = outstandingConfirms.get(deliveryTag);
            System.out.println("未确认的消息是:" + message);
            System.out.println("未确认的消息的标记：" + deliveryTag);
        };
        // 准备消息的监听器 监听哪些消息成功了 哪些消息失败了
        // 1. 监听哪些消息成功了
        // 2. 监听哪些消息失败了
        channel.addConfirmListener(ackCallback, nackCallback);  // 异步监听

        // 开始时间
        long begin = System.currentTimeMillis();

        // 批量发送消息，并批量发布确认
        for (int i = 0; i < MESSAGE_COUNT; i++) {
            String message = "消息" + i;
            channel.basicPublish("", queueName, null, message.getBytes());

            //TODO 1. 此处记录下所有发送的消息   消息的总和
            outstandingConfirms.put(channel.getNextPublishSeqNo(), message);
        }


        // 结束时间
        long end = System.currentTimeMillis();

        System.out.println("用时：" + MESSAGE_COUNT + "异步确认消息耗时" + (end - begin) + "ms");
    }
}
```

#### 4.2.6 发布消息总结

1. 单独发布消息
    - 同步等待确认,简单,但吞吐量非常有限
2. 批量发布消息
    - 批量同步等待确认,简单,合理的吞吐量,但一旦出现问题很难推断出是哪条消息出现了问题
3. 异步处理
    - 最佳性能和资源使用,在出现错误的情况下可以很好地控制,但是实现起来稍微难些


## 5 交换机(Exchanges)

### 5.1 交换机概念

在之前的内容中,我们都是创建一个**工作队列**.我们假设的是工作队列背后,每个任务都恰好交付给一个消费者(工作进程).在这一部分中,我们将做一些完全不同的事情,我们将消息传达给多个消费者.这种模式称为"发布/订阅"

原来的Hello World以及Work Queues:
![20230319161211](https://img01.zzmr.club/img/20230319161211.png)

而现在的发布订阅模式:
![20230319161308](https://img01.zzmr.club/img/20230319161308.png)

为了说明这种情况,我们将构建一个简单的日志系统.它将由两个程序组成:**第一个程序将发出日志消息,第二个程序是消费者.其中我们会启动两个消费者,其中一个消费者接收到消息后把日志存储在磁盘,另一个消费者接收到消息后把消息打印在屏幕上,事实上第一个程序发出的日志消息将广播给所有消费者**

---

RabbitMQ消息传递模型的核心思想是:生产者生产的消息不会直接发送到队列.实际上,通常生产者甚至都不知道这些消息传递到了哪些队列中

相反,**生产者只能将消息发送到交换机(exchange)**,交换机工作的内容非常简单,一方面它接收来自生产者的消息,另一方面将它们推入队列.交换机必须确切知道如何处理收到的消息.是应该把这些消息放到执行队列还是说应该丢弃它们.这就是由交换机的类型来决定的.

![20230319161535](https://img01.zzmr.club/img/20230319161535.png)

---

>Exchanges的类型

总共有以下类型:
- 直接(direct)
- 主题(topic)
- 标题(headers)
- 扇出(fanout)

>无名exchange(默认)

在本教程中前面部分我们对exchange一无所知,但仍然能够将消息发送到队列.之前能实现的原因是因为我们使用的是默认交换,我们通过空字符串`""`进行标识

```java
    channel.basicPublish("", queueName, null, message.getBytes());
```

---

>临时队列

之前的章节我们使用的是具有特定名称的队列,队列的名称我们来说至关重要-我们需要指定我们的消费者去消费哪个队列的消息

每当我们连接到RabbitMQ时,我们都需要一个全新的空队列,为此我们可以创建一个具有**随机名称的队列**,或者能让服务器为我们选择一个随机队列名称那就更好了.其次**一旦我们断开了消费者的连接,队列将被自动删除**

创建临时队列的方式如下:
```java
    String queueName = channel.queueDeclare().getQueue();
```

---

>绑定(bindings)

什么是bingings呢,bingding其实是exchange和queue之间的桥梁,它告诉我们exchage和哪个队列进行了绑定关系.比如说下面这张图告诉我们的就是X与Q1和Q2进行了绑定

![20230319164220](https://img01.zzmr.club/img/20230319164220.png)

在图形化界面里可以直接新建queue和exchange

新建完之后可以进行过绑定
![20230319164445](https://img01.zzmr.club/img/20230319164445.png)

### 5.2 Fanout

#### 5.2.1 Fanout介绍

直译**扇出**,其实就是发布和订阅

这种类型非常简单.正如从名称中猜到的那样,它是将接收到的所有消息**广播**到它知道的所有队列中.系统种默认有些exchange类型

![20230319165237](https://img01.zzmr.club/img/20230319165237.png)

#### 5.2.2 Fanout实战

Logs和临时队列的绑定关系如下图:

![20230319165458](https://img01.zzmr.club/img/20230319165458.png)

>**消息接收**

**ReceiveLogs01.java**

```java
package com.zzmr.zzmrrabbitmq.rabbitmq.five;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.DeliverCallback;
import com.zzmr.zzmrrabbitmq.rabbitmq.utils.RabbitMqUtils;

import java.io.IOException;
import java.util.concurrent.TimeoutException;


/**
 * 接收消息-1
 */
public class ReceiveLogs01 {

    // 交换机的名称
    public static final String EXCHANGE_NAME = "logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();

        //    声明一个交换机
        channel.exchangeDeclare(EXCHANGE_NAME, "fanout");

        //    声明一个队列--临时队列
        /**
         *  队列的名称是随机的
         *  当消费者断开与队列的连接的时候，队列会自动删除
         */
        String queueName = channel.queueDeclare().getQueue();

        /**
         * 绑定交换机与队列
         */
        channel.queueBind(queueName, EXCHANGE_NAME, "");

        System.out.println("ReceiveLogs01 等待接收消息");

        // 接收消息的回调
        DeliverCallback deliverCallback = (consumerTag, message) -> {
            System.out.println("ReceiveLogs01 控制台打印接收到的消息：" + new String(message.getBody(), "UTF-8"));
        };
        // 消费者取消消息的回调--可以简单写：

        channel.basicConsume(queueName, true, deliverCallback, consumerTag ->{});
    }
}
```

还有一个消费者ReceiveLogs02,区别只在于打印的那两个字不同

---

>**消息发送**

**EmitLog.java**

```java
package com.zzmr.zzmrrabbitmq.rabbitmq.five;

import com.rabbitmq.client.Channel;
import com.zzmr.zzmrrabbitmq.rabbitmq.utils.RabbitMqUtils;

import java.io.IOException;
import java.util.Scanner;
import java.util.concurrent.TimeoutException;

/**
 * 发消息
 */
public class EmitLog {

    public static final String EXCHANGE_NAME = "logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();

        channel.exchangeDeclare(EXCHANGE_NAME, "fanout");

        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()) {
            String message = scanner.next();

            channel.basicPublish(EXCHANGE_NAME, "", null, message.getBytes("UTF-8"));
            System.out.println("生产者发送消息成功，消息：" + message);
        }
    }
}
```

发送:
![20230320203858](https://img01.zzmr.club/img/20230320203858.png)

接收:
![20230320203915](https://img01.zzmr.club/img/20230320203915.png)

![20230320203924](https://img01.zzmr.club/img/20230320203924.png)

### 5.3 Direct exchange

>在上一节中,我们构建了一个简单的日志记录系统,我们能够向许多接收者广播日志消息.在本节我们将向其中添加一些特别的功能-比方说我们只让某个消费者订阅发布的部分消息;例如我们只把严重错误消息定向存储到日志文件(以节省磁盘空间),同时仍然能够在控制台上打印所有日志消息;我们再来回顾一下什么是bingings,**绑定是交换机和队列之间的桥梁关系**,也可以这么理解:**队列只对绑定它的交换机的消息感兴趣**.绑定用参数:`routingKey`来表示也可以称该参数为`bingding key`,创建绑定我们用代码:`channel.queueBind(queueBind(queueName,EXCHANGE_NAME,"routingKey"))`,绑定之后的意义由其交换类型决定

#### 5.3.1 Direct exchange介绍

上一节是将所有的消息广播给所有的消费者,这次我们希望**将日志消息写入磁盘的程序仅接收严重错误(erros),而不存储警告(warning)或信息(info)日志

![20230320210132](https://img01.zzmr.club/img/20230320210132.png)

看弹幕说这节课好像有问题,先听听看吧

如果exchange的绑定类型是direct,**但是它绑定的多个队列的key如果都相同**,在这种情况下虽然绑定类型是direct,但是它**表现得就和fanout有点类似了**,就和广播差不多,如上图所示

![20230320210956](https://img01.zzmr.club/img/20230320210956.png)

#### 实战

![20230320212602](https://img01.zzmr.club/img/20230320212602.png)

>类型的枚举,所以可以通过字符串来使用也可以导入枚举
```java
package com.rabbitmq.client;

/**
 * Enum for built-in exchange types.
 */
public enum BuiltinExchangeType {

    DIRECT("direct"), FANOUT("fanout"), TOPIC("topic"), HEADERS("headers");

    private final String type;

    BuiltinExchangeType(String type) {
        this.type = type;
    }

    public String getType() {
        return type;
    }
}
```

三个消费者,分别对应三个`Routing key`

ReceiveLogsDirect01.java

```java
package com.zzmr.zzmrrabbitmq.rabbitmq.six;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.DeliverCallback;
import com.zzmr.zzmrrabbitmq.rabbitmq.utils.RabbitMqUtils;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

import static com.rabbitmq.client.BuiltinExchangeType.DIRECT;

public class ReceiveLogsDirect01 {

    public static final String EXCHANGE_NAME = "direct_logs";

    public static void main(String[] args) throws IOException, TimeoutException {

        Channel channel = RabbitMqUtils.getChannel();

        channel.exchangeDeclare(EXCHANGE_NAME, DIRECT);

        channel.queueDeclare("console", false, false, false, null);

        channel.queueBind("console", EXCHANGE_NAME, "info");
        channel.queueBind("console", EXCHANGE_NAME, "warning");

        // 接收消息的回调
        DeliverCallback deliverCallback = (consumerTag, message) -> {
            System.out.println("ReceiveLogsDirect01 控制台打印接收到的消息：" + new String(message.getBody(), "UTF-8"));
        };
        // 消费者取消消息的回调--可以简单写：

        channel.basicConsume("console", true, deliverCallback, consumerTag -> {
        });
    }
}
```

ReceiveLogsDirect02.java

```java
package com.zzmr.zzmrrabbitmq.rabbitmq.six;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.DeliverCallback;
import com.zzmr.zzmrrabbitmq.rabbitmq.utils.RabbitMqUtils;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

import static com.rabbitmq.client.BuiltinExchangeType.DIRECT;

public class ReceiveLogsDirect02 {

    public static final String EXCHANGE_NAME = "direct_logs";

    public static void main(String[] args) throws IOException, TimeoutException {

        Channel channel = RabbitMqUtils.getChannel();

        channel.exchangeDeclare(EXCHANGE_NAME, DIRECT);

        channel.queueDeclare("disk", false, false, false, null);

        channel.queueBind("disk", EXCHANGE_NAME, "error");

        // 接收消息的回调
        DeliverCallback deliverCallback = (consumerTag, message) -> {
            System.out.println("ReceiveLogsDirect02 控制台打印接收到的消息：" + new String(message.getBody(), "UTF-8"));
        };
        // 消费者取消消息的回调--可以简单写：

        channel.basicConsume("disk", true, deliverCallback, consumerTag -> {
        });
    }
}
```

发送消息

```java
package com.zzmr.zzmrrabbitmq.rabbitmq.six;

import com.rabbitmq.client.Channel;
import com.zzmr.zzmrrabbitmq.rabbitmq.utils.RabbitMqUtils;

import java.io.IOException;
import java.util.Scanner;
import java.util.concurrent.TimeoutException;

public class DirectLogs {

    public static final String EXCHANGE_NAME = "direct_logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();

        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()) {
            String message = scanner.next();
            // 根据routingKey来确定给哪个队列发,如果写error就是2,写info或者warning就是1
            channel.basicPublish(EXCHANGE_NAME, "error", null, message.getBytes("UTF-8"));
            System.out.println("生产者发送消息成功，消息：" + message);
        }
    }
}
```

### 5.4 Topics

#### 5.4.1 遗留的问题

在上一个小节中,我们改进了日志记录系统.我们没有使用只能进行随意广播的fanout交换机,而是使用了direct交换机,从而能实现有选择性地接收日志

尽管使用direct交换机改进了我们的系统,但是它仍然存在局限性:direct如果想绑定多个队列,就办不到了

#### 5.4.2 Topic的要求

发送到类型是topic交换机的消息的`routing_key`不能随意写,必须满足一定的要求:必须是一个单词列表,以点号分隔开.这些单词可以是任意单词,比如说:`stock.usd.nyse`,`nyse.vmw`,`quick.orange.rabbit`,这种类型的,当然这个单词列表最多不能超过255字节

这个规则列表中,其中有两个替换符是大家需要注意的

```
* 可以代替一个单词
# 可以替代零个或多个单词
```

#### 5.4.3 Topic匹配案例

定义绑定关系如下

```
Q1 --> 绑定的是 中间带orange带3个单词的字符串(*.orange.*)
Q2 --> 绑定的是 最后一个单词是rabbit的3个单词(*.*.rabbit),第一个单词是lazy的多个单词(lazy.#)
```

![20230320223800](https://img01.zzmr.club/img/20230320223800.png)

上图是一个队列绑定关系图,我们来看看之间的数据接收情况是怎么样

说白了就是一个规则,只有规则匹配才能发送成功

>当一个队列绑定的键是`#`,那么这个队列将接收所有数据,就有点像fanout了,如果队列绑定键中没有`#`和`*`,那么该队列绑定类型就是direct了

#### 5.4.4 Topic实战

![20230321150238](https://img01.zzmr.club/img/20230321150238.png)

![20230320223800](https://img01.zzmr.club/img/20230320223800.png)

根据上面两张图来编写Topic案例

ReceiveLogsTopic01.java
```java
package com.zzmr.zzmrrabbitmq.rabbitmq.seven;

import com.rabbitmq.client.BuiltinExchangeType;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.DeliverCallback;
import com.zzmr.zzmrrabbitmq.rabbitmq.utils.RabbitMqUtils;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

/**
 * 声明主题交换机及相关队列
 * 消费者C1
 */
public class ReceiveLogsTopic01 {

    public static final String EXCHANGE_NAME = "topic_logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        // 声明交换机
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC);
        //    声明队列
        String queueName = "q1";
        channel.queueDeclare(queueName, false, false, false, null);

        channel.queueBind(queueName, EXCHANGE_NAME, "*.orange.*");
        System.out.println("ReceiveLogsTopic01 等待接收消息....");

        DeliverCallback deliverCallback = (consumerTag, message) -> {
            System.out.println("ReceiveLogsTopic01 接收到的消息：" + new String(message.getBody(), "UTF-8"));
            System.out.println("接收队列：" + queueName + "绑定键" + message.getEnvelope().getRoutingKey());
        };

        channel.basicConsume(queueName, true, deliverCallback, consumerTag -> {
        });
    }

}
```

ReceiveLogsTopic02.java

```java
package com.zzmr.zzmrrabbitmq.rabbitmq.seven;

import com.rabbitmq.client.BuiltinExchangeType;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.DeliverCallback;
import com.zzmr.zzmrrabbitmq.rabbitmq.utils.RabbitMqUtils;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

/**
 * 声明主题交换机及相关队列
 * 消费者C1
 */
public class ReceiveLogsTopic02 {

    public static final String EXCHANGE_NAME = "topic_logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        // 声明交换机
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC);
        //    声明队列
        String queueName = "q2";
        channel.queueDeclare(queueName, false, false, false, null);

        channel.queueBind(queueName, EXCHANGE_NAME, "*.*.rabbit");
        channel.queueBind(queueName, EXCHANGE_NAME, "lazy.#");

        System.out.println("ReceiveLogsTopic02 等待接收消息....");

        DeliverCallback deliverCallback = (consumerTag, message) -> {
            System.out.println("ReceiveLogsTopic02 接收到的消息：" + new String(message.getBody(), "UTF-8"));
            System.out.println("接收队列：" + queueName + "绑定键" + message.getEnvelope().getRoutingKey());
        };

        channel.basicConsume(queueName, true, deliverCallback, consumerTag -> {
        });
    }

}
```

生产者测试发送这些routingkey
![20230321153035](https://img01.zzmr.club/img/20230321153035.png)

EmitLogTopic.java

```java
package com.zzmr.zzmrrabbitmq.rabbitmq.seven;

import com.rabbitmq.client.BuiltinExchangeType;
import com.rabbitmq.client.Channel;
import com.zzmr.zzmrrabbitmq.rabbitmq.utils.RabbitMqUtils;

import java.io.IOException;
import java.util.HashMap;
import java.util.Map;
import java.util.Scanner;
import java.util.concurrent.TimeoutException;

/**
 * 生产者
 */
public class EmitLogTopic {
    public static final String EXCHANGE_NAME = "topic_logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        // 声明交换机
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC);

        Map<String, String> bindingKeyMap = new HashMap<>();
        bindingKeyMap.put("quick.orange.rabbit", "Q1和Q2");
        bindingKeyMap.put("lazy.orange.elephant", "Q1和Q2");
        bindingKeyMap.put("quick.orange.fox", "Q1");
        bindingKeyMap.put("lazy.brown.fox", "Q2");
        bindingKeyMap.put("lazy.pink.rabbit", "Q2");
        bindingKeyMap.put("quick.brown.fox", "null");
        bindingKeyMap.put("quick.orange.male.rabbit", "null");
        bindingKeyMap.put("lazy.orange.male.rabbit", "Q2");


        for (Map.Entry<String, String> bindingKeyEntry : bindingKeyMap.entrySet()) {
            channel.basicPublish(EXCHANGE_NAME, bindingKeyEntry.getKey(), null, bindingKeyEntry.getValue().getBytes("UTF-8"));
            System.out.println("生产者发出消息: " + bindingKeyEntry.getValue());
        }
    }
}
```

>**测试成功,会进行区分分发**

---

这些东西真的有用吗

不清楚啊哈哈哈

怎么感觉又中招了,这甲流

---

## 6 死信队列

>先从概念上解释这个死信队列,死信,顾名思义就是无法被消费的消息,一般来说,producer将消息投递到broker或者直接到broker或者直接到queue里了,consumer从queue取出消息进行消费,但某些时候由于特定的**原因导致queue中的某些消息无法被消费**,这样的消息如果没有后续的处理,就变成了死信,有死信自然就有了死信队列

应用场景:为了保证订单业务的消息数据不丢失,需要使用RabbitMQ的死信队列机制,当消息消费发生异常时,将消息投入死信队列中.还有比如说:**用户在商城下单成功并点击去支付后在指定时间未支付时自动失效**

*后面会有整合SpringBoot,到时候应该就知道到底怎么用了吧*

### 6.1 死信的来源

1. 消息TTL过期
2. 队列达到最大长度(队列满了,无法再添加数据到mq中)
3. 消息被拒绝(`basic.reject`或`basic.nack`)并且`requeue=false`

### 6.2 死信实战

>代码架构图
![20230321155919](https://img01.zzmr.club/img/20230321155919.png)

#### 6.2.1 TTL过期

>消费者01代码
```java
package com.zzmr.zzmrrabbitmq.rabbitmq.eight;

import com.rabbitmq.client.BuiltinExchangeType;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.DeliverCallback;
import com.zzmr.zzmrrabbitmq.rabbitmq.utils.RabbitMqUtils;

import java.io.IOException;
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.TimeoutException;

/**
 * 死信队列 实战
 * 消费者1
 */
public class Consumer01 {

    // 普通交换机的名称
    public static final String NORMAL_EXCHANGE = "normal_exchange";
    // 死信交换机的名称
    public static final String DEAD_EXCHANGE = "dead_exchange";

    // 普通队列名称
    public static final String NORMAL_QUEUE = "normal_queue";
    // 死信队列的名称
    public static final String DEAD_QUEUE = "dead_queue";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();

        // 声明死信和普通类型的交换机
        channel.exchangeDeclare(NORMAL_EXCHANGE, "direct");
        channel.exchangeDeclare(DEAD_EXCHANGE, BuiltinExchangeType.DIRECT);


        Map<String, Object> arguments = new HashMap<>();
        // 过期时间
        // 正常队列设置死信交换机
        arguments.put("x-dead-letter-exchange", DEAD_EXCHANGE);
        // 设置死信RoutingKey
        arguments.put("x-dead-letter-routing-key", "lisi");
        // 过期时间  10s   由发消息的一方指定会更好
        //arguments.put("x-message-ttl", 100000);

        // 声明死信和普通队列
        channel.queueDeclare(NORMAL_QUEUE, false, false, false, arguments);

        channel.queueDeclare(DEAD_QUEUE, false, false, false, null);

        // 绑定普通的交换机与普通队列
        channel.queueBind(NORMAL_QUEUE, NORMAL_EXCHANGE, "zhangsan");
        // 绑定死信的交换机与死信的队列
        channel.queueBind(DEAD_QUEUE, DEAD_EXCHANGE, "lisi");
        System.out.println("等待接收消息....");

        DeliverCallback deliverCallback = (consumerTag, message) -> {
            System.out.println("Consumer01接收到消息：" + new String(message.getBody(), "UTF-8"));
        };
        channel.basicConsume(NORMAL_QUEUE, true, deliverCallback, consumerTag -> {
        });
    }
}
```

>生产者代码
```java
package com.zzmr.zzmrrabbitmq.rabbitmq.eight;

import com.rabbitmq.client.AMQP;
import com.rabbitmq.client.Channel;
import com.zzmr.zzmrrabbitmq.rabbitmq.utils.RabbitMqUtils;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

/**
 * 死信队列实战
 * 生产者
 */
public class Producer {
    public static final String NORMAL_EXCHANGE = "normal_exchange";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();

        AMQP.BasicProperties properties = new AMQP.BasicProperties()
                .builder().expiration("10000").build();

        // 死信消息  设置TTL时间
        for (int i = 1; i <= 10; i++) {
            String message = "info" + i;
            channel.basicPublish(NORMAL_EXCHANGE, "zhangsan", properties, message.getBytes("UTF-8"));
        }
    }
}
```

>测试
1. 首先打开消费者,来声明交换机和队列
2. 关闭消费者,打开生产者
3. 观察两个队列的消息情况,应该是消息首先在normal里面,然后全部转到dead里面

没问题
![20230321172925](https://img01.zzmr.club/img/20230321172925.png)


此时编写C2的代码,也就是消费者2
```java
package com.zzmr.zzmrrabbitmq.rabbitmq.eight;

import com.rabbitmq.client.BuiltinExchangeType;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.DeliverCallback;
import com.zzmr.zzmrrabbitmq.rabbitmq.utils.RabbitMqUtils;

import java.io.IOException;
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.TimeoutException;

/**
 * 死信队列 实战
 * 消费者2
 */
public class Consumer02 {


    // 死信队列的名称
    public static final String DEAD_QUEUE = "dead_queue";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        System.out.println("Consumer02 等待接收消息");
        DeliverCallback deliverCallback = (consumerTag, message) -> {
            System.out.println("Consumer02 接收到消息：" + new String(message.getBody(), "UTF-8"));
        };
        channel.basicConsume(DEAD_QUEUE, true, deliverCallback, consumerTag -> {
        });
    }
}
```

这就很简单了,只需要接收死信队列的消息,打开运行,就能接收到消息了

![20230321173607](https://img01.zzmr.club/img/20230321173607.png)

#### 6.2.2 队列达到最大长度

给普通队列设置最大长度,**只需要在c1中设置即可,注意要把之前的队列删除,不然会报错**
```java
        // 设置正常队列长度的限制
        arguments.put("x-max-length", 6);
```

![20230321174641](https://img01.zzmr.club/img/20230321174641.png)

普通队列的最大长度为6,生产者发送10条消息,此时将c1关闭掉,如果不关闭则会被消息,就不会有消息挤压,所以10条中有4条会进入死信队列
![20230321174747](https://img01.zzmr.club/img/20230321174747.png)

#### 6.2.3 消息被拒

发送消息和C2依然不变,C1更改机制,拒绝`info5`这条消息
```java
package com.zzmr.zzmrrabbitmq.rabbitmq.nine;

import com.rabbitmq.client.BuiltinExchangeType;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.DeliverCallback;
import com.zzmr.zzmrrabbitmq.rabbitmq.utils.RabbitMqUtils;

import java.io.IOException;
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.TimeoutException;

/**
 * 死信队列 实战
 * 消费者1
 */
public class Consumer01 {

    // 普通交换机的名称
    public static final String NORMAL_EXCHANGE = "normal_exchange";
    // 死信交换机的名称
    public static final String DEAD_EXCHANGE = "dead_exchange";

    // 普通队列名称
    public static final String NORMAL_QUEUE = "normal_queue";
    // 死信队列的名称
    public static final String DEAD_QUEUE = "dead_queue";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();

        // 声明死信和普通类型的交换机
        channel.exchangeDeclare(NORMAL_EXCHANGE, "direct");
        channel.exchangeDeclare(DEAD_EXCHANGE, BuiltinExchangeType.DIRECT);


        Map<String, Object> arguments = new HashMap<>();
        // 过期时间
        // 正常队列设置死信交换机
        arguments.put("x-dead-letter-exchange", DEAD_EXCHANGE);
        // 设置死信RoutingKey
        arguments.put("x-dead-letter-routing-key", "lisi");
        // 过期时间  10s   由发消息的一方指定会更好
        //arguments.put("x-message-ttl", 100000);

        // 设置正常队列长度的限制
        //arguments.put("x-max-length", 6);


        // 声明死信和普通队列
        channel.queueDeclare(NORMAL_QUEUE, false, false, false, arguments);

        channel.queueDeclare(DEAD_QUEUE, false, false, false, null);

        // 绑定普通的交换机与普通队列
        channel.queueBind(NORMAL_QUEUE, NORMAL_EXCHANGE, "zhangsan");
        // 绑定死信的交换机与死信的队列
        channel.queueBind(DEAD_QUEUE, DEAD_EXCHANGE, "lisi");
        System.out.println("等待接收消息....");

        DeliverCallback deliverCallback = (consumerTag, message) -> {
            String msg = new String(message.getBody(), "UTF-8");
            if (msg.equals("info5")) {
                System.out.println("Consumer01接收到消息：" + msg + " 此消息被拒绝的");
                channel.basicReject(message.getEnvelope().getDeliveryTag(), false);
            }
            System.out.println("Consumer01接收到消息：" + msg);
            // 手动应答 如果是自动应答，那就不存在拒绝消息了
            channel.basicAck(message.getEnvelope().getDeliveryTag(), false);
        };
        channel.basicConsume(NORMAL_QUEUE, false, deliverCallback, consumerTag -> {
        });
    }
}
```

没问题

![20230321180440](https://img01.zzmr.club/img/20230321180440.png)

## 7 延迟队列

>延迟队列概念

延迟队列,队列内部是有序的,最重要的特性就体现在它的延时属性上,延时队列中的元素是希望在指定时间到了以后或之前取出和处理,简单来说,延时队列就是用来存放需要在指定时间被处理的元素的队列

**延迟队列中存放的就是延迟消息**

>延迟队列使用场景
1. 订单在十分钟之内未支付则自动取消
2. 新创建的店铺,如果在十天内都没有上传过商品,则自动发消息提醒
3. 用户注册成功,如果三天内没有登陆则进行短信提醒
4. 用户发起退款,如果三天内没有得到处理则通知相关运营人员
5. 预定会议,需要在预定的时间点前十分钟通知各个与会人员参加会议

这些场景都有要给特点,需要在某个事件发生之后或者之前的指定时间点完成某一项任务

### 7.1 整合SpringBoot

没啥东西,就是一些依赖

```xml
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.29</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.9.2</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.9.2</version>
        </dependency>
        <!--        RabbitMQ测试依赖-->
        <dependency>
            <groupId>org.springframework.amqp</groupId>
            <artifactId>spring-rabbit-test</artifactId>
            <scope>test</scope>
        </dependency>
```

然后是Swagger配置类,这个以前都没有好好学过,*感觉有时间了可以放到软工项目里试试*

```java
package com.zzmr.springbootrabbitmq.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@Configuration
@EnableSwagger2
public class SwaggerConfig {
    @Bean
    public Docket webApiConfig() {
        return new Docket(DocumentationType.SWAGGER_2)
                .groupName("webApi")
                .apiInfo(webApiInfo())
                .select()
                .build();
    }

    private ApiInfo webApiInfo() {
        return new ApiInfoBuilder()
                .title("rabbitmq 接口文档")
                .description("本文档描述了 rabbitmq 微服务接口定义")
                .version("1.0")
                .contact(new Contact("zzmr", "https://jimmy66886.github.io/",
                        "jim7yang@gmail.com"))
                .build();
    }
}
```

### 7.2 队列TTL

#### 7.2.1 代码架构图

创建两个队列QA和QB,两者队列TTL分别设置为10S和40S,然后在创建一个交换机X和死信交换机Y,它们的类型都是direct,创建一个死因队列QD,它们的绑定关系如下

![20230321211632](https://img01.zzmr.club/img/20230321211632.png)

高烧退了,现在是37.4,好多了

#### 7.2.2 配置文件类代码

这个是干什么的?

是配置RabbitMQ的交换机,队列等信息的配置类,以前都是在生产者和消费者里面配置交换机和队列的信息,但是用了SpringBoot之后,就可以使用配置类来完成这些操作,**让生产者只负责发消息,消费者只负责收消息**

>TtlQueueConfig.java

```java
package com.zzmr.springbootrabbitmq.config;

import org.springframework.amqp.core.*;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.HashMap;
import java.util.Map;

/**
 * TTL队列-存活时间的队列 配置文件类代码
 * TODO 两大交换机(X,Y)，三大队列(QA,QB,QD)
 */
@Configuration
public class TtlQueueConfig {

    //    普通交换机的名称
    public static final String X_EXCHANGE = "X";
    //    死信交换机的名称
    public static final String Y_DEAD_LETTER_EXCHANGE = "Y";
    //    普通队列的名称
    public static final String QUEUE_A = "QA";
    public static final String QUEUE_B = "QB";
    //    死信队列的名称
    public static final String DEAD_LETTER_QUEUE = "QD";

    //    声明xExchange
    @Bean("xExchange")
    public DirectExchange xExchange() {
        return new DirectExchange(X_EXCHANGE);
    }

    //    声明死信交换机
    @Bean("yExchange")
    public DirectExchange yExchange() {
        return new DirectExchange(Y_DEAD_LETTER_EXCHANGE);
    }

    //    声明普通队列 TTL 为10s
    @Bean("queueA")
    public Queue queueA() {
        Map<String, Object> arguments = new HashMap<>(3);
        // 设置死信交换机
        arguments.put("x-dead-letter-exchange", Y_DEAD_LETTER_EXCHANGE);
        // 设置死信Routing
        arguments.put("x-dead-letter-routing-key", "YD");
        // 设置TTL  单位是ms
        arguments.put("x-message-ttl", 10000);
        return QueueBuilder.durable(QUEUE_A).withArguments(arguments).build();
    }

    //    声明普通队列 TTL 为10s
    @Bean("queueB")
    public Queue queueB() {
        Map<String, Object> arguments = new HashMap<>(3);
        // 设置死信交换机
        arguments.put("x-dead-letter-exchange", Y_DEAD_LETTER_EXCHANGE);
        // 设置死信Routing
        arguments.put("x-dead-letter-routing-key", "YD");
        // 设置TTL  单位是ms
        arguments.put("x-message-ttl", 40000);
        return QueueBuilder.durable(QUEUE_B).withArguments(arguments).build();
    }

    // 死信队列
    @Bean("queueD")
    public Queue queueD() {
        return QueueBuilder.durable(DEAD_LETTER_QUEUE).build();
    }

    // TODO    5个部分已经写完，还差绑定关系：  X交换机跟QA和QB绑定，Y交换机和QD绑定
    @Bean
    public Binding queueABindingX(@Qualifier("queueA") Queue queueA,
                                  @Qualifier("xExchange") DirectExchange xExchange) {
        return BindingBuilder.bind(queueA).to(xExchange).with("XA");
    }

    @Bean
    public Binding queueBBindingX(@Qualifier("queueB") Queue queueB,
                                  @Qualifier("xExchange") DirectExchange xExchange) {
        return BindingBuilder.bind(queueB).to(xExchange).with("XB");
    }

    @Bean
    public Binding queueDBindingY(@Qualifier("queueD") Queue queueD,
                                  @Qualifier("yExchange") DirectExchange yExchange) {
        return BindingBuilder.bind(queueD).to(yExchange).with("YD");
    }
    
}
```

说实话这个slf4j也好久没用过了

#### 7.2.3 生产者与消费者

生产者
>SendMsgController.java

```java
package com.zzmr.springbootrabbitmq.controller;

import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.Date;

/**
 * 发送延迟消息   10s/40s
 */
@Slf4j
@RestController
@RequestMapping("/ttl")
public class SendMsgController {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    //    开始发消息
    @GetMapping("/sendMsg/{message}")
    public void sendMsg(@PathVariable("message") String message) {
        log.info("当前时间:{},发送一条信息给两个TTL队列:{}", new Date().toString(), message);

        rabbitTemplate.convertAndSend("X", "XA", "消息来自ttl为10S的队列" + message);
        rabbitTemplate.convertAndSend("X", "XB", "消息来自ttl为40S的队列" + message);
    }
}
```

消费者
>DeadLetterQueueConsumer.java

```java
package com.zzmr.springbootrabbitmq.consumer;

import com.rabbitmq.client.Channel;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.core.Message;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

import java.util.Date;

/**
 * 队列TTL  消费者
 */
@Component
@Slf4j
public class DeadLetterQueueConsumer {

    //    接收消息
    @RabbitListener(queues = "QD")
    public void receiveD(Message message, Channel channel) throws Exception {
        String msg = new String(message.getBody());
        log.info("当前事件:{},收到死信队列的消息:{}", new Date().toString(), msg);
    }

}
```

接收成功,且时间正确,一个经过10s,一个经过40s
![20230322171101](https://img01.zzmr.club/img/20230322171101.png)

第一条消息在10S后变成了死信消息,然后被消费者消费掉,第二条消息在40S之后变成了死信消息,然后被消费掉,这样一个延时队列就打造完成了

不过如果这样使用的话,岂不是**每增加一个新的时间需求,就要新增一个队列**,这里只有10s和40s,如果需要其他的时间,那就要再加一个队列,那样就不太现实了

### 7.3 延时队列优化

在这里新增了一个队列QC,绑定关系如下
![20230322172237](https://img01.zzmr.club/img/20230322172237.png)

QC不设置过期时间了,时间由生产者发消息时指定

然后在配置类中添加下列两个,是QC和QC与交换机X的绑定关系
```java
    //    声明普通队列 QC  此队列的过期时间让生产者自己设置
    @Bean("queueC")
    public Queue queueC() {
        Map<String, Object> arguments = new HashMap<>(3);
        // 设置死信交换机
        arguments.put("x-dead-letter-exchange", Y_DEAD_LETTER_EXCHANGE);
        // 设置死信RoutingKey
        arguments.put("x-dead-letter-routing-key", "YD");
        return QueueBuilder.durable(QUEUE_C).withArguments(arguments).build();
    }

    @Bean
    public Binding queueCBindingX(@Qualifier("queueC") Queue queueC,
                                  @Qualifier("xExchange") DirectExchange xExchange) {
        return BindingBuilder.bind(queueC).to(xExchange).with("XC");
    }
```

发送消息这样改:
```java
    //    开始发消息 消息 TTL
    @GetMapping("/sendExpirationMsg/{message}/{ttlTime}")
    public void sendMsg(@PathVariable("message") String message, @PathVariable("ttlTime") String ttlTime) {
        log.info("当前时间:{},发送一条TTL为{}ms给队列QC,消息为:{}", new Date().toString(), ttlTime, message);
        rabbitTemplate.convertAndSend("X", "XC", message, msg -> {
            //  发送消息的时候的延迟时长
            msg.getMessageProperties().setExpiration(ttlTime);
            return msg;
        });
    }
```

好家伙,这么一改,甚至发出就接受不到了,原来是绑定的交换机绑错了,改好了

出现如下结果:
![20230322175852](https://img01.zzmr.club/img/20230322175852.png)

会发现,后面两条消息明明不是同一ttl,但是事实上却是同一时间接收到的,**这是基于死信存在的问题**

如果在消息属性上设置TTL,消息可能并不会按时死亡,因为**RabbitMQ只会检查第一个消息是否过期**,如果过期则丢到死信队列,**如果第一个消息的延时很长很长,而第二个消息的延时很短,第二个消息并不会优先得到执行**

所以有使用**插件实现延迟队列**

### 7.4 RabbitMQ插件实现延迟队列

那就要先安装插件,我的RabbitMQ是用docker安装的,那这里安装插件应该也有点区别

#### 7.4.1 插件安装

[安装教程](https://blog.csdn.net/q66562636/article/details/125277473)

1. 首先下载这个插件
2. 将插件拷贝到容器中:`docker cp /root/rabbitmq_delayed_message_exchange-3.9.0.ez c9d7d7f17bcd:/plugins`
3. 在容器中的plugins目录中开启插件:`rabbitmq-plugins enable rabbitmq_delayed_message_exchange`

安装成功
![20230322182106](https://img01.zzmr.club/img/20230322182106.png)

重启容器`docker restart 容器ID`

在管理页面可以看出多了一个type类型为x-delayed-message,表示安装成功
![20230322182356](https://img01.zzmr.club/img/20230322182356.png)

之前的延时消息是基于死信队列的情况下,而基于插件的,是在交换机上的

![20230322183334](https://img01.zzmr.club/img/20230322183334.png)

#### 7.4.2 插件实现延迟队列原理图

新增了一个队列delayed.queue,一个自定义交换机delayed.exchange,绑定关系如下

![20230323094830](https://img01.zzmr.club/img/20230323094830.png)
~~*type应该是x-delayed-message*~~

没有问题,虽然交换机是延迟交换机,但是它本来时要求有一个类型的,所以是direct类型

#### 7.4.3 配置文件类代码

在我们自定义的交换机中,这是一种新的交换类型,该类型消息支持延迟投递机制,消息传递后并不会立即投递到目标队列中,而是存储在mnesia(一个分布式数据系统)表中,当达到投递时间时,才投递到目标队列中

**DelayedQueueConfig.java**

```java
package com.zzmr.springbootrabbitmq.config;

import org.springframework.amqp.core.Binding;
import org.springframework.amqp.core.BindingBuilder;
import org.springframework.amqp.core.CustomExchange;
import org.springframework.amqp.core.Queue;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.HashMap;
import java.util.Map;

@Configuration
public class DelayedQueueConfig {
    //    交换机
    public static final String DELAYED_EXCHANGE_NAME = "delayed.exchange";
    //    队列
    public static final String DELAYED_QUEUE_NAME = "delayed.queue";
    //    routingKey
    public static final String DELAYED_ROUTING_KEY = "delayed.routingkey";

    @Bean
    public Queue delayedQueue() {
        return new Queue(DELAYED_QUEUE_NAME);
    }

    //    声明交换机-自定义交换机
    @Bean
    public CustomExchange delayedExchange() {

        Map<String, Object> arguments = new HashMap<>();
        arguments.put("x-delayed-type", "direct");
        /**
         *  交换机名称
         *  交换机类型
         *  是否需要持久化
         *  是否自动删除
         *  其他参数
         */
        return new CustomExchange(DELAYED_EXCHANGE_NAME, "x-delayed-message",
                true, false, arguments);
    }

    // 绑定关系
    @Bean
    public Binding delayedQueueBindingDelayedExchange(@Qualifier("delayedQueue") Queue delayedQueue,
                                                      @Qualifier("delayedExchange") CustomExchange delayedExchange) {
        return BindingBuilder.bind(delayedQueue).to(delayedExchange).with(DELAYED_ROUTING_KEY).noargs();
    }
}
```

#### 7.4.4 生产者

```java
    //    发送基于插件的消息及延迟时间
    @GetMapping("/sendDelayMsg/{message}/{delayTime}")
    public void sendMsg(@PathVariable("message") String message,
                        @PathVariable("delayTime") Integer delayTime) {
        log.info("当前时间:{},发送一条延迟消息为{}ms给延迟队列delayed.queue,消息为:{}", new Date().toString(), delayTime, message);
        rabbitTemplate.convertAndSend("delayed.exchange", "delayed.routingkey", message, msg -> {
            //    发送消息的延迟时间
            msg.getMessageProperties().setDelay(delayTime);
            return msg;
        });
    }
```

这里使用了setDelay()

#### 7.4.5 消费者

**DelayQueueConsumer.java**

```java
package com.zzmr.springbootrabbitmq.consumer;

import com.zzmr.springbootrabbitmq.config.DelayedQueueConfig;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.core.Message;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

import java.util.Date;

/**
 * 消费者 基于插件的延迟消息
 */
@Component
@Slf4j
public class DelayQueueConsumer {
    //    监听消息
    @RabbitListener(queues = DelayedQueueConfig.DELAYED_QUEUE_NAME)
    public void receiveDelayQueue(Message message) {
        String msg = new String(message.getBody());
        log.info("当前时间: {},收到延迟队列的消息: {}", new Date().toString(), msg);
    }
}
```

#### 7.4.6 测试

先发一条20s的消息,再发一条2s的消息,此时2s的消息先到,20s的消息后到,且时间无误
![20230323102601](https://img01.zzmr.club/img/20230323102601.png)

延时队列在需要延时处理的场景下非常有用,使用RabbitMQ来实现延时队列可以很好的利用RabbitMQ的特性,如消息可靠发送,消息可靠投递,死信队列来保障消息至少被消费一次以及未被正确处理的消息不会被丢弃,另外,通过RabbitMQ集群的特性,可以很好的解决单点故障问题,不会因为单个节点挂掉导致延迟队列不可用或者消息丢失

## 8 发布确认高级

在生产环境中由于一些不明原因,**导致rabbitmq重启**,在rabbitmq重启期间生产者消息投递失败,导致消息丢失,需要手动处理和恢复.于是,我们开始思考,如何才能进行RabbitMQ的消息可靠投递呢?特别是在这样比较极端的情况下,**RabbitMQ集群不可用的时候**,无法投递的消息该如何处理呢

### 8.1 基于SpringBoot的发布确认

#### 8.1.1 确认机制

![20230324165744](https://img01.zzmr.club/img/20230324165744.png)

#### 8.1.2 代码架构图

![20230324165943](https://img01.zzmr.club/img/20230324165943.png)

#### 8.1.3 配置文件

在配置文件中添加(当然是rabbitmq下面的一层)
```yml
    publisher-confirm-type: correlated
```
1. NONE-禁用发布确认模式,是默认值
2. CORRELATED-发布消息成功到交换器后会触发回调方法
3. SIMPLE(同步确认)
    1. 和CORRELATED值一样会触发回调方法
    2. 在发布消息成功后使用rabbitTemplate调用waitForConfirms或waitForConfirmsOrDie方法等待broker节点返回发送结果,根据返回结果来判断下一步的逻辑,要注意的点是waitForConfirmOrDie方法如果返回false则会关闭channel,则接下连无法发送消息到broker


#### 8.1.4 配置类

还是一个交换机,一个队列,一个routingkey

**ConfirmConfig.java**

```java
package com.zzmr.springbootrabbitmq.config;

import org.springframework.amqp.core.*;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * 配置发布确认的高级内容
 */
@Configuration
public class ConfirmConfig {
    //    交换机
    public static final String CONFIRM_EXCHANGE_NAME = "confirm_exchange";
    //    队列
    public static final String CONFIRM_QUEUE_NAME = "confirm_queue";
    //    routingKey
    public static final String CONFIRM_ROUTING_KEY = "key1";

    //    声明交换机
    @Bean
    public DirectExchange confirmExchange() {
        return new DirectExchange(CONFIRM_EXCHANGE_NAME);
    }

    //    声明队列
    @Bean
    public Queue confirmQueue() {
        //return new Queue(CONFIRM_QUEUE_NAME);
        return QueueBuilder.durable(CONFIRM_QUEUE_NAME).build();
    }

    @Bean
    public Binding queueBindingExchange(@Qualifier("confirmExchange") DirectExchange confirmExchange,
                                        @Qualifier("confirmQueue") Queue confirmQueue) {
        return BindingBuilder.bind(confirmQueue).to(confirmExchange).with(CONFIRM_ROUTING_KEY);
    }
}
```

#### 8.1.5 生产者

```java
package com.zzmr.springbootrabbitmq.controller;

import com.zzmr.springbootrabbitmq.config.ConfirmConfig;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

//    发送消息-测试发布确认

@Slf4j
@RestController
@RequestMapping("/confirm")
public class ProducerController {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    // 发送一条正常的消息
    @GetMapping("/sendMessage/{message}")
    public void sendMessage(@PathVariable("message") String message) {
        rabbitTemplate.convertAndSend(ConfirmConfig.CONFIRM_EXCHANGE_NAME,
                ConfirmConfig.CONFIRM_ROUTING_KEY, message);
        log.info("发送消息: {}", message);
    }

}
```

#### 8.1.6 消费者

```java
package com.zzmr.springbootrabbitmq.consumer;

import com.zzmr.springbootrabbitmq.config.ConfirmConfig;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.core.Message;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

/**
 * 接收消息
 */
@Slf4j
@Component
public class Consumer {
    @RabbitListener(queues = ConfirmConfig.CONFIRM_QUEUE_NAME)
    public void receiveConfirmMessage(Message message) {
        String msg = new String(message.getBody());
        log.info("接收到的队列confirm.queue消息: {}", msg);
    }
}
```

此时正常访问是没有问题的

![20230325131524](https://img01.zzmr.club/img/20230325131524.png)

#### 8.1.7 回调接口

假设交换机出了问题,消息发不到交换机

这时就可以利用**回调接口**


```java
package com.zzmr.springbootrabbitmq.config;

import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.rabbit.connection.CorrelationData;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import javax.annotation.PostConstruct;

@Component
@Slf4j
public class MyCallBack implements RabbitTemplate.ConfirmCallback {


    @Autowired
    private RabbitTemplate rabbitTemplate;

    // 注入
    // 在bean创建和属性赋值完成后进行初始化方法
    @PostConstruct
    public void init(){
        rabbitTemplate.setConfirmCallback(this);
    }

    /**
     * 换机确认回调方法
     * 1. 发送消息 交换机接收到了 回调
     * 1.1 correlationData 保存回调消息的ID及相关信息
     * 1.2 ack 交换机收到消息 true
     * 1.3 cause 失败的原因  null
     * <p>
     * 2. 发消息 交换机接收失败了 回调
     * 2.1 correlationData 保存回调消息的ID及相关信息
     * 2.2 ack 交换机收到消息 false
     * 2.3 cause 失败的原因
     */
    @Override
    public void confirm(CorrelationData correlationData, boolean ack, String cause) {
        String id = correlationData != null ? correlationData.getId() : "";
        if (ack) {
            log.info("交换机已经收到id为:{}的消息", id);
        } else {
            log.info("交换机未收到id为:{},原因为:{}", id, cause);
        }
    }
}
```

**更改这个交换机的名字,让消息发送不到交换机,来测试回调接口**

可以看出,是能获取到失败的原因
![20230325143324](https://img01.zzmr.club/img/20230325143324.png)

**更改routingkey,模拟队列接受不到消息**

![20230325143623](https://img01.zzmr.club/img/20230325143623.png)

可见,并没有报错,交换机接收到消息了,但是队列出现问题,**消费者并没有接收到消息-未收到失败的回调**

### 8.2 回退消息

#### 8.2.1 Mandatory参数

**在仅开启了生产者确认机制的情况下,交换机接收到消息后,会直接给消息生产者发送确认消息,如果发现该消息不可路由,那么消息会被直接丢弃,此时生产者还不知道消息被丢弃这个事件的**.

**通过设置mandatory参数可以在当消息传递过程中不可达目的地时将消息返回给生产者**

跟上一个配置的位置一样
```yml
    publisher-returns: true
```

将MyCallBack.java修改为:

就是新增一个消息路由失败的通知

```java
package com.zzmr.springbootrabbitmq.config;

import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.core.Message;
import org.springframework.amqp.rabbit.connection.CorrelationData;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import javax.annotation.PostConstruct;

@Component
@Slf4j
public class MyCallBack implements RabbitTemplate.ConfirmCallback, RabbitTemplate.ReturnCallback {


    @Autowired
    private RabbitTemplate rabbitTemplate;

    // 注入
    // 在bean创建和属性赋值完成后进行初始化方法
    @PostConstruct
    public void init() {
        // 将这个类注入到实现类的方法上
        rabbitTemplate.setConfirmCallback(this);
        rabbitTemplate.setReturnCallback(this);
    }

    /**
     * 换机确认回调方法
     * 1. 发送消息 交换机接收到了 回调
     * 1.1 correlationData 保存回调消息的ID及相关信息
     * 1.2 ack 交换机收到消息 true
     * 1.3 cause 失败的原因  null
     * <p>
     * 2. 发消息 交换机接收失败了 回调
     * 2.1 correlationData 保存回调消息的ID及相关信息
     * 2.2 ack 交换机收到消息 false
     * 2.3 cause 失败的原因
     */
    @Override
    public void confirm(CorrelationData correlationData, boolean ack, String cause) {
        String id = correlationData != null ? correlationData.getId() : "";
        if (ack) {
            log.info("交换机已经收到id为:{}的消息", id);
        } else {
            log.info("交换机未收到id为:{},原因为:{}", id, cause);
        }
    }

    /**
     * 消息路由失败时的通知
     * 只有不可达目的地的时候才进行回退
     * 这个好像已经过时了
     *
     * @param message    the returned message.
     * @param replyCode  the reply code.
     * @param replyText  the reply text.
     * @param exchange   the exchange.
     * @param routingKey the routing key.
     */
    @Override
    public void returnedMessage(Message message, int replyCode, String replyText, String exchange, String routingKey) {
        log.error("消息{},被交换机{}退回,退回原因:{},routingKey为:{}",
                new String(message.getBody()), exchange, replyText, routingKey);
    }

}
```

![20230325145632](https://img01.zzmr.club/img/20230325145632.png)

>**但是吧,他用的这个`RabbitTemplate.ReturnCallback`已经过时了,现在是`RabbitTemplate.ReturnsCallback`,我改成这个试试**

修改成功:
![20230325150409](https://img01.zzmr.club/img/20230325150409.png)

```java
package com.zzmr.springbootrabbitmq.config;

import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.core.ReturnedMessage;
import org.springframework.amqp.rabbit.connection.CorrelationData;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import javax.annotation.PostConstruct;

@Component
@Slf4j
public class MyCallBack implements RabbitTemplate.ConfirmCallback, RabbitTemplate.ReturnsCallback {


    @Autowired
    private RabbitTemplate rabbitTemplate;

    // 注入
    // 在bean创建和属性赋值完成后进行初始化方法
    @PostConstruct
    public void init() {
        // 将这个类注入到实现类的方法上
        rabbitTemplate.setConfirmCallback(this);
        // rabbitTemplate.setReturnCallback(this);
        rabbitTemplate.setReturnsCallback(this);
    }

    /**
     * 换机确认回调方法
     * 1. 发送消息 交换机接收到了 回调
     * 1.1 correlationData 保存回调消息的ID及相关信息
     * 1.2 ack 交换机收到消息 true
     * 1.3 cause 失败的原因  null
     * <p>
     * 2. 发消息 交换机接收失败了 回调
     * 2.1 correlationData 保存回调消息的ID及相关信息
     * 2.2 ack 交换机收到消息 false
     * 2.3 cause 失败的原因
     */
    @Override
    public void confirm(CorrelationData correlationData, boolean ack, String cause) {
        String id = correlationData != null ? correlationData.getId() : "";
        if (ack) {
            log.info("交换机已经收到id为:{}的消息", id);
        } else {
            log.info("交换机未收到id为:{},原因为:{}", id, cause);
        }
    }

    /**
     * 消息路由失败时的通知
     * 只有不可达目的地的时候才进行回退
     * 这个好像已经过时了
     *
     * @param message    the returned message.
     * @param replyCode  the reply code.
     * @param replyText  the reply text.
     * @param exchange   the exchange.
     * @param routingKey the routing key.
     */
    //@Override
    //public void returnedMessage(Message message, int replyCode, String replyText, String exchange, String routingKey) {
    //    log.error("消息{},被交换机{}退回,退回原因:{},routingKey为:{}",
    //            new String(message.getBody()), exchange, replyText, routingKey);
    //}
    @Override
    public void returnedMessage(ReturnedMessage returned) {
        log.error("消息:{},被交换机:{}退回,退回原因:{},routingKey为:{}",
                new String(returned.getMessage().getBody()), returned.getExchange(), returned.getReplyText(), returned.getRoutingKey());
    }

}
```

**没啥问题,就是把原来的散装属性封装成一个returned对象,然后在这个对象中用get方法就行了,记得上面注入的时候使用的set方法也要改**

### 8.3 备份交换机

有了mandatory参数和回退消息,我们获得了对无法投递消息的感知能力,有机会在生产者的消息无法被投递时发现并处理.但有时候,我们并不知道该如何处理这些无法路由的消息,最多打个日志,然后触发报警,再手动处理,但是很麻烦,且设置mandatory参数会增加生产者的复杂性,需要添加处理这些被退回的消息逻辑.

前面在设置死信队列的文章中,我们提到,可以为队列设置死信队列来保存消息,可是这些不可路由的消息根本没有机会进入到队列,在RabbitMQ中,有一种备份交换机的机制存在,可以很好的应对这个问题

**什么是备份交换机?备份交换机可以理解为RabbitMQ中交换机的备胎,当我们为某一个交换机声明一个对应的备份交换机时,就是为它创建一个备胎,当交换机接收到一条不可路由消息时,将会把这条消息转发到备份交换机中,由备份交换机来进行转发和处理,通常备份交换机的类型为Fanout,这样就能把所有消息都投递到与其绑定的队列中,然后我们在备份交换机下绑定一个队列,这样所有哪些原交换机无法被路由的消息,就会都进入这个队列.当然,我们还可以建立一个报警队列,用独立的消费者来进行检测和报警**

#### 8.3.1 代码架构图

![20230325152447](https://img01.zzmr.club/img/20230325152447.png)


#### 8.3.2 修改配置类

要添加一个备份交换机和两个队列(一个负责备份,一个负责警告)

最后在修改确认交换机的转发`alternate-exchange`

```java
package com.zzmr.springbootrabbitmq.config;

import org.springframework.amqp.core.*;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * 配置发布确认的高级内容
 */
@Configuration
public class ConfirmConfig {
    //    交换机
    public static final String CONFIRM_EXCHANGE_NAME = "confirm_exchange";

    //    备份交换机
    public static final String BACKUP_EXCHANGE_NAME = "back_exchange";

    //    队列
    public static final String CONFIRM_QUEUE_NAME = "confirm_queue";

    //    备份队列
    public static final String BACKUP_QUEUE_NAME = "backup_queue";
    //    报警队列
    public static final String WARNING_QUEUE_NAME = "warning_queue";

    //    routingKey
    public static final String CONFIRM_ROUTING_KEY = "key1";

    //    声明交换机
    @Bean
    public DirectExchange confirmExchange() {
        //return new DirectExchange(CONFIRM_EXCHANGE_NAME);
        //    还没写确认交换机和备份交换机之间的转发关系
        return ExchangeBuilder.directExchange(CONFIRM_EXCHANGE_NAME).durable(true)
                .withArgument("alternate-exchange", BACKUP_EXCHANGE_NAME).build();
    }

    //    声明备份交换机
    @Bean
    public FanoutExchange backupExchange() {
        return new FanoutExchange(BACKUP_EXCHANGE_NAME);
    }

    //    声明队列
    @Bean
    public Queue confirmQueue() {
        //return new Queue(CONFIRM_QUEUE_NAME);
        return QueueBuilder.durable(CONFIRM_QUEUE_NAME).build();
    }

    //    声明备份队列
    @Bean
    public Queue backupQueue() {
        return QueueBuilder.durable(BACKUP_QUEUE_NAME).build();
    }

    //    声明报警队列
    @Bean
    public Queue warningQueue() {
        return QueueBuilder.durable(WARNING_QUEUE_NAME).build();
    }

    // 绑定备份交换机和两个队列
    @Bean
    public Binding backupQueueBindingExchange(@Qualifier("backupQueue") Queue backQueue,
                                              @Qualifier("backupExchange") FanoutExchange backupExchange) {
        return BindingBuilder.bind(backQueue).to(backupExchange);       // 因为是扇出交换机,所以不需要routingKey
    }

    @Bean
    public Binding warningQueueBindingExchange(@Qualifier("warningQueue") Queue warningQueue,
                                               @Qualifier("backupExchange") FanoutExchange backupExchange) {
        return BindingBuilder.bind(warningQueue).to(backupExchange);       // 因为是扇出交换机,所以不需要routingKey
    }


    // 普通绑定
    @Bean
    public Binding queueBindingExchange(@Qualifier("confirmExchange") DirectExchange confirmExchange,
                                        @Qualifier("confirmQueue") Queue confirmQueue) {
        return BindingBuilder.bind(confirmQueue).to(confirmExchange).with(CONFIRM_ROUTING_KEY);
    }


}
```

#### 8.3.3 报警消费者

```java
package com.zzmr.springbootrabbitmq.consumer;

import com.zzmr.springbootrabbitmq.config.ConfirmConfig;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.core.Message;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

/**
 * 报警消费者
 */

@Component
@Slf4j
public class WarningConsumer {

    @RabbitListener(queues = ConfirmConfig.WARNING_QUEUE_NAME)
    public void receiveWarningMsg(Message message) {
        String msg = new String(message.getBody());
        log.error("报警发现不可路由消息:{}", msg);
    }
}
```

测试,还是通过错误的routingKey来模拟这个效果

![20230325155635](https://img01.zzmr.club/img/20230325155635.png)

证明不可路由的消息成功转发至备份交换机且通过报警队列发送给报警消费者

但是有没有发现之前配置过的mandatory没有生效?就是消息被回退了那个.

**mandatory参数与备份交换机可以一起使用,如果两者同时开启,备份交换机优先级更高**

## 9 RabbitMQ其他知识点

### 9.1 幂等性

>概念

用户对于同一操作发起的一次请求或者多次请求的结果是一致的,不会因为多次点击而产生了副作用.

比如支付过程,用户购买商品后支付,支付扣款成功,但是返回结果的时候网络异常,此时钱已经扣过了,用户再次点击按钮,此时会进行第二次扣款,返回结果成功,用户查询余额发现多扣钱了,流水记录也变成了两条.

此前的单应用系统中,我们只需要把数据操作放入事务中即可,发生错误立即回滚,但是再响应客户端的时候也有可能出现网络中断或者异常

---

>**消息重复消费**

消费者在消费MQ中的消息时,MQ已把消息发送给消费者,消费者在给MQ返回ack时网络中断,故MQ未收到确认信息,该条消息会重新发送给其他的消费者,或者在网络重连后再次发送给该消费者,但实际上该消费者已成功消费了该条消息,造成消费者消费了重复的消息.

>**解决思路**

MQ消费者的幂等性的解决一般使用**全局ID**或者写一个**唯一标识**,比如时间戳或者UUID或者订单消费者消费MQ中的消息也可利用MQ的该id来判断,或者可按自己的规则生成一个全局唯一id,每次消费消息时用该id先判断该消息是否已被消费过.

>**消费端的幂等性保障**

在海量订单生成的业务高峰期,生产端有可能就会重复发送了消息,这时候消费端就要实现幂等性,这就意味着我们的消息永远不会被消费多次,即使我们收到了一样的消息.业界主流的幂等性有两种操作
- 唯一ID+指纹码机制,利用数据库主键去重
- 利用redis的原子性去实现

>**唯一ID+指纹码机制**

指纹码,我们那的一些规则或者时间戳加别的服务给到的唯一信息码,**它并不一定是我们系统生成的**,基本都是由我们的业务规则拼接而来,但是一定要保证唯一性,然后就利用查询语句进行判断这个id是否存在数据库中,优势就是实现简单,就是一个拼接,然后查询判断是否重复,劣势就是在高并发时,如果单个数据库就会有写入性能瓶颈,当然也可以采用分库分表提升性能,但是也不是我们最推荐的方式.

>**Redis原子性**

利用Redis执行setnx命令,天然具有幂等性,从而实现不重复消费.(最终方案)

### 9.2 优先级队列

#### 9.2.1 使用场景

在我们系统中有一个**订单催付**的场景,而Redis只能用List做一个简简单单的消息队列,并不能实现一个优先级的场景,所以订单量大了后采用RabbitMQ进行改造和优化,如果发现是大客户的订单给一个相对比较高的优先级,否则是默认优先级

#### 9.2.2 添加优先级队列

要让队列实现优先级需要做的事情
1. 队列需要设置为优先级队列
2. 消息需要设置消息的优先级
3. 消费者需要等待消息已经发送到队列中才去消费,这样才有机会对消息进行排序

老师用的还是之前的那个老项目,我打算用springBoot

>**生产者**

```java
package com.zzmr.springbootrabbitmq.controller;

import com.zzmr.springbootrabbitmq.config.PriorityConfig;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class PriorityController {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    @GetMapping("/test")
    public void testPriorityQueue() {
        for (int i = 1; i <= 10; i++) {
            String message = "info" + i;
            if (i == 5) {
                System.out.println("i为5");
                rabbitTemplate.convertAndSend(PriorityConfig.EXCHANGE_NAME,
                        PriorityConfig.ROUTING_KEY, message, message1 -> {
                            message1.getMessageProperties().setPriority(5);
                            return message1;
                        });
            } else {
                rabbitTemplate.convertAndSend(PriorityConfig.EXCHANGE_NAME,
                        PriorityConfig.ROUTING_KEY, message);
            }
        }
    }

}
```

>**消费者**

```java
    @RabbitListener(queues = PriorityConfig.PRIORITY_QUEUE_NAME)
    public void receivePriority(Message message){
        String msg = new String(message.getBody());
        log.info("接收到消息:{}",msg);
    }
```

>**配置类**

```java
package com.zzmr.springbootrabbitmq.config;

import org.springframework.amqp.core.*;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class PriorityConfig {

    //  优先级队列
    public static final String PRIORITY_QUEUE_NAME = "priority_queue";
    //  交换机
    public static final String EXCHANGE_NAME = "exchange";

    //  routingKey
    public static final String ROUTING_KEY = "r1";

    //    声明交换机
    @Bean
    public DirectExchange exchange() {
        return new DirectExchange(EXCHANGE_NAME);
    }

    @Bean
    public Queue priorityQueue() {
        // 官方允许是0-255之间，此处设置10，允许优先级范围0-10，不要设置过大，浪费CPU与内存
        return QueueBuilder.durable(PRIORITY_QUEUE_NAME)
                .withArgument("x-max-priority", 10).build();
    }

    @Bean
    public Binding priorityQueueBindExchange(@Qualifier("priorityQueue") Queue queue, @Qualifier("exchange") DirectExchange exchange) {
        return BindingBuilder.bind(queue).to(exchange).with(ROUTING_KEY);
    }

}
```

测试:

![20230326111459](https://img01.zzmr.club/img/20230326111459.png)

### 9.3 惰性队列

从3.6.0开始引入了惰性队列的概念.惰性队列会尽可能的将消息存入磁盘中,而在消费者消费到相应的消息时才会被加载到内存中,它的一个重要的设计目标是能够支持更长的队列,即支持更多的消息存储.当消费者由于各种各样的原因而致使长时间内不能消费消息造成堆积,惰性队列就很有必要了

默认情况下,当生产者将消息发送到RabiitMQ的时候,队列中的消息会尽可能地存储在内存中,这样可以更加快速的将消息发送给消费者.即使时持久化消息,在被写入磁盘的同时也会在内存中驻留一份备份.当RabbitMQ需要释放内存的时候,会将内存中的消息换页至磁盘中,这个操作会耗费较长时间,也会阻塞队列的操作,进而无法接收新的消息.虽然RabbitMQ的开发者们一直在升级相关的算法,但是效果是中不太理想,尤其是在消息量特别大的时候

---

>**两种模式**

队列具备两种模式,default和lazy.默认的为default模式,在3.6.0之前的版本无需做任务变更.lazy模式即为惰性队列的模式,可以通过调用channel.queueDeclare方法的时候在参数中设置,也可以通过Policy的方式设置,如果一个队列同时使用这两种方式设置的话,那么Policy的方法具备更高的优先级.如果要通过声明的方式改变已有的队列模式的话,**那么只能先删除队列,然后再重新声明一个新的**

在队列声明的时候可以通过`x-queue-mode`参数来设置队列的模式,取值为`default`和`lazy`

```java
Map<String, Object> args = new HashMap<String, Object>();
args.put("x-queue-mode","lazy");
channel.queueDeclare("myqueue",false,false,false,args);
```

---

![20230326113355](https://img01.zzmr.club/img/20230326113355.png)

## 10 RabbitMQ集群

所以用Docker怎么搭建RabbitMQ集群?

算了,这个以后再说吧,记得之前学Docker的时候Redis集群也没学.

---

2023年3月26日 11点53分
就到这了

