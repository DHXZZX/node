# All Annotation of Spring

| Annotation                   | 作用                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| @ComponentScan               | 组件扫描                                                     |
| @Bean                        | 将bean添加到容器中                                           |
| @Configuration               | 标注配置类                                                   |
| @Component                   | 标注组件                                                     |
| @Service                     | 标注组件                                                     |
| @Controller                  | 标注组件                                                     |
| @Repository                  | 标注组件                                                     |
| @Conditional                 | 按照一定的条件判断,满足条件给容器注册bean                    |
| @Primary                     | 当有同名的多个配置时,加载该配置                              |
| @Lazy                        | 延迟加载                                                     |
| @Scope                       | prototype singleton request session                          |
| @Import                      | 给容器中导入组件,ImportSelector:返回需要导入的组件名,ImportBeanDefinitionRegistrar:手动注册bean到容器中 |
| @Value                       | 属性赋值                                                     |
| @Autowired                   | 自动织入                                                     |
| @Qualifier                   | 带名字的自动织入 @Resources  @Inject                         |
| @PropertySource              | 取外部配置文件中的k/v保存到运行的环境变量中;加载完外部的配置文件以后使用${}取出配置文件的值 @PropertySources |
| @Profile                     | Environment -Dspring.profiles.active=rd                      |
| @EnableAspectJAutoProxy      | 允许AOP自动配置                                              |
| @Before                      | 前置                                                         |
| @After                       | 后置                                                         |
| @AfterReturning              | 返回值                                                       |
| @AfterThrowing               | 抛出异常                                                     |
| @Around                      | 环绕                                                         |
| @Pointcut                    | 切入点                                                       |
| @EnableTransactionManagement | 允许事务管理                                                 |
| @Transactional               | 声明式事务                                                   |
| @Required                    | 显式声明 该方法必须有值                                      |

ResolvableTypeProvider 