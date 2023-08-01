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
