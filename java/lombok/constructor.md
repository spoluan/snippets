### **Constructors in Lombok**

Lombok provides annotations to automatically generate constructors for Java classes. These annotations save time and reduce boilerplate code by creating constructors based on the fields in the class. The most commonly used annotations for constructors in Lombok are:

1. **`@NoArgsConstructor`**  
2. **`@AllArgsConstructor`**  
3. **`@RequiredArgsConstructor`**

---

### **1. @NoArgsConstructor**

The `@NoArgsConstructor` annotation generates a **no-argument (default) constructor** for the class. This constructor initializes the fields to their default values:
- `null` for objects,
- `0` for numeric types,
- `false` for booleans.

#### **Example:**

```java
import lombok.NoArgsConstructor;

@NoArgsConstructor
public class User {
    private String username;
    private String password;
}
```

**Generated Code:**

```java
public class User {
    private String username;
    private String password;

    public User() {
        // No-argument constructor
    }
}
```

---

### **2. @AllArgsConstructor**

The `@AllArgsConstructor` annotation generates a constructor with **parameters for all fields** in the class, in the order they are declared.

#### **Example:**

```java
import lombok.AllArgsConstructor;

@AllArgsConstructor
public class User {
    private String username;
    private String password;
}
```

**Generated Code:**

```java
public class User {
    private String username;
    private String password;

    public User(final String username, final String password) {
        this.username = username;
        this.password = password;
    }
}
```

---

### **3. @RequiredArgsConstructor**

The `@RequiredArgsConstructor` annotation generates a constructor with **parameters for all `final` fields** or fields annotated with **`@NonNull`**. Fields that are not `final` or `@NonNull` are excluded from the constructor.

#### **Example:**

```java
import lombok.RequiredArgsConstructor;
import lombok.NonNull;

@RequiredArgsConstructor
public class User {
    @NonNull
    private String username;
    private String password;
}
```

**Generated Code:**

```java
public class User {
    private final String username;
    private String password;

    public User(final String username) {
        this.username = username;
    }
}
```

---

### **4. Combining Constructor Annotations**

You can combine annotations to generate multiple constructors in the same class.

#### **Example:**

```java
import lombok.AllArgsConstructor;
import lombok.NoArgsConstructor;

@NoArgsConstructor
@AllArgsConstructor
public class User {
    private String username;
    private String password;
}
```

**Generated Code:**

```java
public class User {
    private String username;
    private String password;

    public User() {
        // No-argument constructor
    }

    public User(final String username, final String password) {
        this.username = username;
        this.password = password;
    }
}
```

---

### **5. Access Level for Constructors**

You can control the access level of the generated constructors using the `AccessLevel` parameter (e.g., `PRIVATE`, `PROTECTED`, `PUBLIC`, etc.).

#### **Example:**

```java
import lombok.NoArgsConstructor;
import lombok.AllArgsConstructor;
import lombok.AccessLevel;

@NoArgsConstructor(access = AccessLevel.PROTECTED)
@AllArgsConstructor(access = AccessLevel.PRIVATE)
public class User {
    private String username;
    private String password;
}
```

**Generated Code:**

```java
public class User {
    private String username;
    private String password;

    protected User() {
        // No-argument constructor with protected access
    }

    private User(final String username, final String password) {
        this.username = username;
        this.password = password;
    }
}
```

---

### **6. Example with All Annotations**

#### **Code:**

```java
import lombok.Getter;
import lombok.Setter;
import lombok.NoArgsConstructor;
import lombok.AllArgsConstructor;
import lombok.RequiredArgsConstructor;
import lombok.NonNull;

@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
public class Product {
    @NonNull
    private String id;
    private String name;
    private double price;
}
```

**Generated Code:**

```java
public class Product {
    private final String id;
    private String name;
    private double price;

    // No-argument constructor
    public Product() {}

    // Constructor with all fields
    public Product(final String id, final String name, final double price) {
        this.id = id;
        this.name = name;
        this.price = price;
    }

    // Constructor with required (final or @NonNull) fields
    public Product(final String id) {
        this.id = id;
    }
}
```

---

### **7. Real-World Example**

#### **Code:**

```java
import lombok.Getter;
import lombok.Setter;
import lombok.NoArgsConstructor;
import lombok.AllArgsConstructor;

@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class Customer {
    private String id;
    private String name;
}
```

#### **Generated Code:**

```java
public class Customer {
    private String id;
    private String name;

    // No-argument constructor
    public Customer() {}

    // All-argument constructor
    public Customer(final String id, final String name) {
        this.id = id;
        this.name = name;
    }
}
```

---

### **8. Constructor with Final Fields Only**

#### **Code:**

```java
import lombok.RequiredArgsConstructor;

@RequiredArgsConstructor
public class Order {
    private final String orderId;
    private String productName;
    private double price;
}
```

**Generated Code:**

```java
public class Order {
    private final String orderId;
    private String productName;
    private double price;

    // Constructor with final fields only
    public Order(final String orderId) {
        this.orderId = orderId;
    }
}
```

---

### **9. Constructor with Access Level and Custom Fields**

#### **Code:**

```java
import lombok.AllArgsConstructor;
import lombok.NoArgsConstructor;
import lombok.AccessLevel;

@NoArgsConstructor(access = AccessLevel.PRIVATE)
@AllArgsConstructor(access = AccessLevel.PUBLIC)
public class Employee {
    private String id;
    private String name;
    private double salary;
}
```

**Generated Code:**

```java
public class Employee {
    private String id;
    private String name;
    private double salary;

    private Employee() {
        // Private no-argument constructor
    }

    public Employee(final String id, final String name, final double salary) {
        this.id = id;
        this.name = name;
        this.salary = salary;
    }
}
```

---

### **10. Summary of Constructor Annotations**

| **Annotation**         | **Description**                                                                                  |
|-------------------------|--------------------------------------------------------------------------------------------------|
| `@NoArgsConstructor`    | Generates a no-argument constructor.                                                            |
| `@AllArgsConstructor`   | Generates a constructor with all fields as parameters.                                          |
| `@RequiredArgsConstructor` | Generates a constructor with `final` or `@NonNull` fields only.                                |
| `AccessLevel`           | Controls the visibility of the generated constructor (e.g., `PRIVATE`, `PROTECTED`, `PUBLIC`). |

---

### **When to Use**

- Use `@NoArgsConstructor` when you need a default constructor for frameworks like Hibernate or JPA.
- Use `@AllArgsConstructor` when you need a constructor that initializes all fields.
- Use `@RequiredArgsConstructor` when you need a constructor for mandatory fields (`final` or `@NonNull`).
