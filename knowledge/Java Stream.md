---
tags:
  - java
summary: Java Stream
use_cases:
  - backend
---

# Java Stream

## Stream

- Java Stream was introduced in Java 8.
- A lazy data processing pipeline for collections, arrays, files, generators, etc.
- Similar to Spark RDD/DataFrame transformations.

## Example

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5);

List<Integer> result = numbers
		.stream() // Create a Stream (source=numbers, operations=[]).
        .filter(x -> x > 0) // NO new list is created.
        .map(x -> x * 10) // NO new list is created.
		.sorted() // NO new list is created
        .toList(); // Execution starts.
```

## Lazy Execution

```java
numbers.stream()
       .filter(x -> {
           System.out.println(x);
           return x > 0;
       });
```

Output:

```text
No output
```

## Terminal Operation

Execution starts when:

```java
count()
toList()
collect()
forEach()
findFirst()
reduce()
```

is called.

## Method Reference

```java
stream.map(User::name)
```

Equivalent to:

```java
stream.map(user -> user.name())
```

`::` passes a method as a function.

Contrast:

```java
User::name   // Function reference
user.name()  // Method call
user.name    // Field access
```