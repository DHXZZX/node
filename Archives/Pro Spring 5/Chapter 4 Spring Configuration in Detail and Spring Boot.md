# Spring Configuration in Detail and Spring Boot

* Managing the bean life cycle
* Making your bean "Spring aware"
* Using FactoryBeans
* Working with JavaBeans PropertyEditors
* Learning more about the Spring ApplicationContext
* Using Java classes for configuration
* Using Spring Boot
* Using configuration enhancements
* Using Groovy for configuration

## Spring's Impact on Application Portability

## Bean Life-Cycle Management

post-initialization

	> the post-initialization event is raised as soon as Spring finishes setting all the property values on the bean and finishes any dependency checks that you configured it to perform

pre-destruction

> if fired just before Spring destroys the bean instance

Spring provides three mechanisms a bean can use to hook into each of these events and perform some additional processing: interface-based, method-based, and annotation-based mechanisms.

![Spring beans life cycle](imgs\Spring beans life cycle.png)

## Hooking into Bean Creation

### Executing a Method When a Bean Is Created

### Implementing the InitializingBean Interface

### Suing the JSR-250 @PostConstruct Annotation

### Declaring an Initialization Method Using @Bean

### Understanding Order of Resolution

* The constructor is called first to created the bean.
* The dependencies are injected(setters are called).
* Now that the bean exist and the dependencies were provided, the pre-initialization BeanPostProcessor infrastructure beans are consulted to see whether they want to call anything from this bean. There are Spring-specific infrastructure beans that preform bean modifications after they are created. The @PostConstruct annotation is registered by CommonAnnotationBeanPostProcessor, so this bean will call the method found annotated with @PostConstruct. This method is executed right after the bean has bean constructed and before the class is put into service, before the actual initialization of the bean(before afterPropertieseSet and init-method).
* The InitializingBean's afterPropertiesSet is executed right after the dependencies are injected. The afterPropertiesSet() method is invoked by a BeanFactory after is has set all the bean properties supplied and has satisfied BeanFactoryAware and ApplicationContextAware.
* The init-method attribute is executed last because this is the actual initialization method of the bean

## Hooking into Bean Destruction

### Executing a Method When a Bean Is Destroyed

### Implementing the DisposableBean Interface

### Using the JSR-250 @PreDestroy Annotation

### Declaring a Destroy Method Using @Bean

### Understanding Order of Resolution

As with the case of bean creation, you can use all mechanisms on the same bean instance for bean destruction. In this case, Spring invokes the method annotated with @PreDestroy first and then DisPosableBean.destroy(), followed by your destroy method configured in your XML definition.

#### Using a Shutdown Hook

## Making Your Beans "Spring Aware"

### Using the BeanNameAware Interface

### Using the ApplicationContextAware Interface

## Use of FactoryBeans

### FactoryBean Example: The MessageDigestFactoryBean

* FactoryBean

```java
public class MessageDigestFactoryBean implements FactoryBean<MessageDigest>,InitializingBean {

    private String algorithmName = "MD5";
    private MessageDigest messageDigest = null;


    @Nullable
    @Override
    public MessageDigest getObject() throws Exception {
        return messageDigest;
    }

    @Nullable
    @Override
    public Class<?> getObjectType() {
        return MessageDigest.class;
    }

    @Override
    public boolean isSingleton() {
        return true;
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        messageDigest = MessageDigest.getInstance(algorithmName);
    }

    public void setAlgorithmName(String algorithmName) {
        this.algorithmName = algorithmName;
    }
}
```

* adaptor

```java
public class MessageDigester {
    private MessageDigest digest1;
    private MessageDigest digest2;

    public void setDigest1(MessageDigest digest1) {
        this.digest1 = digest1;
    }

    public void setDigest2(MessageDigest digest2) {
        this.digest2 = digest2;
    }

    public void digest(String msg) {
        System.out.println("Using digest1");
        digest(msg,digest1);
        System.out.println("Using digest2");
        digest(msg,digest2);
    }

    private void digest(String msg,MessageDigest digest) {
        System.out.println("Using alogrithm: " + digest.getAlgorithm());
        digest.reset();
        byte[] out = digest.digest(msg.getBytes());
        System.out.println(out);
    }
}
```

* config

```xml
<bean id="shaDigest"
      class="com.dhxz.apress.prospring5.ch4.MessageDigestFactoryBean" p:algorithmName="SHA1"/>
<bean id="defaultDigest"
      class="com.dhxz.apress.prospring5.ch4.MessageDigestFactoryBean"/>

<bean id="digester"
      class="com.dhxz.apress.prospring5.ch4.MessageDigester"
      p:digest1-ref="shaDigest"
      p:digest2-ref="defaultDigest"/>
```



## Accessing a FactoryBean Directly

## Using the factory-bean and factory-method Attributes

## JavaBeans PropertyEditors

### Using the Built-in PropertyEditors

![Spring PropertyEditorys](imgs\Spring PropertyEditors.png)

### Creating a Custom PropertyEditor

## More Spring ApplicationContext Configuration

ApplicationContext supports the following features:

* Internationalization
* Even publication
* Resource management and access
* Additional life-cycle interfaces
* Improved automatic configuration of infrastructure components

### Internationalization with the MessageSource

Aside from ApplicationContext, Spring provides three MessageSource implementations

* ResourceBundleMessageSource
* ReloadableResourceBundleMessageSource
* StaticMessageSource

......



## Application Events

### Using Application Events

An event is a class derived from ApplicationEvent, which itself derives from java.util.EventObject. Any bean can listen for events by implementing the ApplicationListener<T> interface; ApplicationContext automatically registers any bean that implements this interface as a listener when it is configured. Events are published using the ApplicationEventPublisher.publishEvent() method, so the publishing class must have knowledge of ApplicationContext(which extends the ApplicationEventPublisher interface). In a web application, this is simple because many of your classes are derived from Spring Framework classes that allow access to ApplicationContext through a protected method. In a stand-alone application, you can have your publishing bean implement ApplicationContextAware to enable it to publish events.

### Considerations for Event Usage

## Accessing Resources

```java
public class ResourceDemo {
    public static void main(String[] args) throws Exception {
        ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext();
        File file = File.createTempFile("test", ".txt");
        file.deleteOnExit();

        Resource res1 = ctx.getResource("file:///"+file.getPath());
        displayInfo(res1);
        Resource res2 = ctx.getResource("classpath:test.txt");
        displayInfo(res2);
        Resource res3 = ctx.getResource("http://www.baidu.com");
        displayInfo(res3);
    }

    private static void displayInfo(Resource res) throws Exception {
        System.out.println(res.getClass());
        System.out.println(res.getURL().getContent());
        System.out.println("");
    }
}
```

## Configuration Using Java Classes

### ApplicationContext Configuration in Java

![Java Configuration Annotations Table](imgs\Java Configuration Annotations Table.png)

### Spring Mixed Configuration

## Profiles

## Spring Profiles Using Java Configuration

### Considerations for Using Profiles

## Environment and PropertySource Abstraction

## Configuration Using Groovy

