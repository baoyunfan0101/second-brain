---
tags:
  - java
summary: Java Record
use_cases:
  - backend
---

# Java Record

## Record

- Java Record was introduced in Java 16.
- An immutable data class.
- Similar to Python's `@dataclass`.

## Example 1

```java
public record User(String name, int age) {}
```

Equivalent to:

```java
import java.util.Objects;

public final class User {

    private final String name;
    private final int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String name() {
        return name;
    }

    public int age() {
        return age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof User user)) return false;
        return age == user.age && Objects.equals(name, user.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }

    @Override
    public String toString() {
        return "User[name=" + name + ", age=" + age + "]";
    }
}
```

Usage:

```java
User user = new User("Alice", 18);

System.out.println(user.name()); // Alice
System.out.println(user.age());  // 18

// Compile error: cannot assign a value to final field
user.name = "Bob";

// Compile error: Record components are immutable
user.age = 25;
```

## Example 2

```java
import java.util.Objects;

public record User(String name, String role) {

    // Predefined global instance
    public static final User ADMIN =
            new User("System", "admin");

    // Compact constructor
    // Parameters (name, role) are automatically available
    public User {
		// Equivalent to:
		// if (name == null) {
		//     throw new NullPointerException("name must not be null");
		// }
		// name = name.trim();
        name = Objects.requireNonNull(name, "name must not be null").trim();

        if (name.isEmpty()) {
            throw new IllegalArgumentException("name cannot be blank");
        }
    }

    // Factory method
    public static User regular(String name) {
        return new User(name, "user");
    }

    public String displayName() {
        return role + ":" + name;
    }
}
```

Usage:

```java
User admin = User.ADMIN;
User alice = User.regular("Alice");

System.out.println(admin.displayName()); // admin:System
System.out.println(alice.displayName()); // user:Alice
```