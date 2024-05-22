### 问题一览

**出现如下报错：**

![[Pasted image 20240504003457.png]]

需要手动改一下报错该列的编码格式，就行：

```SQL
alter table dept change name name varchar(10) character set utf8;

insert into dept (id, name, create_time, update_time) values(1,'学工部',now(),now()),(2,'教研部',now(),now()),(3,'咨询部',now(),now()), (4,'就业部',now(),now()),(5,'人事部',now(),now());
```

**出现中文乱码，将下面properties和全局改成UTF-8编码：**

![[Pasted image 20240504103025.png]]

**设置Mysql提示：**
选中代码段，右键点击显示上下文操作，然后点击语言注入设置，选择Mysql。

![[Pasted image 20240504104603.png|450]]
![[Pasted image 20240504104625.png|550]]

**导入项目报错：**

![[Pasted image 20240504110020.png]]
 file->setting->Buile,Execution,Deployment->Maven->Runner

勾选:Delegate IDE build/run actions to Maven

![[Pasted image 20240504110005.png]]

**在test中新增数据会重复执行：**

![[Pasted image 20240504150527.png]]

去设置里把跳过测试勾选就行：

![[Pasted image 20240504150448.png]]

新增成功：

![[Pasted image 20240504150648.png]]
### 删除操作：

![[Pasted image 20240504111220.png]]

![[Pasted image 20240504111245.png|425]]

注：删除操作有返回值，返回的是这次删除操作所删掉的个数！
   
![[Pasted image 20240504111715.png]]

### 配置日志

```properties
#配置Mybatis的日志，指定输出到控制台  
mybatis.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
```

![[Pasted image 20240504112154.png]]
##### 预编译SQL

![[Pasted image 20240504112432.png]]

![[Pasted image 20240504112926.png|650]]

![[Pasted image 20240504113031.png]]

推荐`#{...}`！

### 新增操作

![[Pasted image 20240504145535.png]]

![[Pasted image 20240504145936.png]]

![[Pasted image 20240504145625.png|575]]

![[Pasted image 20240504145921.png]]

### 更新操作

![[Pasted image 20240504152824.png]]

![[Pasted image 20240504152843.png]]

![[Pasted image 20240504153055.png|475]]

### 查询操作

#### 根据ID查询

![[Pasted image 20240504153456.png]]

![[Pasted image 20240504153902.png|400]]

![[Pasted image 20240504153932.png|327]]
##### 数据封装：

![[Pasted image 20240504153743.png]]

![[Pasted image 20240504153809.png]]

```properties
#开启Mybatis的驼峰命名法自动映射开关  
mybatis.configuration.map-underscore-to-camel-case=true
```

#### 条件查询

![[Pasted image 20240507092257.png]]

用`concat`函数进行sql语句中的字符串拼接！

![[Pasted image 20240504165234.png]]

![[Pasted image 20240507092415.png]]

