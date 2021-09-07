# Generics

The type in angle brackets is referred to as either 'parameterized type', 'type parameter'  or just 'type'.

Examples: 
```java
void addSomething(List<String> strings) {
  strings.add("Something");
}
```

## As soon has you pass it to a non-generic implementation, type vanishes.
```java
import java.util.*;

public class Main1 {

	List<String> getListOfSomething() {

		List list = new ArrayList();

		list.add("Something");

		return list;

	}

	public static void main(String[] args) {
		List list = new Main1().getListOfSomething();
		System.out.println(list);
	}
	
}
```

## Below holds any object:
```java
List<Object> list = new ArrayList<Object>();
```

## Unboxing
With Non-Generic,
```java
List test = new ArrayList();
tset.add(45);
int x = (Integer) test.get(0); // You need to cast
```

With Generics,
```java
List<Integer> test = new ArrayList<>();
tset.add(45);
int x = test.get(0);
```

## Generic Polymorphism (Doesnt work)
```java
List<Button> list = new ArrayList<JButton>();
```


## Problem with Polymorphic Arrays
```java
public void foo() {
	Dog[] dogs = {new Dog(), new Dog()};
}

public void addAnimal(Animal[] animals) {
	animals[0] = new Dog();
}

public void foo() {
	Cat[] cats = {new Cat(), new Cat()};
	addAnimal(cats); // Ouch!
}
```
You get a run time Exception (ArrayStoreException).
The reason this isnt allowed in generics because at runtime generics doesnt exist.

## Wildcard
"Hey, Im using the collection passed in just to invoke methods on the elements - and I promise not to ADD anything into the collection."
```java
public void addAnimal(List<? extends Animal> animals)
```

`<?> extends Animal>` I can be assigned a collection that is a subtype of List and typed for <Animal> or anything that *extends* Animal. And oh yes, I SWEAR that I will not ADD anything into the collection.
	
```java
import java.util.*;

abstract class Animal {
	public abstract void dude();
}

class Dog extends Animal {
	public void dude() {
		System.out.println("Dog Dude");
	}
}

class Cat extends Animal {
	public void dude() {
		System.out.println("Cat Dude");
	}
}

public class Main2 {

	void check(List<? extends Animal> list) {
		System.out.println(list.size());
//		list.add(new Dog());	// COMPILER ERROR
	}

	public static void main(String[] args) {
		List<Animal> list = new ArrayList<>();
		list.add(new Dog());
		list.add(new Cat());

		List<Dog> dogs = new ArrayList<>();
		dogs.add(new Dog());
		new Main2().check(dogs);
	}
	
}
```

> Note: There is no `<? implements Serializable>` on `extends`. If its an interface then `<? extends Serializable>`

## Super

```java
import java.util.*;

class Animal {
	void dude() {}
}

class Dog extends Animal {
	public void dude() {
		System.out.println("Dog Dude");
	}
}

class Cat extends Animal {
	public void dude() {
		System.out.println("Cat Dude");
	}
}

public class Main2 {

	void check(List<? super Dog> list) {
		System.out.println(list.size());
		list.add(new Dog());
//		list.add(new Animal()); // IF YOU TRY TO ADD ANYTHING OTHER THAN `Dog`, IT WILL THROW A COMPILE TIME ERROR
	}

	public static void main(String[] args) {
		List<Animal> list = new ArrayList<>();
		list.add(new Dog());
		list.add(new Cat());

		List<Dog> dogs = new ArrayList<>();
		dogs.add(new Dog());
		new Main2().check(dogs);
		new Main2().check(list);

	}
}
```

So, `<? super Dog>` allows Dog and its super type list but not to add anything other than Dog

## What about List of Arrays?

```java
import java.util.*;

class Animal {
	void dude() {
	}
}

class Dog extends Animal {
	public void dude() {
		System.out.println("Dog Dude");
	}
}

class Cat extends Animal {
	public void dude() {
		System.out.println("Cat Dude");
	}
}

public class Main2 {

	void check(List<? extends Object> list) {
		System.out.println(list.size());
//		list.add(new Dog()); // ERROR
//		list.add(new Animal()); // ERROR
	}

	public static void main(String[] args) {
		List<Animal> list = new ArrayList<>();
		list.add(new Dog());
		list.add(new Cat());

		List<Dog> dogs = new ArrayList<>();
		dogs.add(new Dog());
		new Main2().check(dogs);
		new Main2().check(list);

		Animal[] a = new Animal[4];
		List<Animal[]> dude = new ArrayList<>();
		new Main2().check(dude);
	}
}
```
It works perfectly

## Object vs <?> vs `<? extends Object>`

`<Object>` allows only `List<Object>`, no one else, however, `<?>` allows everyone but not to add.
`<? extends Object>` is absolutely identical to `<?>`.

## Some examples

```java
1. List<?> list = new ArrayList<Dog>();			// Accepted

2. List<? extends Animal> list = new ArrayList<Dog>();	// Accepted

3. List<?> list = new ArrayList<? extends Animal>();

4. List<? extends Dog> list = new ArrayList<Integer>();

5. List<? super Dog> list = new ArrayList<Animal>();	// Accepted

6. List<? super Animal> list= new ArrayList<Dog>();
```
3. You cannot use wildcard notation in the object creation.
6. Dog is below Animal Heirarchy.

## Generic Declarations

```java
interface MyInterface {}

class Bablya<A extends Object, T, E, AnyFuckingName> {
	T t;
	Bablya(T t) {
		this.t = t;
	}

	T getT() {
		return t;
	}
}

public class Main3 {
	public static void main(String[] args) {
		System.out.println(new Bablya<String, Integer, MyInterface, Float>(5).getT());
	}
}
```

## Creating Generic Methods
The class itself doesnt need to be generic; we just want method we can pass a type to and that can use that type to construct a type safe collection.

```java
public class CreateAnArrayList {
	public <T> void makeArrayList(T t) {
		List<T> list = new ArrayList<>();
		list.add(t);
	}
}
```

You need to declare the type variable BEFORE the return type of the method.