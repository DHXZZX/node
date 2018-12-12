`org.springframework.context.ApplicationContext`该接口 主管bean的实例化,配置和组装之前的bean,容器通过读取配置元数据 (metadata)来获取相关信息,以管理bean,元数据可以用xml,Java注解,java代码表示.一些`ApplicationContext` 的实现可以 `out-of-the-box` 

xml `ClassPathXmlApplicationContext` `FileSystemXmlApplicationContext` 



```java
@ComponentScan
@EnableTransactionManagement
@EnableCaching
@EnableWebMvc
@EnableScheduling
@EnableAsync
@EnableAspectJAutoProxy
@EnableLoadTimeWeaving
@EnableSpringConfigured

@ControllerAdvice
@MatrixVariable

@DateTimeFormat

@SpringJUnitConfig
@SpringJUnitWebConfig
@EnabledIf
@DisabledIf


```

