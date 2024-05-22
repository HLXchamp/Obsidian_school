### Redis的Java客户端

![[Pasted image 20240517163026.png|500]]

### Spring Data Redis使用方式

![[Pasted image 20240517214555.png]]

![[Pasted image 20240517214630.png]]

1、配置maven坐标：

```xml
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-data-redis</artifactId>  
</dependency>
```

2、配置Redis数据源：

yml：

```xml
redis:  
  host: ${sky.redis.host}  
  port: ${sky.redis.port}  
  password: ${sky.redis.password}  
  database: ${sky.redis.database}
```

dev.yml：

```xml
redis:  
  host: localhost  
  port: 6379  
  password: 123456  
  database: 10
```

3、编写配置类：

RedisConfiguration：

```java
@Configuration  
@Slf4j  
public class RedisConfiguration {  
  
    @Bean  
    public RedisTemplate redisTemplate(RedisConnectionFactory redisConnectionFactory){  
        log.info("开始创建redis模板对象...");  
        RedisTemplate redisTemplate = new RedisTemplate();  
        //设置redis的连接工厂对象  
        redisTemplate.setConnectionFactory(redisConnectionFactory);  
        //设置redis key的序列化器  
        redisTemplate.setKeySerializer(new StringRedisSerializer());  
        return redisTemplate;  
    }  
}
```

### 操作语句

```java
@SpringBootTest  
public class SpringDataRedisTest {  
  
    @Autowired  
    private RedisTemplate redisTemplate;  
  
    @Test  
    public void testRedisTemplate(){  
        System.out.println(redisTemplate);  
        ValueOperations valueOperations = redisTemplate.opsForValue();  
        HashOperations hashOperations = redisTemplate.opsForHash();  
        ListOperations listOperations = redisTemplate.opsForList();  
        SetOperations setOperations = redisTemplate.opsForSet();  
        ZSetOperations zSetOperations = redisTemplate.opsForZSet();  
    }  
  
    /**  
     * 操作字符串类型的数据  
     */  
    @Test  
    public void testString(){  
        // set get setex setnx  
        redisTemplate.opsForValue().set("name","小明");  
        String city = (String) redisTemplate.opsForValue().get("name");  
        System.out.println(city);  
        redisTemplate.opsForValue().set("code","1234",3, TimeUnit.MINUTES);  
        redisTemplate.opsForValue().setIfAbsent("lock","1");  
        redisTemplate.opsForValue().setIfAbsent("lock","2");  
    }  
  
    /**  
     * 操作哈希类型的数据  
     */  
    @Test  
    public void testHash(){  
        //hset hget hdel hkeys hvals  
        HashOperations hashOperations = redisTemplate.opsForHash();  
  
        hashOperations.put("100","name","tom");  
        hashOperations.put("100","age","20");  
  
        String name = (String) hashOperations.get("100", "name");  
        System.out.println(name);  
  
        Set keys = hashOperations.keys("100");  
        System.out.println(keys);  
  
        List values = hashOperations.values("100");  
        System.out.println(values);  
  
        hashOperations.delete("100","age");  
    }  
  
    /**  
     * 操作列表类型的数据  
     */  
    @Test  
    public void testList(){  
        //lpush lrange rpop llen  
        ListOperations listOperations = redisTemplate.opsForList();  
  
        listOperations.leftPushAll("mylist","a","b","c");  
        listOperations.leftPush("mylist","d");  
  
        List mylist = listOperations.range("mylist", 0, -1);  
        System.out.println(mylist);  
  
        listOperations.rightPop("mylist");  
  
        Long size = listOperations.size("mylist");  
        System.out.println(size);  
    }  
  
    /**  
     * 操作集合类型的数据  
     */  
    @Test  
    public void testSet(){  
        //sadd smembers scard sinter sunion srem  
        SetOperations setOperations = redisTemplate.opsForSet();  
  
        setOperations.add("set1","a","b","c","d");  
        setOperations.add("set2","a","b","x","y");  
  
        Set members = setOperations.members("set1");  
        System.out.println(members);  
  
        Long size = setOperations.size("set1");  
        System.out.println(size);  
  
        Set intersect = setOperations.intersect("set1", "set2");  
        System.out.println(intersect);  
  
        Set union = setOperations.union("set1", "set2");  
        System.out.println(union);  
  
        setOperations.remove("set1","a","b");  
    }  
  
    /**  
     * 操作有序集合类型的数据  
     */  
    @Test  
    public void testZset(){  
        //zadd zrange zincrby zrem  
        ZSetOperations zSetOperations = redisTemplate.opsForZSet();  
  
        zSetOperations.add("zset1","a",10);  
        zSetOperations.add("zset1","b",12);  
        zSetOperations.add("zset1","c",9);  
  
        Set zset1 = zSetOperations.range("zset1", 0, -1);  
        System.out.println(zset1);  
  
        zSetOperations.incrementScore("zset1","c",10);  
  
        zSetOperations.remove("zset1","a","b");  
    }  
  
    /**  
     * 通用命令操作  
     */  
    @Test  
    public void testCommon(){  
        //keys exists type del  
        Set keys = redisTemplate.keys("*");  
        System.out.println(keys);  
  
        Boolean name = redisTemplate.hasKey("name");  
        Boolean set1 = redisTemplate.hasKey("set1");  
  
        for (Object key : keys) {  
            DataType type = redisTemplate.type(key);  
            System.out.println(type.name());  
        }  
  
        redisTemplate.delete("mylist");  
    }  
}
```
