### 通知类型

![[Pasted image 20240511162035.png]]

出现异常后，@Around的原始方法后的部分不会被执行！

![[Pasted image 20240511162733.png]]

![[Pasted image 20240511162940.png]]


```java
@Slf4j  
@Component  
@Aspect  
public class MyAspect1 {  
  
    @Pointcut("execution(* com.itheima.service.impl.DeptServiceImpl.*(..))")  
    public void pt(){}  
  
    @Before("pt()")  
    public void before(){  
        log.info("before ...");  
    }  
  
    @Around("pt()")  
    public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {  
        log.info("around before ...");  
  
        //调用目标对象的原始方法执行  
        Object result = proceedingJoinPoint.proceed();  
  
        log.info("around after ...");  
        return result;  
    }  
  
    @After("pt()")  
    public void after(){  
        log.info("after ...");  
    }  
  
    @AfterReturning("pt()")  
    public void afterReturning(){  
        log.info("afterReturning ...");  
    }  
  
    @AfterThrowing("pt()")  
    public void afterThrowing(){  
        log.info("afterThrowing ...");  
    }  
}
```


### 通知顺序

![[Pasted image 20240511163541.png]]

不加@Order：

![[Pasted image 20240511163603.png|475]]

加了@Order：

![[Pasted image 20240511163649.png|475]]

### 切入点表达式

#### execution

![[Pasted image 20240511164205.png]]


```java
@Pointcut("execution(public void com.itheima.service.impl.DeptServiceImpl.delete(java.lang.Integer))")  
@Pointcut("execution(void com.itheima.service.impl.DeptServiceImpl.delete(java.lang.Integer))")  
@Pointcut("execution(void delete(java.lang.Integer))") //包名.类名不建议省略  
@Pointcut("execution(void com.itheima.service.DeptService.delete(java.lang.Integer))")
```


![[Pasted image 20240511164234.png]]


```java
@Pointcut("execution(void com.itheima.service.DeptService.*(java.lang.Integer))")  
@Pointcut("execution(* com.*.service.DeptService.*(*))")  
@Pointcut("execution(* com.itheima.service.*Service.delete*(*))")  

@Pointcut("execution(* com.itheima.service.DeptService.*(..))")  
@Pointcut("execution(* com..DeptService.*(..))")  
@Pointcut("execution(* com..*.*(..))")  
@Pointcut("execution(* *(..))") //慎用
```

![[Pasted image 20240511164937.png]]

若要关联两个方法，可以用`||`符合关联：

```java
@Pointcut("execution(* com.itheima.service.DeptService.list()) || " +  
        "execution(* com.itheima.service.DeptService.delete(java.lang.Integer))")
```

![[Pasted image 20240511165003.png]]
#### @annotation

![[Pasted image 20240511165339.png]]

需要**配合注解**使用！

新建一个MyLog注解：

```java
package com.itheima.aop;  
  
import java.lang.annotation.ElementType;  
import java.lang.annotation.Retention;  
import java.lang.annotation.RetentionPolicy;  
import java.lang.annotation.Target;  
  
@Retention(RetentionPolicy.RUNTIME) //表示这个注解什么时候生效  
@Target(ElementType.METHOD) // 表示在哪里生效，这里是在方法上  
public @interface MyLog {  
}
```

MyAspect7类：

```java
//切面类  
@Slf4j  
//@Aspect  
@Component  
public class MyAspect7 {  
  
    //匹配DeptServiceImpl中的 list() 和 delete(Integer id)方法  
    //@Pointcut("execution(* com.itheima.service.DeptService.list()) || execution(* com.itheima.service.DeptService.delete(java.lang.Integer))")  
    @Pointcut("@annotation(com.itheima.aop.MyLog)")  
    private void pt(){}  
  
    @Before("pt()")  
    public void before(){  
        log.info("MyAspect7 ... before ...");  
    }  
  
}
```

然后就可以在对应方法上加上这个注解了，有@MyLog就可以执行：

```java
//查询全部部门  
@MyLog  
@GetMapping  
public Result list(){  
    List<Dept> deptList = deptService.list();  
    return Result.success(deptList);  
}  
  
//删除部门  
@MyLog  
@DeleteMapping("/{id}")  
public Result delete(@PathVariable Integer id)  {  
    deptService.delete(id);  
    return Result.success();  
}
```

### 连接点

![[Pasted image 20240511172318.png]]


```java
//切面类  
@Slf4j  
@Aspect  
@Component  
public class MyAspect8 {  
  
    @Pointcut("execution(* com.itheima.service.DeptService.*(..))")  
    private void pt(){}  
  
    @Before("pt()")  
    public void before(JoinPoint joinPoint){  
        log.info("MyAspect8 ... before ...");  
    }  
  
    @Around("pt()")  
    public Object around(ProceedingJoinPoint joinPoint) throws Throwable {  
        log.info("MyAspect8 around before ...");  
  
        //1. 获取 目标对象的类名 .        
        String className = joinPoint.getTarget().getClass().getName();  
        log.info("目标对象的类名:{}", className);  
  
        //2. 获取 目标方法的方法名 .        
        String methodName = joinPoint.getSignature().getName();  
        log.info("目标方法的方法名: {}",methodName);  
  
        //3. 获取 目标方法运行时传入的参数 .        
        Object[] args = joinPoint.getArgs();  
        log.info("目标方法运行时传入的参数: {}", Arrays.toString(args));  
  
        //4. 放行 目标方法执行 .        
        Object result = joinPoint.proceed();  
  
        //5. 获取 目标方法运行的返回值 .        
        log.info("目标方法运行的返回值: {}",result);  
  
        log.info("MyAspect8 around after ...");  
        return result;  
    }  
}
```

![[Pasted image 20240511172431.png|500]]

![[Pasted image 20240511172442.png|625]]
