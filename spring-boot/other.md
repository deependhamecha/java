1. `spring-boot-maven-plugin` - To package executable jar or war archive. Can also use to run the app.
It does nothing but
```bash
./mvnw package
./mvnw spring-boot:run
```

2. `@SpringBootApplication` comprises of `@EnableAutoConfiguration`, `@ComponentScan` and `@Configuration`.

3. Place your main file above all packages.

4. By default, Spring Boot will load static resources from "/static" directory.

5. WARNING: Do not use the `src/main/webapp` directory if your application is packaged as a JAR. Although this is a standard Maven directory, it works only with WAR packaging. It is silently ignored by most build tools if you generate a JAR.

6. `spring-boot-starter-web` is group of all dependencies of Spring like Spring MVC, hibernate validator, REST, etc. rather than specify individually. Eclipse > `POM.xml` > **Dependency Hierarchy** tab to check dependencies.

7. Spring Boot provides **Starter Parent**. This is a special starter that provides Maven defaults.

8. **Spring Boot Actuator** exposes endpoints to monitor and manage your application. By default it adds two endpoints, `/health` and `/info`. You can add
application.properties
```properties
info.app.name=App Name
info.app.description=My Description
```

To expose all endpoints:
application.properties
```properties
management.endpoints.web.exposure.include=*
```

You can secure the routes except for `/health` and `/info` using **spring-boot-starter-security** plugin. However, you can also configure to secure these two routes as well. Default username will be `user` and password will be generated in the console. You can configure in **application.properties**:
```properties
spring.security.user.name=scott
spring.security.user.password=tiger
```

You can disable health and info by:
```properties
management.endpoints.web.exposure.exclude=health,info
```

9. To run Spring Boot manually, run `mvn package` in root directory of the project which will generate jar and then go to `target` folder and run: `java -jar project_name-0.0.1.jar`.

10. Eclipse bug where it shows error icon: add this to **pom.xml**:
```xml
<properties>
	...
	<maven-jar-plugin.version>3.1.1</maven-jar-plugin.version>
</properties>
```

11. DAO Impl
```java
@Override
@Transactional
```

12. Application Properties Show SQL
```java
spring.jpa.show-sql=true
```

13. Database Configuration in **application.properties**
```java
spring.datasource.url=jdbc:mysql://localhost:3306/e2eproject1db
spring.datasource.username=root
spring.datasource.password=abcd
````

14. Spring Data REST like Spring Data JPA exposes rest apis.
Just add the maven dependency.
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-rest</artifactId>
</dependency>
```
Thats it.

If you want different route then use annotation on Repository class.
```java
@RepositoryRestResource(path="members")
public interface EmployeeRepository extends JpaRepository<Employee, Integer> {

}
```

By Default, Spring Data REST will return the first 20 elements. Page size=20. /members?page=0. Its index based.
There are couple of other properties as well to configure Spring Data REST.
```
spring.data.rest.base-path="/magic-api"
spring.data.rest.default-page-size=50
spring.data.rest.max-page-size
```
URL will be `/magic-api/members`

It also gives you sorting(Default asc).
1. /members?sort=lastName,desc
2. /members?sort=lastName,asc,lastName,desc

15. **JpaRepository** extends **PagingAndSortingRepository** which in turn extends **CrudRepository**.

16. TEST Connection
```java
try (Connection connection = dataSource.getConnection()) {
    System.out.println("catalog:" + connection.getCatalog());
} catch(Exception ex) {
	ex.printStackTrace();
}
```

17. `repository.exists(id)` checks whether it hasnt entry with this id.

18. `findOne` returns Object and `findById()` returns an **Optional<T>**.

19. GenerationStrategy.TABLE

SQL
```sql
CREATE TABLE `id_gen` (`gen_name` VARCHAR(60), `gen_value` INT(20));

CREATE TABLE `customer` (`id` INT(11) NOT NULL, `name` VARCHAR(20) NOT NULL, `email` VARCHAR(20) NOT NULL, PRIMARY KEY(`id`));
# OR 
# CREATE TABLE `customer` (`id` INT(11) NOT NULL AUTO_INCREMENT, `name` VARCHAR(20) NOT NULL, `email` VARCHAR(20) NOT NULL, PRIMARY KEY(`id`));
```

Java POJO
```java
@Entity
@Component
@Scope(scopeName = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public class Customer {

	@TableGenerator(name="customer_table", table="id_gen", allocationSize = 50, pkColumnName = "gen_name", valueColumnName = "gen_value")
	@Id
	@GeneratedValue(strategy = GenerationType.TABLE, generator = "customer_table")
	private int id;

	private String name;

	private String email;

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}
	
	public Customer() {
		super();
		System.out.println("CUSTOMER Created.");
	}
	
}
```

20. Use `IdentifyGenerator` for custom generator. Implement this interface and override `generate()` method and return a value. Now use this on `@Id`,
```java
@GenericGenerator(name="any_name", strategy="fully.quaified.nameoftheclass")
@GenericValue(generator="any_name")
```

21. Finder methods
```java
List<Product> findByName(String name);
List<Product> findByNameAndDesc(String name, String desc);
List<Product> findByDescLike(String desc);
List<Product> findByPriceGreaterThan(Double price);
```

Google **"spring data query keywords"**

21. `PagingAndSortingRepository` extends `CRUDRepository`

Paging

Interface           Class
Pageable -------> PageRequest
```java
Pageable pageable = new PageRequest(0, 10);
Page<Product> results = repository.findAll(pageable);
results.forEach(p -> System.out.println(p.getName()));
```

Sorting

Interface            Class
Sorting -----------> Sort, Direction, Order
```java
repository.findAll(new Sort("name").forEach(p -> System.out.println(p.getName()))); // name asc

repository.findAll(new Sort(Direction.DESC, "name").forEach(p -> System.out.println(p.getName()))); // name desc

repository.findAll(new Sort(Direction.DESC, "name", "price").forEach(p -> System.out.println(p.getName()))); // name and price both desc

repository.findAll(new Sort.Order(Direction.DESC, "price"), new Sort.Order("name")); // Different direction for different property. An Order represents a Direction and a property
```

Paging and Sorting
```java
Pageable pageeable = new PageRequest(0, 10, Direction.DESC, "name");
Page<Product> results = repository.findAll(pageable);
results.forEach(p -> System.out.println(p.getName()));
```

15. Logging
```java
@RestController
public class LoggingController {

    Logger logger = LoggerFactory.getLogger(LoggingController.class);

    @RequestMapping("/")
    public String index() {
        logger.trace("A TRACE Message");
        logger.debug("A DEBUG Message");
        logger.info("An INFO Message");
        logger.warn("A WARN Message");
        logger.error("An ERROR Message");

        return "Howdy! Check out the Logs to see the output...";
    }
}
```

In order to activate them without changing the configuration, we can pass the –debug or –trace arguments on the command line:
```
java -jar target/spring-boot-logging-0.0.1-SNAPSHOT.jar --trace
```
```properties
logging.level.root=WARN
```

16. @Query
```java
@Query("from Student")
List<Student> findAllStudents();
```

```java
@Query("select s.firstName, s.lastName from Student s")
List<Object[]> findAllStudentsPartialData();
```

17. Named parameter
```java
@Query("from Student where firstName=:fn")
List<Student> findAllStudentsByFirstName(@Param("fn") String firstName);
```

18. `@Query` wont allow to modify any data as it is read only so you need to annotate it with `@Modifying` annotation.

19. `@Rollback(false)`
```java
@Transactional()
@Rollback(false)
```
JUnit doesn't save, it rollbacks instantly, so make Rollback to false.

20. Paging with JPQL
```java
@Query("from Student")
List<Student> findAllStudents(Pageable pageable);
```

```java
repository.findAllStudents(new PageRequest(0, 10));
```

21. `ResponseEntity` represents an HTTP response, including headers, body, and status. While @ResponseBody puts the return value into the body of the response, ResponseEntity also allows us to add headers and status code.

22. DDL is not supported in JPQL.

23. Inheritance mapping
There are 3 types of Inheritance Type: SINGLE_TABLE, TABLE_PER_CLASS, JOINED

SINGLE_TABLE
```java
@Entity
@Inheritance(strategy=InheritanceType.SINGLE_TABLE)
@DiscriminatorColumn(name="pmode", discriminatorType=DiscriminatorType.STRING)
public abstract class Parent {

	@Id
	private int id;
	private double amount;
}
```

```java
@Entity
@DiscriminatorValue("c1")
public class Child1 {

}
```

TABLE_PER_CLASS
```java
@Entity
@Inheritance(strategy=InheritanceType.TABLE_PER_CLASS)
public abstract class Parent {

	@Id
	private int id;
	private double amount;

}
```

```java
@Entity
@DiscriminatorValue("c2")
public class Child2 {

}
```

JOINED
```java
@Entity
@Inheritance(strategy=InheritanceType.JOINED)
public abstract class Parent {

	@Id
	private int id;
	private double amount;
}
```

```java
@Entity
@PrimaryKeyColumn(name="id")
public class Child1 {

	public String childField;
}
```

```java
Child1 child1 = new Child1();
child1.setId(123);
child1.setAmount(9000);
child1.setChildField("Some Value");
repository.save(child1);
```

Parent TABLE
ID 		amount
123		9000

Child1 TABLE
ID 		childfield
123		Some Value


24. Component Mapping (Same Table different Entity classes)

```java
@Entity
public class Employee {

	@Embedded
	Address address;
}

@Embeddable
public class Address {

}

```

25. There are two types of Cache:
1. L1 Cache
2. L2 Cache
3. Query Cache

L1 Cache is different for every Sessions and L2 Cache(SessionFactory) is common between Sessions. However, L2 cache is not included in Hibernate. For that we use external caching providers like ehCache. Swaram Cache, Jboss Tree Cache, OS Cache, Tangosol Cache, Infinispan, etc.

Query Cache: Hibernate can also cache result set of a query. Hibernate Query Cache doesn’t cache the state of the actual entities in the cache; it caches only identifier values and results of value type. So it should always be used in conjunction with the second-level cache.




#### First Level Cache

Hibernate first level cache is associated with the Session object. Hibernate first level cache is enabled by default and there is no way to disable it. However hibernate provides methods through which we can delete selected objects from the cache or clear the cache completely.

```
@Transactional()
@Test()
public void testCaching() {
	repository.findOne(1);
	repository.findOne(1);
}
```
Check SQL is only fired once and not twice. To disable L1 Cache, remove `@Transactional`.


Important Points about First level cache in Hibernate that can be derived from above program are:

- Hibernate First Level cache is enabled by default, there are no configurations needed for this.
- Hibernate first level cache is session specific, that’s why when we are getting the same data in same session there is no query fired whereas in other session query is fired to load the data.
- Hibernate first level cache can have old values, as you can see above that I have put my program to sleep for 10 seconds and in that time I updated the value (name from Pankaj to PankajK) in database but it didn’t get reflected in the same session. But in other session, we got the updated value.
- We can use session evict() method to remove a single object from the hibernate first level cache.
- We can use session clear() method to clear the cache i.e delete all the objects from the cache.
- We can use session contains() method to check if an object is present in the hibernate cache or not, if the object is found in cache, it returns true or else it returns false.
- Since hibernate cache all the objects into session first level cache, while running bulk queries or batch updates it’s necessary to clear the cache at certain intervals to avoid memory issues.

##### Remove Cached Object from L1
```java
@Autowired
EntityManager entityManager;
...

Session session = entityManager.unwrap(Session.class);
Product product = repository.findOne(1);
session.evict(product); // Remove
```

#### EHCache
- Fast and lightweight
- In memory and disk based
- Timeout, total life time

Steps to Caching:
1. Add Maven Dependency.
2. Enable Cache for the application.
3. Cache `ehcache.xml`.
4. Make entities cacheable.

1. 
```xml
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-ehcache</artifactId>
    <version>5.4.27.Final</version>
</dependency>
```
2.
```properties
spring.jpa.properties.hibernate.cache.use_second_level=true
spring.jpa.properties.hibernate.cache.region_factory_class=org.hibernate.cache.ehcache.EhCacheRegionFactory
spring.jpa.properties.javax.persistence.sharedCache.mode=ALL #Share Objects across sessions
spring.cache.ehcache.config=classpath:ehcache.xml #Config file
```

3.
```xml
<ehcache>
	<diskStore path="java.io.tmpdir"/>
	
	<defaultCache maxElementsInMemory="100" eternal="false" timeToIdleSeconds="5" timeToLiveSeconds="10" overflowToDisk="true"/>

</ehcache>
```

4. 
```java
@Entity
@Cache(strategy=CacheConcurrencyStrategy.READ_ONLY)
public class Product implements Serializable {}
```

##### Cache Concurrency Strategy

- READ_ONLY
- NONSTRICT_READ_WRITE
- READ_WRITE
- TRANSACTIONAL

26. Ignore Database Connectivity in Spring Boot

```java
@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class, DataSourceTransactionManagerAutoConfiguration.class, HibernateJpaAutoConfiguration.class})
```

27.

These are different Form content types defined by W3C. If you want to send simple text/ ASCII data, then x-www-form-urlencoded will work. This is the default.

But if you have to send non-ASCII text or large binary data, the form-data is for that.

You can use Raw if you want to send plain text or JSON or any other kind of string. Like the name suggests, Postman sends your raw string data as it is without modifications. The type of data that you are sending can be set by using the content-type header from the drop down.

Binary can be used when you want to attach non-textual data to the request, e.g. a video/audio file, images, or any other binary data file.


28.
Cascading
1. Persist
2. Merge
3. Remove
4. Refresh
5. Detach
6. All

