### JWT简介

![[Pasted image 20240509203157.png]]

![[Pasted image 20240509203225.png]]

### JWT生成

![[Pasted image 20240509203338.png]]


```java
/**  
 * 生成JWT  
 */@Test  
public void testGenJwt(){  
    Map<String, Object> claims = new HashMap<>();  
    claims.put("id",1);  
    claims.put("name","tom");  
  
    String jwt = Jwts.builder()  
            .signWith(SignatureAlgorithm.HS256, "hlxchamplalalalalalalalalalalalalalalalalalalala")//签名算法,要长一点 
            .setClaims(claims) //自定义内容(载荷)  
            .setExpiration(new Date(System.currentTimeMillis() + 3600 * 1000))//设置有效期为1h  
            .compact();  
    System.out.println(jwt);  
}
```

![[Pasted image 20240509205255.png]]

```java
/**  
 * 解析JWT  
 */@Test  
public void testParseJwt(){  
    Claims claims = Jwts.parser()  
            .setSigningKey("hlxchamplalalalalalalalalalalalalalalalalalalala")  
            .parseClaimsJws("eyJhbGciOiJIUzI1NiJ9.eyJuYW1lIjoidG9tIiwiaWQiOjEsImV4cCI6MTcxNTI2MjczNn0.o56TqXeFYLC5q4hF02fh9uTCnHJ0kMz2_2HYgHovuK0")  
            .getBody();  
    System.out.println(claims);  
}
```

![[Pasted image 20240509210720.png|448]]

注：若出现parseClaimsJws找不到的报错，可以把依赖降低：

```xml
<dependency>  
<groupId>io.jsonwebtoken</groupId>  
<artifactId>jjwt</artifactId>  
<version>0.9.0</version>  
</dependency>
```

### 登陆后下发令牌

![[Pasted image 20240509232025.png]]

引入一个JwtUtils工具类：

```java
package org.example.utils;  
  
import io.jsonwebtoken.Claims;  
import io.jsonwebtoken.Jwts;  
import io.jsonwebtoken.SignatureAlgorithm;  
import java.util.Date;  
import java.util.Map;  
  
public class JwtUtils {  
  
    private static String signKey = "itheima";  
    private static Long expire = 43200000L;  
  
    /**  
     * 生成JWT令牌  
     * @param claims JWT第二部分负载 payload 中存储的内容  
     * @return  
     */  
    public static String generateJwt(Map<String, Object> claims){  
        String jwt = Jwts.builder()  
                .addClaims(claims)  
                .signWith(SignatureAlgorithm.HS256, signKey)  
                .setExpiration(new Date(System.currentTimeMillis() + expire))  
                .compact();  
        return jwt;  
    }  
  
    /**  
     * 解析JWT令牌  
     * @param jwt JWT令牌  
     * @return JWT第二部分负载 payload 中存储的内容  
     */  
    public static Claims parseJWT(String jwt){  
        Claims claims = Jwts.parser()  
                .setSigningKey(signKey)  
                .parseClaimsJws(jwt)  
                .getBody();  
        return claims;  
    }  
}
```

改写LoginController：

```java
@PostMapping("/login")  
public Result login(@RequestBody Emp emp) {  
    log.info("员工登录：{}",emp);  
    Emp e = empService.login(emp); //返回值就是一个员工对象  
  
    //登陆成功，生成令牌，下发令牌  
    if(e != null){  
        Map<String, Object> claims = new HashMap<>();  
        claims.put("id", e.getId());  
        claims.put("name", e.getName());  
        claims.put("username", e.getUsername());  
  
        String jwt = JwtUtils.generateJwt(claims); //jwt包含了当前登录的员工信息  
        return Result.success(jwt);  
    }  
    //登陆失败，返回错误信息  
    return Result.error("用户名或密码错误");  
}

```

![[Pasted image 20240509232816.png]]

下面就是要拦截啦！

![[Pasted image 20240509233156.png]]