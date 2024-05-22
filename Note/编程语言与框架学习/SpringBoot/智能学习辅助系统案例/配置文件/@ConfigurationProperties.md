![[Pasted image 20240509125752.png]]

新建一个AliOSSProperties类（**prefix是必须项！**）：

```java
@Data  
@Component  
@ConfigurationProperties(prefix = "aliyun.oss")  
public class AliOSSProperties {  
    private String endpoint;  
    private String accessKeyId;  
    private String accessKeySecret;  
    private String bucketName;  
}
```


```java
 @Autowired  
    private AliOSSProperties aliOSSProperties;  
  
    /**  
     * 实现上传图片到OSS  
     */    
     public String upload(MultipartFile file) throws IOException {  
  
        //获取阿里云OSS参数  
        String endpoint = aliOSSProperties.getEndpoint();  
        String accessKeyId = aliOSSProperties.getAccessKeyId();  
        String accessKeySecret = aliOSSProperties.getAccessKeySecret();  
        String bucketName = aliOSSProperties.getBucketName();
    }
```

![[Pasted image 20240509130852.png]]

![[Pasted image 20240509131257.png]]