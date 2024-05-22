### Filte过滤器概述和入门

![[Pasted image 20240509233812.png]]

![[Pasted image 20240509233946.png]]

新建一个filter包，里面放DemoFilter案例：

```java
package org.example.filter;  
  
import jakarta.servlet.*;  
import jakarta.servlet.annotation.WebFilter;  
  
import java.io.IOException;  
  
@WebFilter(urlPatterns = "/*")  
public class DemoFilter implements Filter {  
    @Override //初始化方法, 只调用一次  
    public void init(FilterConfig filterConfig) throws ServletException {  
        System.out.println("init 初始化方法执行了");  
    }  
  
    @Override //拦截到请求之后调用, 调用多次  
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {  
        System.out.println("Demo 拦截到了请求...放行前逻辑");  
        //放行  
        chain.doFilter(request,response);  
  
        System.out.println("Demo 拦截到了请求...放行后逻辑");  
    }  
  
    @Override //销毁方法, 只调用一次  
    public void destroy() {  
        System.out.println("destroy 销毁方法执行了");  
    }  
}
```

注：一般只需要实现`doFilter`方法，`init`和`destroy`方法是默认实现的！

### Filter执行流程

![[Pasted image 20240510092021.png]]

![[Pasted image 20240510092204.png]]

![[Pasted image 20240510092655.png]]

![[Pasted image 20240510092747.png|283]]

AbcFilter：

```java
@WebFilter(urlPatterns = "/*")  
public class AbcFilter implements Filter {  
    @Override  
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {  
        System.out.println("Abc 拦截到了请求...放行前逻辑");  
        //放行  
        chain.doFilter(request,response);  
  
        System.out.println("Abc 拦截到了请求...放行后逻辑");  
    }  
}
```

### 用Filter实现登录校验

![[Pasted image 20240510093116.png]]

![[Pasted image 20240510093215.png]]

新建一个LoginCheckFilter类执行过滤功能：

```java
package org.example.filter;  
  
import com.alibaba.fastjson.JSONObject;  
import org.example.pojo.Result;  
import org.example.utils.JwtUtils;  
import jakarta.servlet.*;  
import lombok.extern.slf4j.Slf4j;  
import org.springframework.util.StringUtils;  
import jakarta.servlet.annotation.WebFilter;  
import jakarta.servlet.http.HttpServletRequest;  
import jakarta.servlet.http.HttpServletResponse;  
import java.io.IOException;  
  
@Slf4j  
@WebFilter(urlPatterns = "/*")  
public class LoginCheckFilter implements Filter {  
    @Override  
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {  
        HttpServletRequest req = (HttpServletRequest) request;  //强转  
        HttpServletResponse resp = (HttpServletResponse) response;  
  
        //1.获取请求url。  
        String url = req.getRequestURL().toString();  
        log.info("请求的url: {}",url);  
  
        //2.判断请求url中是否包含login，如果包含，说明是登录操作，放行。  
        if(url.contains("login")){  
            log.info("登录操作, 放行...");  
            chain.doFilter(request,response);  
            return;  
        }  
  
        //3.获取请求头中的令牌（token）。  
        String jwt = req.getHeader("token");  
  
        //4.判断令牌是否存在，如果不存在，返回错误结果（未登录）。  
        if(!StringUtils.hasLength(jwt)){ //检查jwt字符串是否为空和是否为空串  
            log.info("请求头token为空,返回未登录的信息");  
            Result error = Result.error("NOT_LOGIN");  
            //手动转换 对象--json --------> 阿里巴巴fastJSON  
            String notLogin = JSONObject.toJSONString(error);  
            resp.getWriter().write(notLogin);  
            return;  
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
            return;  
        }  
  
        //6.放行。  
        log.info("令牌合法, 放行");  
        chain.doFilter(request, response);  
  
    }  
}
```

执行成功：

![[Pasted image 20240510094900.png]]