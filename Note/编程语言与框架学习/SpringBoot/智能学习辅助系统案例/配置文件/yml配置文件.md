
![[Pasted image 20240509124047.png|475]]

![[Pasted image 20240509124250.png]]

![[Pasted image 20240509124406.png]]

![[Pasted image 20240509124642.png]]

![[Pasted image 20240509125107.png]]

将配置文件改为yml：

```yml
spring:  
  #数据库连接信息  
  datasource:  
    driver-class-name: com.mysql.cj.jdbc.Driver  
    url: jdbc:mysql://localhost:3306/teststudy  
    username: root  
    password: root  
  #文件上传的配置  
  servlet:  
    multipart:  
      max-file-size: 10MB  
      max-request-size: 100MB  
#Mybatis配置  
mybatis:  
  configuration:  
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl  
    map-underscore-to-camel-case: true  
  
#阿里云OSS  
aliyun:  
  oss:  
    endpoint: https://oss-cn-hangzhou.aliyuncs.com  
    accessKeyId: LTAI5tQLcg1c7MfqvnKLMz1V  
    accessKeySecret: 4djwbcPdZhfGWVTHA41w6spNNEnT5L  
    bucketName: web-tlias-hlx
```

之前的properties：

```
#spring.application.name=tlias-web-management  
  
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver  
  
spring.datasource.url=jdbc:mysql://localhost:3306/teststudy  
  
spring.datasource.username=root  
  
spring.datasource.password=root  
  
mybatis.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl  
  
mybatis.configuration.map-underscore-to-camel-case=true  
#配置单个文件上传的最大大小限制  
spring.servlet.multipart.max-file-size=10MB  
#配置单个请求最大大小的限制（一次请求可以传多个文件）  
spring.servlet.multipart.max-request-size=100MB  
  
#阿里云OSS配置  
aliyun.oss.endpoint=https://oss-cn-hangzhou.aliyuncs.com  
aliyun.oss.accessKeyId=LTAI5tQLcg1c7MfqvnKLMz1V  
aliyun.oss.accessKeySecret=4djwbcPdZhfGWVTHA41w6spNNEnT5L  
aliyun.oss.bucketName=web-tlias-hlx
```
