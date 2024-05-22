### AOP概述

![[Pasted image 20240511152343.png]]

![[Pasted image 20240511152404.png]]

### 快速入门

![[Pasted image 20240511152707.png]]

TimeAspect：

```java
package com.itheima.aop;  
  
import lombok.extern.slf4j.Slf4j;  
import org.aspectj.lang.ProceedingJoinPoint;  
import org.aspectj.lang.annotation.Around;  
import org.aspectj.lang.annotation.Aspect;  
import org.springframework.stereotype.Component;  
  
@Slf4j  
@Component  
@Aspect //当前类是AOP类  
public class TimeAspect {  
  
    @Around("execution(* com.itheima.service.*.*(..))")  
    public Object recordTime(ProceedingJoinPoint joinPoint) throws Throwable {  
        //1. 记录开始时间  
        long begin = System.currentTimeMillis();  
  
        //2. 调用原始方法运行  
        Object result = joinPoint.proceed();  
  
        //3. 记录结束时间, 计算方法执行耗时  
        long end = System.currentTimeMillis();  
        log.info(joinPoint.getSignature()+"方法执行耗时: {}ms", end-begin); //拿到执行方法的签名  
  
        return result;  
    }  
}

```

统计信息：
![[Pasted image 20240511154634.png|525]]

![[Pasted image 20240511154810.png]]

### AOP核心功能

![[Pasted image 20240511161309.png]]

![[Pasted image 20240511161510.png]]

最终在Controller中注入的其实是代理对象！代理对象会进行功能增强，执行对应操作后再执行原始操作。