
![[Pasted image 20240507094925.png]]

先在xml文件加上下面语句（可以在mybatis中文网上找）：

```xml
<?xml version="1.0" encoding="UTF-8" ?>  
<!DOCTYPE mapper  
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"  
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
```

![[Pasted image 20240507094831.png]]

![[Pasted image 20240507094800.png]]

执行成功！

可以安装以下插件简化操作：

![[Pasted image 20240507095120.png]]

**使用Mybatis的注解，主要是来完成一些简单的增删改查功能。如果需要实现复杂的SQL功能，建议使用XML来配置映射语句！**