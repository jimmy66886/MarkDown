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

## 6 Zookeeper服务注册与发现

SpringCloud整合Zookeeper代替Eureka-Eureka已经停止更新了

**注册中心Zookeeper**
- zookeeper是一个分布式协调工具,可以实现注册中心功能
- 关闭Linux服务器防火墙后启动zookeeper服务器
- zookeeper服务器取代Eureka服务器,zk作为服务注册中心

**这老师没讲如何安装**
1. 下载:[链接](https://downloads.apache.org/zookeeper/zookeeper-3.5.10/)
2. 解压:`tar -zxvf apache-zookeeper-3.5.10-bin.tar.gz`
3. 新建配置文件:
![20230802100034](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230802100034.png)

这样就OK了

### 服务提供者

新建一个module:`cloud-provider-payment8004`

一个新的依赖:
```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
        </dependency>
```

配置文件:
```yml
server:
  port: 8004

spring:
  application:
    name: cloud-provider-payment
  cloud:
    zookeeper:
      connect-string: 192.168.168.101:2181
```

此时使用zookeeper,就不会再使用eureka的注解了:
```java
@SpringBootApplication
@EnableDiscoveryClient
public class PaymentMain8004 {

    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8004.class, args);
    }

}
```

**Controller**
```java
@RestController
@Slf4j
public class PaymentController {

    @Value("${server.port}")
    private String serverPort;

    @GetMapping("/payment/zk")
    public String paymentZk() {
        return "spring-cloud with zookeeper: " + serverPort + "\t" + UUID.randomUUID().toString();
    }

}
```

此时进行测试,先将zookeeper启动,然后启动8004

![20230802100055](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230802100055.png)

![20230802100152](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230802100152.png)

然后就进入到客户端里了?
![20230802100235](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230802100235.png)

我的是好了,因为springcloud默认带的是3.5.3,我装的是3.5.10,所以能兼容,但是老师的是3.4.9,所以他要解决一下jar包冲突的问题:

老师换成了:
```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
            <!--先排除自带的zookeeper3.5.3-->
            <exclusions>
                <exclusion>
                    <groupId>org.apache.zookeeper</groupId>
                    <artifactId>zookeeper</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <!--添加zookeeper3.4.9版本-->
        <dependency>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
            <version>3.5.10</version>
        </dependency>
```

当然,他下面是3.4.9,那我就3.5.10呗

![20230802101107](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230802101107.png)

这就证明连上了:
![20230802101133](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230802101133.png)

访问接口也是没问题:
![20230802101242](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230802101242.png)

层级递进:
![20230802101429](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230802101429.png)

而注册信息在这:
```json
{
    "name": "cloud-provider-payment",
    "id": "2831e6f2-adc0-4056-9112-430c35849cad",
    "address": "localhost",
    "port": 8004,
    "sslPort": null,
    "payload": {
        "@class": "org.springframework.cloud.zookeeper.discovery.ZookeeperInstance",
        "id": "application-1",
        "name": "cloud-provider-payment",
        "metadata": {}
    },
    "registrationTimeUTC": 1690942240064,
    "serviceType": "DYNAMIC",
    "uriSpec": {
        "parts": [
            {
                "value": "scheme",
                "variable": true
            },
            {
                "value": "://",
                "variable": false
            },
            {
                "value": "address",
                "variable": true
            },
            {
                "value": ":",
                "variable": false
            },
            {
                "value": "port",
                "variable": true
            }
        ]
    }
}
```

证明我们的微服务成功入住进zookeeper

---

那么服务节点是临时节点还是持久节点

当8004服务关闭时,过几秒钟zookeeper就会将该服务删去

### 订单服务注册进Zookeeper

新建一个`cloud-consumerzk-order80`

依赖和之前的8004一样

yml也是差不多,只是把端口号改为80,服务名改为消费者的服务名

还是要用到restTemplate,然后写一个简单的controller测试
```java
@RestController
@Slf4j
public class OrderZKController {

    public static final String INVOKE_URL = "http://cloud-provider-payment";

    @Autowired
    private RestTemplate restTemplate;

    @GetMapping("/consumer/payment/zk")
    public String paymentInfo() {
        String result = restTemplate.getForObject(INVOKE_URL + "/payment/zk", String.class);
        return result;
    }

}
```

测试:
![20230802104142](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230802104142.png)

测试services中就有了新的服务:`cloud-consumerzk-order80`

再测试接口:
![20230802104244](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230802104244.png)

访问成功,也没问题

## 7 Consul服务注册与发现

Consul是一套开源的分布式服务发现和配置管理系统,Go语言开发

### 安装

[安装网站](https://developer.hashicorp.com/consul/downloads?product_intent=consul)

解压,双击运行:
![20230802214724](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230802214724.png)


启动:`consul agent -dev`

访问`localhost:8500`
![20230802214930](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230802214930.png)

### 服务提供者注册

建一个8006:微服务提供者

```xml
        <!--SpringCloud consul-server -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-consul-discovery</artifactId>
        </dependency>
```

yml:
```yml
server:
  port: 8006
spring:
  application:
    name: consul-provider-payment

  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        # hostname: 127.0.0.1
        service-name: ${spring.application.name}
```

然后就是编写接口

好了,已经注册进去了:
![20230802215943](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230802215943.png)

接口也注册成功:
![20230802220031](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230802220031.png)

### 服务消费者注册

还是这些步骤,

```yml
server:
  port: 80
spring:
  application:
    name: cloud-consumer-order

  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        # hostname: 127.0.0.1
        service-name: ${spring.application.name}

```

![20230802220705](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230802220705.png)


测试也是没问题:
![20230802220804](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230802220804.png)


### 三个注册中心的异同点

![20230802221110](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230802221110.png)

分为CP和AP

AP:eureka
CP:Zookeeper/consul

AP架构中,当网络分区出现后,为了保证可用性,系统B可以返回旧值,保证系统的可用性

结论:违背了一执行C的要求,只满足可用性和分区容错,即AP

CP架构,为了保证一致性,就必须拒接请求,否则无法保证一执行

结论:违背了可用性A的要求,只满足一致性和分区容错,即CP

## 8 Ribbon负载均衡服务调用

>概述
简单来说,Ribbon是Netflix发布的开源项目,主要功能是提供**客户端的软件负载均衡算法和服务调用**,在配置文件中列出Load Balancer(简称LB)后面所有的机器,Ribbon会自动帮助你基于某种规则(简单轮询,随机连接)去连接这些机器,我们很容易使用Ribbon实现自定义的负载均衡算法

>LB负载均衡是什么
将用户的请求平摊的分配到多个服务上,而达到系统的HA(高可用)
常见的负载均衡有软件Nginx,LVS,硬件F5等

**Ribbon本地负载均衡客户端VS Nginx服务端负载均衡区别**
Nginx是服务器负载均衡,客户端所有请求都会交给nginx,然后由nginx实现转发请求,即负载均衡是由服务端实现的
Ribbon本地负载均衡,在调用微服务接口时,会在注册中心上获取注册信息服务列表之后缓存到JVM本地,从而在本地实现RPC远程服务调用技术

---

**集中式LB**
即在服务的消费方和提供方之间使用独立的LB设施(可以是硬件如F5,也可以是软件,如nginx),由该设施负责把访问请求通过某种策略转发至服务的提供方

**进程内LB**
将LB逻辑集成到消费方,消费方从服务注册中心获知有哪些地址可用,然后自己再从这些地址中选择出一个合适的服务器

Ribbon就属于进程内LB,它只是一个类库,集成于消费方进程,消费方通过它来获取到服务提供方的地址

**总结**
Ribbon其实就是一个软负载均衡的客户端组件,它可以和其他所需请求的客户端结合使用,和eureka结合只是其中的一个实例

Ribbon的工作流程
1. 选择EurekaServer,它优先选择在同一区域内负载较少的server
2. 根据用户指定的策略,从server取到的服务注册列表中选择一个地址
其中Ribbon提供了多种策略,比如轮询,随机和根据相应时间加权

---

之前写的80并没有引入spring-cloud-starter-ribbon,也可以使用ribbon,那是因为eureka中包含了ribbon
![20230803105612](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230803105612.png)

### 详解RestTemplate

两种方法:
1. xxxForObject
    - 返回对象为响应体中数据转换成的对象,基本上就是json
2. xxxForEntity
    - 返回对象为ResponseEntity对象,包含了响应中的一些重要信息,比如响应头,响应状态码,响应体等
![20230803110122](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230803110122.png)


测试代码:
```java
    @GetMapping("/consumer/payment/getForEntity/{id}")
    public CommonResult<Payment> getPayment2(@PathVariable("id") Long id) {
        ResponseEntity<CommonResult> entity = restTemplate.getForEntity(PAYMENT_URL + "/payment/get/" + id, CommonResult.class);
        if (entity.getStatusCode().is2xxSuccessful()){
            return entity.getBody();
        }else {
            return new CommonResult<>(444,"操作失败");
        }
    }

    @GetMapping("/consumer/payment/postForEntity/create")
    public CommonResult<Payment> create2(Payment payment) {
        ResponseEntity<CommonResult> entity = restTemplate.postForEntity(PAYMENT_URL + "/payment/create", payment, CommonResult.class);
        if (entity.getStatusCode().is2xxSuccessful()) {
            return entity.getBody();
        } else {
            return new CommonResult<>(444, "操作失败");
        }
    }
```

没问题:
![20230803111235](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230803111235.png)

其实就是entity比object返回的结果比较多一些,但是还是object比较常用

### Ribbon默认的负载均衡规则

IRule:根据特定算法中从服务列表中选取一个要访问的服务

这个接口的实现:
![20230803205441](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230803205441.png)

![20230803205504](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230803205504.png)

**规则替换**
1. 修改cloud-consumer-order80
    - 官方文档明确给出了警告,这个自定义配置类不能放在@ComponentScan所扫描的当钱包以及子包下,否则我们自定义的这个配置类就会被所有的Ribbon客户端所共享,达不到特殊化定制的目的了
    - ![20230803210131](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230803210131.png)
2. 配置类:
```java
@Configuration
public class MySelfRule {

    @Bean
    public IRule myRule(){
        return new RandomRule(); // 自定义为随机        
    }

}
```
3. 在主机启动类上加上`@RibbonClient(name = "CLOUD_PAYMENT_SERVICE", configuration = MySelfRule.class)`

启动服务即可

### Ribbon负载均衡算法

*手写一个*

**负载均衡算法: rest接口第几次请求数 `%` 服务器集群总数量 = 实际调用服务器位置下标 ,每次服务重启动后rest接口计数从1开始**

总机器数是2,那么第一次请求1 对2取余,则下标为1,则是第二台机器
![20230803212755](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230803212755.png)

*好像还挺简单的,但是怎么实现呢*

源码:
```java
public Server choose(ILoadBalancer lb, Object key) {
        if (lb == null) {
            log.warn("no load balancer");
            return null;
        }

        Server server = null;
        int count = 0;
        while (server == null && count++ < 10) {
            List<Server> reachableServers = lb.getReachableServers();
            List<Server> allServers = lb.getAllServers();
            int upCount = reachableServers.size();
            int serverCount = allServers.size();

            if ((upCount == 0) || (serverCount == 0)) {
                log.warn("No up servers available from load balancer: " + lb);
                return null;
            }

            int nextServerIndex = incrementAndGetModulo(serverCount);
            server = allServers.get(nextServerIndex);

            if (server == null) {
                /* Transient. */
                Thread.yield();
                continue;
            }

            if (server.isAlive() && (server.isReadyToServe())) {
                return (server);
            }

            // Next.
            server = null;
        }

        if (count >= 10) {
            log.warn("No available alive servers after 10 tries from load balancer: "
                    + lb);
        }
        return server;
    }
```

取下标的方法:
```java
    /**
     * Inspired by the implementation of {@link AtomicInteger#incrementAndGet()}.
     *
     * @param modulo The modulo to bound the value of the counter.
     * @return The next value.
     */
    private int incrementAndGetModulo(int modulo) {
        for (;;) {
            int current = nextServerCyclicCounter.get();
            int next = (current + 1) % modulo;
            if (nextServerCyclicCounter.compareAndSet(current, next))
                return next;
        }
    }
```

### 手写一个

1. 改写8001/8002的Controller
```java
    @GetMapping("/payment/lb")
    public String getPaymentLB() {
        return serverPort;
    }
```
2. ApplicationContextConfig中去掉`@LoadBalanced`注解
3. 编写接口和实现类:
```java

package com.zzmr.springcloud.lb;

import org.springframework.cloud.client.ServiceInstance;

import java.util.List;

/**
 * @author zzmr
 * @create 2023-08-03 21:49
 */
public interface LoadBalancer {

    ServiceInstance instances(List<ServiceInstance> serviceInstances);

}


@Component
public class MyLB implements LoadBalancer {

    private AtomicInteger atomicInteger = new AtomicInteger(0);

    public final int getAndIncrement() {
        int current;
        int next;

        do {
            current = this.atomicInteger.get();
            next = current == 2147483647 ? 0 : current + 1;
        } while (!this.atomicInteger.compareAndSet(current, next));
        System.out.println("****第几次访问,次数next:" + next);
        return next;
    }

    @Override
    public ServiceInstance instances(List<ServiceInstance> serviceInstances) {

        int index = getAndIncrement() % serviceInstances.size();

        return serviceInstances.get(index);
    }
}
```
4. 改写的controller:
```java
    @GetMapping("/consumer/payment/lb")
    public String getPaymentLB() {
        List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
        if (instances == null || instances.size() == 0) {
            return null;
        }

        // 将服务传给负责负载均衡的配置
        ServiceInstance serviceInstance = loadBalancer.instances(instances);
        URI uri = serviceInstance.getUri();
        return restTemplate.getForObject(uri + "/payment/lb", String.class);
    }
```

测试成功:
![20230803221218](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230803221218.png)

嗯,还有好多要学啊

后天就去西安吧

## 9 OpenFeign服务接口调用

OpenFeign是一个声明式WebSevice客户端,使用Feign能让编写Web Service客户端更加简单
**它的使用方法是定义一个服务接口然后在上面添加注解**

**Feign可以与Eureka和Ribbon组合使用以支持负载均衡**

![20230804090820](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230804090820.png) 


### OpenFeign使用

新建`cloud-consumer-feign-order80`
```xml
        <!--openfeign-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
```

配置文件:
```yml
server:
  port: 80
eureka:
  client:
    register-with-eureka: false
    service-url:
      defaultZone: http://eureka7001.com:7001,http://eureka7002.com:7002/eureka/
```
主启动类添加注解:
```java
@SpringBootApplication
@EnableFeignClients
public class OrderFeignMain80 {
    public static void main(String[] args) {
        SpringApplication.run(OrderFeignMain80.class, args);
    }
}
```

接口,这个就有意思了一个注解加接口,直接表示找到`CLOUD-PAYMENT-SERVICE`服务中的`/payment/get{id}`接口(虽然在service中写了Mapping就离谱哈哈哈)
```java
@Component
@FeignClient(value = "CLOUD-PAYMENT-SERVICE")
public interface PaymentFeignService {

    @GetMapping("/payment/get{id}")
    public CommonResult<Payment> getPaymentById(@Param("id") Long id);

}
```

Controller:
```java
@RestController
@Slf4j
public class OrderFeignController {

    @Autowired
    private PaymentFeignService paymentFeignService;

    @GetMapping("/consumer/payment/get/{id}")
    public CommonResult<Payment> getPaymentById(@PathVariable("id") Long id) {
        return paymentFeignService.getPaymentById(id);
    }

}
```

这里就不用再写restTemplate了,因为openfeign已经封装了,在service接口中已经实现了该功能

没问题:
![20230804094528](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230804094528.png)

突然感觉也是好清晰啊,但是如果用了这个OpenFeign,那么如何自己写一个负载均衡的算法呢?*我应该用不到*

### OpenFeign的超时控制

**故意设置超时演示出错情况**
8001接口中
```java
    public String paymentFeignTimeout() {
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return serverPort;
    }
```

80端口进行调用,出现超时报错
![20230804100021](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230804100021.png)

所以可以修改Feign客户端的等待时间,在yml配置文件中设置

```yml
ribbon:
  ReadTimeout: 5000
  ConnectTimeout: 5000
```

成功:
![20230804100343](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230804100343.png)

### OpenFeign日志打印功能

Feign提供了日志打印功能,我们可以通过配置来调整日志级别,从而了解Feign中http请求的细节

说白了就是**对Feign接口的调用情况进行监控和输出**

日志级别:
- NONE: 默认的,不显示任何日志
- BASIC: 仅记录请求方法,URL,响应状态码以及执行时间
- HEADERS: 除了BASIC中定义的信息之外,还有请求和响应的头信息
- FULL: 除了HEADERS中定义的信息之外,还有请求和响应的正文及元数据

配置类:
```java
@Configuration
public class FeignConfig {

    @Bean
    Logger.Level feignLoggerLevel() {
        return Logger.Level.FULL;
    }

}
```

配置文件
```yml
logging:
  level:
    # feign日志以什么级别监控哪个接口
    com.zzmr.springcloud.service.PaymentFeignService: debug
```

没问题:
![20230804101230](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230804101230.png)



## 10 Hystrix

这就已经到了`服务降级`了

**分布式系统面临的问题**
复杂分布式体系结构中的应用程序有数十个依赖关系,每个依赖关系在某些时候将不可避免地失败

Histrix是一个用于处理分布式系统的延迟和容错的开源库,在分布式系统里,许多依赖不可避免的会调用失败,比如超市,异常等,Hystrix能够保证在一个依赖出问题的情况下,**不会导致整体服务失败,避免级联故障,以提高分布式系统的弹性**

**断路器**本身是一种开关装置,当某个服务单元发生故障之后,通过断路器的故障监控(类似熔断保险丝),向调用方返回一个符合预期的,可处理的备选响应(FallBack),而不是长时间的等待或者抛出调用方法无法处理的异常,这样就保证了服务调用方的线程不会被长时间,不必要地占用,从而避免了故障在分布式系统中的蔓延,乃至雪崩

### Hystrix重要概念

1. 服务降级
    - 对方系统不可用了,你需要给我一个兜底的解决方法
    - 程序运行异常
    - 超时
    - 服务熔断触发服务降级
    - 线程池/信号量打满也会导致服务降级
2. 服务熔断
    - 类比保险丝达到最大服务访问后,直接拒绝访问,拉闸限电,然后调用服务降级的方法并返回友好提示
3. 服务限流
    - 秒杀高并发等操作,严禁一窝蜂的过来拥挤,大家排队,一秒钟N个,有序进行

### 使用

新建:`cloud-provider-hystrix-payment8001`

```xml
        <!--hystrix-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
        </dependency>
```

配置文件:
```yml
server:
  port: 8001

spring:
  application:
    name: cloud-provider-hystrix-payment

eureka:
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      #defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka
      defaultZone: http://eureka7001.com:7001/eureka
```

写测试方法,并以上述方法为根基平台
`正确-错误-降级熔断-恢复`

高并发测试:
开启Jmeter,来20000个并发压死8001,20000个请求都去访问paymentinfo_Timeout

没有啊,看一下得了

给timeout发20000个请求,此时ok也被拖慢了,因为这个微服务集中资源都去给timeout接口处理了

上面还是服务**提供者8001自测**,假如此时外部的消费者80也来访问,那消费者只能干等,最终导致消费者端80不满意,服务端8001直接被拖死

---

好久没看了,突然发现,host文件里的内容被删除了...

```txt
127.0.0.1		eureka7001.com
127.0.0.1		eureka7002.com
```

压测之后得到的结果:
8001同一层次的其他接口服务被困死,因为tomcat线程池里面的工作线程已经被挤占完毕,80此时调用8001,客户端访问响应缓慢,转圈圈...

哦,到现在并没有开始用hystrix的功能哎哈哈哈哈操

解决方案:
1. 对方服务8001超时了,调用者80不能一直卡死等待,必须有服务降级
2. 对方服务8001down机了,调用者80不能一直卡死等待,必须有服务降级
3. 对方服务8001OK,调用者80自己出故障有自我要求(自己的等待时间小于服务提供者)

---

#### 服务降级

降级配置:`@HystrixCommand`

给8001自身设置调用超时时间的峰值,峰值内可以正常运行,超过了需要有兜底的方法处理,作服务降级fallback

这样写:
```java
    @Override
    @HystrixCommand(fallbackMethod = "paymentInfo_TimeoutHandler", commandProperties = {
            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "3000")
            // 3s以内,走正常的业务逻辑,3s以上,就会调用兜底方法
    })
    public String paymentInfo_Timeout(Integer id) {
        int timeNumber = 5;
        try {
            TimeUnit.SECONDS.sleep(timeNumber);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "线程池: " + Thread.currentThread().getName() + "paymentInfo_Timeout " + id + "\t" + "哈哈哈哈哈" + "耗时: " + timeNumber + "s";
    }

    public String paymentInfo_TimeoutHandler(Integer id){
        return "线程池: " + Thread.currentThread().getName() + "paymentInfo_TimeoutHandler " + id + "\t" + "嘿嘿嘿";
    }
```

下面的paymentInfo_TimeoutHandler就相当于兜底的方法,当paymentInfo_Timeout鸡了之后,就会调用该方法


**最后在主启动类上加上启动该注解的注解**`@EnableCircuitBreaker`

一般服务降级都是放在客户端的,就是案例中的80

80端的配置文件:
```yml
feign:
  hystrix:
    enabled: true
```

在主启动类上加上注解:`@EnableHystrix`

controller上加入:
```java

    @GetMapping("/consumer/payment/hystrix/timeout/{id}")
    @HystrixCommand(fallbackMethod = "paymentTimeOutFallbackMethod",commandProperties = {
            @HystrixProperty(name="execution.isolation.thread.timeoutInMilliseconds",value="1500")
    })
    public String paymentInfo_TimeOut(@PathVariable("id") Integer id)
    {
        String result = paymentHystrixService.paymentInfo_Timeout(id);
        return result;
    }
    public String paymentTimeOutFallbackMethod(@PathVariable("id") Integer id)
    {
        return "我是消费者80,对方支付系统繁忙请10秒钟后再试或者自己运行出错请检查自己,o(╥﹏╥)o";
    }
```

---

问题:
每一个方法都需要兜底的方法,代码膨胀
和业务逻辑混在一起,混乱

所以就有了全局的兜底方法

除了个别重要核心业务有专属,其它普通的可以通过`@DefaultProperties(defaultFallback="")统一跳转到统一处理结果页面,通过的和独享的各自分开,避免了代码膨胀,合理减少了代码量

```java
package com.zzmr.springcloud.controller;

import com.netflix.hystrix.contrib.javanica.annotation.DefaultProperties;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixProperty;
import com.zzmr.springcloud.service.PaymentHystrixService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

/**
 * @author zzmr
 * @create 2023-08-14 21:03
 */
@RestController
@Slf4j
@DefaultProperties(defaultFallback = "payment_Global_FallbackMethod")
public class OrderHystrixController {

    @Autowired
    private PaymentHystrixService paymentHystrixService;

    @GetMapping("/consumer/payment/hystrix/ok/{id}")
    public String paymentInfo_OK(@PathVariable("id") Integer id) {
        String result = paymentHystrixService.paymentInfo_OK(id);
        return result;
    }

    @GetMapping("/consumer/payment/hystrix/timeout/{id}")
    // @HystrixCommand(fallbackMethod = "paymentTimeOutFallbackMethod", commandProperties = {
    //         @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "4000")
    // })
    @HystrixCommand
    public String paymentInfo_TimeOut(@PathVariable("id") Integer id) {
        int age = 10 / 0;
        String result = paymentHystrixService.paymentInfo_Timeout(id);
        return result;
    }

    public String paymentTimeOutFallbackMethod(@PathVariable("id") Integer id) {
        return "我是消费者80,对方支付系统繁忙请10秒钟后再试或者自己运行出错请检查自己,o(╥﹏╥)o";
    }

    // 下面是全局fallback方法
    public String payment_Global_FallbackMethod() {
        return "Global异常处理信息,请稍后再试";
    }


}

```

---

**Hystrix之通配服务降级**

现在是改写接口,实现通配的服务降级

服务降级,客户端去调用服务端,碰上服务端宕机或关闭,本次实例服务降级处理是在客户端80实现完成的,与服务端8001没有关系,只需要为Feign客户端定义的接口添加一个服务降级处理的实现类即可实现解耦

根据cloud-consumer-feign-hystrix-order80已经有的PaymentHystrixService接口,重新新建一个类(PaymentFallbackService)实现该接口,**统一为接口里面的方法进行异常处理**

PaymentFallbackService类实现PaymentFeignClientService接口

```java
package com.zzmr.springcloud.service;

/**
 * @author zzmr
 * @create 2023-08-14 23:19
 */
@Service
public class PaymentFallbackService implements PaymentHystrixService {
    @Override
    public String paymentInfo_OK(Integer id) {
        return "--------PaymentFallbackService fall back ,ok";
    }

    @Override
    public String paymentInfo_Timeout(Integer id) {
        return "--------PaymentFallbackService fall back ,error";
    }
}
```

然后再改写原接口:
```java
package com.zzmr.springcloud.service;

import com.zzmr.springcloud.entities.CommonResult;
import com.zzmr.springcloud.entities.Payment;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.stereotype.Service;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

/**
 * @author zzmr
 * @create 2023-08-14 20:56
 */
@Service
@FeignClient(value = "CLOUD-PROVIDER-HYSTRIX-PAYMENT", fallback = PaymentFallbackService.class)
public interface PaymentHystrixService {

    @GetMapping("/payment/hystrix/ok/{id}")
    public String paymentInfo_OK(@PathVariable("id") Integer id);

    @GetMapping("/payment/hystrix/timeout/{id}")
    public String paymentInfo_Timeout(@PathVariable("id") Integer id);

}
```

---

#### 服务熔断

类比保险丝达到最大服务访问后,直接拒绝访问,拉闸限电,然后调用服务降级的方法并返回友好提示

**就是保险丝,服务的降级->进而熔断->恢复调用链路**

**当检测到该节点微服务调用响应正常后,快速返回错误的响应信息**

在Spring Cloud框架里,熔断机制通过Hystrix实现,Hystrix会监控微服务间调用的状况,当失败的调用到一定阈值,缺省是5秒内20次调用失败,就会启动熔断机制,熔断机制的注解是`@HystrixCommand`

---

实操

修改cloud-provider-hystrix-payment8001

```java
    //=========服务熔断
    @HystrixCommand(fallbackMethod = "paymentCircuitBreaker_fallback", commandProperties = {
            @HystrixProperty(name = "circuitBreaker.enabled", value = "true"),
            @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold", value = "10"),
            @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds", value = "10000"),
            @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage", value = "60"),
    })
    public String paymentCircuitBreaker(@PathVariable("id") Integer id) {
        if (id < 0) {
            throw new RuntimeException("******id 不能负数");
        }
        String serialNumber = IdUtil.simpleUUID();

        return Thread.currentThread().getName() + "\t" + "调用成功，流水号: " + serialNumber;
    }

    public String paymentCircuitBreaker_fallback(@PathVariable("id") Integer id) {
        return "id 不能负数，请稍后再试，/(ㄒoㄒ)/~~   id: " + id;
    }
```

---

基本就是,先大量访问失败,导致系统认为该服务不太行,然后就会将所有的请求到交给断路器解决,然后再逐渐恢复

这就到总结了啊,感觉也没学到什么...

![20230815215705](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230815215705.png)

好吧,只是服务熔断这块的小总结..

熔断类型:
1. 熔断打开:请求不再进行调用当前服务,内部设置时钟一般为MTTR(平均故障处理时间),当打开时长达到所设时钟则进入半熔断状态
2. 熔断关闭:熔断关闭不会对服务进行熔断
3. 熔断半开:部分请求规则调用当前服务,如果请求成功且符合规则则认为当前服务恢复正常,关闭熔断


```java
    @HystrixCommand(fallbackMethod = "paymentCircuitBreaker_fallback", commandProperties = {
            @HystrixProperty(name = "circuitBreaker.enabled", value = "true"),  // 是否开启断路器
            @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold", value = "10"), // 请求次数
            @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds", value = "10000"),   // 时间窗口期
            @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage", value = "60"),   // 失败率达到多少后跳闸
    })
```
>断路器在什么情况下开始起作用
涉及到断路器的三个重要参数:**快照时间窗,请求总数阈值,错误百分比阈值**
1. 快照时间窗:断路器确定是否打开需要统一一些请求和错误数据,而统计的时间范围就是快照时间窗,默认为最近的10s
2. 请求总数阈值:在快照时间窗内,必须满足请求总数阈值才有资格熔断,默认为20,意味着在10s内,如果该hystrix命令的调用次数不足20次,即使所有的请求都超时或其他原因失败,断路器都不会打开
3. 错误百分比阈值:当请求总数在快照时间窗内超过了阈值,比如发生了30次调用,如果在这30次调用中,有15次发生了超时异常,也就是超过50%的错误百分比,在默认设定50%阈值情况下,这时候就会将断路器打开

>断路器开启或关闭的条件
1. 当满足一定的阈值的时候(默认10s内超过20个请求次数)
2. 当失败率达到一定的时候(默认10s内超过50%的请求失败)
3. 到达以上阈值,断路器将会开启
4. 当开启的时候,所有请求都不会进行转发
5. 一段时间后(默认是5s)这个时候断路器是半开状态,会让其中一个请求进行转发,如果成功,断路器会关闭,若失败,继续开启,重复4和5

---

>断路器打开之后
1. 再有请求调用的时候,将不会调用主逻辑,而是直接调用降级fallback,通过断路器,实现了自动地发现错误并将降级逻辑切换为主逻辑,减少响应延迟的效果
2. 原本的主逻辑要如何恢复
对于这一问题,hystrix也为我们实现了自动恢复功能,当断路器打开,对主逻辑进行熔断之后,hystrix会启动一个休眠时间窗,在这个时间窗内,降级逻辑是**临时的成为主逻辑**,当休眠时间窗到期,断路器将进入半开状态,释放一次请求到原来的主逻辑上,如果此次请求正常返回,那么断路器将继续闭合,主逻辑恢复,如果这次请求依然有问题,断路器继续进入打开状态,休眠时间窗重新计时

---

### hystrix工作流程

![20230815224303](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230815224303.png)

### hystrix仪表盘

除了隔离依赖服务的调用之外,hystrix还提供了准实时的调用监控(hystrix dashboard),hystrix会持续地记录所有通过Hystrix发起的请求的执行信息,并以统计报表和图形的形式展示给用户,包括每秒执行多少成功,多少失败,netflix通过hystrix-metrics-event-stream项目实现了对以上指标的监控,SpringCloud也提供了Hystrix Dashboard的整合,对监控内容转化为可视化界面

---

新建项目:`cloud-consumer-hystrix-dashboard9001`

新依赖:
```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
        </dependency>
```

新注解:`@EnableHystrixDashboard`给主启动类加上,**每多一个功能,都要在主启动类上开启**

访问可得:
![20230816104755](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230816104755.png)

**被监控的前提是要有如下依赖**
```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
```

新版本Hystrix需要在主启动类MainAppHystrix8001中指定监控路径,不然会报404
```java
    /**
     *此配置是为了服务监控而配置，与服务容错本身无关，springcloud升级后的坑
     *ServletRegistrationBean因为springboot的默认路径不是"/hystrix.stream"，
     *只要在自己的项目里配置上下面的servlet就可以了
     */
    @Bean
    public ServletRegistrationBean getServlet() {
        HystrixMetricsStreamServlet streamServlet = new HystrixMetricsStreamServlet();
        ServletRegistrationBean registrationBean = new ServletRegistrationBean(streamServlet);
        registrationBean.setLoadOnStartup(1);
        registrationBean.addUrlMappings("/hystrix.stream");
        registrationBean.setName("HystrixMetricsStreamServlet");
        return registrationBean;
    }
```

9001监控8001
1. 填写监控地址:`http://localhost:8001/hystrix.stream`
2. 开启监控

还是挺直观的,circuit那里有当前的情况显示
![20230816110023](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230816110023.png)

到这就ok了,但是这些可能都不用吧,毕竟后面还有alibaba

## 11 zuul路由网关和Gateway新一代网关

主要看这个`新一代`,但是zuul还是要看的

>Cloud全家桶中有个很重要的组件就是网关，在1.x版本中都是采用的zuul网关;但是在2.x版本中,zuul的升级一直跳票,SpringCluod最后自己研发了一个网关替代zuul,那就是SpringCloud Gateway
一句话:gate是原zuul1.x版的替代

Gateway是在Spring生态系统之上构建的API网关服务,基于Spring5,SpringBoot2和ProjectReactor等技术,Gateway旨在提供一种简单而有效的方式来对API进行路由,以及提供一些强大的过滤器功能,例如:**熔断,限流,重试等**

SpringCloud Gateway使用的是Webflux中的reactor-netty响应式编程组件,底层使用了Netty通讯框架

### Gateway网关工作流程

客户端向Spring Cloud Gateway发出请求,然后在Gateway Handler Mapping中找到与请求相匹配的路由,将其发送到Gateway Web Handler

Handler再通过指定的过滤器链来请求发送到我们实际的服务执行业务逻辑,然后返回.

过滤器之间用虚线分开是因为过滤器可能会发送代理请求之前("pre")或之后("post")执行业务逻辑

>核心逻辑:**路由转发+执行过滤器链

### 创建Gateway9527模块

新加依赖**注意,加上这个依赖后,就不用再加上starter-web和acatur了,加上会报错**
```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-gateway</artifactId>
        </dependency>
```

配置文件:
```yml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway

eureka:
  instance:
    hostname: cloud-gateway-service
  client: #服务提供者provider注册进eureka服务列表内
    service-url:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://eureka7001.com:7001/eureka
```

---

>**三大核心概念**
1. Route(路由):路由是构建网关的基本模块，它由ID，目标URI，一系列的断言和过滤器组成，如果断言为true则匹配该路由
2. Predicate(断言):参考的是Java8的java.util.function.Predicate开发人员可以匹配HTTP请求中的所有内容(例如请求头或请求参数),如果请求与断言相匹配则进行路由
3. Filter(过滤):指的是Spring框架中GatewayFilter的实例,使用过滤器,可以在请求被路由前或者之后对请求进行修改

![20230818114408](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230818114408.png)

所以要在8001外面包一层9527,再给9527加上一些配置
```yml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      routes:
        - id: payment_routh #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          uri: http://localhost:8001          #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**         # 断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          uri: http://localhost:8001          #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/lb/**         # 断言，路径相匹配的进行路由

eureka:
  instance:
    hostname: cloud-gateway-service
  client: #服务提供者provider注册进eureka服务列表内
    service-url:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://eureka7001.com:7001/eureka
```


**此时,如果要访问8001的lb或者是get方法,都会先打到9527**

启动成功后
1. 添加网关前:`http://localhost:8001/payment/get/31`
2. 添加网关后:`http://localhost:9527/payment/get/31`

上面的配置其实就是,当访问9527下的接口时,会自动映射到配置中uri对应的接口

---

上面gateway配置的routes,照着这个格式配就行了,但是gateway还有一种配置方式:硬编码

>gateway硬编码配置方式

**代码中注入RouteLocator的Bean**

然后实现一个功能:`通过9527网关访问到外网的网址:blog.zzmr.club`

```java
package com.zzmr.springcloud.config;

import org.springframework.cloud.gateway.route.RouteLocator;
import org.springframework.cloud.gateway.route.builder.RouteLocatorBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @author zzmr
 * @create 2023-08-18 12:15
 */
@Configuration
public class GateWayConfig {

    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder routeLocatorBuilder) {
        RouteLocatorBuilder.Builder routes = routeLocatorBuilder.routes();
        routes.route("path_route_zzmr",
                r -> r.path("/maps")
                        .uri("http://blog.zzmr.club/maps")).build();
        return routes.build();
    }

}
```

添加这个配置类,就可以实现访问外网的功能了

**流程就是这样的,对9527的请求:`localhost:9527/maps`会自动映射到`http://blog.zzmr.club/maps`**

### GateWay配置动态路由

上面的配置问题:
1. 地址写死了
2. 无法实现负载均衡

所以要用动态路由来实现负载均衡

**通过微服务名实现动态路由**

*默认情况下Gateway会根据注册中心注册的服务列表,以注册中心上微服务名为路径创建`动态路由进行转发,从而实现动态路由的功能`*

为了实现这个效果,开一个7001和8001和8002

改写9527的配置文件
```yml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态创建路由的功能，利用微服务名进行路由
      routes:
        - id: payment_routh #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          #          uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://CLOUD-PAYMENT-SERVICE  # 匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**         # 断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          #          uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://CLOUD-PAYMENT-SERVICE  # 匹配后提供服务的路由地址
          predicates:
            - Path=/payment/lb/**         # 断言，路径相匹配的进行路由

eureka:
  instance:
    hostname: cloud-gateway-service
  client: #服务提供者provider注册进eureka服务列表内
    service-url:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://eureka7001.com:7001/eureka
```

**相当于还是通过服务名了,感觉和之前的ribbon没啥区别**

此时再访问:`http://localhost:9527/payment/lb`,就能发现,实现了负载均衡

### Predicate的使用

**断言**

断言分为好多种,

>1. `-After`

这里的时间格式比较特别:`2023-08-18T22:26:34.784+08:00[Asia/Shanghai]`是这个格式的,要通过以下代码获取:
```java
public class T2 {
    public static void main(String[] args) {
        ZonedDateTime zbj = ZonedDateTime.now(); // 默认时区
        System.out.println(zbj); //2023-08-18T22:26:34.784+08:00[Asia/Shanghai]
    }
}
```

是这样配置的:
```yml
          predicates:
            - Path=/payment/lb/**         # 断言，路径相匹配的进行路由
            - After=2023-08-18T22:26:34.784+08:00[Asia/Shanghai] # 表示在这个时间以后,lb访问请求才有效
```

**就表示这这个配置时间之后才该请求才有效**

这里测试,把时间调到一个小时以后,再次访问,就得到404了
![20230818223125](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230818223125.png)

*一般用于上线未来的接口,但是又不能立即启用*

和之相似的还有Before,和Between,between就是配置两个时间,用逗号隔开:
`- Between=2023-08-18T21:26:34.784+08:00[Asia/Shanghai],2023-08-18T23:26:34.784+08:00[Asia/Shanghai]`

>2. Cookie

Cookie Route Predicate需要两个参数,一个是Cookie name,一个是正则表达式,路由规则会通过获取对应的Cookie name值和正则表达式去匹配,如果匹配上就会执行路由,如果没有匹配上则不执行

```yml
    - Path=/payment/lb/**         # 断言，路径相匹配的进行路由
    - Cookie=username,zzmr
```

**使用curl命令来发送请求**

这个还是第一次知道,就直接在控制台:
![20230818225355](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230818225355.png)

后面`--cookie "username=zzmr"`就表示该请求携带cookie

如果不带指定格式的cookie,就会报404
![20230818225503](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230818225503.png)

>3. Header请求头

```yml
    - Header=X-Request-Id, \d+  # 请求头要有X-Request-Id属性并且值为整数的正则表达式
```

此时还是用curl命令来测试:`http://localhost:9527/payment/lb -H "X-Request-Id:20"`

测试:
![20230818230351](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230818230351.png)

>4. Host

Host Route Predicate 接受一组参数,一组匹配的域名列表,这个模板是ant分隔的模板,用.号作为分隔符,它通过参数中的主机地址作为匹配规则

![20230818231059](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230818231059.png)

>5. Method

这个更简单了,就是指定发送请求的类型:
```yml
    - Method=GET
```

这时就只能接收到GET请求,发送post请求会报404
![20230818231748](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230818231748.png)

>6. Path

就是上面用的那个:`- Path=/payment/lb/**         # 断言，路径相匹配的进行路由`

指定请求路径的

>7. Query(请求参数)
```yml
    - Query=username, \d+  # 要有参数名username并且值还要是整数才能路由
```

就是这样用:
![20230818232458](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230818232458.png)

**注意正则表达式里的反斜杠,刚写错了,看了半天**

### Filter

**路由过滤器可用于修改进入的HTTP请求和返回的HTTP响应，路由过滤器只能指定路由进行使用**

SpringCloud Gateway内置了多种路由过滤器，他们都由GatewayFilter的工厂类来产生

>生命周期:
1. pre(之前)
2. post(之后)

>种类:
1. GatewayFilter(单一)
2. GlobalFilter(全局)

---

过滤器种类太多了,全局的:
```yml
          filters:
            - AddRequestParameter=X-Request-Id,1024 #过滤器工厂会在匹配的请求头加上一对请求头，名称为X-Request-Id值为1024
```

这里只讲自定义全局过滤器

自定义配置:
```java
package com.zzmr.springcloud.filter;

import lombok.extern.slf4j.Slf4j;
import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.core.Ordered;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Component;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

import java.util.Date;

/**
 * @author zzmr
 * @create 2023-08-19 10:47
 */
@Component
@Slf4j
public class MyLogGateWayFilter implements GlobalFilter, Ordered {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        log.info("*******come in MyLogGateWayFilter: " + new Date());
        // 表示要求请求带有请求参数，且第一个为uname
        String uname = exchange.getRequest().getQueryParams().getFirst("uname");
        if (uname == null) {
            log.info("*******用户名为null,非法用户");
            exchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);
            return exchange.getResponse().setComplete();
        }
        return chain.filter(exchange);
    }

    // 这个返回值越小，优先级越高
    @Override
    public int getOrder() {
        return 0;
    }
}
```

此时访问,如果参数不符合要求,则会报406

## 12 SpringCloud Config

分布式配置中心

不知不觉已经看到这了:
![20230819113003](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230819113003.png)

为什么要用这个?-**配置问题**

微服务意味着要将单体应用中的业务拆分成一个个子服务,每个服务的粒度相对较小,因此系统中会出现大量的服务,由于每个服务都需要必要的配置信息才能运行,所以一套集中式的,动态的配置管理设施是必不可少的

springCloud提供了ConfigServer来解决这个问题,我们每一个微服务自己带着一个application.yml,上百个配置文件的管理

---

>是什么
SpringCloud Config为微服务架构中的微服务提供集中化的外部配置支持,配置服务器**为各个不同微服务应用**的所有环境提供了一个**中心化的外部配置**
![20230819113838](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230819113838.png)

>怎么玩
SpringCloud Config分为**服务端和客户端**两个部分

服务端也称为**分布式配置中心,它是一个独立的微服务应用**,用来连接配置服务器并为客户端提供获取配置信息,加密/解密信息等访问接口

客户端则是通过指定的配置中心来管理应用资源,以及与业务相关的配置内容,并在启动的时候从配置中心获取和加载配置信息,配置服务器默认采用git来存储配置信息,这样就有助于对环境配置进行版本管理,并且可以通过git客户端工具来方便的管理和访问配置内容

>能干嘛
1. 集中管理配置文件
2. 不同环境不同配置,动态化的配置更新,分环境部署比如`dev/test/prod/beta/release`
3. 运行期间动态调整配置,不再需要在每个服务部署的机器上编写配置文件,服务会向配置中心统一拉取配置自己的信息
4. 当配置发生变动时,服务不需要重启即可感知到配置的变化并应用新的配置
5. 将配置信息以REST接口的形式暴露

### Config服务端配置

1. 在github新建一个`springcloud-config的新Repository`
2. 由上一步获取刚新建的git地址

然后就是将仓库clone到本地,然后添加些文件什么的

新建模块:`cloud-config-center-3344`
```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-server</artifactId>
        </dependency>
```

然后是配置文件:
```yml
server:
  port: 3344

spring:
  application:
    name:  cloud-config-center #注册进Eureka服务器的微服务名
  cloud:
    config:
      server:
        git:
          uri: git@github.com:jimmy66886/springcloud-config.git #GitHub上面的git仓库名字
          ####搜索目录
          search-paths:
            - springcloud-config
      ####读取分支
      label: master

#服务注册到eureka地址
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka
```

主启动类上加入新的注解:
```java
@EnableConfigServer
```

然后加映射:
![20230819121030](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230819121030.png)

配置这块有坑,如果开着梯子,就访不了7001,然后3344这个也有问题,关了梯子后发现认证失败,解决办法:
1. 将ssh改成https:
2. 加上用户名和密码
```yml
#          uri: git@github.com:jimmy66886/springcloud-config.git #GitHub上面的git仓库名字
          uri: https://github.com/jimmy66886/springcloud-config.git
          ####搜索目录
          search-paths:
            - springcloud-config
#          username: jimmy66886
#          password: 
```

还有就是,分支要改成main,不然还是访问不到:
![20230819122559](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230819122559.png)

---

>配置读取规则

另外的一种访问模式:
![20230820151229](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230820151229.png)


### 客户端配置

新建模块:`cloud-config-client3355`

新依赖:
```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>
```

引入`bootstrap.yml`
>是什么
application.yml是用户及的资源配置项
bootstrap.yml是系统级的,**优先级更高**

SpringCloud会创建一个`Bootstrap context`,作为Spring应用的Application Context的父上下文,初始化的时候,bootstrap Context负责从**外部源**加载配置属性并解析配置,这两个上下文共享一个从外部获取的Environment

Bootstrap属性由高优先级,默认情况下,它们不会被本地配置覆盖,Bootstrap context和application Context有着不同的约定,所以新增了一个bootstrap.yml文件,保证Bootstrap Context和Application Context配置的分离

**要将Client模块下的applicaiton.yml文件改为bootstarp.yml,这时很关键的**

内容:
```yml
server:
  port: 3355

spring:
  application:
    name: config-client
  cloud:
    #Config客户端配置
    config:
      label: main #分支名称
      name: config #配置文件名称
      profile: dev #读取后缀名称   上述3个综合：master分支上config-dev.yml的配置文件被读取http://config-3344.com:3344/master/config-dev.yml
      uri: http://localhost:3344 #配置中心地址k

#服务注册到eureka地址
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka
```
![20230820153001](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230820153001.png)


访问成功,表示3355从3344拿到了配置信息
![20230820153554](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230820153554.png)

---

此时如果修改了仓库里的内容,再次访问,3344是能检测到改变的,因为直连的数据库,但3355拿不到新的数据

**所以要实现`动态刷新`**

### 动态刷新

对3355进行修改

1. 修改YML,暴露监控端口
```yml
management:
  endpoint:
    web:
      exposure:
        include: "*"
```
2. `@RefreshScope`业务类Controller修改

然后测试就行了,但是吧,这个网,不开梯子,进不去github,开了梯子,本地的eureka就有问题,我真是服了

最后一步就是,要给3355发送一个post请求,请求刷新`curl -X POST "http://localhost:3355/actuator/refresh`

...好困

## 13 Bus消息总线

这个看来要配置RabbitMq,记得docker上配置的还在吧

>Bus配置
1. 分布式自动刷新配置功能
2. Spring Cloud Bus配合Spring Cloud Config使用可以实现配置的动态刷新

>是什么
Bus支持两种消息代理:RabbitMQ和kafka

>什么是总线
在微服务架构的系统中,通常会使用**轻量级的消息代理**来构建一个**共用的消息主题**,并让系统中所有微服务实例都链接上来.由于**该主题中产生的消息会被所有实例监听和消费,所以称它为消息总线**,在总线上的各个实例,都可以方便地广播一些需要让其他连接在该主题上的实例都知道的消息

>基本原理
ConfigClient实例都监听MQ中同一个topic(默认是springCloudBus),当一个服务刷新的时候,它会把这个信息放入到Topic中,这样其他监听同一topic的服务就能得到通知,然后去更新自身的配置

### 配置RabbitMq

还是用docker来配置

也没啥配置的,直接起一个容器就行了

### 设计思想

1. 利用消息总线触发一个客户端/bus/refresh,而刷新所有客户端的配置
2. 利用消息总线触发一个服务ConfigServer的/bus/refresh端点,而刷新所有客户端的配置

但是第一种方式不太推荐,原因如下:
- 打破了微服务的职责单一性,因为微服务本身是业务模块,它本不应该承担配置刷新的职责
- 破坏了微服务各节点的对等性
- 有一定的局限性,例如,微服务在迁移时,它的网络地址常常会发生变化,此时如果要做到自动刷新,那么就会增加更多的修改

### 更改配置

1. 修改3344
```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-bus-amqp</artifactId>
        </dependency>
```
2. 改yml
```yml
rabbitmq:
  host: 1.14.102.11
  port: 5672
  username: admin
  password: admin

management:
  endpoint:
    web:
      exposure:
        include: "bus-refresh"
```

这配置的零零散散,好乱啊,晕了已经,甚至还没启动成功

害,贵在坚持,但是不贵

## 14 cloud Stream

>为什么要学
因为后端的数据可能是用的RabbitMq,而大数据层面是用的kafka,这样两者的消息就不适配了

>有一种新的技术诞生,让我们不再关注具体MQ的细节,我们只需要用一种适配绑定的方式,自动的给我们在各种MQ内切换

### cloud Steam简介

>是什么
**屏蔽底层消息中间件的差异,降低切换成本,统一消息的编程模型**

官方定义:Spring Cloud Steam是一个构建消息驱动微服务的框架

应用程序通过inputs或者outputs来与Spring Cloud Stream中的binder对象交互,通过我们配置来binding,而Spring Cloud Stream的binder对象负责与消息中间件交互
**所以,我们只需要搞清楚如何与Spring Cloud Stream交互就可以方便的使用消息驱动的方式**

>就是发布和订阅的模式

### Spring Cloud Stream标准流程套路

1. Binder:很方便的连接中间件,屏蔽差异
2. Channel:通道,是队列Queue的一种抽象,在消息通讯系统中就是实现存储和转发的媒介,通过Channel对队列进行配置
3. Source和Sink:简单的可理解为参照对象是Spring Cloud Steam自身,从Stream发布消息就是输出,接受消息就是输入

### 实操

好家伙,又是3台机子一块来

8801模块,新依赖:
```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
        </dependency>
```

配置文件:
```yml
server:
  port: 8801

spring:
  application:
    name: cloud-stream-provider
  cloud:
    stream:
      binders: # 在此处配置要绑定的rabbitmq的服务信息；
        defaultRabbit: # 表示定义的名称，用于于binding整合
          type: rabbit # 消息组件类型
          environment: # 设置rabbitmq的相关的环境配置
            spring:
              rabbitmq:
                host: localhost
                port: 5672
                username: admin
                password: admin
      bindings: # 服务的整合处理
        output: # 这个名字是一个通道的名称 --生产者
          destination: studyExchange # 表示要使用的Exchange名称定义
          content-type: application/json # 设置消息类型，本次为json，文本则设置“text/plain”
          binder: defaultRabbit # 设置要绑定的消息服务的具体设置

eureka:
  client: # 客户端进行Eureka注册的配置
    service-url:
      defaultZone: http://localhost:7001/eureka
  instance:
    lease-renewal-interval-in-seconds: 2 # 设置心跳的时间间隔（默认是30秒）
    lease-expiration-duration-in-seconds: 5 # 如果现在超过了5秒的间隔（默认是90秒）
    instance-id: send-8801.com  # 在信息列表时显示主机名称
    prefer-ip-address: true     # 访问的路径变为IP地址
```


*但是不知道为什么这个配置文件有些配置是爆红的*

**消息接口实现类**
```java
package com.zzmr.springcloud.service.impl;

import com.zzmr.springcloud.service.IMessageProvider;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.stream.annotation.EnableBinding;
import org.springframework.messaging.MessageChannel;
import org.springframework.messaging.support.MessageBuilder;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;
import javax.xml.transform.Source;
import java.util.UUID;

/**
 * @author zzmr
 * @create 2023-08-22 16:53
 */
@EnableBinding(Source.class) // 定义消息的推送管道
public class IMessageProviderImpl implements IMessageProvider {

    @Resource
    private MessageChannel output;

    @Override
    public String send() {
        String serial = UUID.randomUUID().toString();
        output.send(MessageBuilder.withPayload(serial).build());
        System.out.println("**********serial: "+serial);
        return null;
    }
}
```

这次不是`@Service`了,而是`@EnableBinding(Source.class)`,特别容易引错包的..

---

下午走的时候8801都没启动,回来搞了好久也没启动,才发现原来是Source.class导错包了

>**消费者**

依赖还是那些依赖,但是配置文件改了些内容,从output(生产者)变成了input(消费者)

*倦了吗*

```yml
server:
  port: 8802

spring:
  application:
    name: cloud-stream-consumer
  cloud:
    stream:
      binders: # 在此处配置要绑定的rabbitmq的服务信息；
        defaultRabbit: # 表示定义的名称，用于于binding整合
          type: rabbit # 消息组件类型
          environment: # 设置rabbitmq的相关的环境配置
            spring:
              rabbitmq:
                host: 1.14.102.11
                port: 5672
                username: admin
                password: admin
      bindings: # 服务的整合处理
        input: # 这个名字是一个通道的名称
          destination: studyExchange # 表示要使用的Exchange名称定义
          content-type: application/json # 设置消息类型，本次为对象json，如果是文本则设置“text/plain”
          binder: defaultRabbit # 设置要绑定的消息服务的具体设置

eureka:
  client: # 客户端进行Eureka注册的配置
    service-url:
      defaultZone: http://localhost:7001/eureka
  instance:
    lease-renewal-interval-in-seconds: 2 # 设置心跳的时间间隔（默认是30秒）
    lease-expiration-duration-in-seconds: 5 # 如果现在超过了5秒的间隔（默认是90秒）
    instance-id: receive-8802.com  # 在信息列表时显示主机名称
    prefer-ip-address: true     # 访问的路径变为IP地址
```

因为是消费者,自己并没有业务逻辑,只有一个controller
```java
package com.zzmr.springcloud.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.stream.annotation.EnableBinding;
import org.springframework.cloud.stream.annotation.StreamListener;
import org.springframework.cloud.stream.messaging.Sink;
import org.springframework.messaging.Message;
import org.springframework.web.bind.annotation.RestController;

/**
 * @author zzmr
 * @create 2023-08-23 13:05
 */
@RestController
@EnableBinding(Sink.class)
public class ReceiveMessageListenerController {

    @Value("${server.port")
    private String serverPort;

    @StreamListener(Sink.INPUT)
    public void input(Message<String> message) {
        System.out.println("消费者1号,---->接收到的消息:" + message.getPayload() + "\t    port: " + serverPort);
    }


}
```

这里导包还是有问题,先测试看看吧

看来导包没有问题,先是8801发消息,成功
![20230823131729](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823131729.png)

然后是8802接收消息,成功
![20230823131749](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823131749.png)

### 分组消费与持久化

先新建一个8803消费者,现在就是两个消费者(8802和8803),一个生产者8801,和一个服务注册7001

此时生产者发出消息,两个生产者都能接受到消息

#### 重复消费问题

*我记得之前学rabbitmq里面学到了重复消费这个东西,但是现在已经忘光了*

这是8801发出的消息
![20230823133931](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823133931.png)

这是8802和8803接收到的消息
![20230823134002](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823134002.png)
![20230823134133](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823134133.png)

目前是8802和8803都接收到了消息,存在重复消费的问题

>案例
在以下场景中,订单系统我们做集群部署,都会从RabbitMQ中获取订单信息,那如果**一个订单同时被两个服务获取到**,那么就会造成数据错误,我们得避免这种情况,这时**我们就可以使用stream中的消息分组来解决**
![20230823134840](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823134840.png)

**注意在Stream中处于同一个group中的多个消费者是竞争关系,就能够保证消息只会被其中一个应用消费一次**

```
不同组是可以全面消费的(重复消费)
同一组会发生竞争关系
```

>所以为什么会出现这个重复消费的问题呢?上面看:`不同组是可以全面消费的`,此时在mq中能看到分了两个组,也即是8802和8803是不同的组,所以两者都可以消费到这个消息
![20230823135451](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823135451.png)

>解决
进行分组(group),将消费者分成一个组,这样8802和8803就不会存在重复消费的问题了
1. 自定义配置分组
2. 自定义配置为同一个组,解决重复消费问题

设置分组,只需要在8802和8803的配置文件中加上`group: zzmrA`
```yml
      bindings: # 服务的整合处理
        input: # 这个名字是一个通道的名称
          destination: studyExchange # 表示要使用的Exchange名称定义
          content-type: application/json # 设置消息类型，本次为对象json，如果是文本则设置“text/plain”
          binder: defaultRabbit # 设置要绑定的消息服务的具体设置
          group: zzmrA
```

*这里就写多一点,主要是为了看group的缩进在哪*

目前是8802为`zzmrA`,8803为`zzmrB`,在mq中能看出:
![20230823142222](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823142222.png)

>分布式微服务应用为了实现高可用和负载均衡,实际上都会部署多个实例,本例启动了8802和8803,多数情况,生产者发送消息给某个具体微服务时,只希望被消费一次,按照上面我们启动了两个应用的例子,虽然它们同属一个应用,但是这个消息出现了被重复消费两次的情况,为了解决这个问题,在Spring Cloud Stream中提供了**消费组**的概念

---

此时把8802和8803分成同一个组

8801发送4条
![20230823143644](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823143644.png)
8802收到两条
![20230823143657](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823143657.png)
8803也是收到两条
![20230823143708](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823143708.png)

在mq中,也能看到zzmrA组的消费者有两个:
![20230823143843](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823143843.png)

#### 持久化

1. 停掉8802/8803并去除掉8802的分组
2. 8801先发送4条消息到mq
3. 先启动8802,无分组属性配置,后台没有打出消息
4. 再启动8803,有分组属性配置,后台打出了MQ上的消息

?这就是持久化?

也没配置东西,所以说这是默认的,也就说,消费者掉了之后,消息并不会丢失,会等消费者上线后重新接收

## 15 Sleuth

**分布式请求链路跟踪**(链路监控)

看着好高级啊

>为什么会出现这个技术
在微服务框架中,一个由客户端发起的请求在后端系统中会经过多个不同的服务节点调用来协同产生最后的请求结果,每一个前端请求都会形成一条复杂的分布式服务调用链路,链路中的任何一环出现高延时或错误都会引起整个请求最后的失败---所以需要监控

>是什么
Spring Cloud Sleuth提供了一套完整的服务跟踪的解决方案
在分布式系统中提供追踪解决方案并兼容支持了zipkin

### 搭建链路监控

1. 安装zipkin
    - 下载:SpringCloud从F版起已不需要自己构建ZipkinServer了,只需要调用jar包即可,[链接](https://search.maven.org/remote_content?g=io.zipkin.java&a=zipkin-server&v=LATEST&c=exec)
    - 运行jar包
    - ![20230823152040](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823152040.png)
2. 图形化界面
    - ![20230823152229](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823152229.png)

---

流程:
一条链路通过Trace Id唯一标识,Span标识发起的请求信息,各span通过parent id关联起来
    - Trace:类似于树结构的Span集合,表示一条调用链路,存在唯一标识
    - Span:表示调用链路来源,通俗的理解span就是一次请求信息
![20230823152615](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823152615.png)

### 更改配置

把之前的8001和80修改一下,加上依赖:
```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zipkin</artifactId>
        </dependency>
```

然后再添加配置:
```yml
spring:
  application:
    name: cloud-payment-service
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource            # 当前数据源操作类型
    driver-class-name: com.mysql.cj.jdbc.Driver              # mysql驱动包
    url: jdbc:mysql://localhost:3306/db2019?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: "010203"
  zipkin:
    base-url: http://localhost:9411
    sleuth:
      sampler:
        #采样率值介于 0 到 1 之间，1 则表示全部采集
        probability: 1
```

就是这个位置,80也是一样加上`zipkin.base-url`以及其他的


然后各加上一个接口,就不用写了

然后呢?

然后80调用8001,此时就能在`http://localhost:9411/zipkin`中检测到发起的请求信息啥的

## 16 SpringCloud Alibaba 入门简介

>是什么
Spring Cloud Alibaba,它是由一些阿里巴巴的开源组件和云产品组成的,这个项目的目的是为了让大家所熟知的Spring框架,其优秀的设计模式和抽象理念,以给使用阿里巴巴产品的Java开发者带来使用Spring Boot和Spring Cloud的更多便利

这段话怎么读起来这么别扭

[Cloud Alibaba中文文档](https://github.com/alibaba/spring-cloud-alibaba/blob/2022.x/README-zh.md)

![20230823171610](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823171610.png)

好,这一章就是闲聊,没什么代码啥的

## 17 Nacos

### 介绍和下载

>为什么叫nacos
na:Naming,co:Configuration,最后的s为Service(服务注册/配置)

>是什么
一个更易于构建云原生应用的动态服务发现,配置管理和服务管理平台
Nacos=Eureka+Config+Bus

*就把这玩意当成eureka*

>下载
下哪个版本呢?最新版都到2.1.2了,1.x的还有1.4.6,这个好像是稳定一些的,老师用的是1.1.4

下了个1.4.6的,[下载链接](https://github.com/alibaba/nacos/releases/tag/1.4.6)

找到/bin/startup.cmd,却发现报错:
```
"nacos is starting with cluster"
Error occurred during initialization of VM
Could not reserve enough space for 2097152KB object heap
```
说nacos在集群下启动,目前是用单机版的,所以要改配置
```sh
# 找到set MODE="cluster",改为standalone
set MODE="standalone"
```
启动成功:
![20230823202604](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823202604.png)

进入`http://localhost:8848/nacos`
![20230823202814](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823202814.png)

这界面高级啊(账号密码nacos)
![20230823202845](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823202845.png)

### nacos服务注册

父工程已经引入了alibaba,子模块9001只需要引入:
```xml
        <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
```

配置文件中:
```yml
spring:
  application:
    name: nacos-payment-provider
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #配置Nacos地址

```

随便写一个controller接口
```java
/**
 * @author zzmr
 * @create 2023-08-23 20:42
 */
@RestController
public class PaymentController {

    @Value("${server.port}")
    private String serverPort;

    @GetMapping("/payment/nacos/{id}")
    public String getPayment(@PathVariable Integer id) {
        return "nacos registry,serverPort: " + serverPort + "\t id: " + id;
    }

}
```

测试成功
![20230823204602](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823204602.png)

在8848的服务管理里面可以看到服务列表:
![20230823204800](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823204800.png)

根据9001再写一个9002出来:
![20230823205659](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823205659.png)

*还可以拷贝虚拟端口映射,不过好像不太好用*

>消费者服务注册`cloudalibaba-consumer-nacos-order83`,消费者的依赖是和生产者一样的,都是
```xml
        <!--SpringCloud ailibaba nacos -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
```

**nacos自带ribbon的负载均衡效果**

83的配置文件:
```yml
server:
  port: 83


spring:
  application:
    name: nacos-order-consumer
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848


#消费者将要去访问的微服务名称(注册成功进nacos的微服务提供者)
service-url:
  nacos-user-service: http://nacos-payment-provider
```

上面的`service-url`就可以根据服务名,自动实现负载均衡了(其实就是找到服务名为`nacos-payment-provider`的服务,然后自动分配调用)

看看controller
```java
@RestController
@Slf4j
public class OrderNacosController {

    @Resource
    private RestTemplate restTemplate;

    @Value("service-url.nacos-payment-provider")
    private String serverURL;

    @GetMapping("/consumer/payment/nacos/{id}")
    public String paymentInfo(@PathVariable Integer id) {
        return restTemplate.getForObject(serverURL + "/payment/nacos/" + id, String.class);
    }

}
```

也是没有写ip,而是用的服务名

**这里要注意,使用restTemplate要使用配置类**
```java
package com.zzmr.springcloud.config;

import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

/**
 * @auther zzyy
 * @create 2020-02-10 12:01
 */
@Configuration
public class ApplicationContextBean
{
    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate()
    {
        return new RestTemplate();
    }
}
```

启动服务:
![20230823213553](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823213553.png)

哈哈哈哈,老师正说:如果一切顺利..,然后就变成一切不顺利了,原来是没有给restTemplate的get方法上没加`@LoadBalanced`

然后我又重启项目,发现还是报错,最好找到错误在`@Value`的`${}`取值上,取值的名字写错了

即可实现负载均衡:
![20230823214750](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823214750.png)
![20230823214808](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823214808.png)

### 各种注册中心对比

Nacos的是可以AP/CP互换的
![20230823215114](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823215114.png)


对比图:
![20230823215248](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823215248.png)

>Nacos支持AP和CP模式的切换
C是所有节点在同一时间看到的数据是一致的,而A的定义是所有的请求都会收到响应

何时选择使用何种模式?
一般来说
- 如果不需要存储服务级别的信息且服务实例是通过nacos-client注册,并能够保持心跳上报,那么就可以选择AP模式,当前主流的服务如Spring Cloud和Dubbo服务,都适用于AP模式,AP模式为了服务的可用性而减弱了一致性,因此AP模式下只支持注册临时实例
- 如果需要在服务级别编辑或者存储配置信息,那么CP是必须的,K8S服务和DNS服务则适用于CP模式,CP模式下则支持注册持久化实例,此时则是以Raft协议为集群运行模式,该模式下注册实例之前必须先注册服务,如果服务不存在,则会返回错误

那么一般就是看这个图:
![20230823215903](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823215903.png)

### nacos服务配置中心

#### 基础配置

新建模块:`cloudalibaba-config-nacos-client3377`

新的依赖:
```xml
        <!--nacos-config-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
        </dependency>
        <!--nacos-discovery-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
```

就是多了个`nacos-config`

然后配置文件多了,有两个
1. bootstrap.yml
```yml
# nacos配置
server:
  port: 3377

spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #Nacos服务注册中心地址
      config:
        server-addr: localhost:8848 #Nacos作为配置中心地址
        file-extension: yaml #指定yaml格式的配置


  # ${spring.application.name}-${spring.profile.active}.${spring.cloud.nacos.config.file-extension}
```
2. application.yml
```yml
spring:
  profiles:
    active: dev # 表示开发环境
```

**为什么要两个**
Nacos同springcloud-config一样,在项目初始化时,要保证先从配置中心进行配置拉取,拉去配置后,才能保证项目的正常启动

SpringBoot中配置文件的加载是存在优先级顺序的,bootstrap优先级高于application

---

在`Nacos Spring Cloud`中,dataId的完整格式如下
`${prefix}-${spring.profile.active}.${file-extension}`

1. `prefix`默认为`spring.application.name`的值,也可以通过配置项`spring.cloud.nacos.config.prefix`来配置
2. `spring.profile.active`即为当前环境对应的profile,注意:当`spring.profile.active`为空时,对应的连接符-也将不存在,dataId的拼接格式变成`${prefix}.${file-extension}`
3. `file-extension`为配置内容的数据格式,可以通过配置项`spring.cloud.nacos.config.file-extension`来配置,目前支持`properties`和`yaml`类型
![20230823224331](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823224331.png)

所以,按照公式,这个3377拼出来的就是`nacos-config-client-dev.yml`

然后去nacos配置页面配置:
![20230823224113](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823224113.png)

然后就报错了,DataId写成yml,其实要写成yaml,也就是全称

改成这样:
![20230823224715](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823224715.png)

然后访问:
![20230823225654](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230823225654.png)

修改之后,也能动态刷新

#### 分类配置

>问题一:
实际开发中,通常一个系统会准备
- dev开发环境
- test测试环境
- prod生产环境
如何保证指定环境启动时服务能正确读取到Nacos上相应环境的配置文件呢

>问题2:
一个大型分布式微服务系统会有很多微服务子项目,每个微服务项目又都会有相应的开发环境,测试环境,预发环境,正式环境,那么怎么对这些微服务配置进行管理呢

---

默认情况:Namespace=public,Group=DEFAULT_GROUP,默认Cluster是DEFAULT

1. 指定spring.profile.active和配置文件的DataID来使不同环境下读取不同的配置
2. 默认空间+默认分组+新建dev和test两个DataID
3. 通过spring.profile.active属性就能进行多环境下配置文件的读取


>**根据DataID配置**

新的test配置,不同的DataId,但是是同一组的
![20230824101701](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230824101701.png)

这个application.yml
```yml
spring:
  profiles:
#    active: dev # 表示开发环境
    active: test # 表示开发环境
```

激活哪个,就会用哪个

测试成功
![20230824102113](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230824102113.png)


>**根据group来配置**

新建配置:
![20230824102523](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230824102523.png)

再建一个test
![20230824102811](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230824102811.png)

然后更改配置文件:
![20230824103002](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230824103002.png)

指定TEST_GROUP,就会得到TEST
![20230824103112](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230824103112.png)

`group`改成啥就是啥

>**NameSpace**方案

1. 新建dev/test的Namespace
  - ![20230824105704](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230824105704.png)
2. 回到服务管理-服务列表查看
3. 按照域名配置填写,新增`namespace`
```yml
        group: DEV_GROUP
        namespace: a84323ef-99d0-463f-ae99-b3aa7eec8966
```
4. YML


在dev配置中,添加这些配置:
![20230824111022](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230824111022.png)

...

在考虑是不是要去看黑马的cloud的视频

### **Nacos集群和持久化配置**

默认Nacos使用嵌入式数据库实现数据的存储,如果启用多个默认配置下的Nacos节点,数据存储是存在一执行问题的,为了解决这个问题,Nacos采用了集中存储的方式来支持集群化部署,目前只支持MySql的存储

#### Nacos集群和持久化配置

*nacos默认自带的是嵌入式数据库derby*

1. 去nacos的conf文件夹下,运行nacos-mysql.sql脚本,建库表
2. 找到conf下的application.properties,加上
```properties
spring.datasource.platform=mysql
 
db.num=1
db.url.0=jdbc:mysql://127.0.0.1:3306/nacos_config?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
db.user=root
db.password=010203
```

---

重启nacos,就会发现,之前的配置全没了(傻逼的是,那个url要加一堆东西,才能启动起来...)

新建配置:
![20230824120122](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230824120122.png)

然后在config_info表中即可查看到数据:
![20230824120154](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230824120154.png)

---

>**Linux版Nacos+MySql生产环境配置**

这个有点麻烦了,服务器上不知道能不能配

先把linux版的nacos下载下来,然后传输到服务器上,解压
![20230825105114](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230825105114.png)

集群配置步骤:
1. 要用到数据库,看来还是要用docker来创建一个,创建了半天,还是有权限问题,所以把之前的配置文件路径换成新的了,现在是没问题了
2. 编辑配置文件`application.properties`
```txt
spring.datasource.platform=mysql

db.num=1db.url.0=jdbc:mysql://1.14.102.11:3306/nacos_config?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
db.user=root
db.password=123456
```
3. Linux服务器上的nacos的集群配置`cluster.conf`
	- ![20230825112723](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230825112723.png)
	- 配置内容:
	- ![20230825112921](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230825112921.png)
	- 顺便去控制台放行了端口的防火墙:![20230825113258](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230825113258.png)
4. 编辑Nacos的启动脚本`startup.sh`,使它能够接受不同的启动端口 
	- 集群启动,我们希望可以类似其他软件的shell命令,传递不同的端口号启动不同的nacos实例,命令:`./startup.sh -p 3333`表示启动端口号为3333的nacos服务器实例,和上一步的cluster.conf配置的一致
	- 老师的是这个样子的:![20230825114028](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230825114028.png)
	- 而我的新版默认就是四个参数,用不到加`:p`了
	- 最后面改成:![20230825115050](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230825115050.png)

---

配置nginx
1. docker配置nginx:
	- 执行命令:`docker run --name nginx01 -p 80:80 -v /zzmr/nginx/nginx.conf:/etc/nginx/nginx.conf --privileged=true -d nginx`,即可创建一个nginx容器
	- 该nginx的配置文件就在`/etc/nginx`,这里使用了容器卷,因为要改写配置文件,nginx容器中是没有这个编辑功能的,所以只能改好了然后挂载过去
	- ![20230825130236](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230825130236.png)

---

**启动**

直接执行`bash startup.sh -p 3333`,执行成功,另外两台同理
![20230825131057](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230825131057.png)


...

nacos启动不了,一直提示jdk有问题

**暂时先这样?**
2023年8月25日 14点52分