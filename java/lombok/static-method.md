### **Static Methods in Lombok**

Lombok allows you to generate **static factory methods** for object creation using the `staticName` parameter in constructor annotations like `@NoArgsConstructor`, `@AllArgsConstructor`, and `@RequiredArgsConstructor`. This is useful when you want to enforce the use of static methods for object creation instead of directly calling constructors.

---

### **How It Works**

When you use the `staticName` parameter:
1. Lombok generates a **static method** with the specified name.
2. The generated static method internally calls the constructor to create the object.
3. The constructor becomes **private**, enforcing the use of the static method for object creation.

---

### **Advantages of Using Static Methods**

1. **Improved Readability:** Naming the static method (e.g., `create`, `createEmpty`) makes the purpose of the object creation more explicit.
2. **Encapsulation:** The constructor is private, so the class has better control over object creation.
3. **Custom Logic:** Static methods can be extended to include additional logic before creating the object.

---

### **Annotations Supporting Static Methods**

- `@NoArgsConstructor(staticName = "methodName")`
- `@AllArgsConstructor(staticName = "methodName")`
- `@RequiredArgsConstructor(staticName = "methodName")`

---

## **Examples**

### **1. Using `@NoArgsConstructor` with Static Method**

#### **Code:**
```java
import lombok.Getter;
import lombok.NoArgsConstructor;

@Getter
@NoArgsConstructor(staticName = "createEmpty")
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

    // Private no-argument constructor
    private User() {}

    // Static factory method
    public static User createEmpty() {
        return new User();
    }
}
```

**Usage:**
```java
User user = User.createEmpty(); // Calls the static method
```

---

### **2. Using `@AllArgsConstructor` with Static Method**

#### **Code:**
```java
import lombok.AllArgsConstructor;
import lombok.Getter;

@Getter
@AllArgsConstructor(staticName = "create")
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

    // Private all-argument constructor
    private User(final String username, final String password) {
        this.username = username;
        this.password = password;
    }

    // Static factory method
    public static User create(final String username, final String password) {
        return new User(username, password);
    }
}
```

**Usage:**
```java
User user = User.create("john_doe", "password123"); // Calls the static method
```

---

### **3. Using `@RequiredArgsConstructor` with Static Method**

#### **Code:**
```java
import lombok.Getter;
import lombok.RequiredArgsConstructor;

@Getter
@RequiredArgsConstructor(staticName = "of")
public class Product {
    private final String id;
    private String name;
}
```

**Generated Code:**
```java
public class Product {
    private final String id;
    private String name;

    // Private constructor with required fields
    private Product(final String id) {
        this.id = id;
    }

    // Static factory method
    public static Product of(final String id) {
        return new Product(id);
    }
}
```

**Usage:**
```java
Product product = Product.of("123"); // Calls the static method
```

---

### **4. Combining Static Methods with Multiple Annotations**

You can combine `@NoArgsConstructor` and `@AllArgsConstructor` with different `staticName` values to generate multiple static methods.

#### **Code:**
```java
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.AllArgsConstructor;

@Getter
@NoArgsConstructor(staticName = "createEmpty")
@AllArgsConstructor(staticName = "create")
public class Customer {
    private String id;
    private String name;
}
```

**Generated Code:**
```java
public class Customer {
    private String id;
    private String name;

    // Private no-argument constructor
    private Customer() {}

    // Private all-argument constructor
    private Customer(final String id, final String name) {
        this.id = id;
        this.name = name;
    }

    // Static factory methods
    public static Customer createEmpty() {
        return new Customer();
    }

    public static Customer create(final String id, final String name) {
        return new Customer(id, name);
    }
}
```

**Usage:**
```java
Customer emptyCustomer = Customer.createEmpty(); // Calls createEmpty()
Customer customer = Customer.create("001", "John Doe"); // Calls create()
```

---

### **5. Real-World Example: Login Class**

#### **Code:**
```java
import lombok.Getter;
import lombok.Setter;
import lombok.NoArgsConstructor;
import lombok.AllArgsConstructor;
import lombok.AccessLevel;

@Getter
@NoArgsConstructor(staticName = "createEmpty")
@AllArgsConstructor(staticName = "create")
public class Login {
    @Setter(value = AccessLevel.PROTECTED)
    private String username;

    @Setter(value = AccessLevel.PROTECTED)
    private String password;
}
```

**Generated Code:**
```java
public class Login {
    private String username;
    private String password;

    // Private no-argument constructor
    private Login() {}

    // Private all-argument constructor
    private Login(final String username, final String password) {
        this.username = username;
        this.password = password;
    }

    // Static factory methods
    public static Login createEmpty() {
        return new Login();
    }

    public static Login create(final String username, final String password) {
        return new Login(username, password);
    }
}
```

**Usage:**
```java
Login emptyLogin = Login.createEmpty(); // Calls createEmpty()
Login login = Login.create("admin", "secret"); // Calls create()
```

---

### **6. Advanced Example with Custom Logic in Static Methods**

You can enhance the generated static methods by adding custom logic.

#### **Code:**
```java
import lombok.AllArgsConstructor;

@AllArgsConstructor(staticName = "create")
public class Employee {
    private String id;
    private String name;

    public static Employee create(String id, String name) {
        if (id == null || id.isEmpty()) {
            throw new IllegalArgumentException("ID cannot be null or empty");
        }
        return new Employee(id, name);
    }
}
```

**Usage:**
```java
Employee emp = Employee.create("E001", "Alice"); // Valid
Employee invalidEmp = Employee.create("", "Bob"); // Throws IllegalArgumentException
```

---

### **Summary of Static Methods in Lombok**

| **Annotation**         | **Description**                                                                                  |
|-------------------------|--------------------------------------------------------------------------------------------------|
| `@NoArgsConstructor`    | Generates a no-argument constructor and a static method (if `staticName` is provided).           |
| `@AllArgsConstructor`   | Generates an all-argument constructor and a static method (if `staticName` is provided).         |
| `@RequiredArgsConstructor` | Generates a constructor for `final` or `@NonNull` fields and a corresponding static method.       |

---

### **Benefits of Static Methods**

1. **Encapsulation:** Prevents direct access to constructors.
2. **Custom Naming:** Makes the purpose of object creation explicit.
3. **Custom Logic:** Allows adding validation or pre-processing during object creation.
4. **Consistency:** Enforces a consistent way to create objects.
