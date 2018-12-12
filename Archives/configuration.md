## @configuration

- 配置类 == 配置文件
- 告诉spring这个是配置类

## @ComponentScans

```
@ComponentScans(
		value = {
				@ComponentScan(value="com.atguigu",includeFilters = {
/*						@Filter(type=FilterType.ANNOTATION,classes={Controller.class}),
						@Filter(type=FilterType.ASSIGNABLE_TYPE,classes={BookService.class}),*/
						@Filter(type=FilterType.CUSTOM,classes={MyTypeFilter.class})
				},useDefaultFilters = false)
		}
		)
```

* 实例化AnnotationConfigApplicationContext的流程
  - this()
    -  实例化定义读取器和定义扫描器
       ```
        public AnnotationConfigApplicationContext() {
          this.reader = new AnnotatedBeanDefinitionReader(this);
          this.scanner = new ClassPathBeanDefinitionScanner(this);
        }
       ```
  - register(annotatedClasses)
    - 注册多个注解使之生效
      ```
      public void register(Class<?>... annotatedClasses) {
    		Assert.notEmpty(annotatedClasses, "At least one annotated class must be specified");
    		this.reader.register(annotatedClasses);
    	}
      ```
    - RegisterBean
      - 通过指定的Class注册并,加载其元信息

      ```
      <T> void doRegisterBean(Class<T> annotatedClass, @Nullable Supplier<T> instanceSupplier, @Nullable String name,
    			@Nullable Class<? extends Annotation>[] qualifiers, BeanDefinitionCustomizer... definitionCustomizers) {

    		AnnotatedGenericBeanDefinition abd = new AnnotatedGenericBeanDefinition(annotatedClass);
    		if (this.conditionEvaluator.shouldSkip(abd.getMetadata())) {
    			return;
    		}

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
  - refresh()
    - prepareRefresh()
      容器刷新前准备 设置初始化值和初始化激活标识为初始化其他参数提供资源
      ```
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

      		// Initialize any placeholder property sources in the context environment
          //该方法是提供给子类重写的方法,加载自定义属性
      		initPropertySources();

      		// Validate that all properties marked as required are resolvable
      		// see ConfigurablePropertyResolver#setRequiredProperties
          //该方法校验初始化参数是否全部都有对应的value
      		getEnvironment().validateRequiredProperties();

      		// Allow for the collection of early ApplicationEvents,
      		// to be published once the multicaster is available...
          //初始化一个容器用来存放早期的事件,待事件发布器初始化成功后再依次发布
      		this.earlyApplicationEvents = new LinkedHashSet<>();
      	}
      ```
    - ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory()
      Tell the subclass to refresh the internal bean factory
      ```
      protected ConfigurableListableBeanFactory obtainFreshBeanFactory() {
    		refreshBeanFactory();
    		ConfigurableListableBeanFactory beanFactory = getBeanFactory();
    		if (logger.isDebugEnabled()) {
    			logger.debug("Bean factory for " + getDisplayName() + ": " + beanFactory);
    		}
    		return beanFactory;
    	}
      ```
        - refreshBeanFactory
          >Do nothing: We hold a single internal BeanFactory and rely on callers
          > to register beans through our public methods(or the BeanFactory's)
          ```java
          @Override
        	protected final void refreshBeanFactory() throws IllegalStateException {
        		if (!this.refreshed.compareAndSet(false, true)) {
        			throw new IllegalStateException(
        					"GenericApplicationContext does not support multiple refresh attempts: just call 'refresh' once");
        		}
        		this.beanFactory.setSerializationId(getId());
        	}
          ```
        - ConfigurableListableBeanFactory beanFactory = getBeanFactory()
          >get DefaultListableBeanFactory from Context which refreshed before
        - prepareBeanFactory(beanFactory);
          >Prepare the bean foacory for use in this context.
          > Configure the foacory's stadard context chaaracteristics.
          > such as the context's ClassLoader and post-processors.
          > @Param beanFactory the BeanFactory to Configure
          ```
          protected void prepareBeanFactory(ConfigurableListableBeanFactory beanFactory) {
        		// Tell the internal bean factory to use the context's class loader etc.
            // 实例化类加载器
        		beanFactory.setBeanClassLoader(getClassLoader());
            // 实例化表达式解析器
        		beanFactory.setBeanExpressionResolver(new StandardBeanExpressionResolver(beanFactory.getBeanClassLoader()));
            // 实例化属性解析器
        		beanFactory.addPropertyEditorRegistrar(new ResourceEditorRegistrar(this, getEnvironment()));
        		// Configure the bean factory with context callbacks.
        		beanFactory.addBeanPostProcessor(new ApplicationContextAwareProcessor(this));
        		beanFactory.ignoreDependencyInterface(EnvironmentAware.class);
        		beanFactory.ignoreDependencyInterface(EmbeddedValueResolverAware.class);
        		beanFactory.ignoreDependencyInterface(ResourceLoaderAware.class);
        		beanFactory.ignoreDependencyInterface(ApplicationEventPublisherAware.class);
        		beanFactory.ignoreDependencyInterface(MessageSourceAware.class);
        		beanFactory.ignoreDependencyInterface(ApplicationContextAware.class);
        		// BeanFactory interface not registered as resolvable type in a plain factory.
        		// MessageSource registered (and found for autowiring) as a bean.
        		beanFactory.registerResolvableDependency(BeanFactory.class, beanFactory);
        		beanFactory.registerResolvableDependency(ResourceLoader.class, this);
        		beanFactory.registerResolvableDependency(ApplicationEventPublisher.class, this);
        		beanFactory.registerResolvableDependency(ApplicationContext.class, this);
        		// Register early post-processor for detecting inner beans as ApplicationListeners.
        		beanFactory.addBeanPostProcessor(new ApplicationListenerDetector(this));
        		// Detect a LoadTimeWeaver and prepare for weaving, if found.
        		if (beanFactory.containsBean(LOAD_TIME_WEAVER_BEAN_NAME)) {
        			beanFactory.addBeanPostProcessor(new LoadTimeWeaverAwareProcessor(beanFactory));
        			// Set a temporary ClassLoader for type matching.
        			beanFactory.setTempClassLoader(new ContextTypeMatchClassLoader(beanFactory.getBeanClassLoader()));
        		}
        		// Register default environment beans.
        		if (!beanFactory.containsLocalBean(ENVIRONMENT_BEAN_NAME)) {
        			beanFactory.registerSingleton(ENVIRONMENT_BEAN_NAME, getEnvironment());
        		}
        		if (!beanFactory.containsLocalBean(SYSTEM_PROPERTIES_BEAN_NAME)) {
        			beanFactory.registerSingleton(SYSTEM_PROPERTIES_BEAN_NAME, getEnvironment().getSystemProperties());
        		}
        		if (!beanFactory.containsLocalBean(SYSTEM_ENVIRONMENT_BEAN_NAME)) {
        			beanFactory.registerSingleton(SYSTEM_ENVIRONMENT_BEAN_NAME, getEnvironment().getSystemEnvironment());
        		}
        	}
          ```
