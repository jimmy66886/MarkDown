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



