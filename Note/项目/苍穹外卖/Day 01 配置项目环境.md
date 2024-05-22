### 前端搭建

分支名nginx启动的路径上不能有中文！

### 版本控制

使用git，git checkout的路径上也最好不要有中文！

![[Pasted image 20240514000447.png|300]]

在.gitignore文件里写上不要git上传的文件：

![[Pasted image 20240514000355.png|500]]

### 项目结构

![[Pasted image 20240408235425.png|575]]
![[Pasted image 20240409000021.png|425]]
![[Pasted image 20240409000035.png|625]]
![[Pasted image 20240409000342.png|625]]

### 数据库搭建

![[Pasted image 20240410204321.png]]

导入数据库看这里：[navicat导入sql数据库文件的简单操作步骤](https://blog.csdn.net/m0_52861000/article/details/128527234)
navicat忘记密码：[忘记密码的操作]((https://blog.csdn.net/weixin_43895532/article/details/131232311)

数据库设计文档见[[数据库设计文档]]。

### 前后端联调

![[Pasted image 20240514091708.png]]

![[Pasted image 20240514092804.png]]

#### nginx反向代理与负载均衡

![[Pasted image 20240514092959.png|700]]

![[Pasted image 20240514093136.png|575]]

![[Pasted image 20240514093317.png]]

![[Pasted image 20240514093353.png|625]]

![[Pasted image 20240514093430.png|625]]

### 完善登陆操作

![[Pasted image 20240514093809.png]]

![[Pasted image 20240514095358.png]]

![[Pasted image 20240514095437.png]]

idea可以查看TODO：

![[Pasted image 20240514095948.png|600]]

先在数据库中把密码换成MD5加密后的，然后在mapper判断用户输入的密码转化为MD5加密后与数据库中的字段是否匹配。

```java
package com.sky.service.impl;  
  
@Service  
public class EmployeeServiceImpl implements EmployeeService {  
  
    @Autowired  
    private EmployeeMapper employeeMapper;  
  
    /**  
     * 员工登录  
     *  
     * @param employeeLoginDTO  
     * @return  
     */  
    public Employee login(EmployeeLoginDTO employeeLoginDTO) {  
        String username = employeeLoginDTO.getUsername();  
        String password = employeeLoginDTO.getPassword();  
  
        //1、根据用户名查询数据库中的数据  
        Employee employee = employeeMapper.getByUsername(username);  
  
        //2、处理各种异常情况（用户名不存在、密码不对、账号被锁定）  
        if (employee == null) {  
            //账号不存在  
            throw new AccountNotFoundException(MessageConstant.ACCOUNT_NOT_FOUND);  
        }  
  
        //密码比对  
        //进行md5加密，然后再进行比对  
        password = DigestUtils.md5DigestAsHex(password.getBytes());  
  
        if (!password.equals(employee.getPassword())) {  
            //密码错误  
            throw new PasswordErrorException(MessageConstant.PASSWORD_ERROR);  
        }  
  
        if (employee.getStatus() == StatusConstant.DISABLE) {  
            //账号被锁定  
            throw new AccountLockedException(MessageConstant.ACCOUNT_LOCKED);  
        }  
  
        //3、返回实体对象  
        return employee;  
    }  
  
}
```

### 导入接口文档

![[Pasted image 20240514101606.png|525]]

在这个网址导入：[YApi Pro-高效、易用、功能强大的可视化接口管理平台](https://yapi.pro/project/387553/interface/api)

![[Pasted image 20240514102427.png]]

### Swagger

![[Pasted image 20240514102557.png]]

![[Pasted image 20240514102746.png]]

![[Pasted image 20240514102821.png]]

![[Pasted image 20240514103201.png]]

可以直接调试：

![[Pasted image 20240514103442.png]]

![[Pasted image 20240514103515.png|475]]

#### Swageer常用注解

![[Pasted image 20240514103548.png]]

```java
@Api(tags = "员工相关接口")  
public class EmployeeController 

@ApiOperation(value = "员工登录")  
public Result<EmployeeLoginVO> login(@RequestBody EmployeeLoginDTO employeeLoginDTO)

@ApiModel(description = "员工登录返回的数据格式")  
public class EmployeeLoginVO implements Serializable {  
  
    @ApiModelProperty("主键值")  
    private Long id;
}
```

注：Api这些注解里可以直接写文字，value这些可以省略，但**tags不能省略！**

![[Pasted image 20240514104657.png]]