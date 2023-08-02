2023年3月15日 20点02分

# SpringCloud

开始SpringCloud的学习

分布式微服务架构?
所以说学这个一定要懂得SpringBoot

技术要求:
java8+maven+git,github+**Nginx+RabbitMQ**+SpringBoot2.0

完了,两个没学

那算了,先把Nginx和RabbitMQ学完再来了

bye

2023年3月26日 11点58分

花了11天,才把RabbitMQ看完,后面的Nginx用到的时候再现学吧

绝了,阳哥建议关弹幕学习哈哈哈哈,弹幕还是太杂了

>**不在能知,乃在能行**

## 1 微服务架构与SpringCloud

>微服务架构的概念

微服务架构是一种架构模式,它提倡将单一应用程序划分成一组小的服务,服务之间互相协调,互相配合,为用户提供最终价值.每个服务运行在独立的进程中,服务与服务之间采用轻量级的通信机制互相协作(通常是HTTP协议的RESTful API),每个服务都围绕着具体业务进行构建,并且能够被独立的部署到生成环境,类生产环境等.另外,应当尽量避免统一的,集中式的服务管理机制,对具体一个服务而言,应根据业务上下文,选择合适的语言,工具对其进行构建.

>SpringCloud是什么

SpringCloud=分布式微服务架构的一站式解决方案,是多种微服务架构落地技术的集合体,俗称微服务全家桶

SpringCLoud的技术栈:
![20230731120315](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230731120315.png)
![20230731120510](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230731120510.png)

## 2 从2.2.x和H版开始说起

>上篇和下篇
- 上篇:SpringBoot2.X版和SpringCloud H版
- 下篇:SpringCloud Alibaba

**SpringCloud和SpringBoot之间的依赖关系**

![20230731122342](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230731122342.png)

*所以我们的H要配上2.2.x, 2.3.x*

**或者可以通过一个接口来查询这些信息**:[查询链接](https://start.spring.io/actuator/info)
但是现在看,只有最新版的信息了:
```json
        "spring-cloud": {
            "2021.0.8": "Spring Boot >=2.6.0 and <3.0.0",
            "2022.0.4": "Spring Boot >=3.0.0 and <3.2.0-M1"
        },
```

老师给的版本:
![20230731122852](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230731122852.png)

>为什么要这么选版本?不能直接选最新版?
1. 只用boot,直接用最新
2. 同时用boot和cloud,需要照顾cloud,由cloud决定boot版本

## 3 关于Cloud各种组件的停更/升级/替换

*技术好多,各个部分都是技术技术技术*

![20230731125207](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230731125207.png)

## 4 微服务架构编码构建

### 新建项目

建父工程,这老师好正经啊
![20230731130457](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230731130457.png)

父工程的pom文件:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.zzmr.springcloud</groupId>
    <artifactId>cloud2020</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <!-- 统一管理jar包版本 -->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <junit.version>4.12</junit.version>
        <log4j.version>1.2.17</log4j.version>
        <lombok.version>1.16.18</lombok.version>
        <mysql.version>8.0.26</mysql.version>
        <druid.version>1.1.16</druid.version>
        <mybatis.spring.boot.version>1.3.0</mybatis.spring.boot.version>
    </properties>

    <!-- 子模块继承之后，提供作用：锁定版本+子modlue不用写groupId和version  -->
    <dependencyManagement>
        <dependencies>
            <!--spring boot 2.2.2-->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>2.2.2.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--spring cloud Hoxton.SR1-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--spring cloud alibaba 2.1.0.RELEASE-->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>2.1.0.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql.version}</version>
            </dependency>
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid</artifactId>
                <version>${druid.version}</version>
            </dependency>
            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>${mybatis.spring.boot.version}</version>
            </dependency>
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>${junit.version}</version>
            </dependency>
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <version>${log4j.version}</version>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <version>${lombok.version}</version>
                <optional>true</optional>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <fork>true</fork>
                    <addResources>true</addResources>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

Maven中的DependencyManagement和Dependencies的区别

**通常会在一个组织或者项目的最顶层的父POM中看到dependecyManagement元素**
使用pom.xml中的dependencyManagement元素能让所有在子项目中引用的依赖不用显示的列出版本号,Maven会沿着父子层次向上走,直到找到一个拥有dependencyManagement元素的项目,然后它就会使用这个dependencyManagement元素中指定的版本号

**但是dependencyManagement里只是声明依赖,并不实现引入,因此子项目需要显示的声明需要用的依赖**
**如果不在子项目中声明依赖,是不会从父项目中继承下来的,只有在子项目中写了该依赖项,并且没有指定具体的版本,才会使用这里的依赖**

### 建立子模块

1. 建立Module
2. 改Pom
3. 写YML
4. 主启动
5. 业务类

**cloud-provider-payment8001**的pom:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cloud2020</artifactId>
        <groupId>com.zzmr.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-provider-payment8001</artifactId>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>
        <!--mysql-connector-java-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <!--jdbc-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
```

---

正式写业务逻辑了,老师也是给了步骤:
![20230731180735](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230731180735.png)

...

还是那些操作,并没有什么复杂的,但是,说实话,已经忘了好多东西了.

*热部署我就不用了,电脑带不动*

**cloud-consumer-order80**,消费者下单的模块,此模块只有controller,调用payment8001模块的内容
![20230731214303](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230731214303.png)

但是如何调用呢?

要用到`RestTemplate`,说实话没听说过.

就是可以一个controller调用另一个controller?

一个配置类:
```java
package com.zzmr.springcloud.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

/**
 * @author zzmr
 * @create 2023-07-31 21:48
 */
@Configuration
public class ApplicationContextConfig {

    @Bean
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }

}
```

然后就是controller了:
```java
package com.zzmr.springcloud.controller;

import com.zzmr.springcloud.entities.CommonResult;
import com.zzmr.springcloud.entities.Payment;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

/**
 * @author zzmr
 * @create 2023-07-31 21:41
 */
@RestController
@Slf4j
public class OrderController {

    public static final String PAYMENT_URL = "http://localhost:8001";

    @Autowired
    private RestTemplate restTemplate;

    @GetMapping("/consumer/payment/create")
    public CommonResult<Payment> create(Payment payment) {
        return restTemplate.postForObject(PAYMENT_URL + "/payment/create", payment, CommonResult.class);
    }

    @GetMapping("/consumer/payment/get/{id}")
    public CommonResult<Payment> getPayment(@PathVariable("id") Long id) {
        return restTemplate.getForObject(PAYMENT_URL + "/payment/get/" + id, CommonResult.class);
    }

}
```

这样就可以调用到了?

还真的可以了:
![20230731220019](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230731220019.png)

也就是说,当调用OrderController中的getPayment时,该Controller会去调用payment中的getPaymentById,这样就实现了服务调用?

而这个`xxxForObject`的参数分别是
1. URL(目标请求路径)
2. 请求参数(get请求就没有这个了)
3. 请求返回结果类型

但是,那个create请求,也能过,是为什么呢,根本没有往请求体里放东西啊:
![20230731220644](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230731220644.png)

所以是自动将路径中的变量封装到请求体中了

### 工程重构

系统中有重复部分,要进行重构

新建一个模块:`cloud-api-commons`,引入依赖:
```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
            <version>5.1.0</version>
        </dependency>
    </dependencies>
```

将entities提取出来,放到该模块下,然后重新安装(clean,install)
最后在80和8001两个模块的Pom文件中引入该模块:
```xml
        <dependency>
            <groupId>com.zzmr.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
```

`gav`这三个就行了

*奶奶滴,为什么这个edge看b站视频,这么占用CPU,就开一个标签,风扇就狂转,而chrome没事*

## 5 Eureka服务注册与发现

从现在就开始将各种springcloud的各种技术加入到这几个模块中

`Eureka`年`[ju'ri:ke]`

>什么是服务治理
Spring Cloud封装了Netflix公司开发的Eureka模块来实现服务治理
在传统的rpc远程调用框架中,管理每个服务与服务之间依赖关系比较复杂,管理比较复杂,所以需要使用服务治理,管理服务与服务器之间的关系,可以实现服务调用,负载均衡,容错等,实现服务发现与注册

---

什么是服务注册与发现
![20230801113440](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230801113440.png)
**系统中每个微服务与Eureka Server服务注册中心维持心跳连接,来让Eureka Server来监控系统中各个微服务是否正常运行**
![20230801113651](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230801113651.png)

**Eureka包含两个组件:Eureka Server和Eureka Client**
1. Eureka Server提供服务注册服务
    - 各个微服务节点通过配置启动后,会在EurekaServer中进行注册,这样EurekaServer中的服务注册表中将会存储所有可用服务节点的信息,服务节点的信息可以在界面中直观看到
2. Eureka Client通过注册中心进行访问
    - 是一个Java客户端,用于简化EurekaServer的交互,客户端同时也具备一个内置的,使用轮询(round-robin)负载算法的负载均衡器,在应用启动后,将会在EurekaServer发送心跳(默认周期为30秒),如果EurekaServer在多个心跳周期内没有接收到某个节点的心跳,EurekaServer将会从服务注册表中把这个服务节点移除(默认90秒)

---

### EurekaServer服务端安装

大概就是这个样子:
![20230801115023](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230801115023.png)

1. IDEA生成eurekaServer端服务注册中心,类似物业公司

pom.xml:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cloud2020</artifactId>
        <groupId>com.zzmr.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-eureka-server7001</artifactId>

    <dependencies>
        <!--eureka-server-->
        <!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-netflix-eureka-server -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
            <version>2.1.2.RELEASE</version>
        </dependency>
        <!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
        <dependency>
            <groupId>com.zzmr.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <!--boot web actuator-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--一般通用配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
        </dependency>
    </dependencies>

</project>
```

application.yml
```yml
server:
  port: 7001

eureka:
  instance:
    hostname: localhost # eureka服务端的实例名称
  client:
    # false表示不向注册中心注册自己
    register-with-eureka: false
    # false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    fetch-registry: false
    service-url:
      # 设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址
      defaultZone: http:/${eureka.instance.hostname}:${server.port}/eureka/
```

由于这个支持注册中心,所以本身并没有业务类

但是启动类上要加上@EnableEurekaServer
```java
package com.zzmr.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

/**
 * @author zzmr
 * @create 2023-08-01 12:28
 */
@SpringBootApplication
@EnableEurekaServer
public class EurekaMain7001 {
    public static void main(String[] args) {
        SpringApplication.run(EurekaMain7001.class, args);
    }
}
```

启动成功:
![20230801125240](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230801125240.png)


### 微服务8001入住进Eureka Server

EurekaClient端cloud-provider-payment8001将注册进EurekaServer成为服务提供者provider,类似尚硅谷学校对外提供授课服务

1. 改Pom,添加依赖:
```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            <version>2.1.2.RELEASE</version>
        </dependency>
```
2. 写YML
```yml
eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
      #单机版
      defaultZone: http://localhost:7001/eureka
```
3. 主启动
加上`@EnableEurekaClient`
4. 测试
![20230801130232](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230801130232.png)

>服务名的绑定关系:
![20230801130344](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230801130344.png)

### EurekeClient端cloud-consumer-order80

将注册进EurekaServer成为服务消费者consumer,类似来尚硅谷上课消费的各位同学

将该模块的pom修改为:
```yml
server:
  port: 80

spring:
  application:
    name: cloud-order-service  

eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
      #单机版
      defaultZone: http://localhost:7001/eureka
```

还是上面一样的步骤,这里不再赘述
![20230801131013](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230801131013.png)

### 集群配置

Eureka集群:**互相注册,相互守望**
![20230801133213](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230801133213.png)

服务注册:将服务信息注册进注册中心
服务发现:从注册中心上获取服务信息
实质:存key服务名,去value调用地址

![20230801131820](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230801131820.png)

>问题:微服务RPC远程服务调用最核心的是什么
是高可用,搭建Eureka注册中心集群,实现负载均衡+故障容错

1. 参考7001新建模块7002
2. 修改映射配置(模拟多台机器)
![20230801133937](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230801133937.png)
3. 修改配置文件
```yml
server:
  port: 7001

eureka:
  instance:
    hostname: eureka7001.com # eureka服务端的实例名称
  client:
    # false表示不向注册中心注册自己
    register-with-eureka: false
    # false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    fetch-registry: false
    service-url:
      # 设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址
      defaultZone: http://eureka7002.com:${server.port}/eureka/
```

4. 成功:
![20230801134626](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230801134626.png)

#### 订单支付两微服务注册进Eureka集群

主要是修改配置文件
```yml
      #单机版
#      defaultZone: http://localhost:7001/eureka
      # 集群版
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka
```

就是加上两个地址就行了

?不行啊,一直是只能有一个机子能检测到微服务,而且是谁在后,谁能检测到,问问chatgpt吧

奶奶滴,原来是集群那配置错了

错误:
```yml
      defaultZone: http://eureka7002.com:${server.port}/eureka/
```
正确:
```yml
      defaultZone: http://eureka7002.com:7002/eureka/
```

就是不要用`${server.port}`了,直接写目标端口号

#### 支付微服务集群配置

就是增加多个`cloud-provider-payment`

参考8001新建8002

关键在于要需改8001/8002的Controller

修改之后的:
```java
package com.zzmr.springcloud.controller;

import com.zzmr.springcloud.entities.CommonResult;
import com.zzmr.springcloud.entities.Payment;
import com.zzmr.springcloud.service.PaymentService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.*;

/**
 * @author zzmr
 * @create 2023-07-31 18:43
 */
@RestController
@Slf4j
public class PaymentController {

    @Autowired
    private PaymentService paymentService;

    @Value("${server.port}")
    private String serverPort;

    @PostMapping("/payment/create")
    public CommonResult create(@RequestBody Payment payment) {
        int result = paymentService.create(payment);
        log.info("*****插入结果：" + result);
        if (result > 0) {
            return new CommonResult<>(200, "插入数据成功,serverPort: " + serverPort, result);
        } else {
            return new CommonResult(444, "插入数据库失败", null);
        }
    }

    @GetMapping("/payment/get/{id}")
    public CommonResult getPaymentById(@PathVariable("id") Long id) {
        Payment payment = paymentService.getPaymentById(id);
        log.info("******查询成功:" + payment);
        if (payment != null) {
            return new CommonResult(200, "查询成功,serverPort" + serverPort, payment);
        } else {
            return new CommonResult(444, "没有对应数据,查询ID为" + id, null);
        }
    }

}
```

**此时通过80访问,仍然还是只有8001接收到请求,那是因为consumer模块中的Controller是写死的:**
```java
    public static final String PAYMENT_URL = "http://localhost:8001";
```
发送请求的路径应该为服务名(8001和8002作为服务提供方的服务名)
```java
    public static final String PAYMENT_URL = "http://cloud-payment-service";
```

然后一测试,发现直接报错了...哈哈哈哈哈哈,原因是少个注解
**要使用@LoadBalanced注解赋予RestTemplate负载均衡的能力**
```java
    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
```

**此时通过80再访问,就是一替一次了(轮询)**

### actuator微服务信息完善

就是加别名

原来的是这种:
![20230801202320](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230801202320.png)

是主机加模块名,我们要进行自定义:
```yml
  instance:
    instance-id: payment8001
```
加上这个就可以实现此功能了:
![20230801202536](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230801202536.png)

加上这个就可以实现**显示ip地址了**
```java
  instance:
    instance-id: payment8001
    prefer-ip-address: true
```

在鼠标浮在payment800x上就会显示ip地址:
![20230801202852](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230801202852.png)

### 服务发现Discovery

*洗澡去,一会再看*

**简单来说,就是暴露自己的服务信息**

1. 添加新的对象:
```java
    @Autowired
    private DiscoveryClient discoveryClient;
```
2. 添加测试:
```java
    @GetMapping("/payment/discovery")
    public Object discovery() {

        // 获取服务清单列表
        List<String> services = discoveryClient.getServices();
        for (String service : services) {
            log.info("*****element: " + service);
        }

        // 获取对应微服务名称含有的微服务列表
        List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
        for (ServiceInstance instance : instances) {
            log.info("*****element: " + instance.getServiceId() + "\t" + instance.getHost() + "\t" + instance.getPort() + "\t" + instance.getUri());
        }

        return this.discoveryClient;
    }
```
3. 启动类上添加注解:
```java
@EnableDiscoveryClient
```

就完工了


![20230801205614](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230801205614.png)

![20230801205638](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230801205638.png)

### Eureka自我保护

**概述**
保护模式主要用于一组客户端和Eureka Server之间存在网络分区场景下的保护,**一旦进入保护模式,EurekaServer将会尝试保护其服务注册表中的信息,不再删除服务注册表中的数据,也就是不会注销任何微服务**

**就是,某时刻某一个微服务不可用了,Eureka不会立刻清理,依旧会对该微服务的信息进行保存**

---

怎么禁止?

7001模块上加
```yml
  server:
    enable-self-preservation: false
    eviction-interval-timer-in-ms: 2000
```

![20230801211950](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230801211950.png)

8001模块加上:
```yml
  instance:
    instance-id: payment8001
    prefer-ip-address: true
    # Eureka客户端向服务端发送心跳的时间间隔,单位为秒,默认是30s
    lease-renewal-interval-in-seconds: 1
```

此时就会非常的`无情`

