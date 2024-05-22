### 入门

![[Pasted image 20240510095204.png]]

![[Pasted image 20240510095312.png]]

新建一个interceptor包，在其中建一个LoginCheckInterceptor类：

```java
package org.example.interceptor;  
  
import com.alibaba.fastjson.JSONObject;  
import jakarta.servlet.http.HttpServletRequest;  
import jakarta.servlet.http.HttpServletResponse;  
import lombok.extern.slf4j.Slf4j;  
import org.example.pojo.Result;  
import org.example.utils.JwtUtils;  
import org.springframework.stereotype.Component;  
import org.springframework.util.StringUtils;  
import org.springframework.web.servlet.HandlerInterceptor;  
import org.springframework.web.servlet.ModelAndView;  
  
@Slf4j  
@Component  
public class LoginCheckInterceptor implements HandlerInterceptor {  
    @Override //目标资源方法运行前运行, 返回true: 放行, 放回false, 不放行  
    public boolean preHandle(HttpServletRequest req, HttpServletResponse resp, Object handler) throws Exception {  
        //1.获取请求url。  
        String url = req.getRequestURL().toString();  
        log.info("请求的url: {}",url);  
  
        //2.判断请求url中是否包含login，如果包含，说明是登录操作，放行。  
        if(url.contains("login")){  
            log.info("登录操作, 放行...");  
            return true;  
        }  
  
    }  
  
    @Override //目标资源方法运行后运行  
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {  
        System.out.println("postHandle ...");  
    }  
  
    @Override //视图渲染完毕后运行, 最后运行  
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {  
        System.out.println("afterCompletion...");  
    }  
  
}
```

还要新建一个config包，建一个配置类WebConfig：

```java
package org.example.config;  
  
import org.example.interceptor.LoginCheckInterceptor;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.context.annotation.Configuration;  
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;  
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;  
  
@Configuration // 配置类  
public class WebConfig implements WebMvcConfigurer {  
    @Autowired  
    private LoginCheckInterceptor loginCheckInterceptor;  
  
    @Override  
    public void addInterceptors(InterceptorRegistry registry) {  
        registry.addInterceptor(loginCheckInterceptor).addPathPatterns("/**").excludePathPatterns("/login");  
    }  
}
```

### 详解

![[Pasted image 20240510151114.png]]

![[Pasted image 20240510151948.png]]

### 实现登录校验

和过滤器Filter的逻辑差不多，只是放行和不放行的逻辑不同，这里放行`return true;`不放行则`return false;`

```java
@Override //目标资源方法运行前运行, 返回true: 放行, 放回false, 不放行  
public boolean preHandle(HttpServletRequest req, HttpServletResponse resp, Object handler) throws Exception {  
    //1.获取请求url。  
    String url = req.getRequestURL().toString();  
    log.info("请求的url: {}",url);  
  
    //2.判断请求url中是否包含login，如果包含，说明是登录操作，放行。  
    if(url.contains("login")){  
        log.info("登录操作, 放行...");  
        return true;  
    }  
  
    //3.获取请求头中的令牌（token）。  
    String jwt = req.getHeader("token");  
  
    //4.判断令牌是否存在，如果不存在，返回错误结果（未登录）。  
    if(!StringUtils.hasLength(jwt)){  
        log.info("请求头token为空,返回未登录的信息");  
        Result error = Result.error("NOT_LOGIN");  
        //手动转换 对象--json --------> 阿里巴巴fastJSON  
        String notLogin = JSONObject.toJSONString(error);  
        resp.getWriter().write(notLogin);  
        return false;  
    }  
  
    //5.解析token，如果解析失败，返回错误结果（未登录）。  
    try {  
        JwtUtils.parseJWT(jwt);  
    } catch (Exception e) {//jwt解析失败  
        e.printStackTrace();  
        log.info("解析令牌失败, 返回未登录错误信息");  
        Result error = Result.error("NOT_LOGIN");  
        //手动转换 对象--json --------> 阿里巴巴fastJSON  
        String notLogin = JSONObject.toJSONString(error);  
        resp.getWriter().write(notLogin);  
        return false;  
    }  
  
    //6.放行。  
    log.info("令牌合法, 放行");  
    return true;  
}
```
