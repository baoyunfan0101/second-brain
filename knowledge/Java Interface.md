---
tags:
  - java
summary: Java Interface
use_cases:
  - backend
---

# Java Interface

## Default Method

A method with an implementation inside an interface.

Example:

```java
interface Logger {
    void log(String msg);

    // Default implementation provided by the interface
    default void logError(String msg) {
        log("[ERROR] " + msg);
    }
}

class ConsoleLogger implements Logger {
    @Override
    public void log(String msg) {
        System.out.println(msg);
    }
}

public class Main {
    public static void main(String[] args) {
        Logger logger = new ConsoleLogger();

        logger.log("Hello");
        logger.logError("Something went wrong");
    }
}
```

## Functional Interface

An interface with exactly one abstract method.

Example:

```java
@FunctionalInterface
interface Action {

    // The single abstract method
    void execute(String input);
}

public class Main {
    public static void main(String[] args) {

        // Lambda expression implementing Action
        Action action = msg -> System.out.println("Action: " + msg);

        action.execute("Hello");

        // Equivalent to:
        /*
        Action action = new Action() {
            @Override
            public void execute(String input) {
                System.out.println("Action: " + input);
            }
        };
        */
    }
}
```

## Marker Interface

An interface with no methods.

Example:

```java
// No methods
interface SerializableMarker {
}

class User implements SerializableMarker {
    String name = "Alice";
}

public class Main {
    public static void main(String[] args) {
        User user = new User();

        // Check whether the object has the marker
        if (user instanceof SerializableMarker) {
            System.out.println("Serializable object");
        }
    }
}
```

## Generic Interface

An interface parameterized by one or more types.

Example:

```java
interface Box<T> {

    void set(T value);

    T get();
}

class StringBox implements Box<String> {

    private String value;

    @Override
    public void set(String value) {
        this.value = value;
    }

    @Override
    public String get() {
        return value;
    }
}

public class Main {
    public static void main(String[] args) {

        Box<String> box = new StringBox();

        box.set("hello");

        String value = box.get();

        System.out.println(value);
    }
}
```

## Sealed Interface (Java 17+)

An interface that explicitly controls which classes may implement it.

Example:

```java
// Only Circle and Rectangle are allowed to implement Shape
sealed interface Shape permits Circle, Rectangle {
}

final class Circle implements Shape {
}

final class Rectangle implements Shape {
}

// Compilation error:
// Triangle is not listed in permits
final class Triangle implements Shape {
}

public class Main {
    public static void main(String[] args) {

        Shape shape = new Circle();

        if (shape instanceof Circle) {
            System.out.println("Circle");
        }
    }
}
```