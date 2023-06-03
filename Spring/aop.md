# AOP的使用

还是重新看一遍这个吧,现在是完全不会好吧

## 注解实现AOP

>首先是applicationContext.xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:app="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">


    <!--    注解扫描包-->
    <context:component-scan base-package="com.zzmr.aop.annotation"/>

    <!--开启基于注解的aop-->
    <app:aspectj-autoproxy/>

</beans>
```

主要就是那一行`<app:aspectj-autoproxy/>`

>接口:

```java
package com.zzmr.aop.annotation;

public interface Calculator {

    int add(int i, int j);

    int sub(int i, int j);

    int mul(int i, int j);

    int div(int i, int j);
}
```

>接口实现类:

```java
package com.zzmr.aop.annotation;

import org.springframework.stereotype.Component;

@Component
public class CalculatorImpl implements Calculator {
    @Override
    public int add(int i, int j) {
        int result = i + j;
        System.out.println("方法内部：result:" + result);
        return result;
    }

    @Override
    public int sub(int i, int j) {
        int result = i - j;
        System.out.println("方法内部：result:" + result);
        return result;
    }

    @Override
    public int mul(int i, int j) {
        int result = i * j;
        System.out.println("方法内部：result:" + result);
        return result;
    }

    @Override
    public int div(int i, int j) {
        int result = i / j;
        System.out.println("方法内部：result:" + result);
        return result;
    }
}
```


切面类:
```java
package com.zzmr.aop.annotation;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.Signature;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

import java.util.Arrays;

@Aspect
@Component
public class LoggerAspect {

    @Pointcut("execution(* com.zzmr.aop.annotation.CalculatorImpl.*(..))")
    public void pointCut() {
    }

    @Before("pointCut()")
    public void beforeAdviceMethod(JoinPoint joinPoint) {
        Signature signature = joinPoint.getSignature();
        Object[] args = joinPoint.getArgs();
        System.out.println("LoggerAspect:前置通知" + "方法:" + signature.getName() + "  参数:" + Arrays.toString(args));
    }

    @After("pointCut()")
    public void afterAdviceMethod(JoinPoint joinPoint) {
        Signature signature = joinPoint.getSignature();
        Object[] args = joinPoint.getArgs();
        System.out.println("LoggerAspect:后置通知" + "方法:" + signature.getName() + "  参数:" + Arrays.toString(args));
    }

    // 要想在返回通知中获取目标对象方法的返回值，只需要通过@AfterReturning注解的returning属性
    // 就可以将通知啊方法的某个参数指定为接收目标对象方法的返回值的参数
    @AfterReturning(value = "pointCut()", returning = "result")
    public void afterReturningAdviceMethod(JoinPoint joinPoint, Object result) {
        Signature signature = joinPoint.getSignature();
        System.out.println("LoggerAspect:返回通知" + "方法:" + signature.getName() + ",结果：" + result);
    }

    @AfterThrowing(value = "pointCut()", throwing = "ex")
    public void afterThrowingAdviceMethod(JoinPoint joinPoint, Throwable ex) {
        Signature signature = joinPoint.getSignature();
        System.out.println("LoggerAspect:异常通知" + "方法:" + signature.getName() + "异常:" + ex);
    }

    @Around(value = "pointCut()")
    public Object aroundAdviceMethod(ProceedingJoinPoint joinPoint) {
        Object result = null;
        Object[] args = joinPoint.getArgs();
        try {
            System.out.println("环绕通知:前置通知的位置" + "参数:" + Arrays.toString(args));
            result = joinPoint.proceed();
            System.out.println("环绕通知:返回通知的位置" + "结果:" + result);
        } catch (Throwable e) {
            e.printStackTrace();
            System.out.println("环绕通知:异常通知的位置" + "异常为:" + e);
        } finally {
            System.out.println("环绕通知:后置通知的位置");
        }
        return result;
    }


}
```

>测试代码:

```java
package com.zzmr.aop.annotation;

import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class AspectTest {


    @Test
    public void testBean() {
        ApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");
        Calculator calculator = ioc.getBean(Calculator.class);
        int result1 = calculator.div(4, 3);
        // int result2 = calculator.sub(1, 1);
        // int result3 = calculator.mul(1, 1);
        // int result4 = calculator.div(1, 1);
    }

}

```


## 注意事项

1. 添加代理对象之后,不能直接获取目标对象了,而是要使用目标对象的接口(代理对象)来获取
```java
@Test
public void testBefore(){
    ApplicationContext ioc = new ClassPathXmlApplicationContext("aop-annotation.xml");
    // CalculatorImpl calculator = ioc.getBean(CalculatorImpl.class);  这么写会报错-NoSuchBeanDefinitionException
    Calculator calculator = ioc.getBean(Calculator.class);
    int result = calculator.add(1, 1);
}
```

2. 重用切入点表达式,使用`@PointCut()`注解
```java
    /*
        第一个 * 表示任意的修饰符和任意的返回类型
        第二个 * 表示类中的所有方法
        最后括号中的两个点表示任意的参数类型
    */
    @Pointcut("execution(* com.zzmr.aop.annotation.CalculatorImpl.*(..))")
    public void pointCut() {
    }
```

3. JoinPoint和signature以及args
    - JoinPoint:连接点,可以获取签名信息:`joinPoint.getSignature()`
    - signature:签名信息,可以通过它获取方法名`signature.getName()`
    - args:参数列表,通过joinPoint.getArgs()获取

4. 每个通知方法的参数有joinPoint和result,result就是方法的返回值
5. 环绕通知比较特殊,是一个通知包含了所有其他的通知:
```java
    @Around(value = "pointCut()")
    public Object aroundAdviceMethod(ProceedingJoinPoint joinPoint) {
        Object result = null;
        Object[] args = joinPoint.getArgs();
        try {
            System.out.println("环绕通知:前置通知的位置" + "参数:" + Arrays.toString(args));
            result = joinPoint.proceed();
            System.out.println("环绕通知:返回通知的位置" + "结果:" + result);
        } catch (Throwable e) {
            e.printStackTrace();
            System.out.println("环绕通知:异常通知的位置" + "异常为:" + e);
        } finally {
            System.out.println("环绕通知:后置通知的位置");
        }
        return result;
    }
```
其中的`joinPoint.proceed()`就表示方法执行