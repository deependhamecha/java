# Annotations

Normally, Java annotations are not present in your Java code after compilation. It is possible, however, to define your own annotations that are available at runtime. These annotations can then be accessed via Java Reflection, and used to give instructions to your program, or some third party API.

A Java annotation can have elements for which you can set values. An element is like an attribute or parameter. Here is an example of a Java annotation with an element:

```java
@Entity(tableName = "vehicles")
```

In case an annotation contains just a single element, it is convention to name that element value, like this:

```java
@InsertNew(value = "yes")
```
When an annotation just contains a single element named value, you can leave out the element name, and just provide the value. Here is an example of an annotation element with only the value provided:

```java
@InsertNew("yes")
```

You can place Java annotations above classes, interfaces, methods, method parameters, fields and local variables.

Java comes with three built-in annotations which are used to give the Java compiler instructions. These annotations are:

```java
@Deprecated
@Override
@SuppressWarnings
```

#### @Retention

You can specify for your custom annotation if it should be available at runtime, for inspection via reflection. You do so by annotating your annotation definition with the `@Retention` annotation. Here is how that is done:

```java
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

@Retention(RetentionPolicy.RUNTIME)

@interface MyAnnotation {

    String   value() default "";

}
```
Notice the `@Retention` annotation added above the MyAnnotation definition:

```java
@Retention(RetentionPolicy.RUNTIME)
```

The RetentionPolicy class contains two more values you can use:

`RetentionPolicy.CLASS` means that the annotation is stored in the .class file, but not available at runtime. This is the default retention policy, if you do not specify any retention policy at all.

`RetentionPolicy.SOURCE` means that the annotation is only available in the source code, and not in the `.class` files and not a runtime. If you create your own annotations for use with build tools that scan the code, you can use this retention policy. That way the .class files are not polluted unnecessarily.

#### @Target

You can specify which Java elements your custom annotation can be used to annotate. You do so by annotating your annotation definition with the `@Target` annotation. Here is a `@Target` Java annotation example:

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Target;

@Target({ElementType.METHOD})
public @interface MyAnnotation {

    String   value();
}
```
This example shows a Java annotation that can only be used to annotate methods.

The **ElementType** class contains the following possible targets:

```java
ElementType.ANNOTATION_TYPE
ElementType.CONSTRUCTOR
ElementType.FIELD
ElementType.LOCAL_VARIABLE
ElementType.METHOD
ElementType.PACKAGE
ElementType.PARAMETER
ElementType.TYPE
```
Most of these are self explaining, but a few are not. Therefore I will explain the targets which are not obvious.

The `ANNOTATION_TYPE` target means Java annotation definitions. Thus, the annotation can only be used to annotate other annotations. Like the `@Target` and `@Retention` annotations.

The `TYPE` target means any type. A type is either a class, interface, enum or annotation.

#### @Inherited

The `@Inherited` annotation signals that a custom Java annotation used in a class should be inherited by subclasses inheriting from that class. Here is an `@Inherited` Java annotation example:

```java
java.lang.annotation.Inherited

@Inherited
public @interface MyAnnotation {

}

@MyAnnotation
public class MySuperClass { ... }

public class MySubClass extends MySuperClass { ... }
```
In this example the class MySubClass inherits the annotation `@MyAnnotation` because MySubClass inherits from MySuperClass, and MySuperClass has a `@MyAnnotation` annotation.

#### @Documented

The `@Documented` annotation is used to signal to the JavaDoc tool that your custom annotation should be visible in the JavaDoc for classes using your custom annotation. Here is a `@Documented` Java annotation example:

```java
import java.lang.annotation.Documented;

@Documented
public @interface MyAnnotation {

}

@MyAnnotation
public class MySuperClass { ... }
```

When generating JavaDoc for the MySuperClass class, the `@MyAnnotation` is now included in the JavaDoc.

You will not use the `@Documented` annotation often, but now you know it exists, if you should need it.