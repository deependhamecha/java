## Hibernate

Check version of MySQL 
```sql
SHOW VARIABLES LIKE "%version%";
```

### SessionFactory
Reads the hibernate config file. Creates session objects. Heavey-weight object. Only create once in your app.

### Session
Wraps a JDBC Connection. Main object used to save/retrieve objects. Short-lived object. Retrieved from SessionFactory.

### Hibernate Config
Hibernate tries to find file in /src(which is in classpath) with name **hibernate.cfg.xml**. If you want to specify manually or any other file path then use overloaded method of configure. 

Default selection of config file:
```
SessionFactory factory = new Configuration().configure().addAnnotatedClass(Student.class).buildSessionFactory();
Session session = factory.getCurrentSession();
```

Manual selection of config file:
```
SessionFactory factory = new Configuration().configure("hibernate.cfg.xml").addAnnotatedClass(Student.class).buildSessionFactory();
Session session = factory.getCurrentSession();
```

### Save an object

```java

import java.sql.Connection;
import java.sql.DriverManager;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;


public class TestJDBC {

	public static void main(String[] args) {
		// TODO Auto-generated method stub

		String url = "jdbc:mysql://localhost:3306/hb_student_tracker?useSSL=false";
		
		String user = "root";
		String password = "abcd";
		
		SessionFactory factory = new Configuration().configure().addAnnotatedClass(Student.class).buildSessionFactory();
		Session session = factory.getCurrentSession();
		
		try {
			
			// Create a student object
			Student student = new Student("Deepen", "Dhamecha", "deepen@gmail.com");

			// Start a transaction
			session.beginTransaction();

			// Save the student object
			session.save(student);
			
			// Commit transaction
			session.getTransaction().commit();
		} catch(Exception ex) {
			ex.printStackTrace();
		} finally {
			factory.close();
		}
	}

}
```

### Read an object

You need a default constructor for hibernate to create the bean.

```
package com.hibernate1.jdbc;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

public class ReadObject {

	public static void main(String args[]) {
		String url = "jdbc:mysql://localhost:3306/hb_student_tracker?useSSL=false";
		
		String user = "root";
		String password = "abcd";
		
		SessionFactory factory = new Configuration().configure().addAnnotatedClass(Student.class).buildSessionFactory();
		Session session;

		try {
			session = factory.getCurrentSession();
			
			// Start a transaction
			session.beginTransaction();
			
			// Read the object
			Student student = session.get(Student.class, 1);
			
			// Commit the transaction
			session.getTransaction().commit();
			
			System.out.println("Student: "+ student.getFirstName());
			
		} catch(Exception ex) {
			
		} finally {
			factory.close();
		}
	}
}
```

### Read objects with Hibernate Query Language(HQL)

```java
package com.hibernate1.jdbc;

import java.util.List;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;

public class GetAllObjects {

	public static void main(String[] args) {
		String url = "jdbc:mysql://localhost:3306/hb_student_tracker?useSSL=false";
		
		String user = "root";
		String password = "abcd";
		
		SessionFactory factory = new Configuration().configure().addAnnotatedClass(Student.class).buildSessionFactory();
		Session session = null;
		try {
			
			session = factory.getCurrentSession();
			Transaction transaction = session.beginTransaction();
			
			// List<Student> list = session.createQuery("from Student").getResultList();
			List<Student> list = session.createQuery("from Student s where s.firstName='Dilisha'").getResultList();

			transaction.commit();
			
			System.out.println("Student");
			displayStudents(list);
			
		} catch(Exception ex) {
			ex.printStackTrace();
		} finally {
			factory.close();
		}

	}

	private static void displayStudents(List<Student> list) {
		list.forEach(System.out::println);
	}

}
```

### Update a record using object
```java
package com.hibernate1.jdbc;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

public class UpdateObject {

	public static void main(String[] args) {

		String url = "jdbc:mysql://localhost:3306/hb_student_tracker?useSSL=false";
		
		String user = "root";
		String password = "abcd";
		
		SessionFactory factory = new Configuration().configure().addAnnotatedClass(Student.class).buildSessionFactory();
		Session session = null;

		try {
			session = factory.getCurrentSession();
			
			
			session.beginTransaction();
			
			Student student = session.get(Student.class, 1);

			System.out.println("STUDENT: "+ student);

			student.setFirstName("Deepen");

			session.getTransaction().commit();
		} catch(Exception ex) {
			ex.printStackTrace();
		} finally {
			factory.close();
		}
	}

}
```

### Update a record using HQL update

```java
package com.hibernate1.jdbc;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

public class UpdateObject {

	public static void main(String[] args) {

		String url = "jdbc:mysql://localhost:3306/hb_student_tracker?useSSL=false";
		
		String user = "root";
		String password = "abcd";
		
		SessionFactory factory = new Configuration().configure().addAnnotatedClass(Student.class).buildSessionFactory();
		Session session = null;

		try {
			session = factory.getCurrentSession();
			
			session.beginTransaction();

			session.createQuery("update Student set email='dude@gmail.com' where id=1").executeUpdate();

			session.getTransaction().commit();
		} catch(Exception ex) {
			ex.printStackTrace();
		} finally {
			factory.close();
		}
	}

}
```

### Delete an Object

```java
package com.hibernate1.jdbc;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;

public class DeleteObject {

	public static void main(String[] args) {
		String url = "jdbc:mysql://localhost:3306/hb_student_tracker?useSSL=false";
		
		String user = "root";
		String password = "abcd";
		
		SessionFactory factory = new Configuration().configure().addAnnotatedClass(Student.class).buildSessionFactory();
		Session session = null;

		try {
			session = factory.getCurrentSession();
			
			session.beginTransaction();
			
			// With Object
//			Student student = session.get(Student.class, 2);
//			session.delete(student);
			
			// With HQL
			session.createQuery("delete from Student where id=1").executeUpdate();

			session.getTransaction().commit();
			
		} catch(Exception ex) {
			ex.printStackTrace();
		} finally {
			
		}
	}

}

```

### @OneToOne (Uni-directional mapping)

InstructorDetail.java
```java
package com.hibernate1.jdbc.onetoone;

import javax.persistence.CascadeType;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.OneToOne;
import javax.persistence.Table;

@Entity
@Table(name="instructor_details")
public class InstructorDetail {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	@Column(name="id")
	private int id;
	
	@Column(name="youtube_channel")
	private String youtubeChannel;
	
	@Column(name="description")
	private String description;

	@OneToOne(mappedBy = "instructorDetail", cascade = CascadeType.ALL)
	private Instructor instructor;

	public Instructor getInstructor() {
		return instructor;
	}

	public void setInstructor(Instructor instructor) {
		this.instructor = instructor;
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getYoutubeChannel() {
		return youtubeChannel;
	}

	public void setYoutubeChannel(String youtubeChannel) {
		this.youtubeChannel = youtubeChannel;
	}

	public String getDescription() {
		return description;
	}

	public void setDescription(String description) {
		this.description = description;
	}

	public InstructorDetail(String youtubeChannel, String description) {
		super();
		this.youtubeChannel = youtubeChannel;
		this.description = description;
	}

	@Override
	public String toString() {
		return "InstructorDetail [id=" + id + ", youtubeChannel=" + youtubeChannel + ", description=" + description
				+ "]";
	}

	public InstructorDetail() {
		super();
		// TODO Auto-generated constructor stub
	}
	
	
}
```

Instructor.java
```java
package com.hibernate1.jdbc.onetoone;

import javax.persistence.CascadeType;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.OneToOne;
import javax.persistence.Table;

@Entity()
@Table(name="instructors")
public class Instructor {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	@Column(name="id")
	private int id;
	
	@Column(name="first_name")
	private String firstName;
	
	@Column(name="last_name")
	private String lastName;
	
	

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


	@Override
	public String toString() {
		return "Instructor [id=" + id + ", firstName=" + firstName + ", lastName=" + lastName + ", instructorDetail="
				+ instructorDetail + "]";
	}


	public Instructor(String firstName, String lastName, InstructorDetail instructorDetail) {
		super();
		this.firstName = firstName;
		this.lastName = lastName;
		this.instructorDetail = instructorDetail;
	}


	public Instructor() {
		super();
		// TODO Auto-generated constructor stub
	}	
}
```

#### Saving Uni-directional

#### Deleting Uni-directional

#### Saving Bi-directional

#### Deleting Bi-directional

You save data from owner and not from child(mappedBy) else only child's entry is saved not the parent.
If you have a scenario where child would {CascadeType.DETACH, CascadeType.MERGE, CascadeType.PERSIST, CascadeType.REFRESH} do all except for CascadeType.REMOVE then it would throw an Exception. Deletion from owner is fine but deletion from child(mappedBy) causes "javax.persistence.EntityNotFoundException: deleted object would be re-saved by cascade error". So you need to break the bidirectional ref. You do that by `instructorDetail.getInstructor().setInstructorDetail(null);` from your **Main**. By this you are explictly setting NULL in your foreign key column.