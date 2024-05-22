
![[Pasted image 20240502194313.png]]
![[Pasted image 20240502194332.png]]

@Component  // 将当前类交给IOC容器管理，成为IOC容器中的bean
@Autowired // 运行时，IOC容器会提供该类型的bean对象，并赋值给该变量 —— 依赖注入

如果有相同的A，B类都想交给IOC容器管理，在想应用的类上加上@Component，不用的类上的@Component注释掉就行。注：同一个类只能有一个@Component！