---
title: SpringBoot利用AOP实现记录操作日志
date: 2023-09-20 20:50:10
tags:
  - study
  - AOP
  - SpringBoot
categories: 学习
top_img: /images/bg1.jpg
---

## 利用AOP实现记录操作日志

#### 首先导入AOP依赖

```xml
 <!--aop-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

创建OperateLog实体类 （pojo包）

```java
package com.mpk.project.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.time.LocalDateTime;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class OperateLog {
    private String ipAddress;
    private LocalDateTime operateTime;
    private String className;
    private String methodName;
    private String methodParams;
    private long costTime;
    private String jsonString;

}
```

#### 创建aop.LogAop （aop包）

```java
package com.mpk.project.aop;

import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONObject;
import com.mpk.project.pojo.OperateLog;
import jakarta.servlet.http.HttpServletRequest;
import lombok.extern.slf4j.Slf4j;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import java.time.LocalDateTime;
import java.util.Arrays;

/**
 * @author mpk16
 */
@Slf4j
@Component
@Aspect
public class LogAop {


    @Autowired
    HttpServletRequest request;


    @Around("@annotation(com.mpk.project.anno.Log)")
    public Object recordLog(ProceedingJoinPoint joinPoint) throws Throwable {
        //操作人的IP
        String ipAddress = request.getRemoteAddr();
        //操作时间
        LocalDateTime operateTime = LocalDateTime.now();


        //操作类名
        String className = joinPoint.getTarget().getClass().getName();

        //操作方法名
        String methodName = joinPoint.getSignature().getName();

        //操作方法参数
        Object[] args = joinPoint.getArgs();
        String methodParams = Arrays.toString(args);


        //调用原始目标方法运行
        long begin = System.currentTimeMillis();
        Object result = joinPoint.proceed();
        long end = System.currentTimeMillis();

        //操作耗时
        long costTime = end - begin;
        //方法返回值
        String returnValue = JSONObject.toJSONString(result);


        OperateLog operateLog = new OperateLog(ipAddress, operateTime, className, methodName, 
                methodParams,costTime, returnValue);
        //只是打印了一下未写入数据库中
        System.out.println(operateLog);

        return result;


    }


}
```

##### 使用JSONObject需添加依赖
``` xml
<dependency>
<groupId>com.alibaba</groupId>
<artifactId>fastjson</artifactId>
<version>2.0.21</version>
</dependency>
```

#### 自定义注解anno.Log （anno包下）

```java
package com.mpk.project.anno;


import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Log {
}
```



#### 并标注在需要记录日志的方法上 （Controller层）

```java
@CrossOrigin //跨域
@RestController
@RequiredArgsConstructor
public class LoginController {

   
    private  final  UserService userService;
    
    @Log
    @RequestMapping("/login")
    Result getUser(@RequestBody User user1) {

        User user = userService.getUser(user1);
        if (user != null) {
            HashMap<String, Object> claims = new HashMap<>();
            claims.put("name", user.getName());
            claims.put("gender", user.getGender());
            claims.put("area", user.getArea());
            String jwt = JwtUtils.generateJwt(claims);
            Claims claims1 = JwtUtils.parseJWT(jwt);
            System.out.println(claims1);
            return Result.success(jwt);
        } else {
            return Result.fail();
        }
    }
}
```

#### service层 ,mapper层用mybatis-plus实现 （省略）

