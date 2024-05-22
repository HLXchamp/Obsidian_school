
![[Pasted image 20240511112341.png]]

### 事务管理案例

![[Pasted image 20240511112416.png]]

![[Pasted image 20240511112521.png]]

在EmpMapper中添加一个根据部门ID删除员工的操作：

```java
//根据部门ID删除对应员工信息  
    @Delete("delete from emp where dept_id = #{deptId}")  
    void deleteByDeptId(Integer deptId);  
```

在DeptServiceImpl的删除部门方法中调用此方法：

```java
@Autowired  
private EmpMapper empMapper;

@Transactional //spring事务管理  
@Override  
public void delete(Integer id) {  
    deptMapper.deleteById(id); //根据ID删除部门  
  
    empMapper.deleteByDeptId(id); //删除与部门相关联的员工  
}
```

![[Pasted image 20240511150308.png]]

在yml中开启事务管理日志：

```yml
#spring事务管理日志  
logging:  
  level:  
    org.springframework.jdbc.support.JdbcTransactionManager: debug
```

### 事务管理属性

![[Pasted image 20240511150732.png]]

`@Transactional(rollbackFor = Exception.class)`代表出现任何异常都进行回滚！

![[Pasted image 20240511151059.png]]

![[Pasted image 20240511151759.png]]

![[Pasted image 20240511151841.png]]
