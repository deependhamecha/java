# Functional programming and Lambda

### Defining Functional Interface

1. A functional interface has only one abstract method.
2. If `@FunctionInterface` annotation is not applied and if there is only one abstract method in the interface then it considers it as a **FunctionInterface**.
3. If `@FunctionInterface` annotation is applied, only one abstract method is allowed.
4. It is a good practice to annotate it as other developers would understand or else they may misunderstand it as normal interface.

```java
@FunctionalInterface
public interface Sprint {
  public void sprint(Animal animal);
}

public interface Run extends Sprint {}

public interface SprintFaster extends Sprint {
  public void sprint(Animal animal);
}
public interface Skip extends Sprint {
  public default int getHopCount(Kangaroo kangaroo) {return 10;}
  public static void skip(int speed) {}
}
```

### Lambda

Lambda is just an implementation of an interface.

Example:
```java
interface Fruit {
	void display();
}
public class Lamda2 {

	public static void main(String[] args) {

		Fruit fruit1 = () -> System.out.println("Apple");
		Fruit fruit2 = () -> System.out.println("Mango");
		Fruit fruit3 = () -> System.out.println("Orange");
		
		fruit1.display();
		fruit2.display();
		fruit3.display();
	}

}
```


Lambda expressions can access static variables, instance variables, effectively final method parameters, and effectively final local variables.

We know it is effectively final since there are no reassignments to that variable. an effectively final local variable. A lambda can’t access private variables in another class.

```java
interface Gorilla { String move(); }

class GorillaFamily {
    static String walk = "walk";
    
    private String name="Deepen";

    void everyonePlay(final boolean baby) {
        String approach = "ample";
        // approach = "run";

        play(() -> walk+" "+this.name); // it can access private members of the same class
        play(() -> baby ? "hitch a ride": "run");
        play(() -> approach);

    }

    void play(Gorilla g) {
        System.out.println(g.move());
    }
}

public class Lambda1 {
    public static void main(String[] a) {
        new GorillaFamily().everyonePlay(true);
    }    
}
```

For Lambda, there are 2 basic requirements:
1. A Valid Interface
2. It should have only one abstract method

Now, there are pre built Lambda expressions for common purpose which can be used. Consider it kind of like a Wrapper around your class. For example, in above case Gorilla has no input and returns a value, so rather than creating an interface separately use pre built Supplier interface instead.

1. [Supplier](#supplier)
2. [Consumer and BiConsumer](#consumer)
3. [Predicate and BiPredicate](#predicate)
4. [Function and BiFunction](#functionbifunction)
5. [UnaryOperator and BinaryOperator](#unarybinaryoperator)

# Supplier <a name="supplier"></a>
A Supplier is used when you want to generate or supply values without taking any input. T is your return type.

```java
@FunctionalInterface public class Supplier<T> {
	public T get();
}
```
Now the question arises is that, how do you call the method or return the value of it?

```java
import java.util.function.Supplier;


public class Lambda3Supplier {
	public static void main(String[] ar) {
		
		Supplier<String> dog = () -> "Bark";
		
		System.out.println(dog.get());
	}
}
```

Try to Supplier, you will get this:

```bash
com.practice.Lambda4MethodReference$$Lambda$25/0x0000000800ba7840@52cc8049
```

Then comes $$, which means that the class doesn’t exist in a class file on the file system. It exists only in memory. You don’t need to worry about the rest.

Below exampe
```java
interface Name {
	void getName(String name);
}

public class Lambda4MethodReference {
	public static void main(String[] a) {
		Name nameObj1 = (String name) -> System.out.println(name);
		
		Name nameObj2 = System.out::println;
		
		nameObj1.getName("Bruce");
		
		nameObj2.getName("Clark");
	}
}
```

It is also used for creating object with new
```java
class Dog {
	String bark() {
		return "Bark";
	}
}

public class Lambda4MethodReference {

	public static void main(String[] a) {

		Supplier<Dog> dog = Dog::new;

		System.out.println(dog.get().bark());
	}
}
```

Another example with collection:
```java
Supplier<ArrayList<String>> s1 = ArrayList<String>::new;
```

# Consumer and BiConsumer <a name="consumer"></a>

You use a Consumer when you want to do something with a parameter but not return anything. BiConsumer does the same thing except that it takes two parameters.

```java
import java.util.function.Consumer;

public class Lambda5Consumer {

	public Lambda5Consumer() {
		// TODO Auto-generated constructor stub
		System.out.println("Constructor");
	}

	public String toString() {
		return "Lambda5Consumer";
	}

	public static void main(String[] args) {

		Consumer<Lambda5Consumer> consumer = System.out::println;
		// Consumer<Lambda5Consumer> consumer = (x) -> System.out.println(x);
		consumer.accept(new Lambda5Consumer());
		
		Consumer<String> s1 = System.out::println;
		// Consumer<String> s1 = (x) -> System.out.println(x);
		s1.accept("Tony Stark");
	}
}
```

BiConsumer Example (Customer Class):
```java
import java.util.function.BiConsumer;

class Rectangle {
	private int length;
	private int breadth;
	
	void calculate(int length, int breadth) {
		System.out.println(length*breadth+" Sq.");
	}
	
	static void calculateWithStatic(int length, int breadth) {
		System.out.println(length*breadth+" Sq.");
	}
}
public class Lambda6CustomMethodReference {

	public static void main(String[] args) {
		// TODO Auto-generated method stub

		Rectangle rectangle = new Rectangle();
		BiConsumer<Integer, Integer> biConsumer = rectangle::calculate;
//		BiConsumer<Integer, Integer> biConsumer = (x, y) -> rectangle.calculate(x, y);
		
		biConsumer.accept(4, 5);
		
		BiConsumer<Integer, Integer> biConsumer1 = Rectangle::calculateWithStatic;
		biConsumer1.accept(3, 4);
	}
}
```

# Predicate and BiPredicate <a name="predicate"></a>

Predicate accepts one input and returns a boolean.
BiPredicate accepts two inputs and returns a boolean.

```java
import java.util.function.BiPredicate;
import java.util.function.Predicate;

class Strings {
	
	static boolean compare(String s1, String s2) {
		return s1.equals(s2);
	}
}

public class Lambda7PredicateBi {

	public static void main(String[] a) {
		Predicate<String> p1 = String::isEmpty;
		
		System.out.println(p1.test(" "));
		
		
		BiPredicate<String, String> p2 = Strings::compare;
		System.out.println(p2.test("D", "D"));
	}
}
```
Note: Using methods on Predicates

Suppose that we have these two Predicates:
```java
Predicate<String> egg = s -> s.contains("egg");
Predicate<String> brown = s -> s.contains("brown");
```
Now we want a Predicate for brown eggs and another for all other colors of eggs. We
could write this by hand:
```java
Predicate<String> brownEggs = s -> s.contains("egg") && s.contains("brown");
Predicate<String> otherEggs = s -> s.contains("egg") && ! s.contains("brown");
```
This works, but it’s not great. It’s a bit long to read, and it contains duplication. What if
we decide the letter e should be capitalized in eggs? We’d have to change it in three variables:
egg, brownEggs, and otherEggs.
A better way to deal with this situation is to use two of the default methods on
Predicate:
```java
Predicate<String> brownEggs = egg.and(brown);
Predicate<String> otherEggs = egg.and(brown.negate());
```
Neat! Now we are reusing the logic in the original Predicates to build two new ones. It’s
shorter and clearer what the relationship is between the Predicates. We can also change
the spelling of egg in one place, and the other two objects will have new logic because
they reference it.

# Function and BiFunction <a name="functionbifunction"></a>

Take Inputs of different types and return different type.

```java
import java.util.function.BiFunction;
import java.util.function.Function;

class AnyClass {
	public String concatenate(String s1, Integer s2) {
		return s1.toLowerCase()+""+s2;
	}
}

public class Lambda8FunctionBi {

	public static void main(String[] args) {
		// TODO Auto-generated method stub

		String name = new String("Deepen");
		Function<Integer, Character> f1 = name::charAt;
		Function<String, Integer> f2 = String::length;

		System.out.println(f1.apply(1));
		System.out.println(f2.apply(name));

		AnyClass nameObj = new AnyClass(); 
		BiFunction<String, Integer, String> bif1 = nameObj::concatenate;

		System.out.println("BiFunction: "+bif1.apply("Deepen", 123));
	}
}
```

# UnaryOperator and BinaryOperator <a name="unarybinaryoperator"></a>
UnaryOperator and BinaryOperator are a special case of a function. They require all type parameters to be the same type. In fact, UnaryOperator extends Function. A BinaryOperator merges two values into one of the same type. Adding two number is a binary operation. Similarly, BinaryOperator extends BiFunction.

### Method References (::)

There are following types of method references:
1. Reference to a static method.
2. Reference to an instance method.
3. Reference to a constructor.

1. 
```java
interface Sayable {
    void say();  
}  
public class MethodReference {  
    public static void saySomething(){  
        System.out.println("Hello, this is static method.");  
    }  
    public static void main(String[] args) {  
        // Referring static method  
        Sayable sayable = MethodReference::saySomething;  
        // Calling interface method  
        sayable.say();  
    }  
}
```
2. 
```java
interface Sayable{  
    void say();  
}  
public class InstanceMethodReference {  
    public void saySomething(){  
        System.out.println("Hello, this is non-static method.");  
    }  
    public static void main(String[] args) {  
        InstanceMethodReference methodReference = new InstanceMethodReference(); // Creating object  
        // Referring non-static method using reference  
            Sayable sayable = methodReference::saySomething;  
        // Calling interface method  
            sayable.say();  
            // Referring non-static method using anonymous object  
            Sayable sayable2 = new InstanceMethodReference()::saySomething; // You can use anonymous object also  
            // Calling interface method  
            sayable2.say();  
    }  
}
```

3. 
```java
interface Messageable{  
    Message getMessage(String msg); // Make sure the return type is of the Class
}  
class Message{  
    Message(String msg){  
        System.out.print(msg);  
    }

	void dude() {
		System.out.println(dude);
	}
}  
public class ConstructorReference {  
    public static void main(String[] args) {  
        Messageable hello = Message::new;  
        Message message = hello.getMessage("Hello");
		message.dude();
    }  
}  
```