# `AnnotationConfigApplicationContext`

> 配置类 包扫描
>
> 所用到的注解
>
> > `@Configuration` 告诉Spring这是一个配置类 
> >
> > `@ComponentScans` 包扫描组
> >
> > `@ComponentScan` 指定要扫描的包
> >
> > `excludeFilters = Filter[]` 指定扫描的时候按照什么规则排除哪些组件
> >
> > `includeFilters = Filter[]` 指定稻苗的时候 **只需要** 包含哪些组建
> >
> > `FilterType.ANNOTTATION` 按照注解
> >
> > `FilterType.ASSIGNABLE_TYPE` 按照给定的类型
> >
> > `FilterType.ASPECTJ` 按照ASPECTJ表达式
> >
> > `FIlterType.REGEX` 按照正则表达式
> >
> > `FilterType.CUSTOM` 按照自定义规则
> >
> > ```java
> > //配置类==配置文件
> > @Configuration  //告诉Spring这是一个配置类
> > 
> > @ComponentScans(
> > 		value = {
> > 				@ComponentScan(value="com.atguigu",includeFilters = {
> > /*						@Filter(type=FilterType.ANNOTATION,classes={Controller.class}),
> > 						@Filter(type=FilterType.ASSIGNABLE_TYPE,classes={BookService.class}),*/
> > 						@Filter(type=FilterType.CUSTOM,classes={MyTypeFilter.class})
> > 				},useDefaultFilters = false)	
> > 		}
> > 		)
> > public class MainConfig {
> > 	
> > 	//给容器中注册一个Bean;类型为返回值的类型，id默认是用方法名作为id
> > 	@Bean("person")
> > 	public Person person01(){
> > 		return new Person("lisi", 20);
> > 	}
> > 
> > }
> > ```
> >
> >  ## 自定义类型过滤器
> >
> > > 实现`TypeFilter` 接口
> >
> > ```java
> > public class MyTypeFilter implements TypeFilter {
> > 
> > 	/**
> > 	 * metadataReader：读取到的当前正在扫描的类的信息
> > 	 * metadataReaderFactory:可以获取到其他任何类信息的
> > 	 */
> > 	@Override
> > 	public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory)
> > 			throws IOException {
> > 		// TODO Auto-generated method stub
> > 		//获取当前类注解的信息
> > 		AnnotationMetadata annotationMetadata = metadataReader.getAnnotationMetadata();
> > 		//获取当前正在扫描的类的类信息
> > 		ClassMetadata classMetadata = metadataReader.getClassMetadata();
> > 		//获取当前类资源（类的路径）
> > 		Resource resource = metadataReader.getResource();
> > 		
> > 		String className = classMetadata.getClassName();
> > 		System.out.println("--->"+className);
> > 		if(className.contains("er")){
> > 			return true;
> > 		}
> > 		return false;
> > 	}
> > }
> > ```

# `@ComponentScan` 指定组件扫描范围

# `@Bean` 为容器中添加组件

## 指定初始化方法

​	

## `@Conditional` 满足当前条件在该类或方法中的配置才会生效

