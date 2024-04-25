
![[Pasted image 20240422195155.png]]

```java
/**
 * HelloController是一个Spring Boot控制器类。
 * @RestController注解表明这是一个RESTful风格的控制器，它会自动将方法的返回值转换为JSON格式。
 */
@RestController
public class HelloController {
    // sayHello方法处理对"/hello"端点的HTTP GET请求。
    @RequestMapping("/hello")
    public String sayHello() {
        // 打印"Hello World!"到控制台
        System.out.printf("Hello World!\n");
        // 返回"Hello World!"字符串作为HTTP响应体
        return "Hello World!";
    }
}
```

![[Pasted image 20240422202936.png|500]]

![[Pasted image 20240422202952.png]]

![[Pasted image 20240422230841.png]]

![[Pasted image 20240422230918.png]]
