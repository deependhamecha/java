# Spring Data JPA

SQL
```sql
create table users (id integer(10), name varchar(255), password varchar(255), email varchar(100), mobileNumber varchar(20), occupation varchar(255));
```

1. Create Entity
```java
@Entity
@Table(name = "users")
public class User {

	@Id
	@GeneratedValue(strategy = GenerationType.AUTO)
	private Long id;

	private String name;
	private String password;
	private String email;
	
	@Column(name = "MOBILE_NUMBER")
	private String mobileNumber;
	private String occupation;

	public Long getId() {
		return id;
	}
	public void setId(Long id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getPassword() {
		return password;
	}
	public void setPassword(String password) {
		this.password = password;
	}
	public String getEmail() {
		return email;
	}
	public void setEmail(String email) {
		this.email = email;
	}
	public String getMobileNumber() {
		return mobileNumber;
	}
	public void setMobileNumber(String mobileNumber) {
		this.mobileNumber = mobileNumber;
	}
	public String getOccupation() {
		return occupation;
	}
	public void setOccupation(String occupation) {
		this.occupation = occupation;
	}	
}
```
2. Create Repository
```java
@Repository
public interface UserRepository extends CrudRepository<User, Long> {
    
    public List<User> findAll();
}
```
3. Open Test class and write

## Create
```java
	@Autowired
	UserRepository userRepository;
	
	@Test
	void testCreate() {
		User user = new User();
		user.setId((long)3);
		user.setName("Sharvil");
		user.setPassword("dude");
		user.setEmail("sharvil@gmail.com");
		user.setMobileNumber("9898989898");
		user.setOccupation("Student");
		
		userRepository.save(user);
	}
```

**Note**: MSSQL might not work with `GenerationType.AUTO`, change it to  `GenerationType.IDENTITY`.

## Read

```java
@Test
	public void testGet() {
		if(userRepository.existsById(1)) {
			userRepository
				.findById(1)
				.ifPresent(
					(e) -> {
						assertNotNull(e);
						assertEquals(e.getName(), "Sharvil");
					}
				);
		}
	}
```

## Update

```java
	@Test
	void testUpdate() {
        if(userRepository.existsById(2)) {
			userRepository
				.findById(2)
				.ifPresent(
					(e) -> {
						e.setEmail("dude@gmail.com");
						User user = userRepository.save(e);
						assertNotNull(user);
						assertEquals(user.getName(), "Sharvil");
					}
				);
		}
	}
```

## Delete

```java
	@Test
	void testDelete() {
        if(userRepository.existsById(2)) {
			userRepository
				.findById(2)
				.ifPresent(
					(e) -> {
						userRepository.delete(e);
						assertNull(e);
					}
				);
		}
	}
```



## Table Generator

```sql
create table employee(id int PRIMARY KEY AUTO_INCREMENT, name varchar(20));
create table id_gen(gen_name varchar(255) PRIMARY KEY, gen_val int(20));
```


### Auto increment
```java
@Id
@GeneratedValue(strategy=GenerationType.IDENTITY)
private int id;
```

### Table type strategy

Custom Table Generator
```java
@Id
@TableGenerator(name="employee_gen", table="id_gen", pkColumnName="gen_name", valueColumnName="gen_val", allocationSize=10)
@GeneratedValue(strategy=GenerationType.TABLE, generator="employee_gen")
private int id;
```

### Custom ID Generator

Custom ID Generator class
```java
public class CustomRandomGenerator implements IdentiferGenerator {

	@Override
	public Serializable generate(SessionImplementor arg0, Object arg1) throws HibernateException {
		
		int id = 0;
		Random random = new Random();
		random.nextInt(100000);

		return id;
	}
}
```

Entity class
```java
@Entity
public class Employee {

	// emp_id is custom name, Generator name

	@Id
	@GenericGenerator(name="emp_id", strategy="packagename.CustomRandomGenerator")
	@GeneratedValue(generator="emp_id")
	public int id;

	....
}
```

## Query Methods

Derived queries with the predicates `IsStartingWith, StartingWith, StartsWith, IsEndingWith, EndingWith, EndsWith, IsNotContaining, NotContaining, NotContains, IsContaining, Containing, Contains` the respective arguments for these queries will get sanitized. This means if the arguments actually contain characters recognized by LIKE as wildcards these will get escaped so they match only as literals. The escape character used can be configured by setting the escapeCharacter of the @EnableJpaRepositories annotation. 

```java
	@Test
	@Disabled
	public void testQueryMethod1() {
		Optional<User> user$ = userRepository.findByName("Deepen");
		user$.ifPresent(e -> {assertNotNull(e);});
		
		//user$.ifPresent(System.out::println);
	} 
	
	@Test
	@Disabled
	public void testQueryMethod2() {
		//Optional<User> user$ = userRepository.findByNameAndEmail("Deepen", "sharvil@gmail.com");
		//user$.ifPresent(e -> {assertNotNull(e);});
		
		//user$.ifPresent(e -> {System.out.println("e: "+e.getName());});
		
		Optional<List<User>> user$ = userRepository.findByNameAndEmail("Deepen", "sharvil@gmail.com");
		user$.ifPresent(e1 -> {
			e1.forEach(
				e -> {
					System.out.println("e: "+e.getName());
				}
			);
		});
	}
	
	
	@Test
	@Disabled
	public void testQueryMethod3() {
		
		Optional<List<User>> user$ = userRepository.findByEmailAndPassword("sharvil@gmail.com", "dude");
		user$.ifPresent(e1 -> {
			e1.forEach(
				e -> {
					System.out.println("e: "+e.getName());
				}
			);
		});
	}
	
	@Test
	public void testQueryMethod4() {
		
		Optional<User> user$ = userRepository.findByNameStartingWith("Dee");
		user$.ifPresent(
		
			e -> {
				System.out.println("e: "+e.getName());
			}
		
		);
	}
	
```

```java
@Repository
public interface UserRepository extends CrudRepository<User, Integer> {
	
	public List<User> findAll();
	public Optional<User> findByName(String name);
	//public Optional<User> findByNameAndEmail(String name, String email);
	public Optional<List<User>> findByNameAndEmail(String name, String email);
	
	public Optional<List<User>> findByEmailAndPassword(String name, String email);
	public Optional<User> findByNameStartingWith(String name);
}
```


[Continue](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods.named-queries)

## Paging and Sorting

Earlier versions had `PageRequest()` constructor and in newer version it is `PageRequest.of()`.


To enable Paging and Sorting do this:
```java
public interface ProductRepository extends PagingAndSortingRepository<Product, Integer> {

}
```

To use:

```java
@Test
public void testFindAllPage() {
	Pageable pageable = new PageRequest(0, 1); // 0 is the page index, 1 is size
	Page<Product> results = repository.findAll(pageable);
	results.forEach(p -> System.out.println(p.getName()));
}
```

Order is means column name.
Direction is default to ASC.

Sort
```java
repository
	.findAll(Sort.by(Direction.DESC, "name", "price"))
	.forEach(p -> System.out.println(p.getName()));
```

Easier way,

```java
Pageable sortedByName = 
  PageRequest.of(0, 3, Sort.by("name"));

Pageable sortedByPriceDesc = 
  PageRequest.of(0, 3, Sort.by("price").descending());

Pageable sortedByPriceDescNameAsc = 
  PageRequest.of(0, 5, Sort.by("price").descending().and(Sort.by("name")));
```

## JPQL

Example:
```
Employee
	int id;
	String firstName;
	String lastName;
```

```
employee_table
	empid
	fname
	lname
```

JPQL
```
select * from Employee
select firstName, lastName from Employee
```

Start by creating table:
```
create table student(
	id int PRIMARY KEY IDENTITY(1,1),
	fname varchar(20),
	lname varchar(20),
	score int
	)
```

###### JPA Entity
```java
@Entity
public class Student {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private int id;

	@Column(name="fname")
	private String firstName;
	
	@Column(name="lname")
	private String lastName;

	private int score;

	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public String getFirstName() {
		return firstName;
	}
	public void setFirstName(String firstName) {
		this.firstName = firstName;
	}
	public String getLastName() {
		return lastName;
	}
	public void setLastName(String lastName) {
		this.lastName = lastName;
	}
	public int getScore() {
		return score;
	}
	public void setScore(int score) {
		this.score = score;
	}

	@Override
	public String toString() {
		return "Student [id=" + id + ", firstName=" + firstName + ", lastName=" + lastName + ", score=" + score + "]";
	}	
}
```

If you need all columns then skip `select` statement.

```java
@Repository
public interface StudentRepository extends CrudRepository<Student, Integer> {

	@Query("from Student")
	List<Student> findAllStudents();
}
```

###### Reading Partial Date
```java
@Query("select firstName, lastName from Student st")
	List<Object[]> findAllStudentsWithFNameAndLName();
```

```java
this
	.studentRepository
	.findAllStudentsWithFNameAndLName()
	.forEach(e -> System.out.println(e[0]+" "+e[1]));
```

###### Using Named Query Parameters
```java
@Repository
public interface StudentRepository extends CrudRepository<Student, Integer> {

	@Query("from Student where firstName=:firstName")
	List<Student> findAllStudents(@Param("firstName") String firstName);

	@Query("select firstName FROM Student WHERE score BETWEEN :min AND :max")
	List<Object[]> findStudentsForGivenScore(@Param("min") int min, @Param("max") int max);
}
```

```java
this.studentRepository.findAllStudentsWithFirstName("deepen").forEach(System.out::println);
List<Object[]> students = this.studentRepository.findStudentsForGivenScore(70, 100);
```

###### Modifying Data
```java
@Modifying
@Query("delete from Student where name=:firstname")
boolean deleteStudent(@Param("firstname") String firstname);
```

```java
@Test
@Transactional
@Rollback(false)
public void testFindAllStudents() {
	boolean flag = this.studentRepository.deleteStudent("Dilisha");		
}
```
If you do not provide `@Rollback` for Test, it will by default rollback.
Always, remember, while modifying data, always annotate it with `@Transaction`.

###### Paging with JPQL
```java
@Query("from Student")
List<Student> findStudentsWithPageNo(Pageable pageable);
```

```java
@Test
public void testfindStudentsWithPageNo() {
	this.studentRepository.findStudentsWithPageNo(PageRequest.of(0, 2)).forEach(e -> System.out.println(e.getFirstName()));
}
```

## Native Query

```java
@Query("select * from student", nativeQuery=true)
public List<Student> findAllStudentsWithNQ()
```
Thats it, 

###### Named parameters in Native Query
```java
@Query("select * from student where fname=:firstname", nativeQuery=true)
public List<Student> findbyFirstName(@Param("firstName") String firstName)
```

## Inheritance mapping

SQL
```
use twitterdb;

create table payment(
id int PRIMARY KEY IDENTITY(1,1),
pmode varchar(2),
amount decimal(8,3) ,
cardnumber varchar(20),
checknumber varchar(20)
);
```

Same table but different entity models.
Payment (Parent)
--> Credit Card (Child)
--> Cheque (Child)

##### SINGLE_TABLE
```java
@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
@DiscriminatorColumn(name = "pmode", discriminatorType = DiscriminatorType.STRING) // cc=credit, ch=cheque
@Table(name = "payment")
public class Payment {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private int id;
	private double amount;

	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public double getAmount() {
		return amount;
	}
	public void setAmount(double amount) {
		this.amount = amount;
	}
}
```

```java
@Entity
@DiscriminatorValue(value = "ch")
public class Cheque extends Payment {

	@Column(name = "checknumber")
	private String chequeNumber;

	public String getChequeNumber() {
		return chequeNumber;
	}

	public void setChequeNumber(String chequeNumber) {
		this.chequeNumber = chequeNumber;
	}
}
```

```java
@Entity
@DiscriminatorValue(value = "cc")
public class CreditCard extends Payment {

	@Column(name = "cardnumber")
	private String creditCard;

	public String getCreditCard() {
		return creditCard;
	}

	public void setCreditCard(String creditCard) {
		this.creditCard = creditCard;
	}	
}
```


##### TABLE_PER_CLASS

This will keep inheritance between objects but will store records in separate table.

```java
@Entity
@Inheritance(strategy = InheritanceType.TABLE_PER_CLASS)
public class Payment {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private int id;
	private double amount;

	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public double getAmount() {
		return amount;
	}
	public void setAmount(double amount) {
		this.amount = amount;
	}
}
```

```java
@Entity
@Table(name="cheque")
public class Cheque extends Payment {

	@Column(name = "checknumber")
	private String chequeNumber;

	public String getChequeNumber() {
		return chequeNumber;
	}

	public void setChequeNumber(String chequeNumber) {
		this.chequeNumber = chequeNumber;
	}
}
```

```java
@Entity
@Table(name="creditcard")
public class CreditCard extends Payment {

	@Column(name = "cardnumber")
	private String creditCard;

	public String getCreditCard() {
		return creditCard;
	}

	public void setCreditCard(String creditCard) {
		this.creditCard = creditCard;
	}	
}
```

##### JOINED

Common entries in parent table and rest in subclass tables.
```java
@Entity
@Inheritance(strategy = InheritanceType.JOINED)
public class Payment {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private int id;
	private double amount;

	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public double getAmount() {
		return amount;
	}
	public void setAmount(double amount) {
		this.amount = amount;
	}
}
```

```java
@Entity
@Table(name="cheque")
@PrimaryKeyJoinColumn("id")
public class Cheque extends Payment {

	@Column(name = "checknumber")
	private String chequeNumber;

	public String getChequeNumber() {
		return chequeNumber;
	}

	public void setChequeNumber(String chequeNumber) {
		this.chequeNumber = chequeNumber;
	}
}
```

```java
@Entity
@Table(name="creditcard")
@PrimaryKeyJoinColumn("id")
public class CreditCard extends Payment {

	@Column(name = "cardnumber")
	private String creditCard;

	public String getCreditCard() {
		return creditCard;
	}

	public void setCreditCard(String creditCard) {
		this.creditCard = creditCard;
	}	
}

## Component Mapping

When a class has-a relationship then use `@Embedded` and `@Embeddable` annotation,
where table will be one but different Entity class for each.

```sql
use twitterdb;

create table employee(
id int,
name varchar(20),
streetaddress varchar(30),
city varchar(20),
state varchar(20),
zipcode varchar(20),
country varchar(20)
)
```

```java
@Entity
public class Employee {

	@Id
	private int id;
	
	private String name;
	
	@Embedded
	private Address address;

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

	public Address getAddress() {
		return address;
	}

	public void setAddress(Address address) {
		this.address = address;
	}
}
```

```java
@Embeddable
public class Address {

	@Column(name = "streetaddress")
	private String streetAddress;
	private String city;
	private String state;
	private String zipcode;
	private String country;

	public String getStreetAddress() {
		return streetAddress;
	}
	public void setStreetAddress(String streetAddress) {
		this.streetAddress = streetAddress;
	}
	public String getCity() {
		return city;
	}
	public void setCity(String city) {
		this.city = city;
	}
	public String getState() {
		return state;
	}
	public void setState(String state) {
		this.state = state;
	}
	public String getZipcode() {
		return zipcode;
	}
	public void setZipcode(String zipcode) {
		this.zipcode = zipcode;
	}
	public String getCountry() {
		return country;
	}
	public void setCountry(String country) {
		this.country = country;
	}	
}
```

```java
@Repository
public interface EmployeeRepository extends CrudRepository<Employee, Integer> {}
```

```java
@Test
public void testEmbedded() {
	Employee emp = new Employee();
	Address address = new Address();
	
	emp.setId(1);
	emp.setName("Dude");
	address.setCity("Mumbai");
	address.setCountry("India");
	address.setState("Maharashtra");
	address.setStreetAddress("LBS Road");
	address.setZipcode("400086");
	emp.setAddress(address);

	this.empRepo.save(emp);
}
```

## Relationships

Step by step we will explore all the attributes of the annotation and concepts of relationships.

#### OneToMany
```java
@Entity
@Table(name="customer")
public class Customer {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private int id;
	
	private String name;
	
	/*
	 *	customer table owns the relationship 
	 *
	*/
	@OneToMany(mappedBy = "customer")
	private Set<PhoneNumber> phoneNumbers;
	

	public Set<PhoneNumber> getPhoneNumbers() {
		return phoneNumbers;
	}

	public void setPhoneNumbers(Set<PhoneNumber> phoneNumbers) {
		this.phoneNumbers = phoneNumbers;
	}

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
}
```

```java
@Entity
@Table(name = "phone_number")
public class PhoneNumber {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private int id;
	
	private String number;
	
	private String type;
	
	@ManyToOne
	@JoinColumn(name = "customer_id") // Foreign Key
	private Customer customer;

	public Customer getCustomer() {
		return customer;
	}

	public void setCustomer(Customer customer) {
		this.customer = customer;
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getNumber() {
		return number;
	}

	public void setNumber(String number) {
		this.number = number;
	}

	public String getType() {
		return type;
	}

	public void setType(String type) {
		this.type = type;
	}
}
```

```java
@Test
public void testOneToMany() {
	Customer customer = new Customer();
	customer.setName("Neha");
	
	HashSet<PhoneNumber> numbers = new HashSet<>();
	
	PhoneNumber pn1 = new PhoneNumber();
	pn1.setNumber("9876543210");
	pn1.setType("Mobile");
	
	PhoneNumber pn2 = new PhoneNumber();
	pn2.setNumber("9876543201");
	pn2.setType("Home");
	
	numbers.add(pn1);numbers.add(pn2);
	
	customer.setPhoneNumbers(numbers);
	
	this.customerRepo.save(customer);
}
```
1. This will only store records into `customer` table and not phone number table since, we havent
cascade it.
2. `@OneToMany(mappedBy)` points to the property in `PhoneNumber` class. If you change the name `customer`
to `customer1` then it throw an exception saying
```
mappedBy reference an unknown target entity property: com.twitter.models.PhoneNumber.customer in com.twitter.models.Customer.phoneNumbers
```
3. The owner side, which has foreign key should have `@JoinColumn(name="foreign_key")` which specifies foreign key column.
4. The non-owner side should have `@OneToMany(mappedBy="attributeName")`.
5. Type of object is decided by the Type/Generic Type(Collections). Suppose you have a collection without generic type,
then specify target class by `@OneToMany(targetEntity=PhoneNumber.class)` accordingly.

```java
@Test
public void testManyToOne() {
	Customer customer = this.customerRepo.findById(1).orElse(null);
	
	PhoneNumber pn1 = new PhoneNumber();
	pn1.setNumber("9876543210");
	pn1.setType("Mobile");
	pn1.setCustomer(customer);
	this.phoneNumberRepo.save(pn1);
}
```
Consider, same other way around for PhoneNumber.

#### Cascade

Update the data with relationships

```java
@OneToMany(mappedBy = "customer", cascade = CascadeType.ALL)
```

This will save the phone numbers but will not map `customer_id` and the column will be `NULL`.

So, if you want to update rest of the fields but do not want to update the foreign key, then this
will work.

#### Lazy Initializing
Now, if you do
```java
@Test
public void testRead() {
	Customer customer = this.customerRepo.findById(8).orElse(null);
	customer.getPhoneNumbers().forEach(number -> System.out.println(number.getNumber()));
}
```

It will throw an exception:
```
org.hibernate.LazyInitializationException: failed to lazily initialize a collection of role:
com.twitter.models.Customer.phoneNumbers, could not initialize proxy - no Session
```

Which means, it will only load Customer data but it wont load relationship object.

By Default, relationship objects are lazy loaded. If you want EAGER you can specify using

```java
@OneToMany(mappedBy = "customer", cascade = CascadeType.ALL, fetch = FetchType.EAGER)
private Set<PhoneNumber> phoneNumbers;
```

Note the query fired.
```sql
customer0_.id as id1_0_0_, customer0_.name as name2_0_0_, phonenumbe1_.customer_id as customer4_3_1_, phonenumbe1_.id as id1_3_1_, phonenumbe1_.id as id1_3_2_, phonenumbe1_.customer_id as customer4_3_2_, phonenumbe1_.number as number2_3_2_, phonenumbe1_.type as type3_3_2_ from customer customer0_ left outer join phone_number phonenumbe1_ on customer0_.id=phonenumbe1_.customer_id where customer0_.id=?
```

Now, if you are fetching phoneNumber and doing `getCustomer()` then it will load Customer on to
the phone number object because the **owner** is `Customer` irrespective of the `FetchType`.
Owner should have `FetchType` specified.

If you want lazy loaded objects then apply `@Transactional` annotation to the method where you are
fetching and it will fire queries separately.

```java
Customer customer = this.customerRepo.findById(8).orElse(null);
customer.getPhoneNumbers().forEach(number -> System.out.println(number.getNumber()));
```

```sql
Hibernate: select customer0_.id as id1_0_0_, customer0_.name as name2_0_0_ from customer customer0_ where customer0_.id=?
Hibernate: select phonenumbe0_.customer_id as customer4_3_0_, phonenumbe0_.id as id1_3_0_, phonenumbe0_.id as id1_3_1_, phonenumbe0_.customer_id as customer4_3_1_, phonenumbe0_.number as number2_3_1_, phonenumbe0_.type as type3_3_1_ from phone_number phonenumbe0_ where phonenumbe0_.customer_id=?
```

##### Update
```java
@Test
@org.springframework.transaction.annotation.Transactional
@Rollback(false)
public void testUpdate() {
	Customer customer = this.customerRepo.findById(8).orElse(null);
	Set<PhoneNumber> pns = customer.getPhoneNumbers();
	pns.forEach(number -> number.setType("Cell"));
	
	this.customerRepo.save(customer);
}
```

Now, here fetchtype is LAZY and if you update PhoneNumber but do not update Customer then, hibernate
is smart enough to identify and it wont fire update query onto customer table.
```java
@Test
@org.springframework.transaction.annotation.Transactional
@Rollback(false)
public void testUpdate() {
	Customer customer = this.customerRepo.findById(8).orElse(null);
	Set<PhoneNumber> pns = customer.getPhoneNumbers();
	pns.forEach(number -> number.setType("Cell"));
	
	this.customerRepo.save(customer);
}
```

```java
@Test
@org.springframework.transaction.annotation.Transactional
@Rollback(false)
public void testUpdate() {
	Customer customer = this.customerRepo.findById(8).orElse(null);
	customer.setName("Dude1");
	Set<PhoneNumber> pns = customer.getPhoneNumbers();
	pns.forEach(number -> number.setType("Cell1"));
	
	this.customerRepo.save(customer);
}
```

##### Delete

If you delete Customer then it will also delete phone numbers first. If you do not want to delete
phonenumbers then specify cascade type manually and do not mention `CascadeType.DELETE`.

Now, it will fire two queries.

#### Many to Many mapping
```sql
use TWITTERDB;

create table programmer(
id int PRIMARY KEY IDENTITY(1,1),
name varchar(20),
salary int
)

create table project(
id int PRIMARY KEY IDENTITY(1,1),
name varchar(20)
)

create table programmers_projects(
id int PRIMARY KEY IDENTITY(1,1),
programmer_id int,
project_id int,
FOREIGN KEY (programmer_id)
REFERENCES programmer(id),
FOREIGN KEY (project_id)
REFERENCES project(id)
)

select * from programmer
select * from project
select * from programmers_projects
```

```java
@Entity
public class Programmer {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private int id;
	
	private String name;
	
	private int salary;
	
	@ManyToMany(cascade = CascadeType.ALL)
	@JoinTable(
			name = "programmers_projects",
			joinColumns=@JoinColumn(name = "programmer_id", referencedColumnName = "id"),
			inverseJoinColumns = @JoinColumn(name="project_id", referencedColumnName = "id")
	)
	Set<Project> projects;

	public Set<Project> getProjects() {
		return projects;
	}

	public void setProjects(Set<Project> projects) {
		this.projects = projects;
	}

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

	public int getSalary() {
		return salary;
	}

	public void setSalary(int salary) {
		this.salary = salary;
	}	
}
```

```java
@Entity
public class Project {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private int id;

	private String name;
	
	@ManyToMany(mappedBy = "projects")
	Set<Programmer> programmers;

	public Set<Programmer> getProgrammers() {
		return programmers;
	}

	public void setProgrammers(Set<Programmer> programmers) {
		this.programmers = programmers;
	}

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
}
```
The `@JoinTable` annotation is used to configure the join table for the relationship. The two join 
columns in the join table are distinguished by means of the owning and inverse sides. The join 
column to the owning side is described in the `joinColumns` element, while the join column to the 
inverse side is specified by the `inverseJoinColumns` element. You can see from the previous example 
that the values of these elements are actually `@JoinColumn` annotations embedded within the 
`@JoinTable` annotation.

When no `@JoinTable` annotation is present on the owning side, then a default join table named `<Owner>_<Inverse>` is assumed.

```java
@Test
public void testm2mCreateProgrammer() {
	Programmer programmer = new Programmer();
	programmer.setName("Deepen");
	programmer.setSalary(10000);

	HashSet<Project> projects = new HashSet<Project>();

	Project p1 = new Project();
	p1.setName("IBC");

	projects.add(p1);
	programmer.setProjects(projects);

	this.programmerRepo.save(programmer);
}

@Test
@org.springframework.transaction.annotation.Transactional
public void testm2mLoadProgrammer() {
	this
		.programmerRepo
		.findById(2)
		.ifPresent(e -> e.getProjects()
							.forEach(e1 -> System.out.println(e1.getName()))
		);
}
```

##### One to One mapping

```java
@Entity
public class Person {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private int id;

	@Column(name = "first_name")
	private String firstName;
	
	@Column(name = "last_name")
	private String lastName;
	
	private int age;

	@OneToOne(mappedBy = "person")
	private License license;

	public License getLicense() {
		return license;
	}

	public void setLicense(License license) {
		this.license = license;
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getFirstName() {
		return firstName;
	}

	public void setFirstName(String firstName) {
		this.firstName = firstName;
	}

	public String getLastName() {
		return lastName;
	}

	public void setLastName(String lastName) {
		this.lastName = lastName;
	}

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}	
}
```

```java
@Entity
public class License {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private int id;

	private String type;

	@Column(name = "valid_from")
	private Date validFrom;

	@Column(name = "valid_to")
	private Date validTo;

	// the entity who is saving must have cascadetype
	@OneToOne(cascade = CascadeType.ALL)
	@JoinColumn(name = "person_id")
	private Person person;

	public Person getPerson() {
		return person;
	}

	public void setPerson(Person person) {
		this.person = person;
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getType() {
		return type;
	}

	public void setType(String type) {
		this.type = type;
	}

	public Date getValidFrom() {
		return validFrom;
	}

	public void setValidFrom(Date validFrom) {
		this.validFrom = validFrom;
	}

	public Date getValidTo() {
		return validTo;
	}

	public void setValidTo(Date validTo) {
		this.validTo = validTo;
	}
}
```

```java
@Test
@Transactional
@Rollback(false)
public void testOne2OneLicense() {
	License license = new License();
	license.setType("Car");
	license.setValidFrom(new Date());
	license.setValidTo(new Date());
	
	Person person = new Person();
	person.setFirstName("Deepen");
	person.setLastName("Dhamecha");
	person.setAge(30);

	license.setPerson(person);

	this.licenseRepo.save(license);
}
```

You can also have one extra table for `@OneToMany` relation.
```java
@Entity 
public class Employee { 
	@Id private int id; 
	private String name; 

	@OneToMany 
	@JoinTable(name="EMP_PHONE", 
		joinColumns=@JoinColumn(name="EMP_ID"), 
		inverseJoinColumns=@JoinColumn(name="PHONE_ID"))

	private Collection<Phone> phones; 
	// ...
}
```
The name of the join table would default to EMPLOYEE_PHONE and would have a join column named EMPLOYEE_ID after the name of the Employee
entity and its primary key column. The inverse join column would be named PHONES_ID, which is 
the concatenation of the phones attribute in the Employee entity and the ID primary key column of the 
PHONE table.

#### Types of Cascade
> Pending......


## Caching

There are 2 levels of caching:
1. Level 1: Session
2. Level 2: SessionFactory

#### Level 1
Hibernate session stores in cache when data is accessed first time. Every single session will have
different **Cache**.

#### Level 2
All sessions will share a common **Cache**.

Hibernate provides Level 1 cache but for Level 2, we need external libraries and those are:
1. ehcache
2. swaram cache
3. Jboss Tree cache
4. OS cache
5. Tangosol cache

For level 1 caching, you will need to add `@Transactional` annotation.

```java
@Test
@Transactional
public void testCaching() {
	repository.findById(1).get();
	repository.findById(1).get();
}
```

Here, there will be only 1 select query fired. If you remove `@Transactional`, then caching wont work.

#### Remove Object from Cache
```java
@Autowired
EntityManager entityManager;

@Test
@Transactional
public void testEvict() {
	Session sesssion = entityManager.unwrap(Session.class);

	Product product = repository.findById(1);

	session.evict(product);

	repository.findById(1);
}
```

Now, it will fire two queries since we removed the object from Session.


### EhCache

Its a Second level cache provider. Its an in-memory and disk based. We can give life time of an object.

Steps to configure:
1. Add maven dependency.
2. Enable Cache for the application.
**application.properties**`
```properties
spring.jpa.properties.hibernate.cache.use_second_level_cache=true
spring.jpa.properties.hibernate.cache.region.factory_class=org.hibernate.cache.ehcache.EhCacheRegion
spring.jpa.properties.javax.persistence.sharedCache.mode=ALL #Cache Shared across session
spring.cache.ehcache.config=classpath:ehcache.xml
```
3. Create ehcache.xml
```xml
<ehcache>
	<diskStore path="java.io.tmpdir"/>
	
	<defaultCache maxElementsInMemory="100" eternal="false" timeToIdleSeconds="5" timeToLiveSeconds="10" overflowToDisk="true"/>

</ehcache>
```
`maxElementsInMemory=100 Cached elements in memory`
`eternal=Should not live forever`
`timeToIdleSeconds=Object is not accessed for more than 5 seconds, then remove it`
`timeToLiveSeconds=After 10 seconds the object will be removed.`
`overflowToDisk=If overflow then store to Disk`

4. Make entities cacheable.

#### Caching Concurrent Strategies

1. READ_ONLY
Only when the application is reading the data and not updating it. If you update it then it will
throw an exception.

2. NONSTRICT_READ_WRITE
Cache will be updated only when a particular transaction completely commits.

3. READ_WRITE
When data is updating, it will implement a lock on to cached object. So that when object accessed, it 
will go to transaction.

4. TRANSACTIONAL

```java
@Entity
@Cache(usage=CacheConcurrencyStrategy.READ_ONLY)
public class Product implements Serializable {} // Make sure to implement Serializable
```

If you remove `@Cache` annotation then it will disable caching.

## Transaction

Transaction App calls Transaction Manager/Co-ordinator which inturns calls Resource Manager which does
the operation with DB.

`@Transactional` wont commit the whole method runs properly(without Runtime exception or error). Check while debugging.
```java
@Test
@Transactional
@Rollback(false)
public void testTransactional() {
	License license = this.licenseRepo.findById(3).orElse(null);
	license.setType("Bike");
	this.licenseRepo.save(license);

	license.setType("Bike with Gear"); // Breakpoint here.
	this.licenseRepo.save(license);
}
```

## Save and Retrive files (Blob)

The data types in SQL Server that allow for BLOB storage are `VARCHAR(MAX)`, `NVARCHAR(MAX)` and `VARBINARY(MAX)`. 

**My SQL**
```sql
use mydb

create TABLE image(
	id BIGINT NOT NULL,
	name varchar(100) NOT NULL,
	data BLOB NOT NULL,
	PRIMARY KEY(id)
);

-- drop table image
```


```java
@Test
public void testImageSave() throws IOException {
	Image image = new Image();
	image.setId(1);
	image.setName("maven.jpg");
	
	// Read the file
	File file = new File("D:\\1154724.png");
	byte[] fileContent = new byte[(int) file.length()];

	FileInputStream fileInputStream = new FileInputStream(file);
	fileInputStream.read(fileContent);

	image.setData(fileContent);
	
	this.imageRepo.save(image);
	fileInputStream.close();
}
```

To retrieve files from database, just take File object and do `FileOutputstream` by doing `fos.write(image.getData())`.

## Stored Procedures

There are 2 approaches using Spring Data JPA, one of them is using `@NamedStoredProcedureQuery` but with version to version it keeps changing.

Best approach to using **Stored Procedure** is by using **Spring JDBC**.

```sql
select * from product;

DELIMITER //

CREATE PROCEDURE GetAllProducts()
BEGIN
	SELECT *  FROM product;
END //


DELIMITER //

CREATE PROCEDURE GetAllProductsByPrice(IN price_in decimal)
BEGIN
	SELECT *  FROM product where price>price_in;
END //

DELIMITER //

CREATE PROCEDURE GetAllProductsCountByPrice(IN price_in decimal)
BEGIN
	SELECT count(*)  FROM product where price>price_in;
END //

drop procedure GetAllProducts;
drop procedure GetAllProductsByPrice;
drop procedure GetAllProductsCountByPrice;
```

```java
@Query(value="Call GetAllProducts", nativeQuery=true)
List<Product> findAllProducts();
```

Invoke using input params
```java
@Query(value="Call GetAllProductsByPrice (:priceIn)", nativeQuery=true)
List<Product> findAllProductsByPrice(Double priceIn);
```

```java
@Query(value="Call GetAllProductsByPrice (:priceIn)", nativeQuery=true)
int countAllProductsByPrice(Double priceIn); // It will automatically be converted to int
```


