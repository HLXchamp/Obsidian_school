![[Pasted image 20240512161434.png]]

![[Pasted image 20240512161610.png]]

![[Pasted image 20240512173335.png]]

![[Pasted image 20240512173653.png|183]]

![[Pasted image 20240512173716.png|188]]

LogAspect：

```java
package org.example.aop;  
  
import com.alibaba.fastjson.JSONObject;  
import io.jsonwebtoken.Claims;  
import jakarta.servlet.http.HttpServletRequest;  
import lombok.extern.slf4j.Slf4j;  
import org.aspectj.lang.ProceedingJoinPoint;  
import org.aspectj.lang.annotation.Around;  
import org.aspectj.lang.annotation.Aspect;  
import org.example.mapper.OperateLogMapper;  
import org.example.pojo.OperateLog;  
import org.example.utils.JwtUtils;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.stereotype.Component;  
  
import java.time.LocalDateTime;  
import java.util.Arrays;  
  
@Slf4j  
@Component  
@Aspect //切面类  
public class LogAspect {  
  
    @Autowired  
    private HttpServletRequest request;  
  
    @Autowired  
    private OperateLogMapper operateLogMapper;  
  
    @Around("@annotation(org.example.anno.Log)")  
    public Object recordLog(ProceedingJoinPoint joinPoint) throws Throwable {  
        //操作人ID - 当前登录员工ID  
        //获取请求头中的jwt令牌, 解析令牌  
        String jwt = request.getHeader("token");  
        Claims claims = JwtUtils.parseJWT(jwt);  
        Integer operateUser = (Integer) claims.get("id");  
  
        //操作时间  
        LocalDateTime operateTime = LocalDateTime.now();  
  
        //操作类名  
        String className = joinPoint.getTarget().getClass().getName();  
  
        //操作方法名  
        String methodName = joinPoint.getSignature().getName();  
  
        //操作方法参数  
        Object[] args = joinPoint.getArgs();  
        String methodParams = Arrays.toString(args);  
  
        long begin = System.currentTimeMillis();  
        //调用原始目标方法运行  
        Object result = joinPoint.proceed();  
        long end = System.currentTimeMillis();  
  
        //方法返回值  
        String returnValue = JSONObject.toJSONString(result);  
  
        //操作耗时  
        Long costTime = end - begin;  
  
  
        //记录操作日志  
        OperateLog operateLog = new OperateLog(null,operateUser,operateTime,className,methodName,methodParams,returnValue,costTime);  
        operateLogMapper.insert(operateLog);  
  
        log.info("AOP记录操作日志: {}" , operateLog);  
  
        return result;  
  
    }  
}
```

在对应的Controller上加上@Log注解就行，DeptContoller和EmpController上加。

![[Pasted image 20240512173414.png]]


##### 还有一个案例见苍穹外卖[[1、公共字段自动填充]]。