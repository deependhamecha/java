## Spring Dependency Injection <a name="springdi"></a>

There are couple of ways to do a thing in Spring. Alternate approaches are also specified following a particular.

In Spring, objects arent responsible for finding or creating the other objects that they need to do their jobs. Instead, the container gives them references to the objects that they collaborate with. The act of creating objects and associating them is wiring. 

The Spring container is responsible for creating the beans in your application and coordinating the relationships between those objects via DI. It's your responsibility as a developer to tell Spring which beans to create and how to wire them together. When it comes to expressing a bean wiring specification, Spring is incredibly flexible, offering three primary wiring mechanisms:
- Explicit configuration in XML
- Explicit configuration in Java
- Implicit bean discovery and automatic wiring.

### Different Spring Libraries

For Spring Framework, spring-core contains mainly core utilities and common stuff (like enums) and because it's really critical for Spring, probably all other Spring modules depend on it (directly or transitively).

In turn spring-context provides Application Context, that is Spring's Dependency Injection Container and it is probably always defined in POMs of artifacts that use Spring Framework somehow. In fact, spring-context depends on spring-core so by defining spring-context as your dependency, you have spring-core in your classpath as well.

### Automatically Wiring Beans
It can be done using two approaches :
- *Component scanning* - Spring automatically discovers bean to be created in the application context.
- *Autowiring* - Spring automatically satisfies bean dependencies.
*(I will get onto this later)*

There are 3 things in Spring to make it work:
1. Making a bean to be injected.
2. Creating a configuration class. Initializing a object inside the configuration class.
3. Getting the object from context.

Simple Example of Java based configuration:
        
**Student.java**
```java
@Component
public class Student {
	private String name;
	private int age;
	private int marks;
	

	public Student(String name) {
		super();
		this.name = name;
	}

	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public int getMarks() {
		return marks;
	}
	public void setMarks(int marks) {
		this.marks = marks;
	}
	
	
}
```
   
2. **ApplicationConfiguration.java**
```java
@Configuration
@ComponentScan(basePackages="com.springcore.dependencyinjection.beans")
public class ApplicationConfiguration {

	@Bean
	public Student student() {
		return new Student("Deepen");
	}
}
```
      
3. **Main.java**
```java
ApplicationContext context = new AnnotationConfigApplicationContext(ApplicationConfiguration.class);
    	 
        Student student = (Student) context.getBean("student");
 
        System.out.println( student.getName() );
```


```java
@Component
public class Dog {
  
}
```
This simple annotation identifies this class as a component class and serves as a clue to Spring that a bean should be created for the class. There's no need to explicity configure a `Dog` bean. Component Scanning isnt turned on by default, so you need to tell Spring where to look for Components(Beans).

With Java : 
```java
@Configuration
@ComponentScan(basePackages="com.componentscan")
public class MyConfiguration {
}
```
With XML : 
```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  	xmlns:c="http://www.springframework.org/schema/c"
    xmlns:p="http://www.springframework.org/schema/p"
	  xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context-4.3.xsd">

<!--    <context:annotation-config /> -->
   <context:component-scan base-package="com.componentscan" />

</beans>
```

### Naming a component-scanned bean
By Default, beans are given default ids of camelCase letters of Class. If you need to give different name then use
```java
@Component("myName")
public class Dog {
}
```

Another way is to use `@Named` annotation in a similar way.

## Setting a base package name during component-scan
```java
@Configuration
@ComponentScan("myName")
public class Dog {}
```
To be specific, use:
```java
@Configuration
@ComponentScan(basePackages="myName")
public class Dog {}
```

## Autowiring

If `@Autowired` doesn't satisfy, it will throw an Exception while initializing container, to avoid that use :
```java
@Autowired(required=false)
```
`@Autowired` is a Spring specific annotation. To use JSR based, use `@Inject` annotation.


## Configuration (Java based vs XML based)
```java

import com.componentscan.Dog;

@Configuration
public class MyConfiguration {
    
    @Bean
    public Dog dog() {
        return new Dog("Deepen");
    }
}
```
is same as
```XML
<bean id="dog" class="com.componentscan.Dog">
	<property name="name" value="Deepen"/>
</bean>
```

## Wiring beans in java

Lets say if you are using libraries and you dont have the source code. So, you cannot use `@Autowired`. You need to manually inject beans into the container.

You can do explicit configuration using Java based or XML based configuration.

## Declaring a simple bean

To declare a bean in JavaConfig, you write a method that creates an instance of the desired type and annotate it with `@Bean`.

```java
@Bean
public Dog dog() {
	return new Dog();
}
```

To give alias,
```java
@Bean(name="mydog")
public Dog dog() {
	return new Dog();
}
```

If bean is not given id, it will take fully qualified class name. Using XML:
```xml
<bean id="dog" class="com.mypackage.Dog" />
```

## Constructor Injection

```java
@Bean
public Dog dog() {
	return new Dog("Tommy");
}
```

```XML
<bean id="dog" class="com.mypackage.Dog">
	<constructor-arg value="Tommy" />
</bean>
```

If there are more than one arguments to the constructor: 
```XML
<bean id="dog" class="com.mypackage.Dog">
	<constructor-arg type="String" index="0" value="Tommy" />
	<constructor-arg type="int" index="1" value="10" />
</bean>
```
Constructor Injection using property names : 
```XML
<bean id="dog" class="com.mypackage.Dog" c:_name="Tommy" c:_age="10" />
```
Using only index
```XML
<bean id="dog" class="com.mypackage.Dog" c:_0="Tommy" c:_1="10" />
```

```XML
<bean id="dog" clas="com.mypackage.Dog" c:cd-ref="myName" />
```

```XML
<bean id="dog" class="com.mypackage.Dog" c:_0-ref="myName" c:_1-ref="myName1" />
OR
<bean id="dog" class="com.mypackage.Dog">
	<constructor-arg ref="myName" index="0" />
	<constructor-arg ref="myName1" index="1" />
</bean>
```

Java based is pretty straight forward.

It will create bean using default constructor.
Spring's XML doesn' benefit from compile-time verification of the Java types being referred to.
## Referring to other beans

```xml
// XML CODE
```

```java
@Bean
public Dog dog() {
	return new Dog(myName());
}
```

Above approach (java based) is best choice because it doesn't depend on the referred bean to be in the same configuration class.

## Property Injection
```XML
<bean id="dog" class="com.mypackage.Dog" p:name-ref="myName" /> // REFERENCE
OR
<bean id="dog" class="com.mypackage.Dog" p:name="Tommy" /> // LITERAL
```

As with c-namespace, the only difference between wiring a bean reference and wiring a literal value is the presence or absnece of a `-ref` suffix. Without the `-ref` suffix, you're wiring literal values.

> Note : You cannot use `p` namespace for wiring collections but you can use `util` namespace.

## Defining List separately

```XML
<util:list id="namesList">
	<value>Dude1</value>
	<value>Dude2</value>
	<value>Dude3</value>
</util:list>

<bean id="dog" class="com.mypackage.Dog" p:names-ref="namesList" />
```

## Referencing Multiple Configurations files (Java based)

```java
@Configuration
@Import(MyConfig.class) // @Import({MyConfig1.class, MyConfig2.class}) FOR MORE THAN ONE FILES
public class DudeConfig {
	
	// Other beans
	@Bean
	//.....
}
```

## Referencing XML Configuration in Java based Configuration
```java
@Configuration
@ImportResource("classpath:spring.xml")
```

## Importing XML Configuration in XML based configuration
```xml
<import resource="spring.xml" />
```

## Configurating profile beans

```java
@Configuration
@Profile("dev")
public class MyConfiguration {}
```
This defines bean configuration to `dev` profile. It tells Spring that the beans in this configuration class should be created only if the dev profile is active. If the `dev` profile isn't active. If the `dev` profile isn't active, then the `@Bean ` method will be ignored.

In Spring 3.1, you could only use `@Profile` at the class level. Starting with Spring 3.2, however, you can use `@Profile` at the method

> Note : Any bean that isn't given a profile will always be created and active.

## Specifing default and active profile

**web.xml**
```xml
// Set default profile for servlet
<servlet>
	<init-param>
		<param-name>spring.profiles.default</param-name>
		<param-value>dev</param-value>
	</init-param>
</servlet>
<context-param>
	<param-name>spring.profiles.default</param-name>
	<param-value>dev</param-value>
</context-param>
```

To Activate Dev Profile using annotation,

> Note : You can activate multiple profiles at the same time, but it doesn't make sense. `dev` and `prod` activated together?

```java
@ActiveProfiles("dev") // Put array of strings for multiple active profiles
```

Spring 4 offers conditional beans



### Spring Dependency Injection (Setter vs Constructor vs Property)

Since manual config is straight forward, we will have examples of Autowired. Lets check pros and cons of each approach, which one to use when.

MyConfiguration.java
```java
@Configuration
@ComponentScan({"com.springjavaanno.service", "com.springjavaanno.pojo"})
public class ApplicationContextConfig {}
```

#### Constructor Injection

Bean **House.java**
```java
public House {

	@Autowired
	public House(Table table) {

	}
}
```

#### Setter Injection

```java
public House {

	private House house;

	private House getHouse() {
		return this.house;
	}

	@Autowired
	private void setHouse(House house) {
		this.house = house;
	}
}
```

#### Property Injection

```java
public House {

	@Autowired
	private House house;
}
```

Which is the best way to do?
- Setter Injection with `@Requied`  annotation.



#### @Scope

Scope can be defined at either bean definition or on class.

If there are two classes with a has-a relationship and if parent is Singleton, child becomes singleton. Even if you specify `@Scope("prototype")`, it is singleton if its parent is singleton.

The latest version of Spring framework defines 6 types of scopes:

1. singleton
2. prototype
3. request
4. session
5. application
6. websocket

Pretty straight forward scopes.

### Bean Lifecycle Methods(Hooks) in Spring

**House.java**
```java
public class House implements InitializingBean, DisposableBean {

	@Autowired
	@Qualifier("table")
	private Furniture furniture;
	
	{
//		System.out.println("I1: "+this.furniture);
//		System.out.println("I2: "+this.getWindow());
//		System.out.println("I3: "+this.furniture);
	}
	
	public Furniture getFurniture() {
		return furniture;
	}

	public void setFurniture(Furniture furniture) {
//		System.out.println("FURNITURE: "+ furniture);
		this.furniture = furniture;
	}

	private Window window;


	public Window getWindow() {
		return window;
	}

	@Autowired
	@Qualifier("window")
	public void setWindow(Window window) {
//		System.out.println("WINDOW: "+window);
		this.window = window;
	}

	@Value("${house.name}")
	private String name;

//	public House() {
//		System.out.println("House Instantiated.");
//	}

	@Autowired
	public House(@Qualifier("databaseService") RandomService random) {
//		System.out.println("House Instantiated.");
//		System.out.println("random: "+random);
		
//		System.out.println("GETSERVICETYPE: "+random.getServiceType());
	}
	
	// 3
	void afterInit() {
		System.out.println("After Init called.");
	}
	
	
	// -2
	void beforeDestroy() {
		System.out.println("Before Destroy called.");
	}
	
	public String print() {
//		System.out.println("FRN: "+this.furniture);
//		System.out.println("HOUSE: "+this);
		return "This is my House named: "+this.name;
	}

	// 2
	@Override
	public void afterPropertiesSet() throws Exception {
		// TODO Auto-generated method stub
		System.out.println("afterPropertiesSet");
	}

	// -1
	@Override
	public void destroy() throws Exception {
		// TODO Auto-generated method stub

		System.out.println("destroy");
	}
}
```

**MyBeanPostProcessor.java**
```java
package com.springjavaanno.pojo;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;
import org.springframework.stereotype.Component;

@Component
public class MyBeanPostProcessor implements BeanPostProcessor {

	// 4
	@Override
	public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {

		System.out.println("postProcessAfterInitialization: "+bean);
		return bean;
	}

	// 1
	@Override
	public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {

		System.out.println("postProcessBeforeInitialization: "+bean);
		return bean;
	}

}
```

**ACConfig.java**
```java
@Configuration
@PropertySource("classpath:house.properties")
public class ApplicationContextConfig {

	@Bean()
	public MyBeanPostProcessor myBeanPostProcessor() {
		return new MyBeanPostProcessor();
	}

	@Bean(initMethod = "afterInit", destroyMethod = "beforeDestroy")
	public House house() {
		House house = new House(databaseService());
		return house;
	}

	@Bean
	public Table table() {
		return new Table();
	}
	
	@Bean
	public RandomService databaseService() {
		return new DatabaseService();
	}
	
	@Bean
	public Window window() {
		return new Window();
	}
}

```

1. There is no `initMethod` and `beforeDestroy` while autowiring.
2. Order of precedence:
  1. BeanPostProcessor
  2. InitializingBean, DisposableBean
  3. Custom Methods(initMethod, beforeDestroy)

	Output
```sh
postProcessBeforeInitialization: com.springjavaanno.service.DatabaseService@399f45b1
postProcessAfterInitialization: com.springjavaanno.service.DatabaseService@399f45b1
Table Instantiated.
postProcessBeforeInitialization: com.springjavaanno.pojo.Table@3c9754d8
postProcessAfterInitialization: com.springjavaanno.pojo.Table@3c9754d8
postProcessBeforeInitialization: com.springjavaanno.pojo.Window@7dc0f706
postProcessAfterInitialization: com.springjavaanno.pojo.Window@7dc0f706
postProcessBeforeInitialization: com.springjavaanno.pojo.House@4009e306
afterPropertiesSet
After Init called.
postProcessAfterInitialization: com.springjavaanno.pojo.House@4009e306
This is my House named: Nav Jyothi CHS
destroy
Before Destroy called.
```
