### bean的获取

![[Pasted image 20240512190328.png]]

注：用`getBean`方法时，一定要注入IOC容器对象`ApplicationContext`！

```java
@SpringBootTest
class SpringbootWebConfig2ApplicationTests {

    @Autowired
    private ApplicationContext applicationContext; //IOC容器对象

    //获取bean对象
    @Test
    public void testGetBean(){
        //根据bean的名称获取
        DeptController bean1 = (DeptController) applicationContext.getBean("deptController"); //将Object类型转化成deptController类型
        System.out.println(bean1);

        //根据bean的类型获取
        DeptController bean2 = applicationContext.getBean(DeptController.class);
        System.out.println(bean2);

        //根据bean的名称 及 类型获取
        DeptController bean3 = applicationContext.getBean("deptController", DeptController.class);
        System.out.println(bean3);
    }
}
```

默认是单例：

![[Pasted image 20240512190657.png|450]]
### bean的作用域

![[Pasted image 20240512190952.png]]

### 第三方bean

![[Pasted image 20240512191359.png]]

推荐新建一个配置文件，把第三方bean配置在启动类当中：

```java
package com.itheima.config;

import com.itheima.service.DeptService;
import org.dom4j.io.SAXReader;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration //配置类
public class CommonConfig {

    //声明第三方bean
    @Bean //将当前方法的返回值对象交给IOC容器管理, 成为IOC容器bean
          //通过@Bean注解的name/value属性指定bean名称, 如果未指定, 默认是方法名
    public SAXReader reader(DeptService deptService){
        System.out.println(deptService);
        return new SAXReader();
    }
}
```
