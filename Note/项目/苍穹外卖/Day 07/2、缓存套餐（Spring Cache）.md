### Spring Cache

![[Pasted image 20240521220516.png]]


```xml
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-cache</artifactId>  
</dependency>
```


![[Pasted image 20240521220542.png]]

### 入门案例

在启动类上加注解：

![[Pasted image 20240521221856.png|550]]

@CachePut使用方式，用于添加缓存：

![[Pasted image 20240521221754.png]]

@Cacheable使用方法，**spring catch底层是基于代理技术，加了此注解spring catch就会为当前controller创建一个代理对象，请求这个方法之前，先进入请求对象查询redis缓存没，查到了就直接返回，不进入方法**：

![[Pasted image 20240521222219.png|600]]

@CacheEvict使用方法，用于清理缓存：

![[Pasted image 20240521222914.png|600]]

删除所有缓存：

![[Pasted image 20240521223121.png|500]]

### 代码开发

![[Pasted image 20240521223220.png]]

User的SetmealController的查询套餐方法上加上@Cacheable，没有的话就加入缓存：

```Java
/**  
 * 条件查询  
 *  
 * @param categoryId  
 * @return  
 */  
@GetMapping("/list")  
@ApiOperation("根据分类id查询套餐")  
@Cacheable(cacheNames = "setmealCache", key = "#categoryId") //key: setmealCache::100  
public Result<List<Setmeal>> list(Long categoryId) {  
    Setmeal setmeal = new Setmeal();  
    setmeal.setCategoryId(categoryId);  
    setmeal.setStatus(StatusConstant.ENABLE);  
  
    List<Setmeal> list = setmealService.list(setmeal);  
    return Result.success(list);  
}
```

admin的SetmealController的对应增删改操作加上对应注解：

注：在新增操作中，一般是向数据库中插入一条新记录，而不是更新已存在的缓存数据。因此，使用`@CachePut`注解在新增操作上可能并不合适，因为它不会触发缓存的更新，而是将返回值缓存起来，但在新增场景中，返回值通常是新增记录的标识，而不是查询结果。

相比之下，`@CacheEvict`注解更适合在新增操作中使用。`@CacheEvict`注解的作用是清除指定缓存的数据，通常用于使缓存失效或清除缓存中的旧数据。在新增操作中，可以使用`@CacheEvict`注解清除与新增相关的缓存数据，以确保下次读取时能够**获取最新的数据**，确保数据一致性。


```java
@RestController  
@Slf4j  
@RequestMapping("/admin/setmeal")  
@Api(tags = "套餐相关接口")  
public class SetmealController {  
  
    @Autowired  
    private SetmealService setmealService;  
  
    @PostMapping  
    @ApiOperation("新增套餐")  
    @CacheEvict(cacheNames = "setmealCache", key = "#categoryId") //key: setmealCache::100  
    public Result insertWithDish(@RequestBody SetmealDTO setmealDTO) {  
        log.info("新增套餐：{}", setmealDTO);  
        setmealService.insertWithDish(setmealDTO);  
        return Result.success();  
    }  
  
    @DeleteMapping  
    @ApiOperation("批量删除套餐")  
    @CacheEvict(cacheNames = "setmealCache", allEntries = true) //清理所有缓存  
    public Result deleteBatch(@RequestParam List<Long> ids){  
        log.info("批量删除套餐：{}",ids);  
        setmealService.deleteBatch(ids);  
        return Result.success();  
    }  
  
    @PutMapping  
    @ApiOperation("根据ID修改套餐")  
    @CacheEvict(cacheNames = "setmealCache", allEntries = true) //清理所有缓存  
    public Result update(@RequestBody SetmealDTO setmealDTO) {  
        log.info("根据ID修改套餐：{}",setmealDTO);  
        setmealService.updateWithDish(setmealDTO);  
        return Result.success();  
    }  
  
    @PostMapping("/status/{status}")  
    @ApiOperation("起售停售套餐")  
    @CacheEvict(cacheNames = "setmealCache", allEntries = true) //清理所有缓存  
    public Result updateStatus(@PathVariable Integer status, Long id) {  
        setmealService.startOrStop(status, id);  
        return Result.success();  
    }  
}
```

测试没问题：

![[Pasted image 20240521230008.png]]