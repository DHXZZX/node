# Introducing IoC and DI in Spring

* Inversion of control concepts
* Inversion of control in Spring
* Dependency injection in Spring
* Configuring the Spring application context

## Inversion of Control and Dependency Injection

In general,IoC can be decomposed into two subtypes: dependency injection and dependency lookup

## Types of Inversion of Control

### Dependency pull

In dependency pull, dependencies
are pulled from a registry as required. Anyone who has ever written code to access an EJB (2.1 or prior
versions) has used dependency pull (that is, via the JNDI API to look up an EJB component).

![Dependency pull via JNDI lookup](imgs\Dependency pull via JNDI lookup.png)

### Contextualized Dependency Lookup

Contextualized dependency lookup (CDL) is similar, in some respects, to dependency pull, but in CDL,
lookup is performed against the container that is managing the resource, not from some central registry, and it is usually performed at some set point.

![Contextualized dependency lookup](imgs\Contextualized dependency lookup.png)

### Injection vs. Lookup

The real question is this: given the choice, which method should you use, injection or lookup?

The
answer is most definitely injection. If you look at the code in the previous code samples, you can clearly see
that using injection has zero impact on your components’ code. The dependency pull code, on the other
hand, must actively obtain a reference to the registry and interact with it to obtain the dependencies, and
using CDL requires your classes to implement a specific interface and look up all dependencies manually.
When you are using injection, the most your classes have to do is allow dependencies to be injected by using either constructors or setters.

Using injection, you are free to use your classes completely decoupled from the IoC container that
is supplying dependent objects with their collaborators manually, whereas with lookup, your classes are
always dependent on the classes and interfaces defined by the container. Another drawback with lookup
is that it becomes difficult to test your classes in isolation from the container. Using injection, testing your
components is trivial because you can simply provide the dependencies yourself by using the appropriate
constructor or setter.

### Setter Injection vs. Constructor Injection

Now that we have established which method of IoC is preferable, you still need to choose whether to use
setter injection or constructor injection.

Constructor injection is particularly useful when you absolutely
must have an instance of the dependency class before your component is used.

Setter injection is useful in a variety of cases. If the component is exposing its dependencies to the
container but is happy to provide its own defaults, setter injection is usually the best way to accomplish
this.

Another benefit of setter injection is that it allows dependencies to be declared on an interface, although this is not as useful as you might first think.



configuration parameter:

* Configuration parameters are passive.
* Configuration parameters are usually information, not other components.
* Configuration parameters are usually simple values or collections of simple
  values.



In general, you should choose an injection type based on your use case. Setter-based injection allows
dependencies to be swapped out without creating new objects and also lets your class choose appropriate
defaults without the need to explicitly inject an object. Constructor injection is a good choice when you
want to ensure that dependencies are being passed to a component and when designing for immutable
objects. Do keep in mind that while constructor injection ensures that all dependencies are provided to a
component, most containers provide a mechanism to ensure this as well but may incur a cost of coupling
your code to the framework.

## Inversion of Control in Spring

![Contextualized dependency lookup](imgs\Contextualized dependency lookup.png)



An interesting feature of Spring’s IoC container is that it has the ability to act as an adapter between its
own dependency injection container and external dependency lookup containers.





## ApplicationContext

### Setting Spring Configuration Options

### Basic Configuration Overview





* Although it is easy to add dependencies this way, we must be careful not to violate the

single responsibility principle. Having more dependencies means more responsibilities
for a class, which might lead to a difficulty of separating concerns at refactoring time.
The situation when a class becomes bloated is easier to see when dependencies are set
using constructors or setters but is quite well hidden when using field injection.

* The responsibility of injecting dependencies is passed to the container in Spring,

but the class should clearly communicate the type of dependencies needed using
a public interface, through methods or constructors. Using field injections, it can
become unclear what type of dependency is really needed and if the dependency is
mandatory or not.

* Field injection introduces a dependency of the Spring container, as the @Autowired

annotation is a Spring component; thus, the bean is no longer a POJO and cannot be
instantiated independently.

* Field injection cannot be used for final fields. This type of fields can only be initialized using

constructor injection.

* Field injection introduces difficulties when writing tests as the dependencies have to

be injected manually.



## Choosing an Instantiation Mode

In general, singletons should be used in the following scenarios:

* Shared object with no state

  > You have an object that maintains no state and has many dependent objects. Because you do not need synchronization if there is no state, you do not need to create a new instance of the bean each time a dependent object needs to use it for some processing.

* Shared object with read-only state

  > This is similar to the previous point, but you have some read-only state. In this case, you still do not need synchronization, so creating an instance to satisfy each request for the bean is just adding overhead.

* Shared object with shared state

  > If you have a bean that has state that must be shared, singleton is the ideal choice. In this case, ensure that your synchronization for state writes is as granular as possible.

* High-throughput objects with writable state

  > If you have a bean that is used a great deal in your application, you may find that keeping a singleton and synchronizing all write access to the bean state allows for better performance than constantly creating hundreds of instances of the bean. When using this approach, try to
  > keep the synchronization as granular as possible without sacrificing consistency. You will find that this approach is particularly useful when your application creates a large number of instances over a long period of time, when your shared object has only a small amount of writable state, or when the instantiation of a new instance is expensive.

You should consider using nonsingletons in the following scenarios:

* Objects with writable state

  > If you have a bean that has a lot of writable state, you may find that the cost of synchronization is greater than the cost of creating a new instance to handle each request from a dependent object.

* Objects with private state

  > Some dependent objects need a bean that has private state so that they can conduct their processing separately from other objects that depend on that bean. In this case, singleton is clearly not suitable, and you should use nonsingleton.



## Resolving Dependencies

During normal operation, Spring is able to resolve dependencies by simply looking at your configuration
file or annotations in your classes.In this way, Spring can ensure that each bean is configured in the correct
order so that each bean has its dependencies correctly configured.

通常情况下,Spring能够通过简单查找配置文件或注解来解决依赖,通过这种方式,Spring能确保每个bean都是以正确的方式排序,因此其依赖的bean也会被正确的配置.

If Spring did not perform this and just created the beans and configured them in any order, a bean could be created and configured before its dependencies.

如果Spring不能确保这个并正好需要创建这个bean并配置他的顺序,这个bean可以在他被依赖前创建

这是你不想的也会导致你程序的问题

Unfortunately, Spring is not aware of any dependencies that exist between beans in your code that are
not specified in the configuration.

Spring不会注入任何不在配置中的bean.

如果你直接调用Context的getBean方法, 而不是让Spring来为你注入依赖,那么,Spring不会取解决你直接获取的bean的依赖问题.

@DependsOn : 显示声明,某个组件依赖于其他组件



