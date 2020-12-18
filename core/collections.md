## Collections

If you say "collection":
1. collection which represents any of the data structures in which objects are stored and iterated over.
2. Collection which is actually the java.util.Collection interface from which Set, List and Queue extend.
3. Collections is the java.util.Collections class that holds a pile of static utility methods for use with collections.

Collections come in four basic falvors:
- Lists
- Sets
- Maps
- Queues

There are sub-flavors within those four flavors of collections:

- Sorted
- Unsorted
- Ordered
- Unordered

An implementation class can be unsorted and unordered, ordered but unsorted or both ordered and sorted. But an implementation can never be sorted but unordered.

### List Interface

All three List implementations are ordered by index position.
Three list implementations:
- Array - Think of this as a growable array. It gives you fast iteration and fast random access. Choose this over a LinkedList when you need fast iteration but aren't as likely to be doing a lot of insertion and delection.

- Vector - A Vector is basically the same as an ArrayList, but Vector methods are synchronized for thread safety.

- LinkedList - Keep in mind that a LinkedList may iterate more slowly than an ArrayList, but its good choice when you need fast insertion and deletion.

### Set Interface

A Set cares about uniqueness - it doesn't allow duplicates.

- HashSet - A HashSet is an unsorted, unordered Set. Use this class when you want no duplicates and dont care about order when you iterate it.

- LinkedHashSet - A LinkedHashSet is an ordered version of HashSet that maintains a doubly-linked List across all elements. Use this class instead of HashSet when you care about iteration order. 

- TreeSet - Its a sorted set.

### Map Interface

A Map cares about unique identifiers.

- HashMap - The HashMap gives you an unsorted, unordered Map. HashMap allows one null key and multiple null values in a collection.

- Hashtable - Its Synchronized. Remember that you don't synchronize a class, so when we say that Vector and Hashtable are synchronized, we just mean that the key methods of the class are synchronized. While HashMap lets you have null values as well as one null key, a Hashtable doesn't let you have anything that's null.

- LinkedHashMap - Like its Set counterpart, LinkedHashSet. LinkedHashMap has faster iteration than HashMap but slower insertion and deletion then HashMap.

- TreeMap - TreeMap is sorted Map

### Queue Interface

- PriorityQueue



### Sorting Collections

```java
ArrayList<String> stuff = new ArrayList<>();
stuff.add('B');stuff.add('C');stuff.add('A');
Collections.sort(stuff)
```

### The Comparable Interface

The Comparable interface is used by the Collections.sort() method and the java.util.Arrays.sort() method to sort Lists and arrays of objects, resp.

The `compareTo()` method returns an int with the following characteristics:
- negative - If thisObject < anotherObject
- zero - If thisObject == anotherObject
- position - If thisObject > anotherObject

The `sort()` method uses `compareTo()` to determine how the List or object array should be sorted.

```java
class DVDInfo implements Comparable<DVDInfo> {

	public int compareTo(DVDInfo d) {
		return title.compareTo(d.getTitle());
	}
}
```

### Sorting with Comparator

The Comparator interface gives you the capability to sort a given collection any number of different ways.
```java
```

### Sorting with the Arrays Class

The good news is that sorting arrays of objects is just like sorting collections of objects. The `Arrays.sort()` method is overridden in the same way the `Collections.sort()` method is:
- `Arrays.sort(arrayToSort)`
- `Arrays.sort(arrayToSort, Comparator)`


  Dont be fooled by the exam question that tries to sort a primitive array using a Comparator.

### Searching Arrays and Collections

- Searches are performed using the `binarySearch()` method.
- Successful searches return the int index of the element being searched.
- Unsuccessful searches return an int index that represents the insertion point. The insertion point is the place in the collection/array where the element would be inserted to keep the collection/array properly sorted.
- The collection/array being searched must be sorted before you can search it.
- If you attempt to search an array or collection that has not already been sorted, the results of the search will not be predictable.
- If the collection/array you want to search was sorted in natural order, it must be searched in natural order.
- Remember that Comparators cannot be used when searching arrays of primitives.