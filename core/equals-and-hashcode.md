### Overriding equals()

`==` operator evaluates to true only when both references refer to the same object. When you really need to know if two references are identical, use ==.

For example: If two references refer to one car, then you know that both are talking about one car, not two cars that have the same attributes.

```java
class Moof {
	private int moofValue;

	Moof(int val) {
		this.moofValue = val;
	}

	public int getMoofValue() {
		return moofValue;
	}

	public boolean equals(Object o) {
		if((o instanceof Moof) && ((Moof)o).getMoofValue() == this.moofValue) {
			return true;
		} else {
			return false;
		}
	}
}
```

You'd still have to do the instanceof test just to be sure that you could cast the object argument to the correct type so that you can access its methods or variables in order to actually do the comparison.

### The equals() Contract

- It is reflexive. For any reference value x, x.equals(x) should return true.
- It is symmetric. For any reference value, x and y, x.equals(y) should return true if and only if y.equals(x) return true.
- It is transitive. For any reference values x, y, and z if x.equals(y) returns true and y.equals(z) returns true, then x.equals(z) must return true.
- it is consistent. For any reference values x and y, multiple invocations of x.equals(y) consistently return true or consistently return false, provided no information used in equals comparisons on the object is modified.
- For any non-null reference value x, x.equals(null) should return false.

### hashCode()

Collections such as `HashMap` and `HashSet` use the hashcode value of an object to determine how the object should be stored in the collection, and the hashcode is used again to help locate the objects in the collection.

**EXPLANATION MISSING HERE**

The hashcode tells you only which bucket to go into, but not how to locate the name once we're in that bucket.

Hashing retrieval is a two-step process:
1. Find the right bucket(using hashCode())
2. Search the bucket for the right element(using equals())

It doesn't matter how equal the objects are if their hashcodes don't reflect that. If two objects are equal, their hashcodes must be equal as well.

### Implementing hashCode()

And to follow the contract, thinjk about what you do in the `equals()` method. You compare attributes.

Your hashCode() implementation should use the same instance variables.

A `hashCode()` that returns the same value for all instances whether they're equal or not is still a legal-even appropriate-hashCode() method.

```java
public int hashCode() {
	return 1234;
}
```

This does not violate the contract. But the problem here is, all the objects would land in the same bucket.

### The hashCode() Contract

| Condition                    | Required                     | Not Required               |
|------------------------------|------------------------------|----------------------------|
| x.equals(y) == true          | x.hashCode() == y.hashCode() |                            |
| x.hashCode() == y.hashCode() |                              | x.equals(y) == true        |
| x.equals(y) == false         |                              | No hashCode() requirements |
| x.hashCode() != y.hashCode() | x.equals(y) == false         |                            |


**EXPLANATION MISSING**

  Bottom line: `transient` vairables can really mess with your equals() and hashCode() implementations. Keep variables non-transient or, if they must be marked transient, don't use them to determine hashcodes or equality.