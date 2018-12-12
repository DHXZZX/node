[TOC]



# 实例化`AnnotationConfigApplicationContext`

```java
//通过带配置类的构造函数实例化容器
	/**
	 * Create a new AnnotationConfigApplicationContext, deriving bean definitions
	 * from the given annotated classes and automatically refreshing the context.
	 * @param annotatedClasses one or more annotated classes,
	 * e.g. {@link Configuration @Configuration} classes
	 */
	public AnnotationConfigApplicationContext(Class<?>... annotatedClasses) {
		this();
		register(annotatedClasses);
		refresh();
	}
```



## `this()`

```java
	/**
	 * Create a new AnnotationConfigApplicationContext that needs to be populated
	 * through {@link #register} calls and then manually {@linkplain #refresh refreshed}.
	 */
	public AnnotationConfigApplicationContext() {
        // 实例化注解bean定义读取器
		this.reader = new AnnotatedBeanDefinitionReader(this);
        // 实例化定义扫描器
		this.scanner = new ClassPathBeanDefinitionScanner(this);
	}
```

## `register(annotatedClasses);`

```java
	/**
	 * Register one or more annotated classes to be processed.
	 * <p>Note that {@link #refresh()} must be called in order for the context
	 * to fully process the new classes.
	 * @param annotatedClasses one or more annotated classes,
	 * e.g. {@link Configuration @Configuration} classes
	 * @see #scan(String...)
	 * @see #refresh()
	 * 注册一个或多个添加了注解的类到处理器中
	 * 该方法必须被调用,以确保新添加的类全部被加载到处理器中
	 */
	public void register(Class<?>... annotatedClasses) {
		Assert.notEmpty(annotatedClasses, "At least one annotated class must be specified");
		this.reader.register(annotatedClasses);
	}
	/**
	 * Register one or more annotated classes to be processed.
	 * <p>Calls to {@code register} are idempotent; adding the same
	 * annotated class more than once has no additional effect.
	 * @param annotatedClasses one or more annotated classes,
	 * e.g. {@link Configuration @Configuration} classes
	 */
	public void register(Class<?>... annotatedClasses) {
		for (Class<?> annotatedClass : annotatedClasses) {
			registerBean(annotatedClass);
		}
	}
	/**
	 * Register a bean from the given bean class, deriving its metadata from
	 * class-declared annotations.
	 * @param annotatedClass the class of the bean
	 */
	public void registerBean(Class<?> annotatedClass) {
		doRegisterBean(annotatedClass, null, null, null);
	}
	/**
	 * Register a bean from the given bean class, deriving its metadata from
	 * class-declared annotations.
	 * @param annotatedClass the class of the bean 添加了注解的bean
	 * @param instanceSupplier a callback for creating an instance of the bean
	 * (may be {@code null}) 实例化器
	 * @param name an explicit name for the bean bean的名字
	 * @param qualifiers specific qualifier annotations to consider, if any,
	 * in addition to qualifiers at the bean class level
	 * @param definitionCustomizers one or more callbacks for customizing the
	 * factory's {@link BeanDefinition}, e.g. setting a lazy-init or primary flag
	 * @since 5.0
	 * 通过给定的类注册一个bean,从类的通告注解中加载其元信息
	 */
	<T> void doRegisterBean(Class<T> annotatedClass, @Nullable Supplier<T> instanceSupplier, @Nullable String name,
			@Nullable Class<? extends Annotation>[] qualifiers, BeanDefinitionCustomizer... definitionCustomizers) {
		//将给定的bean进行包装,返回AnnotatedGenericBeanDefinition
		AnnotatedGenericBeanDefinition abd = new AnnotatedGenericBeanDefinition(annotatedClass);
        //判断该bean的加载时机
		if (this.conditionEvaluator.shouldSkip(abd.getMetadata())) {
			return;
		}
		//设置实例化器
		abd.setInstanceSupplier(instanceSupplier);
		ScopeMetadata scopeMetadata = this.scopeMetadataResolver.resolveScopeMetadata(abd);
		abd.setScope(scopeMetadata.getScopeName());
		String beanName = (name != null ? name : this.beanNameGenerator.generateBeanName(abd, this.registry));

		AnnotationConfigUtils.processCommonDefinitionAnnotations(abd);
		if (qualifiers != null) {
			for (Class<? extends Annotation> qualifier : qualifiers) {
				if (Primary.class == qualifier) {
					abd.setPrimary(true);
				}
				else if (Lazy.class == qualifier) {
					abd.setLazyInit(true);
				}
				else {
					abd.addQualifier(new AutowireCandidateQualifier(qualifier));
				}
			}
		}
		for (BeanDefinitionCustomizer customizer : definitionCustomizers) {
			customizer.customize(abd);
		}

		BeanDefinitionHolder definitionHolder = new BeanDefinitionHolder(abd, beanName);
		definitionHolder = AnnotationConfigUtils.applyScopedProxyMode(scopeMetadata, definitionHolder, this.registry);
		BeanDefinitionReaderUtils.registerBeanDefinition(definitionHolder, this.registry);
	}
```

```java
	/**
	 * Determine if an item should be skipped based on {@code @Conditional} annotations.
	 * @param metadata the meta data
	 * @param phase the phase of the call
	 * @return if the item should be skipped
	 */
	public boolean shouldSkip(@Nullable AnnotatedTypeMetadata metadata, @Nullable ConfigurationPhase phase) {
    	//如果元信息没有或者Conditional注解 false
		if (metadata == null || !metadata.isAnnotated(Conditional.class.getName())) {
			return false;
		}
		// 如果没有传入枚举
		if (phase == null) {
			if (metadata instanceof AnnotationMetadata &&
					ConfigurationClassUtils.isConfigurationCandidate((AnnotationMetadata) metadata)) {
				return shouldSkip(metadata, ConfigurationPhase.PARSE_CONFIGURATION);
			}
			return shouldSkip(metadata, ConfigurationPhase.REGISTER_BEAN);
		}

		List<Condition> conditions = new ArrayList<>();
		for (String[] conditionClasses : getConditionClasses(metadata)) {
			for (String conditionClass : conditionClasses) {
				Condition condition = getCondition(conditionClass, this.context.getClassLoader());
				conditions.add(condition);
			}
		}

		AnnotationAwareOrderComparator.sort(conditions);

		for (Condition condition : conditions) {
			ConfigurationPhase requiredPhase = null;
			if (condition instanceof ConfigurationCondition) {
				requiredPhase = ((ConfigurationCondition) condition).getConfigurationPhase();
			}
			if ((requiredPhase == null || requiredPhase == phase) && !condition.matches(this.context, metadata)) {
				return true;
			}
		}

		return false;
	}

```

## `refresh();`

```java
	/** Synchronization monitor for the "refresh" and "destroy" */
	private final Object startupShutdownMonitor = new Object();

	@Override
	public void refresh() throws BeansException, IllegalStateException {
		synchronized (this.startupShutdownMonitor) {
			// Prepare this context for refreshing.
			prepareRefresh();

			// Tell the subclass to refresh the internal bean factory.
			ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

			// Prepare the bean factory for use in this context.
			prepareBeanFactory(beanFactory);

			try {
				// Allows post-processing of the bean factory in context subclasses.
				postProcessBeanFactory(beanFactory);

				// Invoke factory processors registered as beans in the context.
				invokeBeanFactoryPostProcessors(beanFactory);

				// Register bean processors that intercept bean creation.
				registerBeanPostProcessors(beanFactory);

				// Initialize message source for this context.
				initMessageSource();

				// Initialize event multicaster for this context.
				initApplicationEventMulticaster();

				// Initialize other special beans in specific context subclasses.
				onRefresh();

				// Check for listener beans and register them.
				registerListeners();

				// Instantiate all remaining (non-lazy-init) singletons.
				finishBeanFactoryInitialization(beanFactory);

				// Last step: publish corresponding event.
				finishRefresh();
			}

			catch (BeansException ex) {
				if (logger.isWarnEnabled()) {
					logger.warn("Exception encountered during context initialization - " +
							"cancelling refresh attempt: " + ex);
				}

				// Destroy already created singletons to avoid dangling resources.
				destroyBeans();

				// Reset 'active' flag.
				cancelRefresh(ex);

				// Propagate exception to caller.
				throw ex;
			}

			finally {
				// Reset common introspection caches in Spring's core, since we
				// might not ever need metadata for singleton beans anymore...
				resetCommonCaches();
			}
		}
	}
```

### `prepareRefresh();` Prepare this context for refreshing

```java
	/**
	 * Prepare this context for refreshing, setting its startup date and
	 * active flag as well as performing any initialization of property sources.
	 */
	protected void prepareRefresh() {
		this.startupDate = System.currentTimeMillis();
		this.closed.set(false);
		this.active.set(true);

		if (logger.isInfoEnabled()) {
			logger.info("Refreshing " + this);
		}
		// 初始化占位符代替的属性的值到容器环境中
		// Initialize any placeholder property sources in the context environment
		initPropertySources();

		// Validate that all properties marked as required are resolvable
		// see ConfigurablePropertyResolver#setRequiredProperties
		getEnvironment().validateRequiredProperties();

		// Allow for the collection of early ApplicationEvents,
		// to be published once the multicaster is available...
		this.earlyApplicationEvents = new LinkedHashSet<>();
	}
```

#### `initPropertySources();`

>该方法为空方法,可以被子类重写以更改或赋值环境变量

#### `getEnvironment().validateRequiredProperties();`

> 校验标识了必须的属性是否存在

```java
	// 该校验方法就是判断属性值是否为null
	@Override
	public void validateRequiredProperties() {
		MissingRequiredPropertiesException ex = new MissingRequiredPropertiesException();
		for (String key : this.requiredProperties) {
			if (this.getProperty(key) == null) {
				ex.addMissingRequiredProperty(key);
			}
		}
		if (!ex.getMissingRequiredProperties().isEmpty()) {
			throw ex;
		}
	}
```

#### `this.earlyApplicationEvents = new LinkedHashSet<>();`

> 实例化存放早期事件的容器
>
> ```java
> private Set<ApplicationEvent> earlyApplicationEvents;
> ```



### `ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();`

> 通知子容器初始化bean Factory
> 
> ```java
> 	/**
> 	 * Tell the subclass to refresh the internal bean factory.
> 	 * @return the fresh BeanFactory instance
> 	 * @see #refreshBeanFactory()
> 	 * @see #getBeanFactory()
> 	 */
> 	protected ConfigurableListableBeanFactory obtainFreshBeanFactory() {
> 		refreshBeanFactory();
> 		ConfigurableListableBeanFactory beanFactory = getBeanFactory();
> 		if (logger.isDebugEnabled()) {
> 			logger.debug("Bean factory for " + getDisplayName() + ": " + beanFactory);
> 		}
> 		return beanFactory;
> 	}
> ```
> > 刷新子容器
> > ```java
> > 	/**
> > 	 * Subclasses must implement this method to perform the actual configuration load.
> > 	 * The method is invoked by {@link #refresh()} before any other initialization work.
> > 	 * <p>A subclass will either create a new bean factory and hold a reference to it,
> > 	 * or return a single BeanFactory instance that it holds. In the latter case, it will
> > 	 * usually throw an IllegalStateException if refreshing the context more than once.
> > 	 * @throws BeansException if initialization of the bean factory failed
> > 	 * @throws IllegalStateException if already initialized and multiple refresh
> > 	 * attempts are not supported
> > 	 */
> > // 所有的子类必须实现该方法去执行加载配置
> > // 
> > 	protected abstract void refreshBeanFactory() throws BeansException, IllegalStateException;
> > ```
> > 
> > 子容器实例化后的beanFactory必须从这里返回
> > ```java
> > 
> > 	/**
> > 	 * Subclasses must return their internal bean factory here. They should implement the
> > 	 * lookup efficiently, so that it can be called repeatedly without a performance penalty.
> > 	 * <p>Note: Subclasses should check whether the context is still active before
> > 	 * returning the internal bean factory. The internal factory should generally be
> > 	 * considered unavailable once the context has been closed.
> > 	 * @return this application context's internal bean factory (never {@code null})
> > 	 * @throws IllegalStateException if the context does not hold an internal bean factory yet
> > 	 * (usually if {@link #refresh()} has never been called) or if the context has been
> > 	 * closed already
> > 	 * @see #refreshBeanFactory()
> > 	 * @see #closeBeanFactory()
> > 	 */
> > 	@Override
> > 	public abstract ConfigurableListableBeanFactory getBeanFactory() throws IllegalStateException;
> > ```
> >
> > 
### `prepareBeanFactory(beanFactory);`

> 初始化Bean Factory之前的对所需参数的赋值操作
>
> > ```java
> > /**
> > 	 * Configure the factory's standard context characteristics,
> > 	 * such as the context's ClassLoader and post-processors.
> > 	 * @param beanFactory the BeanFactory to configure
> > 	 */
> > 	protected void prepareBeanFactory(ConfigurableListableBeanFactory beanFactory) {
> > 		// 初始化类加载器
> > 		beanFactory.setBeanClassLoader(getClassLoader());
> >         // 初始化表达式解析器
> > 		beanFactory.setBeanExpressionResolver(new StandardBeanExpressionResolver(beanFactory.getBeanClassLoader()));
> >         // 初始化属性编辑器
> > 		beanFactory.addPropertyEditorRegistrar(new ResourceEditorRegistrar(this, getEnvironment()));
> > 
> > 		// 配置忽略自动注入的接口
> > 		beanFactory.addBeanPostProcessor(new ApplicationContextAwareProcessor(this));
> > 		beanFactory.ignoreDependencyInterface(EnvironmentAware.class);
> > 		beanFactory.ignoreDependencyInterface(EmbeddedValueResolverAware.class);
> > 		beanFactory.ignoreDependencyInterface(ResourceLoaderAware.class);
> > 		beanFactory.ignoreDependencyInterface(ApplicationEventPublisherAware.class);
> > 		beanFactory.ignoreDependencyInterface(MessageSourceAware.class);
> > 		beanFactory.ignoreDependencyInterface(ApplicationContextAware.class);
> > 
> > 		// BeanFactory interface not registered as resolvable type in a plain factory.
> > 		// MessageSource registered (and found for autowiring) as a bean.
> > 		beanFactory.registerResolvableDependency(BeanFactory.class, beanFactory);
> > 		beanFactory.registerResolvableDependency(ResourceLoader.class, this);
> > 		beanFactory.registerResolvableDependency(ApplicationEventPublisher.class, this);
> > 		beanFactory.registerResolvableDependency(ApplicationContext.class, this);
> > 
> > 		// Register early post-processor for detecting inner beans as ApplicationListeners.
> > 		beanFactory.addBeanPostProcessor(new ApplicationListenerDetector(this));
> > 
> > 		// Detect a LoadTimeWeaver and prepare for weaving, if found.
> > 		if (beanFactory.containsBean(LOAD_TIME_WEAVER_BEAN_NAME)) {
> > 			beanFactory.addBeanPostProcessor(new LoadTimeWeaverAwareProcessor(beanFactory));
> > 			// Set a temporary ClassLoader for type matching.
> > 			beanFactory.setTempClassLoader(new ContextTypeMatchClassLoader(beanFactory.getBeanClassLoader()));
> > 		}
> > 
> > 		// Register default environment beans.
> > 		if (!beanFactory.containsLocalBean(ENVIRONMENT_BEAN_NAME)) {
> > 			beanFactory.registerSingleton(ENVIRONMENT_BEAN_NAME, getEnvironment());
> > 		}
> > 		if (!beanFactory.containsLocalBean(SYSTEM_PROPERTIES_BEAN_NAME)) {
> > 			beanFactory.registerSingleton(SYSTEM_PROPERTIES_BEAN_NAME, getEnvironment().getSystemProperties());
> > 		}
> > 		if (!beanFactory.containsLocalBean(SYSTEM_ENVIRONMENT_BEAN_NAME)) {
> > 			beanFactory.registerSingleton(SYSTEM_ENVIRONMENT_BEAN_NAME, getEnvironment().getSystemEnvironment());
> > 		}
> > 	}
> > ```
> >
> > 