# Java Persistence API <a name="jpa"></a>

“The domain model has a class. The database has a table. They look pretty similar. It should be simple to convert one to the other automatically.” This is a thought we’ve probably all had at one point or another while writing yet another Data Access Object (DAO) to convert Java Database Connectivity (JDBC) result sets into something object-oriented.

### JPA History

Earlier versions of EJB, defined persistence layer combined with business logic layer using javax.ejb.EntityBean Interface.

- While introducing EJB 3.0, the persistence layer was separated and specified as JPA 1.0 (Java Persistence API). The specifications of this API were released along with the specifications of JAVA EE5 on May 11, 2006 using JSR 220.

- JPA 2.0 was released with the specifications of JAVA EE6 on December 10, 2009 as a part of Java Community Process JSR 317.

- JPA 2.1 was released with the specification of JAVA EE7 on April 22, 2013 using JSR 338.

### JPA Relationships
![](/deependhamecha/java/raw/master/jpa/images/jpa_class_relationships.png)
- The relationship between EntityManagerFactory and EntityManager is one-to-many. It is a factory class to EntityManager instances.

- The relationship between EntityManager and EntityTransaction is one-to-one. For each EntityManager operation, there is an EntityTransaction instance.

- The relationship between EntityManager and Query is one-to-many. Many number of queries can execute using one EntityManager instance.

- The relationship between EntityManager and Entity is one-to-many. One EntityManager instance can manage multiple Entities.

> Previously, hibernate-annotations was released and versioned from hibernate core. But from version 3.5 and up it is included with hibernate core. And for some reason it was still released from 3.5.0 to 3.5.6 but you do not need it anymore.

<persistence-unit transaction-type="RESOURCE_LOCAL">
<persistence-unit transaction-type="JTA">

JPA implementations have the choice of managing transactions themselves (RESOURCE_LOCAL), or having them managed by the application server's JTA implementation. In most cases, RESOURCE_LOCAL is fine. This would use basic JDBC-level transactions. The downside is that the transaction is local to the JPA persistence unit, so if you want a transaction that spans multiple persistence units (or other databases), then RESOURCE_LOCAL may not be good enough.