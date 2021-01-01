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
