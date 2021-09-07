# Inner Classes

# Nested Classes

There are 4 types of nested classes in Java : 
1. Member Inner Class
2. Static Nested Class
3. Anonymous Inner Class
4. Local Anonymous Inner Class

## 1. Inner Classes

1. Class can have all access specifiers (private, protected, default, public).

2. It can also access private members of the Outer Class : 
```java
class Outer {
  private int x =5; 
  
  class Inner {
    int x = Outer.this.x; // Initializing with Outer instance variable.
  }
}
```

3. You nest inner classes to create Inception and refer to one another's members using above syntax.
```java
class Dream1 {
  private int a=3
  class Dream2 {
    private int a=3;
    class Dream3 {
      private int a = Dream1.this.a;
    }
  }
}
```

4. Can extend any class and any number of interfaces.
```java
class AnyClass {
  interface AnyInterface {}
  
  class AnyInnerClass implements AnyInterface {}
}
```

5. Can extend classes outside the Outer class but not the inner classes of any class as they are inner classes are used by instantiating.
```java
public class Item1 {
  class Item1Inner {
  
  }
}

public class Outer {
  class Inner extends Item1 { // This is possible
    
  }
  
  class Inner extends Item1.Item1Inner {}   // but not this
}
```

You can extend this type of inner classes using **Static Nested Classes**.

6. Can have **abstract** and **final** keywords.
```java
class Outer {
  abstract class Abc {}
  
  class Def extends Abc {}

  final class Xyz {}
}
```

7. **Cannot have static members**.

## Static Inner Class

Has same properties as inner classes but with subtle diferences :

1. Cannot access outer class members directly rather it needs to create an instance.
```java
class Outer {
  int x=5;
  static class StaticInner {
    Outer.this.x; // FAILS...JUST IMAGEINE, YOU ARE IN STATIC AND TRYING TO ACCESS USING THIS
    // Just like main method
    new Outer().x;
  }
}
```

2. Refer to 1.5 point and come back :

```java
public class Item1 {
  class Item1Inner {
  
  }
}

public class Outer {
  static class Inner extends Item1 { // This is possible
    
  }
  
  class Inner extends Item1.Item1Inner {} // Can do this
```

## 3. Anonymous Inner Class

1. Normal Anonymous class

```java
class Class1 {
  public void method1() {}
}

Class1 c = new Class() {
  public void method1() {}
};
```
## Anonymous Local Class

If defined inside a method then can use local variables only if they are explicitly defined final or effectively final.