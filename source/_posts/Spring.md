---
title: Spring
date: 2023-09-19 20:52:44
tags: 
- spring
- AOP
- 事务管理
categories: 学习
---

## Spring

### 事务管理

事务是一组操作的集合，他是一个不可分割的工作单位，这些操作 要么同事成功，要么同事失败。

### Spring事务管理

- 注解：@Transactional

- 位置：业务（service）层的方法上、类上、接口上

- 作用：将当前方法交给spring进行事务管理，方法执行前，开启事务;成功执行完毕，提交事务;出现异常，回滚事务

​            一般增加在业务的增删改上，而且是多次数据访问

#### 事务进阶

##### rollbackFor

默认情况下，只有出现RuntimeException 才回滚异常。rollbackFor属性用于控制出现何种异常类型，回滚事务。

```java
@Transactional (rollbackFor= Exception.class)
```

##### propagation

事务传播行为:指的就是当一个事务方法被另一个事务方法调用时，这个事务方法应该如何进行事务控制

```java
@Transactional(propagation= Propagation.REQUIRED)
//【默认值】需要事务，有则加入，无则创建新事务
@Transactional(propagation= Propagation.REQUIRES_NEW)
    //需要新事务，无论有无,总是创建新事务
```



```yaml
logging:
  level:
    #开启事务管理日志
    org.springframework.jdbc.support.JdbcTransactionManager: debug
```





### Spring AOP

#### AOP概述

概念:AOP(Aspect Oriented Programming)面向切面编程，一种编程范式作用︰在不惊动原始设计的基础上为方法进行功能增强

##### AOP的应用场景

- 日志记录
- 事务管理
- 权限验证
- 性能监测

#### 快速入门

```xml
	<!--AOP依赖-->
 <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
            <scope>test</scope>
        </dependency>
```

```java
package com.mpk.project.aop;

import lombok.extern.slf4j.Slf4j;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

/**
 * @author mpk
 */
@Slf4j
@Component  //声明当前类为Aspect切面，并交给Spring容器管理
@Aspect
public class AopDemo {

    @Pointcut("execution(* com.mpk.project.service.impl.StudentServiceImpl.* (..))")
    public void pt() {

    }


        //前置通知   
        @Before("pt()")
        public void beforeAdvice(JoinPoint joinPoint){
           log.info( "========== 【Aspectj前置通知 Before】 ==========");
        }


        //后置通知：方法正常执行后，有返回值，执行该后置通知：如果该方法执行出现异常，则不执行该后置通知
        @AfterReturning(value = "pt()",returning = "returnVal")
        public void afterReturningAdvice(JoinPoint joinPoint,Object returnVal){
           log.info("========== 【Aspectj后置通知 AfterReturning】 ==========");
        }

        //后置通知
        @After("pt()")
        public void afterAdvice(JoinPoint joinPoint){
           log.info("========== 【Aspectj后置通知 After】 ==========");
        }

        //环绕通知
        @Around("pt()")
        public Object aroundAdvice(ProceedingJoinPoint joinPoint) throws Throwable {
           log.info("##########【环绕通知中的前置通知 Around】##########");
            Object returnVale = joinPoint.proceed();
           log.info("##########【环绕通知中的后置通知 Around】##########");
            return returnVale;
        }

        // 异常通知：方法出现异常时，执行该通知
        @AfterThrowing(value = "pt()",throwing = "ex")
        public void throwAdvice(JoinPoint joinPoint, Exception ex){
           log.info("********** 【Aspectj异常通知 AfterThrowing】执行开始 **********");
           log.info("出现异常：" + ex.getMessage());
           log.info("********** 【Aspectj异常通知 AfterThrowing】执行结束 **********");
        }

    }

```



#### 核心概念

1. 连接点(joinPoint)

​    2. 切入点([Pointcut](https://so.csdn.net/so/search?q=Pointcut&spm=1001.2101.3001.7020))

​    3. 通知(Advice)

​    4. 通知类

​     5.切面(Aspect)

#### 通知类型

##### Spring中AOP的通知类型

- @Around：环绕通知，此注解标注的通知方法在目标方法前、后都被执行

- @Before：前置通知，此注解标注的通知方法在目标方法前被执行

- @After ：后置通知，此注解标注的通知方法在目标方法后被执行，无论是否有异常都会执行

- @AfterReturning ： 返回后通知，此注解标注的通知方法在目标方法后被执行，有异常不会执行

- @AfterThrowing ： 异常后通知，此注解标注的通知方法发生异常后执行

  ```tex
  - 程序没有发生异常的情况下:
  
  @AfterThrowing标识的通知方法不会执行。
  ```

  ```tex
  - 程序发生异常的情况下：
  
  @AfterThrowing标识的通知方法执行了，@AfterReturning标识的通知方法不会执行
  @Around环绕通知中原始方法调用时有异常，通知中的环绕后的代码逻辑也不会在执行了 （因为原始方法调用已经出异常了）
  ```

##### 注意事项

@Around环绕通知需要自己调用 ProceedingJoinPoint.proceed() 来让原始方法执行，其他通知不需要考虑目标方法执行
@Around环绕通知方法的返回值，必须指定为Object，来接收原始方法的返回值，否则原始方法执行完毕，是获取不到返回值的。

#### 切入点表达式

##### 2.1 语法格式

切入点表达式标准式：动作关键字(访问修饰符 返回值 包名.类/接口名.方法名(参数) 异常名)

对于这个格式，我们不需要硬记，通过一个例子，理解它:

```
execution(public User com.itheima.service.UserService.findById(java.lang.Intager))
```



- execution：动作关键字，描述切入点的行为动作，例如execution表示执行到指定切入点
- public: 访问修饰符,还可以是public，private等，可以省略
- User：返回值，写返回值类型
- com.itheima.service：包名，多级包使用点连接
- UserService:类/接口名称
- findById：方法名
- int:参数，直接写参数的类型，多个类型用逗号隔开
- 异常名：方法定义中抛出指定异常，可以省略

##### 2.2通配符

*****：单个独立的任意符号。可以独立出现，也可以作为前缀或者后缀的匹配符出现

```
execution（public * com.itheima.*.UserService.find*(*))
```


匹配com.itheima包下的任意包中的UserService类或接口中所有find开头的带有一个参数的方法

**..**：多个连续的任意符号，可以独立出现，常用于简化包名与参数的书写

```
execution（public User com..UserService.findById(..))
```

匹配com包下的任意包中的UserService类或接口中所有名称为findById的方法。参数任意，可以带，可以不带，可以带多个。



#### 连接点

连接点是可以被AOP控制的方法。

- 对于@Around通知，获取连接点信息只能使用ProceedingJoinPoint类型
- 对于其他四种通知，获取连接点信息只能使用JoinPoint，它是ProceedingJoinPoint的父类型









#### 
