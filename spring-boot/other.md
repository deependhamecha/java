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