### Streams

```java
	Stream<String> empty = Stream.empty();
	Stream<Integer> singleElement = Stream.of(1);
	Stream<Integer> fromArray = Stream.of(1,2,3);
	Stream<String> list = Arrays.asList("a", "b", "c");
	Stream<String> fromList = list.stream();
	Stream<String> fromListParallel = list.parallelStream();
````

The method signatures uses varargs.

  Just keep in mind that it isnt worth working in parallel for small streams.

List can only be finite but streams can be infinite.

```java
Stream<Double> randoms = Stream.generate(Math::random);
Stream<Integer> oddNumbers = Stream.iterate(1, n -> n+2);
```

If you print stream object then you will get something like
```bash
java.util.stream.ReferencePipeline$Head@7291c18f
```

### count()
```java
Stream<Integer> fromArray = Stream.of(1,2,3);
System.out.println(fromArray.count()); // 3
```

### min() and max()
The min() and max() methods allow you to pass a custom comparator and find the smallest or largest value in a finite stream according to that sort order. They cant be used with infinite stream.
```java
Stream<String> s = Stream.of("monkey", "ape", "bonobo");
Optional<String> min = s.min((s1, s2) -> s1.length() - s2.length());
min.ifPresent(System.out::println); //ape
```
```java
Optional<?> minEmpty = Stream.empty().min((s1, s2) -> 0);
System.out.println(minEmpty.isPresent()); // false
```
Since the stream is empty, the comparator is never called and no value is present in the Optional.

### findAny() and findFirst()
findAny is useful when you are working wiht a parallel stream. It gives Java the flexibility to return to you the first element it comes by rather than the one that needs to be first in the stream based on the intermediate operations.

```java
Stream<String> s = Stream.of("monkey", "gorilla", "bonobo");
Stream<String> infinite = Stream.generate(() -> "chimp");
s.findAny().ifPresent(System.out::println); // monkey
infinite.findAny().ifPresent(System.out::println); // chimp
```

### allMatch(), anyMatch() and noneMatch()
```java

```

### forEach()
```java

```

### reduce()

### collect()

### filter()

### distinct()

### limit() and skip()

### map()


### flatMap()

### sorted()

### peek()

